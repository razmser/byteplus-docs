# Multi Agent

When a task requires different roles, such as code review and test design, to work together, you can use multi\-agent. The main agent is responsible for breaking down tasks, selecting subagents, and aggregating the results from each role into the final response.

This topic uses reviewing a Python login API as an example. You will configure two fixed members for the main agent: a code reviewer and a test engineer. Then you will create a session, submit a review task, and use threads to view each member's execution process. Before you begin, prepare an API key, a model ID, an available environment, and the IDs and version numbers of two member agents.

## Choose a collaboration mode

ModelArk supports two multi\-agent configurations: `multiagent_20261001` and `coordinator`. First, choose a configuration based on whether the task requires continuous collaboration, cross\-level dispatching, and runtime role selection. Then prepare the subagents.

|Comparison item |`multiagent_20261001` |`coordinator` |
|---|---|---|
|Member parameter |`subagents` |`agents` |
|Member source |Predefined agents, self, and inline agents |Predefined agents and Self |
|Use agents with `multiagent_20261001` as predefined members |Supported |Not supported |
|Delegation method |After the target thread accepts the task, the dispatcher continues execution |The delegator waits for the subagent to finish execution |
|Result return |The result is asynchronously returned to the parent thread that created the target thread |The result is returned as the tool result for this delegation |
|Thread lifecycle |The thread remains in the current session and can continue to receive tasks |Each delegation creates a one\-time thread, which is not reused after the delegation ends. |
|Subagent hierarchy |Up to 3 levels |Up to 1 level |

### `multiagent_20261001` mode

`multiagent_20261001` is suitable where multiple roles continuously collaborate in independent threads, or where subagents need to continue deriving and dispatching tasks. It follows these rules:

* After a subagent thread is created, it remains in the current session and can continue to receive tasks. It can also be interrupted or archived independently.

* After the target thread accepts the task, the dispatcher can continue its own work without waiting.

* An agent can dispatch tasks to other existing subagent threads in the same session, including threads at the same level or different levels. The dispatcher does not directly receive the execution result from the target thread.

* A subagent's result is always asynchronously returned to the parent thread that created it, not to the thread that most recently dispatched the task. If the parent thread is `idle`, the result wakes it up. If the parent thread is still running, the result is queued for processing.

* As long as any thread is still running, the session remains `running`. The session enters `idle` only after all threads stop running. At this point, you still need to check `stop_reason` to determine whether the task has ended or is waiting for an external operation.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/managed-agent/multiagent-thread-result-routing.svg)

### `coordinator` mode

`coordinator` is suitable for tasks where the main agent calls fixed members one by one and waits directly for the result of each call. It follows these rules:

* Each delegation creates a one\-time sub\-thread.

* The main agent waits for the subagent to complete the task and continues processing after receiving the result.

* The subagent's final output is returned to the delegator as the tool result for this delegation.

* After the delegation is complete, the one\-time sub\-thread becomes `idle` and can still be queried through the thread API.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/managed-agent/coordinator-delegation-result-routing.svg)

