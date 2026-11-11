# Start session

A session is one instance of a managed agent running an agent in an environment. A session maintains conversation history across multiple interactions and preserves sandbox state, allowing the agent to remember what it did in previous turns.

There are two ways to start a session:

1. **Create first, then send the first event**: When creating a session, configure the sandbox and bind the agent and environment. At this point, the agent does **not** start any work. Then use a `user.message` event to hand the task to the agent, and the session enters the `running` state and starts execution.

2. **Send initial events during creation**: Pass `initial_events` in the create request. After creation is complete, the server delivers the events in order and starts the agent, so you do not need to call the send event API again.

## Prerequisites

Before you begin, make sure you have:

* An API key configured as the `ARK_API_KEY` environment variable. For details, see [API key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey).

* A created Agent. For details, see [Define an Agent](define-agent.md).

* A created Environment. For details, see [Configure cloud environments](configure-cloud-environment.md).

For the base URL and authentication used in the examples in this section, see [Base URL and authentication](base-url-and-authentication.md).

## Create a session

Creating a session requires two upstream resource IDs:

* **Agent ID**: obtained after you create an agent with [Define an agent](define-agent.md), in a format such as `agent-20260701120000-xxxxx`.

* **Environment ID**: obtained after you create an environment with [Configure cloud environments](configure-cloud-environment.md), in a format such as `env-20260701120000-xxxxx`.

### Use the latest agent version (recommended for getting started)

An agent is a versioned resource. When the `agent` ID is passed as a string, the session starts with the **latest version** of that agent.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "agent": "agent-20260701120000-abcde",
    "environment_id": "env-20260701120000-fghij"
  }'
```

The response returns the full session record. The `id` parameter (in a format such as `sesn-20260701120100-xxxxx`) is the entry point for all subsequent operations:

```json
{
  "id": "sesn-20260701120100-xxxxx",
  "type": "session",
  "status": "idle",
  "environment_id": "env-20260701120000-xxxxx",
  "agent": {
    "id": "agent-20260701120000-xxxxx",
    "type": "agent",
    "version": 3
  },
  "created_at": "2026-06-29T10:00:00Z",
  "updated_at": "2026-06-29T10:00:00Z",
  "resources": [],
  "vault_ids": null
}
```

In the response, `status` is `idle`, indicating that the session is ready and waiting for the first event. Valid values of status include `initializing`, `idle`, `running`, `rescheduling`, `upgrading`, `failed`, and `terminated`. After a normal turn ends, the session returns to `idle`. When it enters `idle`, the platform saves a snapshot of the sandbox state for subsequent recovery. For details about the state machine and sandbox state retention period, see [Data retention and deletion impact](manage-session.md#checkpoint-retention).

### Lock an agent version (canary release scenario)

When you need to lock a session to a specific version of an agent, such as for rollback, canary comparison, or a finalized product release, pass `agent` as an object and explicitly specify `version`:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "agent": {"type": "agent", "id": "agent-20260701120000-abcde", "version": 1},
    "environment_id": "env-20260701120000-fghij"
  }'
```

After the version is locked, even if the agent releases a new version later, this session still runs with the behavior of `version: 1`. This lets you roll out a new version in canary stages without affecting the behavior consistency of existing sessions.

## Send initial events during creation (Optional)

If the task is already clear when you create the session, you can pass `initial_events` directly in the request to combine session creation and sending the first event into a single call. After the session is created, the server delivers the events in the requested order and starts the agent loop, so you do not need to call the send event API again.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "agent": "agent-20260701120000-abcde",
    "environment_id": "env-20260701120000-fghij",
    "initial_events": [
      {
        "type": "user.message",
        "content": [
          {"type": "text", "text": "Analyze the product materials, clarify the requirement boundaries, and provide acceptance test cases."}
        ]
      }
    ]
  }'
