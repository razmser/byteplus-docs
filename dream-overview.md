# Use Dream to organize long-term memory

When an agent accumulates duplicate, outdated, or conflicting memories across multiple sessions, manually organizing them one by one becomes increasingly slow and makes it easy to miss stable patterns from historical tasks. **Dream** can organize existing memories and related sessions according to specified rules, generating an independent new memory store in the background for reuse in subsequent tasks.

## Use case

Choose how to use Dream based on the problem you need to solve:

|Scenario |When to use |Value |
|---|---|---|
|Periodic organization |The memory store has accumulated duplicate, outdated, or conflicting content. |Reduce memory noise and preserve stable conclusions for subsequent sessions. |
|Cold\-start a memory store |No long\-term memory has been established yet, but reusable historical sessions are available. |Convert historical conversations into the first memory store to avoid reorganizing and re\-entering them. |
|Extract a specific topic |The main memory store contains multiple topics, and dedicated memory needs to be prepared for a specific task. |Isolate relevant knowledge and reduce interference from irrelevant content in agent retrieval and decision\-making. |
|Cross\-user knowledge accumulation |Multiple users repeatedly encounter similar problems and have developed reusable solutions. |After removing identity information, convert personal experience into reusable problem patterns and solution steps for the team. |

The following tasks are not suitable for Dream:

* **Modify part of a single memory**: directly call the memory update API.

* **Immediately record new information while a session is running**: mount the memory store with `read_write` when creating the session, and explicitly instruct the agent in the system prompt or task instructions to write information that needs to be retained long term.

## Core concepts

* **Input memory store**: Provides the long\-term memories before organization. When cold\-starting only from historical conversations, you can use an empty store.

* **Historical sessions**: Provide the conversation assets for organization. Each Dream requires selecting 1–100 related sessions.

* **Instructions**: define the retention scope, privacy removal, conflict handling, and output structure.

* **Dream**: performs memory filtering, merging, deduplication, and archiving in the background.

* **Output memory store**: stores the organization results and is independent of the input memory store.

## How it works

Dream processes memories in the following order:

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/managed-agent/dream-memory-consolidation-flow.svg)

## Organize and mount long\-term memory

The following steps start with preparing inputs, then create a Dream, wait for the task to finish, check the output, and mount it to a new session after the results meet expectations.

### 1. Prepare inputs

Dream reads one memory store and 1–100 historical sessions. The input scope directly affects organization quality:

* **Organize existing memories**: select the store that needs deduplication, updates, or reorganization.

* **Cold\-start from historical sessions**: create an empty store first, and then use it as the input.

* **Extract a specific topic**: select the main store that contains general background information.

* Select only sessions related to the goal of this organization, and exclude test sessions, invalid conversations, and temporary information that should not be stored long term.

* Check sensitive information and ensure that the subsequent instructions include the corresponding removal rules.

Complete the following preparations before running the examples:

* cURL and `jq` are installed locally.

* The model corresponding to `MODEL_ID` has been activated in the current project.

* The input memory store and historical sessions belong to the current account and project.

Record the input resources and execution model. Subsequent examples continue to use these environment variables:

```bash
export ARK_API_KEY="<ARK_API_KEY>"
export MODEL_ID="<MODEL_ID>"
export INPUT_MEMORY_STORE_ID="<INPUT_MEMORY_STORE_ID>"
export SESSION_ID_1="<SESSION_ID_1>"
export SESSION_ID_2="<SESSION_ID_2>"
```

> **Note**

> Do not directly use sessions that contain unmasked personal information for team\-level organization. If the instructions do not explicitly require removing identity information such as names, email addresses, URLs, and order numbers, the output may continue to retain this content.

### 2. Create dream