The rest of this topic uses `multiagent_20261001` as an example to describe subagent sources, configuration, and session management. If an existing agent uses `coordinator`, see [Migrate from ](multi-agent.md#migrate-from-coordinator)[coordinator](multi-agent.md#migrate-from-coordinator) for migration restrictions.

## Use cases

`multiagent_20261001` supports three subagent sources. You can choose based on whether the roles are fixed, whether they need to be reused, and whether temporary roles need to be created at runtime:

|Source |Note |Supported use cases |Configuration method |
|---|---|---|---|
|Predefined agent |Agents that are created in advance and can have their versions fixed |The roles and capabilities are stable and need to be reused across multiple tasks |Reference existing agents in `predefined_agents` |
|Self |Reuse the current agent's model and capabilities, but store the conversation context separately. |Multiple similar subtasks need to be processed in parallel. |Add `{"type":"self"}` to `predefined_agents` |
|Inline Agent |A temporary role defined by the main agent at runtime, without creating an agent resource. |The roles or division of work cannot be determined in advance. |Set `inline_agents.type` to `enabled` |

The three sources can be used at the same time. In the API request, set `multiagent.type` to `multiagent_20261001` and configure these sources in `multiagent.subagents`.

Self only reuses the current agent's model, system prompt, and capabilities. It does not copy the agent's predefined member list. Inline agents also do not receive the member list of the agent that creates them.

## Configure the main agent

### Prepare members and delegation rules

When you use existing agents as fixed members, first create the members and record their agent IDs and version numbers. For details, see [Agent](define-agent.md).

The following example uses two fixed members:

|Role |Responsibilities |
|---|---|
|Code reviewer |Check code quality, architecture, and security risks, and output issues and modification suggestions |
|Test engineer |Design test cases, and describe the coverage and uncovered risks. |

Configure local environment variables:

```Bash
export ARK_API_KEY="<ARK_API_KEY>"
export ARK_BASE_URL="https://ark.ap-southeast.bytepluses.com/api/v3"
export COORDINATOR_MODEL_ID="<MODEL_ID>"
export REVIEWER_AGENT_ID="<REVIEWER_AGENT_ID>"
export REVIEWER_AGENT_VERSION="<REVIEWER_AGENT_VERSION>"
export TESTER_AGENT_ID="<TESTER_AGENT_ID>"
export TESTER_AGENT_VERSION="<TESTER_AGENT_VERSION>"
```

Neither Self nor inline agents require agent resources to be created in advance. After the main agent selects Self, the system creates a new subagent thread. It uses the model, system prompt, tools, MCP, and skills of the main agent in the current session, but stores the conversation context separately. An inline agent is a role and system prompt defined by the main agent at runtime, and it uses an independent thread.

The system prompt of the main agent must answer three questions:

1. What each fixed member is responsible for.

2. When to create Self or an inline agent.

3. How to aggregate and respond after receiving member results.

The names and responsibilities of fixed members must be consistent with the configuration. When names are similar or responsibilities overlap, the main agent is more likely to select the wrong member.

If the main agent is allowed to create inline agents, you also need to define the following boundaries:

* An inline agent uses the model, tools, MCP, and skills of the agent that creates it. You cannot add or remove these capabilities separately during creation.

* Delegation content must include responsibilities, necessary context, expected deliverables, and file ownership.

* Write large intermediate artifacts to the shared file system. Messages should pass only file paths and conclusion summaries.

* The user interacts only with the main agent. The main agent aggregates subagent results into a unified response.

The following prompt snippet can be used as a starting point:

```Text
You are responsible for completing the product plan and choosing the collaboration method based on task complexity.

- Assign code review to the code reviewer.
- Assign test design to the test engineer.
- For multiple similar subtasks, you can create Self instances to process them in parallel.
- For specialized roles that cannot be predefined, you can create inline agents as needed.

When delegating, describe the responsibilities, context, expected deliverables, and file paths.
After receiving all necessary results, aggregate them into a final response.
```

### Create the main agent

The following cURL command creates the main agent with two predefined agents, one Self, and enabled inline agents, and pins the versions of the predefined agents:

**cURL**

```Bash
: "\\${ARK_BASE_URL:?Set ARK_BASE_URL}"
: "\\${ARK_API_KEY:?Set ARK_API_KEY}"
: "\\${COORDINATOR_MODEL_ID:?Set COORDINATOR_MODEL_ID}"
: "\\${REVIEWER_AGENT_ID:?Set REVIEWER_AGENT_ID}"
: "\\${REVIEWER_AGENT_VERSION:?Set REVIEWER_AGENT_VERSION}"
: "\\${TESTER_AGENT_ID:?Set TESTER_AGENT_ID}"
: "\\${TESTER_AGENT_VERSION:?Set TESTER_AGENT_VERSION}"

COORDINATOR_SYSTEM='You have the following team members:

Code reviewer
- Check code quality, architecture, and security risks.
- Output a list of issues, severity levels, and suggestions for changes.
- Not responsible for writing new features.

Test engineer
- Design test cases and check coverage.
- Output test cases, coverage, and uncovered risks.
- Do not modify product requirements.

First split the task, then delegate each part separately. After receiving all results, summarize them into a final conclusion.'
export COORDINATOR_SYSTEM

main_agent_payload=$(
  jq -n \
    --arg model_id "$COORDINATOR_MODEL_ID" \
    --arg system "$COORDINATOR_SYSTEM" \
    --arg reviewer_id "$REVIEWER_AGENT_ID" \
    --argjson reviewer_version "$REVIEWER_AGENT_VERSION" \
    --arg tester_id "$TESTER_AGENT_ID" \
    --argjson tester_version "$TESTER_AGENT_VERSION" \
    '{
      name: "engineering-lead",
      model: {id: $model_id},
      system: $system,
      multiagent: {
        type: "multiagent_20261001",
        subagents: {
          type: "enabled",
          inline_agents: {type: "enabled"},
          predefined_agents: [
            {type: "agent", id: $reviewer_id, version: $reviewer_version},
            {type: "agent", id: $tester_id, version: $tester_version},
            {type: "self"}
          ]
        }
      }
    }'
)

main_agent=$(
  curl -sS --fail-with-body "$ARK_BASE_URL/agents" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d "$main_agent_payload"
)

export COORDINATOR_AGENT_ID
export COORDINATOR_AGENT_VERSION
COORDINATOR_AGENT_ID=$(jq -er '.id' <<<"$main_agent")
COORDINATOR_AGENT_VERSION=$(jq -er '.version' <<<"$main_agent")

printf 'Main Agent ID: %s\n' "$COORDINATOR_AGENT_ID"
printf 'Main Agent version: %s\n' "$COORDINATOR_AGENT_VERSION"
```

The script writes the main agent ID and version number to `COORDINATOR_AGENT_ID` and `COORDINATOR_AGENT_VERSION`. After you confirm that both variables are not empty, create the session. For parameter descriptions, see [Create agent](create-agent-api.md).

The core configuration submitted by the script is as follows:

```JSON
{
  "type": "multiagent_20261001",
  "subagents": {
    "type": "enabled",
    "inline_agents": {"type": "enabled"},
    "predefined_agents": [
      {"type": "agent", "id": "<AGENT_ID>", "version": 1},
      {"type": "self"}
    ]
  }
}
```

`subagents` determines whether the main agent can create subagents and which subagent sources it can use:

* When `subagents.type` is set to `disabled`, the main agent does not create subagents.

* When `inline_agents.type` is set to `disabled`, the main agent can select members only from `predefined_agents`.

* If there are no predefined members, pass `predefined_agents: []`.

> **Note**

> When `subagents.type` is `enabled`, omitting `inline_agents` allows inline agents to be created. To prevent temporary roles from being created at task runtime, you must explicitly pass `"inline_agents": {"type": "disabled"}`.

**Which configurations subagents use**

After you create a session, the three types of subagents determine their model and capability configuration according to the following rules:

|Source |Configuration used |Effect of later updates |
|---|---|---|
|Predefined agent |Uses the member agent configuration recorded when the session was created, including the model, system prompt, tools, MCP, and skills. |Later updates to the member agent do not change the current session. |
|Self |Uses the main agent's model, system prompt, tools, MCP, and skills in the current session. |If the main agent configuration was temporarily modified when the session was created, Self also uses the modified values. |
|Inline Agent |Defines the name and system prompt at runtime. The model, tools, MCP, and skills are the same as those of the agent that created it. |No separate agent resource is created, and capabilities cannot be adjusted separately during creation. |

Subagents in the same session share the environment, sandbox, file system, and bound vault credentials, but they do not automatically share conversation history. Therefore, you must determine the environment and the vault credentials to mount when you create the session.

> **Note**

> A shared file system is not the same as shared context. After one thread writes to a file, other threads can read the file, but the main agent does not automatically receive the write process or conclusions. The subagent must explicitly return the file path and summary.

**Configuration limits**

* The fixed member list can contain up to 20 items.

* By default, the same session can contain up to 20 threads, including the main thread; different limits can be configured for the account and project.

* When using `multiagent_20261001`, a session can create up to three levels of subagents, including predefined agents, Self, and inline agents.

* Updating the main agent does not modify the configuration used by existing sessions.

* The main agent and subagents incur model and runtime resource usage separately. For details, see [Pricing](model-pricing.md#ma_billing).

### Update and check configuration

When you need to add or remove predefined members or switch member versions, first call [Get agent](get-agent-api.md) to query the main agent. The update request must include the returned `version` to avoid overwriting other updates. The following example submits `predefined_agents` and the matching system prompt as a whole.

```Bash
export COORDINATOR_AGENT_VERSION="<LATEST_VERSION>"

COORDINATOR_PREDEFINED_AGENTS_JSON=$(
  jq -n \
    --arg reviewer_id "$REVIEWER_AGENT_ID" \
    --argjson reviewer_version "$REVIEWER_AGENT_VERSION" \
    --arg tester_id "$TESTER_AGENT_ID" \
    --argjson tester_version "$TESTER_AGENT_VERSION" \
    '[
      {type: "agent", id: $reviewer_id, version: $reviewer_version},
      {type: "agent", id: $tester_id, version: $tester_version},
      {type: "self"}
    ]'
)
export COORDINATOR_PREDEFINED_AGENTS_JSON
```

The creation script has exported `COORDINATOR_SYSTEM`. Before updating, confirm that this variable stores the complete system prompt currently used by the main agent.

**cURL**

```Bash
: "\\${ARK_BASE_URL:?Set ARK_BASE_URL}"
: "\\${ARK_API_KEY:?Set ARK_API_KEY}"
: "\\${COORDINATOR_AGENT_ID:?Set COORDINATOR_AGENT_ID}"
: "\\${COORDINATOR_AGENT_VERSION:?Set COORDINATOR_AGENT_VERSION}"
: "\\${COORDINATOR_SYSTEM:?Set COORDINATOR_SYSTEM}"
: "\\${COORDINATOR_PREDEFINED_AGENTS_JSON:?Set COORDINATOR_PREDEFINED_AGENTS_JSON}"

update_payload=$(
  jq -n \
    --argjson version "$COORDINATOR_AGENT_VERSION" \
    --arg system "$COORDINATOR_SYSTEM" \
    --argjson predefined_agents "$COORDINATOR_PREDEFINED_AGENTS_JSON" \
    '{
      version: $version,
      system: $system,
      multiagent: {
        type: "multiagent_20261001",
        subagents: {
          type: "enabled",
          inline_agents: {type: "enabled"},
          predefined_agents: $predefined_agents
        }
      }
    }'
)

main_agent=$(
  curl -sS --fail-with-body \
    "$ARK_BASE_URL/agents/$COORDINATOR_AGENT_ID" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d "$update_payload"
)

export COORDINATOR_AGENT_VERSION
COORDINATOR_AGENT_VERSION=$(jq -er '.version' <<<"$main_agent")

printf 'Main Agent version: %s\n' "$COORDINATOR_AGENT_VERSION"
```

The script writes the new version number to `COORDINATOR_AGENT_VERSION`. If the API returns `version: conflict`, query the latest version again, merge the configuration, and submit it again.

**Update semantics**

|Request format |Update result |
|---|---|
|Omit top\-level `multiagent` |Preserves the complete multi\-agent configuration |
|Pass `multiagent: null` |Deletes the multi\-agent configuration and changes to a single agent |
|Omit `multiagent.subagents.predefined_agents` |Preserves the current predefined member list |
|Pass `multiagent.subagents.predefined_agents: []` |Clears the predefined member list |
|Set `subagents.type` or `inline_agents.type` to `disabled` |Disables creating subagents or inline agents respectively |

**Migrate from ** **`coordinator`**

`coordinator` uses `agents` to configure members, and `multiagent_20261001` uses `subagents`. During migration, you must submit the complete `subagents` configuration. Existing `agents` are not converted automatically.

Follow these rules when switching configurations:

* An agent configured as a single agent or an agent that uses `coordinator` can switch to `multiagent_20261001`. The request must include the complete `subagents` configuration.

* After using `multiagent_20261001`, you cannot switch back to `coordinator`. To switch to a single agent, pass `multiagent: null` to clear the configuration.

* Updating an agent only creates a new version and does not change existing sessions. To have existing sessions use the new configuration, see [Upgrade the session configuration](multi-agent.md#upgrade-session-config).

After creation or update is complete, call [Get agent](get-agent-api.md) to check the following:

* `version` is the new version generated by this save.

* `multiagent.type` is `multiagent_20261001`.

* `multiagent.subagents.type` is `enabled`.

* `multiagent.subagents.predefined_agents` includes the expected predefined agents and Self.

* `multiagent.subagents.inline_agents.type` is consistent with whether dynamic roles are allowed.

* The fixed members' `id` and `version` match the plan.

* The role names in the system prompt match the member responsibilities.

* Following the principle of least privilege, configure only the tools, MCP, and skills required for the task for the main agent and member agents.

Inline agents do not create independent agent resources, so they do not appear in the agent list. At task runtime, the system creates an independent thread for the temporary role.

## Run a session

After the configuration is complete, create a session and send the task to the main agent. Regardless of whether the main agent selects a predefined agent, Self, or an inline agent, use the same session and thread interfaces to observe execution, handle tool results, or interrupt the task.

Before you start, create or reuse an environment and record the environment ID. For details, see [Configure cloud environments](configure-cloud-environment.md).

Configure the environment variables required for running:

```Bash
export ARK_API_KEY="<ARK_API_KEY>"
export ARK_BASE_URL="https://ark.ap-southeast.bytepluses.com/api/v3"
export COORDINATOR_AGENT_ID="<MAIN_AGENT_ID>"
export ENVIRONMENT_ID="<ENVIRONMENT_ID>"
```

The sample script continues to use the `COORDINATOR_AGENT_ID` variable name to represent the main agent responsible for coordination.

### Create a session

A session carries one multi\-agent task. The main agent and each subagent use separate threads:

```Text
Session
└── Main Thread
    ├── Thread A: predefined agent
    ├── Thread B: Self
    └── Thread C: inline agent
```

Each thread has a stable `session_thread_id` and stores messages and reasoning context independently.`parent_thread_id` records the creation relationship: the main thread returns `null`, and a child agent thread returns the ID of the parent thread that created it.

Threads in the same session share the environment, sandbox, file system, and bound vault credentials, but they do not automatically share conversation history. After one thread writes a file, other threads can read it; if the conclusion also needs to be passed along, the child agent should also return the file path and a summary.

> **Tip**

> Predefined agents, Self, and inline agents only affect the configuration source of a child agent. At task runtime, all three types of child agents use independent threads in the current session and are managed through the same set of session and thread APIs. An inline agent is a temporary role within the current session and does not create an agent resource or a new session.

The main process uses two terminals. Terminal A receives the session event stream, and Terminal B creates a session, sends tasks, and queries results. To view the complete process for a specific thread in real time, open Terminal C.

Create a session in Terminal B:

**cURL**

```Bash
: "\\${ARK_BASE_URL:?Set ARK_BASE_URL}"
: "\\${ARK_API_KEY:?Set ARK_API_KEY}"
: "\\${COORDINATOR_AGENT_ID:?Set COORDINATOR_AGENT_ID}"
: "\\${ENVIRONMENT_ID:?Set ENVIRONMENT_ID}"

payload=$(
  jq -n \
    --arg agent_id "$COORDINATOR_AGENT_ID" \
    --arg environment_id "$ENVIRONMENT_ID" \
    '{
      agent: $agent_id,
      environment_id: $environment_id,
      title: "Multi Agent task"
    }'
)

session=$(
  curl -sS --fail-with-body "$ARK_BASE_URL/sessions" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d "$payload"
)

export SESSION_ID
SESSION_ID=$(jq -er '.id' <<<"$session")
printf 'Session ID: %s\n' "$SESSION_ID"
```

The script writes the session ID to `SESSION_ID`. After confirming that this variable is not empty, subscribe to the event stream. At this point, only the main thread is created. The system creates the corresponding child agent thread only when the main agent delegates a task for the first time. The session records the main agent configuration and runtime limits used when it was created. Later agent updates do not affect this session.

### Submit and observe a task

**Subscribe to the session events**

Configure the same session ID in Terminal A:

```Bash
export ARK_API_KEY="<ARK_API_KEY>"
export ARK_BASE_URL="https://ark.ap-southeast.bytepluses.com/api/v3"
export SESSION_ID="<SESSION_ID>"
```

Establish session event stream

**cURL**

```Bash
: "\\${ARK_BASE_URL:?Set ARK_BASE_URL}"
: "\\${ARK_API_KEY:?Set ARK_API_KEY}"
: "\\${SESSION_ID:?Set SESSION_ID}"

curl -sS -N --fail-with-body --max-time 600 \
  "$ARK_BASE_URL/sessions/$SESSION_ID/events/stream" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Accept: text/event-stream"
```

After Terminal A outputs `: ready`, keep the connection open, and then send the task. Through the session event stream, you can see:

* The overall session status.

* Thread creation, running, idle, rescheduling, and termination.

* Collaboration messages between threads for delegation, reporting, and additional requirements.

* Custom tool calls initiated by subagents and tool authorization events that must be handled by the client.

* Built\-in tool calls that need to be executed by a worker in a self\-hosted environment.

To view a child agent's reasoning, replies, tool results, spans, or preview frames, query the corresponding thread events.

**Submit a task**

User messages are always sent to the main agent. The main agent selects a predefined agent, Self, or inline agent based on the task:

```Bash
TASK_TEXT=$(cat <<'EOF'
Review the following Python login API implementation. Assign appropriate child agents to analyze code quality, security risks, and test coverage, and then summarize the conclusions.

def login(username, password):
    key = f"login_failures:{username}"
    failures = int(redis.get(key) or 0)
    if failures >= 5:
        return {"ok": False, "reason": "locked"}
    if not verify_password(username, password):
        redis.incr(key)
        redis.expire(key, 1800)
        return {"ok": False, "reason": "denied"}
    redis.delete(key)
    return {"ok": True}
EOF
)
export TASK_TEXT
```

**cURL**

```Bash
: "\\${ARK_BASE_URL:?Set ARK_BASE_URL}"
: "\\${ARK_API_KEY:?Set ARK_API_KEY}"
: "\\${SESSION_ID:?Set SESSION_ID}"
: "\\${TASK_TEXT:?Set TASK_TEXT}"

payload=$(
  jq -n --arg text "$TASK_TEXT" \
    '{events: [{type: "user.message", content: [{type: "text", text: $text}]}]}'
)

curl -sS --fail-with-body \
  "$ARK_BASE_URL/sessions/$SESSION_ID/events" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d "$payload"
```

When you use `multiagent_20261001`, successful task delivery only means that the target thread has received the task. It does not mean that the child agent has completed it. The main agent can continue processing other work, and the child agent's result is returned asynchronously after execution ends.

When the main agent dispatches a task to a member for the first time, `session.thread_created` appears in the session event stream. When additional requirements are needed, you can continue using the original thread without recreating the context.

**View threads**

List all threads in the current session in Terminal B:

**cURL**

```Bash
: "\\${ARK_BASE_URL:?Set ARK_BASE_URL}"
: "\\${ARK_API_KEY:?Set ARK_API_KEY}"
: "\\${SESSION_ID:?Set SESSION_ID}"

threads=$(
  curl -sS --fail-with-body \
    "$ARK_BASE_URL/sessions/$SESSION_ID/threads?limit=100" \
    -H "Authorization: Bearer $ARK_API_KEY"
)

printf 'Agent\tThread ID\tParent Thread ID\tStatus\n'
jq -r '.data[] | [.agent_name, .id, (.parent_thread_id // "-"), .status] | @tsv' <<<"$threads"

export SESSION_THREAD_ID
if ! SESSION_THREAD_ID=$(
  jq -er 'first(.data[] | select(.parent_thread_id != null and .status != "archived" and .status != "terminated") | .id)' \
    <<<"$threads"
); then
  printf 'No active child thread is available yet.\n' >&2
  return 1 2>/dev/null || exit 1
fi
printf 'Selected child thread ID: %s\n' "$SESSION_THREAD_ID"
```

The script writes the thread ID of the first child agent to `SESSION_THREAD_ID`. When checking other threads, update this variable and clear the pagination identifier before querying the history:

```Bash
export SESSION_THREAD_ID="<SESSION_THREAD_ID>"
unset THREAD_EVENTS_PAGE
```

Query the complete history of this thread:

**cURL**

```Bash
: "\\${ARK_BASE_URL:?Set ARK_BASE_URL}"
: "\\${ARK_API_KEY:?Set ARK_API_KEY}"
: "\\${SESSION_ID:?Set SESSION_ID}"
: "\\${SESSION_THREAD_ID:?Set SESSION_THREAD_ID}"

request_args=(
  --get
  "$ARK_BASE_URL/sessions/$SESSION_ID/threads/$SESSION_THREAD_ID/events"
  --data-urlencode "limit=100"
  --data-urlencode "order=asc"
)

if [[ -n \\${THREAD_EVENTS_PAGE:-} ]]; then
  request_args+=(--data-urlencode "page=$THREAD_EVENTS_PAGE")
fi

if ! events=$(
  curl -sS --fail-with-body \
    "\\${request_args[@]}" \
    -H "Authorization: Bearer $ARK_API_KEY"
); then
  return 1 2>/dev/null || exit 1
fi

printf 'Events:\n'
jq -c '.data[]' <<<"$events"

export THREAD_EVENTS_PAGE
THREAD_EVENTS_PAGE=$(jq -r '.next_page // empty' <<<"$events")
printf 'Next page: %s\n' "$THREAD_EVENTS_PAGE"
```

If `Next page` is not empty, keep `THREAD_EVENTS_PAGE` and repeat until the next page token is empty. Clear this variable again before switching threads.

When you query thread details or a list, `stats` returns the runtime duration, and `usage` returns the accumulated model tokens, cache usage, and server\-side tool usage for this thread.

To view this thread in real time, establish an event stream in Terminal C:

**cURL**

```Bash
: "\\${ARK_BASE_URL:?Set ARK_BASE_URL}"
: "\\${ARK_API_KEY:?Set ARK_API_KEY}"
: "\\${SESSION_ID:?Set SESSION_ID}"
: "\\${SESSION_THREAD_ID:?Set SESSION_THREAD_ID}"

curl -sS -N --fail-with-body --max-time 600 \
  "$ARK_BASE_URL/sessions/$SESSION_ID/threads/$SESSION_THREAD_ID/stream" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Accept: text/event-stream"
```

After the thread event stream outputs `: ready`, query the history again. Merge real\-time and historical results by event `id`, and ignore duplicate events. The message arrival order in session and thread event streams may differ. You can use `session_thread_id`, `from_session_thread_id`, and `to_session_thread_id` to identify a delegation and its returned result.

**Determine whether this turn is complete**

The normal event trace is as follows:

```Text
session.thread_created
agent.thread_message_sent
session.thread_status_idle
agent.message
session.status_idle
```

The task for this turn succeeds only if all of the following conditions are met:

* The main thread generates the final `agent.message`.

* The session enters `idle` with `stop_reason.type=end_turn`.

* No thread remains in `running` or `rescheduling`.

* No termination or error event that requires handling occurs in this turn.

> **Note**

> HTTP 200 only indicates that the server has accepted the event.`session.status_idle` does not necessarily mean that the task succeeded. You still need to check `stop_reason` and the status of each thread.

## Manage sessions

### Continue delegation and handle pending operations

After a session ends with `end_turn`, you can continue sending new `user.message` messages to the same session. Messages are still sent to the main agent. In the task, specify the role to continue processing, existing results, and new requirements. The main agent can reuse the original thread or create a new thread.

When you use `multiagent_20261001`, you can reuse an existing child agent thread. For task acceptance, result return, and parent thread wake\-up rules, see [Choose a collaboration mode](multi-agent.md#choose-collaboration-mode).

For the same task, we recommend that you always use the same session to preserve context. Create a new session only when you need to isolate a new task, change the environment, or make all threads use a new configuration uniformly.

**Thread status**

|Status |Meaning |
|---|---|
|`running` |The thread is processing the current work. |
|`idle` |The current work is paused or complete, and tasks can continue to be dispatched. |
|`rescheduling` |The thread is automatically recovering from a temporary error. |
|`archived` |After the session is archived, child threads enter this state. They can no longer receive tasks, but they can still be queried. |
|`terminated` |The thread has been terminated and can no longer receive any input. This occurs when sessions in the legacy collaboration mode restore threads from events. |
|`unknown` |The thread status cannot be determined yet. Continue querying subsequent events. |

An interrupt only stops the current execution. After the interrupt completes, the thread can continue receiving tasks.

**Handle pending operations**

When a thread is waiting for tool confirmation or an external tool result, the session enters `idle` with `requires_action`:

1. Keep the event stream connections for the session and the target thread open. If the connection has been closed, reconnect first and wait for `: ready`.

2. Clear the pagination token and backfill the session history:

   ```Bash
   unset SESSION_EVENTS_PAGE
   ```

   **cURL**

```Bash
   : "\\${ARK_BASE_URL:?Set ARK_BASE_URL}"
   : "\\${ARK_API_KEY:?Set ARK_API_KEY}"
   : "\\${SESSION_ID:?Set SESSION_ID}"
   
   request_args=(
     --get
     "$ARK_BASE_URL/sessions/$SESSION_ID/events"
     --data-urlencode "limit=100"
     --data-urlencode "order=asc"
   )
   
   if [[ -n \\${SESSION_EVENTS_PAGE:-} ]]; then
     request_args+=(--data-urlencode "page=$SESSION_EVENTS_PAGE")
   fi
   
   if ! events=$(
     curl -sS --fail-with-body \
       "\\${request_args[@]}" \
       -H "Authorization: Bearer $ARK_API_KEY"
   ); then
     return 1 2>/dev/null || exit 1
   fi
   
   printf 'Events:\n'
   jq -c '.data[]' <<<"$events"
   
   export SESSION_EVENTS_PAGE
   SESSION_EVENTS_PAGE=$(jq -r '.next_page // empty' <<<"$events")
   printf 'Next page: %s\n' "$SESSION_EVENTS_PAGE"
   ```

3. Record `session_thread_id` and all `stop_reason.event_ids` from `session.thread_status_idle`, and then find these events in the corresponding thread history.

4. For `agent.tool_use` or `agent.mcp_tool_use`, set the thread ID and tool call ID to confirm:

   ```Bash
   export SESSION_THREAD_ID="<SESSION_THREAD_ID>"
   export TOOL_USE_ID="<TOOL_USE_ID>"
   ```

   **cURL**

```Bash
   : "\\${ARK_BASE_URL:?Set ARK_BASE_URL}"
   : "\\${ARK_API_KEY:?Set ARK_API_KEY}"
   : "\\${SESSION_ID:?Set SESSION_ID}"
   : "\\${SESSION_THREAD_ID:?Set SESSION_THREAD_ID}"
   : "\\${TOOL_USE_ID:?Set TOOL_USE_ID}"
   
   payload=$(
     jq -n \
       --arg thread_id "$SESSION_THREAD_ID" \
       --arg tool_use_id "$TOOL_USE_ID" \
       '{
         events: [{
           type: "user.tool_confirmation",
           session_thread_id: $thread_id,
           tool_use_id: $tool_use_id,
           result: "allow"
         }]
       }'
   )
   
   curl -sS --fail-with-body \
     "$ARK_BASE_URL/sessions/$SESSION_ID/events" \
     -H "Authorization: Bearer $ARK_API_KEY" \
     -H "Content-Type: application/json" \
     -d "$payload"
   ```

5. For `agent.custom_tool_use`, run the corresponding tools in your application and then send `user.custom_tool_result`. The request must include `custom_tool_use_id`. For details, see [Tools](tools.md).

6. After processing all `event_ids`, continue monitoring. The task may enter `requires_action` again; the current turn is complete only when the session finally ends with `end_turn`.

Tool confirmations and tool results must include the original call ID. Sending a new `user.message` cannot resolve `requires_action`.

### Interrupt threads and upgrade sessions

**Interrupt threads**

To interrupt only one thread, set `SESSION_THREAD_ID`. To interrupt all active threads in the session, omit this variable:

```Bash
# Interrupt a specific thread
export SESSION_THREAD_ID="<SESSION_THREAD_ID>"

# Interrupt all active threads
# unset SESSION_THREAD_ID
```

**cURL**

```Bash
: "\\${ARK_BASE_URL:?Set ARK_BASE_URL}"
: "\\${ARK_API_KEY:?Set ARK_API_KEY}"
: "\\${SESSION_ID:?Set SESSION_ID}"

if [[ -n \\${SESSION_THREAD_ID:-} ]]; then
  payload=$(
    jq -n --arg thread_id "$SESSION_THREAD_ID" \
      '{events: [{type: "user.interrupt", session_thread_id: $thread_id}]}'
  )
else
  payload=$(jq -n '{events: [{type: "user.interrupt"}]}')
fi

curl -sS --fail-with-body \
  "$ARK_BASE_URL/sessions/$SESSION_ID/events" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d "$payload"
```

Interrupting does not roll back content that has already been written to the shared file system, and it does not delete the thread. After interrupting a specific thread, wait for the thread to enter `idle`. After interrupting all threads, wait for the session to enter `idle`.

**Upgrade session configuration**

After you update the main agent, existing sessions do not automatically use the new configuration. Upgrade a session only when all of the following conditions are met:

* The session is currently `idle`, and the previous turn ended with `end_turn`.

* All threads have completed their current work.

* There are no pending tool authorizations or tool results, and no child agent is waiting for a reply from another thread.

* There are no other upgrade requests in progress.

> **Note**

> The upgrade direction must follow these rules:

* > Sessions that use a single agent or `coordinator` can be upgraded to `multiagent_20261001`.

* > Sessions that already use `multiagent_20261001` can continue to be upgraded to a new version of this type.

* > To change back to a single agent, first set the main agent's `multiagent` to `null`, and then upgrade the session to this new version.

* > After using `multiagent_20261001`, you cannot switch back to `coordinator`.

Set the version number obtained after updating the main agent, and then create an upgrade task:

```Bash
export COORDINATOR_AGENT_VERSION="<TARGET_AGENT_VERSION>"
```

**cURL**

```Bash
: "\\${ARK_BASE_URL:?Set ARK_BASE_URL}"
: "\\${ARK_API_KEY:?Set ARK_API_KEY}"
: "\\${SESSION_ID:?Set SESSION_ID}"
: "\\${COORDINATOR_AGENT_ID:?Set COORDINATOR_AGENT_ID}"
: "\\${COORDINATOR_AGENT_VERSION:?Set COORDINATOR_AGENT_VERSION}"

payload=$(
  jq -n \
    --arg agent_id "$COORDINATOR_AGENT_ID" \
    --argjson version "$COORDINATOR_AGENT_VERSION" \
    '{agent: {type: "agent_with_upgrades", id: $agent_id, version: $version}}'
)

curl -sS --fail-with-body \
  "$ARK_BASE_URL/sessions/$SESSION_ID/upgrades" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d "$payload"
```

Wait for the session to return to `idle` using the target version:

**cURL**

```Bash
: "\\${ARK_BASE_URL:?Set ARK_BASE_URL}"
: "\\${ARK_API_KEY:?Set ARK_API_KEY}"
: "\\${SESSION_ID:?Set SESSION_ID}"
: "\\${COORDINATOR_AGENT_ID:?Set COORDINATOR_AGENT_ID}"
: "\\${COORDINATOR_AGENT_VERSION:?Set COORDINATOR_AGENT_VERSION}"
: "\\${SESSION_UPGRADE_MAX_ATTEMPTS:=60}"
: "\\${SESSION_UPGRADE_INTERVAL_SECONDS:=2}"

upgrade_result="timeout"

for ((attempt = 1; attempt <= SESSION_UPGRADE_MAX_ATTEMPTS; attempt++)); do
  session=$(
    curl -sS --fail-with-body \
      "$ARK_BASE_URL/sessions/$SESSION_ID" \
      -H "Authorization: Bearer $ARK_API_KEY"
  ) || {
    upgrade_result="request_failed"
    break
  }

  session_status=$(jq -er '.status' <<<"$session") || {
    upgrade_result="invalid_response"
    break
  }
  session_agent_id=$(jq -r '.agent.id // empty' <<<"$session")
  session_agent_version=$(jq -r '.agent.version // empty' <<<"$session")

  printf 'Attempt %s: status=%s, agent=%s, version=%s\n' \
    "$attempt" \
    "$session_status" \
    "$session_agent_id" \
    "$session_agent_version"

  if [[
    $session_status == "idle" &&
    $session_agent_id == "$COORDINATOR_AGENT_ID" &&
    $session_agent_version == "$COORDINATOR_AGENT_VERSION"
  ]]; then
    upgrade_result="completed"
    break
  fi

  case "$session_status" in
    terminated | failed)
      upgrade_result="$session_status"
      break
      ;;
  esac

  sleep "$SESSION_UPGRADE_INTERVAL_SECONDS"
done

if [[ $upgrade_result != "completed" ]]; then
  printf 'Session upgrade did not complete: %s\n' "$upgrade_result" >&2
  false
fi
```

By default, the script queries every 2 seconds, up to 60 times. Success is returned only when the status is `idle` and `agent.id` and `agent.version` match the target.

After the upgrade is complete, newly created threads use the upgraded configuration, while existing threads keep the configuration from when they were created. If the upgrade times out, enters a failure state, or all threads need to switch to the same configuration, create a new session.

## Limits and troubleshooting

|Signal |Meaning |Processing action |
|---|---|---|
|SSE connection interrupted |Events that occur during the interruption are not automatically pushed to the current connection. |Recreate the connection first, then query history and deduplicate by event ID. |
|`requires_action` |Waiting for tool confirmation or an external tool result |Process all `stop_reason.event_ids`. |
|`session.thread_status_rescheduled` |The thread is automatically recovering from a temporary error. |Continue waiting for automatic recovery, and do not start a manual retry at the same time. |
|`session.thread_status_terminated` |The thread has been terminated. |Query the thread history and error events, and dispatch the task again after the session becomes idle. |
|The thread remains in `running` for a long time. |The current execution has not ended. |Send `user.interrupt` with `session_thread_id`. |
|`retries_exhausted` |The retry attempts for the current turn have been exhausted. |Adjust the input and retry. If you need to modify the agent configuration, update it and then upgrade the session or create a new session. |
|`InternalServiceError.MultiagentPolicyUnavailable` |The multi\-agent policy is temporarily unavailable. |Keep the request parameters unchanged and retry later. |

When using a self\-hosted environment, you do not need to configure tool execution capabilities separately for each child agent. The worker listens for the session event stream, receives built\-in tool calls initiated by the main agent and child agents, and executes them in event order. When returning `user.tool_result`, you must include `tool_use_id`. For details, see [Tools](tools.md).

**Runtime limits**

* Threads in the same session share runtime resources, but each thread generates its own model and runtime resource usage. For billing information, see [Managed Agents](model-pricing.md#ma_billing).

* If a process failure occurs before supplemental message processing is complete, query the thread history and task status first after recovery. Resend the message only after you confirm that it has not been processed.

## Clean up resources

After confirming that the artifacts have been saved elsewhere, delete the session created on this page using the exact ID. If the session is still running, interrupt all threads first and wait for `idle`. Keep the main agent by default:

```Bash
export CONFIRM_DELETE="yes"
export DELETE_COORDINATOR="no"
```

Only change `DELETE_COORDINATOR` to `yes` when the main agent is no longer used by any other session.

**cURL**

```Bash
: "\\${ARK_BASE_URL:?Set ARK_BASE_URL}"
: "\\${ARK_API_KEY:?Set ARK_API_KEY}"
: "\\${SESSION_ID:?Set SESSION_ID}"
: "\\${CONFIRM_DELETE:?Set CONFIRM_DELETE to yes}"
: "\\${DELETE_COORDINATOR:=no}"

if [[ $CONFIRM_DELETE != "yes" ]]; then
  printf 'Set CONFIRM_DELETE=yes to delete the recorded resources.\n' >&2
  return 1 2>/dev/null || exit 1
fi

curl -sS --fail-with-body \
  "$ARK_BASE_URL/sessions/$SESSION_ID" \
  -X DELETE \
  -H "Authorization: Bearer $ARK_API_KEY"

if [[ $DELETE_COORDINATOR == "yes" ]]; then
  : "\\${COORDINATOR_AGENT_ID:?Set COORDINATOR_AGENT_ID}"
  curl -sS --fail-with-body \
    "$ARK_BASE_URL/agents/$COORDINATOR_AGENT_ID" \
    -X DELETE \
    -H "Authorization: Bearer $ARK_API_KEY"
fi
```

The command does not delete member agents or the environment.

> **Warning**

> Deleted sessions or agents cannot be restored. Use only the exact IDs recorded on this page. Do not use fuzzy name matching or batch\-delete shared resources.

**Related documents**

* **Agent tools**: see [Tools](tools.md).

* **MCP**: see [MCP](mcp.md).

* **Skills**: see [Skills](skills.md).

* **Session and thread events**: see [Session event stream](session-event-stream.md).

* **Query threads**: see [List threads](list-threads-api.md).

* **Upgrade a session**: see [Upgrade session](upgrade-session-api.md).