```

`initial_events` follows these rules:

* An array with a maximum of 50 items. If omitted or set to `[]`, it is handled as a normal create request, and the session is created in the `idle` state.

* Currently, only `user.message` is supported. Events such as tool results, tool confirmations, and interrupts are based on interactions that have already occurred in the session and can be sent only after the session starts.`user.message` content blocks are consistent with the send event API. They can mix text, images, documents, and video. For the parameter format, see [Session event structure reference](sessions-events-reference-v2.md).

* Events are persisted in the order of the request. The create response does not echo `initial_events`; to confirm whether events have been saved, query with [Get session event list](list-events-api.md).

* After non\-empty `initial_events` is passed, the `status` in the create response may be `running`, or it may still be `idle` because of status synchronization latency. To determine whether the agent has started, use the event list or the session status returned by a later query as the source of truth.

* `running` indicates that the events have been accepted and the agent loop has started. It does not mean the task has completed. Initial events are processed in batches and are not equivalent to a single merged `user.message`.

### Validation and failure handling

The server validates all initial events before creating the session: if any event is invalid, the request fails and no session is created. The following cases all return `InvalidParameter`:

* The number of events exceeds 50.

* `type` is not `user.message`.

* The event content does not meet the validation rules for the send event API.

> **Tip**

> When you use `initial_events`, events start being delivered while the create request is being processed. At this point, the session ID is not available yet, so you cannot establish an SSE connection in advance. If you need to receive the complete real\-time event stream from the first turn, create the session first, then subscribe to the event stream, and finally send the first event; events that have already been generated can be queried from the event list at any time.

## Override agent configuration (optional)

Most sessions directly pass an agent ID or version reference and use the full configuration of that version. Pass the `agent_with_overrides` object only when the current task requires different system prompts, models, tools, MCP services, skills, multi\-agent collaboration configurations, or context compaction.

The following example replaces the system prompt and model in this session, replaces the tool list with a built\-in toolset, and clears the base agent's skills and MCP services:

**Curl**

```Bash
session=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/sessions" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<EOF
{
  "agent": {
    "type": "agent_with_overrides",
    "id": "$AGENT_ID",
    "version": 1,
    "system": "You are a code review assistant that only reports reproducible issues and provides suggestions for changes.",
    "model": {
      "id": "dola-seed-2-1-turbo-260628"
    },
    "tools": [
      {
        "type": "agent_toolset_20260916",
        "name": "agent_toolset_20260916",
        "configs": [
          {
            "name": "bash",
            "enabled": true,
            "permission_policy": {
              "type": "always_allow"
            }
          }
        ],
        "default_config": {
          "enabled": false,
          "permission_policy": {
            "type": "always_allow"
          }
        }
      }
    ],
    "skills": [],
    "mcp_servers": []
  },
  "environment_id": "$ENVIRONMENT_ID",
  "title": "Session with agent configuration overrides"
}
EOF
)

SESSION_ID=$(jq -er '.id' <<<"$session")
echo "Session ID: $SESSION_ID"
```

Agent overrides follow these rules:

* `agent.id` points to the base agent. Use the optional `agent.version` to specify the base version. If omitted, the latest version is used.

* Omitted parameters use the base agent's configuration. Omitted parameters in `model` also use the corresponding configuration of the base agent.

* `system` and `model` modify only the agent snapshot of the current session. They do not create a new agent version or affect other sessions.

* **Tool and resource lists**: The parameters `tools`, `mcp_servers`, and `skills` are full\-list overrides. Passing a non\-empty array replaces the entire list. Passing `[]` clears the list. Only when the parameter is omitted does it use the base agent's list.

* **Context management strategy**: The parameter `context_management` is a full\-list override. If omitted or set to `null`, it uses the strategy of the base agent version. Passing one item replaces the entire list. Passing `[]` clears custom strategies and switches to the runtime default behavior. The session response may show the platform's current default compaction strategy or default threshold. The current default threshold is `192000` tokens. This displayed value is not written back to the session snapshot. To lock the threshold, explicitly pass `compact_threshold` when creating the session.

* **Multi\-agent configuration**: The parameter `multiagent` is a full\-object override. If omitted, it uses the base agent's configuration. When the configuration is provided, `type` is fixed as `coordinator`, `agents` can contain up to 20 items, and passing `[]` clears the sub\-agent list.

* When `multiagent.agents[].type` is `agent`, enter the sub\-agent ID from the current account and project. You can specify a version with `version`; if the version is omitted, the latest version is used. When it is set to `self`, the current main agent's configuration is reused.

* After creation succeeds, check the `agent` object in the response to confirm the configuration snapshot actually saved for this session.

* After the multi\-agent configuration takes effect, the main agent can delegate tasks to sub\-agents. The platform creates separate threads for sub\-agents and returns the sub\-agents' results to the main thread.

> **Note**

> When you override lists, omission and clearing have different meanings:

* > Do not pass `[]` to `tools`, `mcp_servers`, `skills`, or `multiagent.agents` to indicate "no changes". An empty array clears the corresponding list from the base agent. Omit the parameter when you do not need to override it.

* > `context_management: []` also does not disable automatic compaction. It only clears the custom compaction strategy inherited by the current session and switches to the platform default compaction strategy. To configure the threshold and summary instructions, see [Configure context compaction](context-compaction.md).

## Override environment configuration (optional)

Most sessions directly pass `environment_id` and use the environment's full runtime configuration. Only when the current task needs different dependency packages, environment variables, initialization scripts, or output storage locations should you use the `environment` object instead.

Environment overrides follow these rules:

* `environment.id` points to the environment used as the base configuration.

* `environment.config.type` must be the same as the type of the base environment.

* `environment_id` and `environment` can be provided at the same time, but when both are provided, `environment.id` must be the same as `environment_id`; otherwise, the request is rejected.

* Omitted `config` sub\-parameters use the base environment's configuration. The provided sub\-parameters are written only to the environment snapshot of the current session. They do not modify the original environment or affect other sessions created later.

### Override dependency packages, environment variables, and initialization scripts

When you need to add dependencies, inject task\-specific variables, or run initialization commands for a single session, override `config.packages`, `config.env`, and `config.setup_script`:

**Curl**

```Bash
session=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/sessions" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<EOF
{
  "agent": "$AGENT_ID",
  "environment": {
    "type": "environment_with_overrides",
    "id": "$ENVIRONMENT_ID",
    "config": {
      "type": "cloud",
      "packages": {
        "pip": ["pyfiglet==1.0.2"]
      },
      "env": {
        "SESSION_PROFILE": "document-analysis"
      },
      "setup_script": "mkdir -p /workspace/bootstrap && echo ready > /workspace/bootstrap/status.txt"
    }
  },
  "title": "Session with runtime configuration overrides"
}
EOF
)