Use the instructions in the example to create a Dream. If you need to adjust the organization rules, see [Best practices for writing instructions](dream-overview.md#instructions).

```bash
dream=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/dreams" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<EOF
{
  "inputs": [
    {
      "type": "memory_store",
      "memory_store_id": "$INPUT_MEMORY_STORE_ID"
    },
    {
      "type": "sessions",
      "session_ids": [
        "$SESSION_ID_1",
        "$SESSION_ID_2"
      ]
    }
  ],
  "model": "$MODEL_ID",
  "instructions": "Keep stable preferences and verified conclusions; delete duplicate, outdated, and personally identifiable information; organize results by topic.",
  "output_memory_store_name": "dream-curated-memory",
  "output_memory_store_description": "Long-term memory consolidated by Dream"
}
EOF
)

printf '%s\n' "$dream"

DREAM_ID=$(jq -er '.id' <<<"$dream")
OUTPUT_MEMORY_STORE_ID=$(
  jq -er '.outputs[] | select(.type == "memory_store") | .memory_store_id' <<<"$dream"
)

export DREAM_ID OUTPUT_MEMORY_STORE_ID
printf 'Dream ID: %s\n' "$DREAM_ID"
printf 'Output Memory Store ID: %s\n' "$OUTPUT_MEMORY_STORE_ID"
```

The script records `DREAM_ID` and `OUTPUT_MEMORY_STORE_ID` from the response. Subsequent examples continue to use these two variables.

> **Note**

> The creation response returns `OUTPUT_MEMORY_STORE_ID`, which only means that the server has allocated an output memory store for this task. When Dream is in the `pending` or `running` state, this store cannot be read, modified, or mounted. After the task finishes, follow the instructions of [Verify the output](dream-overview.md#verify-output).

### 3. Track the task

Poll the task through the [Get Dream](get-dream-api.md) API until the status changes to `completed`, `failed`, or `canceled`.

|Status |Description |
|---|---|
|`pending` |The task has been accepted and is waiting to run. |
|`running` |The task is reading the inputs and organizing memories. |
|`completed` |The task is complete, and the output memory store can be mounted, read, and modified. |
|`failed` |The task failed. Identify the cause through `error.type` and `error.message`; the output memory store may retain partial results. |
|`canceled` |The task has been canceled; the output memory store may be empty or may retain partial results generated before cancellation. |

`pending` and `running` are not failure states, and the client should continue querying. Dream runs for up to 48 hours; after the task exceeds this limit, it enters the `failed` state, and `error.type` is `timeout`.

The following script queries once every 30 seconds and waits for up to 48 hours. After the task finishes, the script updates `OUTPUT_MEMORY_STORE_ID`; if the task fails or is canceled, it also outputs error information and prompts you to check partial results:

```bash
MAX_ATTEMPTS=5760
POLL_INTERVAL_SECONDS=30
status=""

for ((attempt = 1; attempt <= MAX_ATTEMPTS; attempt++)); do
  dream=$(
    curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/dreams/$DREAM_ID" \
      -H "Authorization: Bearer $ARK_API_KEY"
  )

  status=$(jq -er '.status' <<<"$dream")
  printf 'Dream status: %s (%d/%d)\n' "$status" "$attempt" "$MAX_ATTEMPTS"

  case "$status" in
    completed | failed | canceled)
      break
      ;;
  esac

  if ((attempt < MAX_ATTEMPTS)); then
    sleep "$POLL_INTERVAL_SECONDS"
  fi
done

if [[ "$status" != "completed" && "$status" != "failed" && "$status" != "canceled" ]]; then
  printf 'Dream did not reach a terminal state within 48 hours.\n' >&2
  exit 1
fi

OUTPUT_MEMORY_STORE_ID=$(
  jq -er '.outputs[] | select(.type == "memory_store") | .memory_store_id' <<<"$dream"
)

export OUTPUT_MEMORY_STORE_ID
printf 'Output Memory Store ID: %s\n' "$OUTPUT_MEMORY_STORE_ID"

if [[ "$status" == "failed" || "$status" == "canceled" ]]; then
  jq -r '.error.message // ("Dream ended with status: " + .status)' <<<"$dream" >&2
  printf 'Inspect the output Memory Store before deciding whether to use it.\n' >&2
fi
```

> **Task tracking notes**

* > **Cancel a task**: when canceling a task in the `pending` or `running` state, the [Cancel Dream](cancel-dream-api.md) API first records the cancellation intent. The API response may still return `pending` or `running`, so you need to continue querying until the task finishes.

* > **View usage**: Query the number of input, output, and cached tokens actually consumed by the task through `usage`. `usage=null` indicates that usage data is not yet available in the current response.

* > **View runtime**: Query the cumulative runtime of Dream excluding idle intervals (in seconds) through `running_time`. `null` is returned when this value is not yet available.

* > **Query historical tasks**: filter tasks by status or input memory store through the [List Dreams](list-dreams-api.md) API, and sort them by creation time. When the response contains `next_page`, pass that value unchanged into the next request.

* > **Troubleshoot errors**: If an HTTP request fails, see [error codes](error-codes.md) for details. Asynchronous errors that occur after Dream has been accepted are returned through `error.type` and `error.message`.

### 4. Verify the output

When the status is `completed`, verify the output memory store. When the status is `failed` or `canceled`, only check whether partial results remain, and troubleshoot the cause using `error.type` and `error.message`; do not directly use this store in subsequent sessions.

The following script lists the memories on the first page and reads the full content of the first memory:

```bash
memories=$(
  curl -sS --fail-with-body \
    "https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/$OUTPUT_MEMORY_STORE_ID/memories?limit=100&path_prefix=/&order_by=path&depth=2" \
    -H "Authorization: Bearer $ARK_API_KEY"
)

jq -r '.data[] | select(.type == "memory") | [.id, .path] | @tsv' <<<"$memories"

MEMORY_ID=$(
  jq -r '[.data[] | select(.type == "memory")][0].id // empty' <<<"$memories"
)

if [[ -z "$MEMORY_ID" ]]; then
  printf 'No Memory is available in the output store.\n' >&2
  exit 1
fi

curl -sS --fail-with-body \
  "https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/$OUTPUT_MEMORY_STORE_ID/memories/$MEMORY_ID" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  | jq '{path, content}'

NEXT_PAGE=$(jq -r '.next_page // empty' <<<"$memories")
if [[ -n "$NEXT_PAGE" ]]; then
  printf 'More Memories are available. Continue with page=%s.\n' "$NEXT_PAGE"
fi
```

When the response contains `next_page`, use this value as the next page identifier to continue querying until all memory has been checked. When the status is `completed`, focus on verifying the following:

1. Whether the topic scope complies with the instructions.

2. Whether duplicate and conflicting content has been merged.

3. Whether the directory paths make it easy for the agent to retrieve information.

4. Whether private information and outdated content have been removed.

Select the next action based on the task status and check results:

|Task status |Output check result |Next action |
|---|---|---|
|`completed` |All acceptance criteria are met. |Mount it to a new session in `read_only` mode. |
|`completed` |Only a small amount of content or some paths are incorrect. |Update, move, or delete the corresponding memory, and then verify it again. |
|`completed` |The overall scope, conflict handling, or directory structure is incorrect. |Adjust the input or instructions, and then create the Dream again. |
|`failed` or `canceled` |The store is empty or contains only partial results. |Check only the existing results, and do not use them directly in subsequent sessions; delete the output store after confirming it is no longer useful. |

### 5. Use or delete the output

After the status is `completed` and verification passes, create a new session and mount the output memory store:

```bash
export AGENT_ID="<AGENT_ID>"
export ENVIRONMENT_ID="<ENVIRONMENT_ID>"
```

```bash
session=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/sessions" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<EOF
{
  "agent": "$AGENT_ID",
  "environment_id": "$ENVIRONMENT_ID",
  "resources": [
    {
      "type": "memory_store",
      "memory_store_id": "$OUTPUT_MEMORY_STORE_ID",
      "access": "read_only"
    }
  ]
}
EOF
)

printf '%s\n' "$session"

SESSION_ID=$(jq -er '.id' <<<"$session")
export SESSION_ID
```

After creation succeeds, the `id` in the response is the ID of the new session. The example explicitly uses `read_only` to prevent the new session from modifying the verified organization results.

If the agent needs to continue updating this store, change `access` to `read_write`.

* **Fix the output**: if only a small amount of content or some paths do not match expectations, directly update, move, or delete the corresponding memory in the output store, and then verify it again.

* **Reorganize**: if the overall scope, conflict handling, or directory structure does not match expectations, adjust the input or instructions, and then create the Dream again. After confirming that the new results are usable, delete the old output store.

* **Keep the input**: the input memory store is not automatically switched or deleted, so it can continue to serve as the original version or rollback reference.

* **Delete the output**: after confirming that the output content is no longer needed, [delete the output memory store](delete-memory-store-api.md); this does not affect the input store.

## Billing

A Dream task incurs the following two costs:

* **Memory organization model call fee**: billed based on the input, output, and cached tokens actually consumed by the task. Use `usage` to view the token usage for this task.

* **Agent runtime fee**: Dream uses the agent runtime when performing memory organization in the background, billed based on the actual runtime duration. Use `running_time` to view the cumulative runtime after excluding idle intervals.

Billing formula: `Dream cost = memory organization model call fee + agent runtime fee`. For model unit prices, agent runtime unit prices, and billing settlement rules, see [Managed Agents billing](model-pricing.md#managed-agents).

## Best practices for writing instructions

Instructions describe the organization goal, retention and exclusion scope, conflict handling method, and output structure. They are not suitable for specifying precise changes to a single memory.

Executable instructions usually include the following information:

* **Organization goal**: describe what task the output memory store serves.

* **Retention scope**: specify the topics, facts, and stable preferences to retain.

* **Exclusion scope**: specify the noise, outdated content, and private information to ignore.

* **Merge rules**: specify how to handle duplicate, conflicting, or time\-varying records.

* **Organization method**: specify the expected topic grouping and path structure.

You can start with the following template:

```text
Organization goal: <task_served_by_the_output_memory_store>
Retention scope: <topics_facts_and_stable_preferences_to_retain>
Exclusion scope: <noise_outdated_content_and_private_information_to_remove>
Conflict handling: <selection_criteria_and_applicable_conditions_for_conflicting_information>
Output structure: <expected_topic_grouping_and_paths>
```

For example, organizing code review memory:

```text
Organization goal: generate long-term memory for use in subsequent code review sessions.
Retention scope: code style preferences, project conventions, and verified troubleshooting methods.
Exclusion scope: one-off debugging records, names, email addresses, URLs, and account identifiers.
Conflict handling: keep the more recent conclusion, and record the conditions under which the conclusion applies.
Output structure: classify by topics/code-review, topics/project-rules, and topics/troubleshooting.
```

Dream is suitable for filtering, merging, and reorganizing information in batches, but it does not guarantee targeted changes by file path or paragraph position. When you only need to adjust a single memory, call the memory update API directly.

## API reference

* **Create Dream API**: [Create Dream](create-dream-api.md)

* **Get Dream API**: [Get Dream](get-dream-api.md)

* **List Dreams API**: [List Dreams](list-dreams-api.md)

* **Cancel Dream API**: [Cancel Dream](cancel-dream-api.md)

* **Delete memory store API**: [Delete memory store](delete-memory-store-api.md)