SESSION_ID=$(jq -er '.id' <<<"$session")
echo "Session ID: $SESSION_ID"
```

* **`config.packages`**: configures the `pip`, `apt`, `npm`, `cargo`, `gem`, or `go` dependencies preinstalled when the current session starts. Passing an object replaces the dependency package configuration of the base environment as a whole; passing an empty object `{}` clears all dependencies.

* **`config.env`**: configures environment variables for the current session as string key\-value pairs. Passing an object replaces the environment variables of the base environment as a whole; passing an empty object `{}` clears all environment variables.

* **`config.setup_script`**: configures the Bash initialization script to run when the current session starts. Passing a non\-empty string replaces the script of the base environment; passing an empty string `""` clears it. The script runs in the `/workspace` directory, and its content cannot exceed 256 KiB (262144 bytes).

> **Note**

> If `setup_script` exceeds the size limit, it is not executed. If the script exits with a non\-zero status code, operations completed before the exit are not rolled back. In both cases, the session may still be created successfully and continue running. Have the script write a checkable marker file, and verify that dependencies, directories, or files are ready before sending the task.

> **Tip**

> These parameters are used to override the managed runtime environment of a `cloud` environment. The dependencies, environment variables, and initialization process of a `self_hosted` environment are managed by enterprise workers.

After creation succeeds, `environment.type` in the response is `environment_with_overrides`, and `environment.overridden_fields` lists the `config` sub\-parameters actually overridden this time. If no override object is used, `environment.type` is `environment`, and the response does not return `overridden_fields`.

### Override output storage

To reuse the environment's output storage configuration, pass `environment_id` when creating a session:

* If the environment has not configured `config.tos`: the outputs will be stored in ModelArk managed TOS. You need to download the required files before the platform TTL expires.

* If the environment has `config.tos` configured: artifacts that the platform registers from the outputs directory with the Files API are written to the TOS bucket you configured. The Files API writes them with the prefix `{prefix}outputs/{env-id}/{session-id}/`, and the final object path is based on `tos.object_key` in the response.

Only when the current task needs to use a different bucket or `prefix`, create the session with the `environment` object and temporarily override `config.tos` through `environment_with_overrides`:

**Curl**

```Bash
session=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/sessions" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<EOF
{
  "agent": "$AGENT_ID",
  "environment": {
    "type": "environment_with_overrides",
    "id": "$ENVIRONMENT_ID",
    "config": {
      "type": "cloud",
      "tos": {
        "bucket": "<TOS_BUCKET>",
        "prefix": "session-outputs/"
      }
    }
  },
  "title": "Session with output storage override"
}
EOF
)

SESSION_ID=$(jq -er '.id' <<<"$session")
echo "Session ID: $SESSION_ID"
```

Output storage overrides follow these rules:

* `config.tos` is an atomic configuration. When you pass a non\-empty object, both `bucket` and `prefix` must be provided.

* When `config.tos` is omitted, outputs use the storage location configured in the environment; when an empty object `{}` is passed, the outputs of this session are stored in ModelArk managed TOS.

> **Note**

> Whether you reuse or override the storage configuration, have the agent write deliverables to the outputs directory, such as `/workspace/outputs/` or `/mnt/session/outputs/`. A `config.tos` override only affects the storage location of artifacts after they are registered with the Files API; it does not change the sandbox mount directory for `type: tos` in `resources`.

## Inject end\-user credentials through vaults (optional)

If the agent needs to use authentication information in third\-party calls such as MCP tools, CLI, or SDK (for details, see [Use Vaults authentication](use-vaults-authentication.md)), reference pre\-stored credentials with `vault_ids` when creating a session. ModelArk injects secrets by credential type. After automatic refresh is enabled for OAuth credentials, ModelArk obtains a new token when the access token is about to expire.

The minimal example below mounts vaults (credential vaults) to a session:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "agent": "agent-20260701120000-abcde",
    "environment_id": "env-20260701120000-fghij",
    "vault_ids": ["vlt-20260701120000-pqrst"]
  }'
```

For multiple vaults matching rules, runtime behavior when no match is found, rotation, and diagnostics, see [Use Vaults authentication](use-vaults-authentication.md).

## Start a session: subscribe to the event stream first, then send the first event.

> **Note**

> **When****`initial_events`****is not used, creating a session only completes sandbox configuration and does not start any work.** You must send a `user.message` event before the agent starts execution.

This decoupled design lets the client inject vaults credentials and check the sandbox environment before sending the first event to start the agent. To receive events for this turn in real time, take the following steps in order:

1. Open the `GET /sessions/{session_id}/events/stream` event stream.

2. Wait for the server to return `: ready` to confirm that the subscription has been established.

3. Submit a `user.message` to `POST /sessions/{session_id}/events`.

4. Continue reading the same event stream until you receive `session.status_idle` or `session.status_terminated`.

> **Note**

> SSE only pushes events generated after the connection is established. It does not replay historical events. If you send the message before establishing the connection, the client may miss messages, tool calls, or status events that have already been generated.

The following example requires `curl` and `jq` to be installed locally:

**Curl**

```Bash
run_session_turn() (
  local session_id="$1"
  local user_text="$2"
  local stream_dir
  local stream_pipe=""
  local stream_error
  local stream_pid=""
  local stream_number=0
  local stream_ready=false
  local seen_ids_file
  local events_payload
  local turn_done=false
  local result_code=1
  local message_count=0
  local reconnect_count=0
  local max_reconnects="\\${ARK_MAX_SSE_RECONNECTS:-3}"
  local max_history_pages="\\${ARK_MAX_HISTORY_PAGES:-100}"
  local line
  local data

  : "\\${ARK_BASE_URL:?Set ARK_BASE_URL before calling run_session_turn}"
  : "\\${ARK_API_KEY:?Set ARK_API_KEY before calling run_session_turn}"
  : "\\${session_id:?Pass a Session ID as the first argument}"
  : "\\${user_text:?Pass a user message as the second argument}"
  command -v curl >/dev/null || {
    printf 'curl is required.\n' >&2
    return 1
  }
  command -v jq >/dev/null || {
    printf 'jq is required.\n' >&2
    return 1
  }
  [[ $max_reconnects =~ ^[0-9]+$ ]] || {
    printf 'ARK_MAX_SSE_RECONNECTS must be a non-negative integer.\n' >&2
    return 1
  }
  [[ $max_history_pages =~ ^[1-9][0-9]*$ ]] || {
    printf 'ARK_MAX_HISTORY_PAGES must be a positive integer.\n' >&2
    return 1
  }

  stream_dir=$(mktemp -d) || {
    printf 'Failed to create a temporary directory for the SSE stream.\n' >&2
    return 1
  }
  stream_error="$stream_dir/stream-error.log"
  seen_ids_file="$stream_dir/seen-event-ids"
  : >"$stream_error"
  : >"$seen_ids_file"

  stop_stream() {
    exec 3<&- || true
    if [[ -n $stream_pid ]] && kill -0 "$stream_pid" 2>/dev/null; then
      kill "$stream_pid" 2>/dev/null || true
    fi
    if [[ -n $stream_pid ]]; then
      wait "$stream_pid" 2>/dev/null || true
    fi
    if [[ -n $stream_pipe ]]; then
      rm -f "$stream_pipe"
    fi
    stream_pid=""
    stream_pipe=""
  }

  cleanup_stream() {
    stop_stream
    rm -rf "$stream_dir"
  }
  trap cleanup_stream EXIT

  start_stream() {
    stop_stream
    stream_number=$((stream_number + 1))
    stream_pipe="$stream_dir/events-$stream_number"
    stream_ready=false

    if ! mkfifo "$stream_pipe"; then
      printf 'Failed to create the SSE pipe at %s.\n' "$stream_pipe" >&2
      return 1
    fi

    curl -sS -N --fail-with-body --max-time 600 \
      "$ARK_BASE_URL/sessions/$session_id/events/stream" \
      -H "Authorization: Bearer $ARK_API_KEY" \
      -H "Accept: text/event-stream" >"$stream_pipe" 2>>"$stream_error" &
    stream_pid=$!
    exec 3<"$stream_pipe"

    while IFS= read -r line <&3; do
      line=\\${line%$'\r'}
      if [[ $line == ": ready" ]]; then
        stream_ready=true
        break
      fi
    done

    if [[ $stream_ready != true ]]; then
      printf 'SSE stream closed before it was ready.\n' >&2
      if [[ -s $stream_error ]]; then
        tail -n 1 "$stream_error" >&2
      fi
      return 1
    fi
  }

  event_seen() {
    local event_id="$1"
    [[ -n $event_id ]] && grep -Fqx -- "$event_id" "$seen_ids_file"
  }

  remember_event() {
    local event_id="$1"
    if [[ -n $event_id ]] && ! event_seen "$event_id"; then
      printf '%s\n' "$event_id" >>"$seen_ids_file"
    fi
  }

  process_event() {
    local event_json="$1"
    local event_id
    local event_type
    local stop_reason
    local retry_status
    local pending_ids

    event_id=$(jq -r '.id // empty' <<<"$event_json" 2>/dev/null || true)
    if event_seen "$event_id"; then
      return 0
    fi
    remember_event "$event_id"

    event_type=$(jq -r '.type // empty' <<<"$event_json" 2>/dev/null || true)
    case "$event_type" in
      agent.message)
        jq -r '.content[]? | select(.type == "text") | .text' <<<"$event_json"
        message_count=$((message_count + 1))
        ;;
      agent.tool_use | agent.mcp_tool_use | agent.custom_tool_use)
        printf '[tool] %s\n' "$(jq -r '.name // .type' <<<"$event_json")"
        ;;
      session.error)
        retry_status=$(jq -r '.error.retry_status.type // empty' <<<"$event_json")
        printf '[error] %s: %s\n' \
          "$(jq -r '.error.type // "unknown_error"' <<<"$event_json")" \
          "$(jq -r '.error.message // "No error message returned."' <<<"$event_json")" >&2
        case "$retry_status" in
          retrying)
            printf '[session] The service is retrying automatically.\n' >&2
            ;;
          exhausted)
            printf '[session] Retries were exhausted for this turn.\n' >&2
            ;;
          terminal)
            printf '[session] The Session cannot continue after this error.\n' >&2
            ;;
          *)
            printf '[session] Unknown error retry status: %s\n' "$retry_status" >&2
            result_code=4
            turn_done=true
            ;;
        esac
        ;;
      session.status_rescheduled)
        printf '[session] The Session was rescheduled; continue waiting.\n' >&2
        ;;
      session.status_idle)
        stop_reason=$(jq -r '.stop_reason.type // empty' <<<"$event_json")
        case "$stop_reason" in
          end_turn)
            if ((message_count == 0)); then
              printf 'The turn ended without an agent.message event.\n' >&2
              result_code=5
            else
              printf '[session] end_turn\n' >&2
              result_code=0
            fi
            turn_done=true
            ;;
          requires_action)
            pending_ids=$(jq -r '.stop_reason.event_ids // [] | join(",")' <<<"$event_json")
            printf '[session] requires_action: %s\n' "$pending_ids" >&2
            result_code=2
            turn_done=true
            ;;
          retries_exhausted)
            printf '[session] retries_exhausted\n' >&2
            result_code=3
            turn_done=true
            ;;
          *)
            printf 'Unknown session.status_idle stop_reason: %s\n' "$stop_reason" >&2
            result_code=5
            turn_done=true
            ;;
        esac
        ;;
      session.deleted)
        printf '[session] deleted\n' >&2
        result_code=4
        turn_done=true
        ;;
      session.status_terminated)
        printf '[session] terminated\n' >&2
        result_code=4
        turn_done=true
        ;;
    esac
  }

  read_history() {
    local mode="$1"
    local page=""
    local page_count=0
    local response
    local event_json

    while :; do
      page_count=$((page_count + 1))
      if ((page_count > max_history_pages)); then
        printf 'History recovery exceeded %s pages.\n' "$max_history_pages" >&2
        return 1
      fi

      if [[ -n $page ]]; then
        response=$(curl -sS --fail-with-body --get \
          "$ARK_BASE_URL/sessions/$session_id/events" \
          -H "Authorization: Bearer $ARK_API_KEY" \
          --data-urlencode "limit=200" \
          --data-urlencode "order=asc" \
          --data-urlencode "page=$page") || return 1
      else
        response=$(curl -sS --fail-with-body --get \
          "$ARK_BASE_URL/sessions/$session_id/events" \
          -H "Authorization: Bearer $ARK_API_KEY" \
          --data-urlencode "limit=200" \
          --data-urlencode "order=asc") || return 1
      fi

      while IFS= read -r event_json; do
        [[ -n $event_json ]] || continue
        if [[ $mode == "seed" ]]; then
          remember_event "$(jq -r '.id // empty' <<<"$event_json")"
        else
          process_event "$event_json"
          [[ $turn_done == true ]] && break
        fi
      done < <(jq -c '.data[]?' <<<"$response")

      [[ $turn_done == true ]] && return 0
      page=$(jq -r '.next_page // empty' <<<"$response")
      [[ -n $page ]] || return 0
    done
  }

  if ! start_stream; then
    return 1
  fi

  # Mark existing history so reconnect recovery only emits this turn.
  if ! read_history seed; then
    printf 'Failed to read the Session history before sending the message.\n' >&2
    return 1
  fi

  events_payload=$(jq -n --arg text "$user_text" \
    '{events: [{type: "user.message", content: [{type: "text", text: $text}]}]}')

  curl -sS --fail-with-body \
    "$ARK_BASE_URL/sessions/$session_id/events" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d "$events_payload" >/dev/null || return 1

  while [[ $turn_done != true ]]; do
    while IFS= read -r line <&3; do
      line=\\${line%$'\r'}
      if [[ $line == ": server draining, reconnect" ]]; then
        break
      fi
      [[ $line == data:* ]] || continue
      data=\\${line#data:}
      data=\\${data# }
      process_event "$data"
      [[ $turn_done == true ]] && break
    done

    [[ $turn_done == true ]] && break
    if ((reconnect_count >= max_reconnects)); then
      printf 'SSE reconnect limit reached before the turn finished.\n' >&2
      return 1
    fi

    reconnect_count=$((reconnect_count + 1))
    printf '[session] Reconnecting SSE and recovering history (%s/%s).\n' \
      "$reconnect_count" "$max_reconnects" >&2

    # Open the replacement stream first, then recover the disconnect window.
    if ! start_stream; then
      return 1
    fi
    if ! read_history recover; then
      printf 'Failed to recover Session history after reconnecting.\n' >&2
      return 1
    fi
  done

  return "$result_code"
)
```

```bash
export ARK_API_KEY="<ARK_API_KEY>"
export ARK_BASE_URL="https://ark.ap-southeast.bytepluses.com/api/v3"

run_session_turn \
  "sesn-20260701120100-xxxxx" \
  "List the files in the current working directory."
```

### Response model and next steps

After the event is sent, the session enters the `running` state. The example above receives messages, tool calls, and status events over the established SSE stream, and stops listening after the session enters `idle` or `terminated`. For event parameters and how to resume after a disconnection, see [Session event stream](session-event-stream.md); if you only need to poll the status, you can periodically get session details. For details, see [Manage session](manage-session.md).
