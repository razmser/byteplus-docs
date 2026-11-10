# Session event stream

Communication with Managed Agents is based on events. During integration, first establish SSE and wait for `: ready`, then send events and receive complete events, and finally determine the result based on `stop_reason`. If you need to display usage or perform billing, read the cumulative usage snapshot. After the connection is interrupted, establish a new stream first, and then use the historical events API to fill in the interruption window.

## Prerequisites

Before you begin, make sure you have:

* An API key configured as the `ARK_API_KEY` environment variable. For details, see [API key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey).

* A created Agent. For details, see [Define an Agent](define-agent.md).

* A created Environment. For details, see [Configure cloud environments](configure-cloud-environment.md).

For the base URL and authentication used in the examples in this section, see [Base URL and authentication](base-url-and-authentication.md).

Install `curl` and `jq` locally.

## Core interaction flow

The following flow applies to business applications in cloud and self\-hosted environments:

1. Establish SSE via `GET /sessions/{session_id}/events/stream`.

2. Wait for the server to return `: ready`.

3. Send `user.message` via `POST /sessions/{session_id}/events`.

4. Continuously receive `agent.*`, `session.*`, and `span.*` events.

5. Consider the current turn to have ended normally only when `session.status_idle` is received later and `stop_reason.type=end_turn`.

If you need to display usage or perform billing, read the `session.usage` cumulative usage snapshot returned before `session.status_idle`.`session.usage` is not a condition for determining that the current turn is complete.

> **Note**

> **Do not send a message before establishing SSE.** SSE only pushes events generated after the connection is established. If you send a message before establishing SSE, you will miss real\-time events generated before the connection is established, and the client will also need to query historical events to fill the gap.

When a task turn is completed normally, events flow in the following order:

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/managed-agent/session-turn-event-sequence.svg)

* The client establishes SSE and waits for `: ready` before sending `user.message`, to avoid missing events that were generated before the connection was established.

* `session.status_running` indicates that the agent has started processing the task. `span.*` and `agent.thinking` are generated during task execution, and the complete reply is returned by `agent.message`.

* At the end of the current turn, the server returns the final `session.thread_status_idle`, `session.usage`, and `session.status_idle` in sequence. Regardless of the `stop_reason`, `session.usage` is returned before the session enters `idle`.

* Receiving `agent.message` or `session.usage` does not mean the current turn has ended. The client continues reading events. Only `session.status_idle` with `stop_reason.type=end_turn` indicates normal completion.

To directly reuse the complete implementation, see [Run the complete example](session-event-stream.md#complete-example).

## Understand event communication

### What each API does

Select the corresponding API based on the use case:

|API |Client action |Returned content and limitations |
|---|---|---|
|`GET /sessions/{session_id}/events/stream` |Establish SSE and wait for `: ready` to receive session events in real time |It only pushes events generated after the connection is established. It does not replay historical events. |
|`POST /sessions/{session_id}/events` |Send `user.*` or `system.message` events |HTTP 200 only indicates that the event has been accepted. It does not mean the agent has processed it or that the current turn has ended. |
|`GET /sessions/{session_id}/events` |Query complete persisted events by page, for historical viewing and post\-disconnection backfill checks. |Does not include `event_start` or `event_delta` preview frames, and does not provide real\-time push. |
|`GET /sessions/{session_id}` |Query the latest session status, cumulative usage, and configuration snapshot. |Does not return complete event history or agent reply content. |

### Event model

Event types follow the `{domain}.{action}` naming convention. First determine the direction and responsibilities based on the event domain, and then read the corresponding structure based on the full `type`.

|Event domain |Direction |Note |
|---|---|---|
|`user.*`, `system.message` |Client to session |Submit user messages, dynamic system prompts, interrupts, tool confirmations, or tool results. |
|`agent.*` |Session to client |Return messages, thinking process, tool calls, and main\-child thread communication events. |
|`session.*` |Session to client |Return running, waiting, rescheduling, termination, error states, and cumulative usage of the session or child threads. |
|`span.*` |Session to client |Return the process and results of model calls. |

Each complete event includes `id`, `type`, and `processed_at`. When `processed_at` is `null`, it indicates that the event has entered the queue but has not finished processing. For complete event types, parameters, values, and nested structures, see [Session event structure reference](sessions-events-reference-v2.md).

### Event scope for multithreaded sessions

In multi\-agent sessions, in addition to events from the session's main thread, the session event stream also returns the following events that must be observed or handled uniformly at the session layer:

* Child thread creation and status changes.

* Collaboration messages between the main agent and subagents for delegation, reporting, and supplemental requirements.

* Custom tool calls initiated by subagents and tool authorization events that must be handled by the client.

Thread\-related events use `session_thread_id` to identify the thread they belong to. The subagent's thinking process, regular replies, tool results, spans, and preview frames are not fully expanded in the session event stream. Query them by using [List thread events](list-thread-events-api.md) or [Stream thread events](stream-thread-events-api.md).

When backfilling after a disconnection, first use the session event list to fill in missing session\-layer events. When you need to reconstruct the complete execution process of a child thread, query that thread's events by `session_thread_id` and deduplicate them by event ID. Do not infer event ownership based on adjacent events, agent names, or arrival time.

### Runtime environment differences

Business applications in both environments use the same Session Events API. The environment type only changes where the main agent's Bash and file tools are executed; it does not change the executor of other tools:

|Comparison item |Cloud environment |Self\-hosted environment |
|---|---|---|
|Session events |The business application establishes session SSE and sends events. |The business application establishes session SSE and sends events. |
|The main agent's `bash`, `read`, `write`, `edit`, `glob`, and `grep`. |Executed in the ModelArk cloud sandbox, with results returned through `agent.tool_result`. |Executed by the enterprise worker and sandbox, with results returned through `user.tool_result`. |
|`web_fetch`, `web_search`, and multimodal tools. |Executed on the ModelArk side |Executed on the ModelArk side |
|MCP tool |Executed on the ModelArk side |Executed on the ModelArk side |
|Custom tools |Executed by the business application |Executed by the business application |
|Additional workflow |None |Poll worker / acknowledge work, establish worker SSE, and maintain heartbeat |

In a self\-hosted environment, the business application does not directly call the Work API. For the deployment and runtime mechanism of workers, see [Configure and run a self-hosted environment](configure-self-hosted-environment.md).

## Send events

The client sends a `user.message` event to the session through `POST /sessions/{session_id}/events`. `content` is a block array that can mix four types of blocks: `text`, `image`, `document`, and `video`.

* The `source.type` of an `image` supports three types: `base64` (inline Base64, requiring `media_type`), `url` (publicly accessible URL), and `file` (uploaded file ID, with the `file_id` parameter).

* The `source.type` of a `document` supports four types: `file` (uploaded file ID), `url` (publicly accessible URL), `base64` (inline Base64, requiring `media_type`), and `text` (directly inline plain text, with `media_type: text/plain` plus the `data` parameter). The optional `title` and `context` parameters add a title and background description to the document.

* The `source.type` of a `video` supports three types: `base64` (inline Base64, requiring `media_type` and `data`), `url` (publicly accessible URL), and `file` (uploaded video file ID, with the `file_id` parameter). All three sources can set the frame sampling frequency via `fps`, with a value range of 0.2 to 5.

* The same request's `content` array can mix multiple image, document, or video blocks.

* To dynamically append a system prompt for the current turn, you can place a `system.message` immediately after `user.message` in the `events` array. For details, see [Dynamic system prompt](session-event-stream.md#dynamic-system-prompt).

### Common content combination examples

**Plain text**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions/sesn-20260701120100-klmno/events \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "events": [{
      "type": "user.message",
      "content": [{"type": "text", "text": "Help me analyze this sales data and identify anomalies in Q2"}]
    }]
  }'
```

**Text + image (URL)**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions/sesn-20260701120100-klmno/events \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "events": [{
      "type": "user.message",
      "content": [
        {"type": "text", "text": "What is wrong about this trend graph?"},
        {"type": "image", "source": {"type": "url", "url": "https://cdn.example.com/q2-trend.png"}}
      ]
    }]
  }'
```

**Text + image (base64)**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions/sesn-20260701120100-klmno/events \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "events": [{
      "type": "user.message",
      "content": [
        {"type": "text", "text": "What is wrong about this trend graph?"},
        {"type": "image", "source": {"type": "base64", "media_type": "image/png", "data": "iVBORw0KGgoAAAANSUhEUgAA..."}}
      ]
    }]
  }'
```

**Text + image (file_id)**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions/sesn-20260701120100-klmno/events \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "events": [{
      "type": "user.message",
      "content": [
        {"type": "text", "text": "What is wrong about this trend graph?"},
        {"type": "image", "source": {"type": "file", "file_id": "file_img_abc123"}}
      ]
    }]
  }'
```

**Text + document (file_id)**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions/sesn-20260701120100-klmno/events \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "events": [{
      "type": "user.message",
      "content": [
        {"type": "text", "text": "Summarize the key risks in this audit report"},
        {"type": "document", "title": "Q2 audit report.pdf", "context": "Internal audit draft issued by Big4", "source": {"type": "file", "file_id": "file_pdf_xxx"}}
      ]
    }]
  }'
```

**Text + document (inline plain text)**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions/sesn-20260701120100-klmno/events \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "events": [{
      "type": "user.message",
      "content": [
        {"type": "text", "text": "Help me rewrite the following description to make it more technical"},
        {"type": "document", "title": "Original draft", "source": {"type": "text", "media_type": "text/plain", "data": "This system provides data analysis capabilities, including but not limited to report generation, anomaly detection..."}}
      ]
    }]
  }'
```

**Text + document (base64 PDF)**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions/sesn-20260701120100-klmno/events \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "events": [{
      "type": "user.message",
      "content": [
        {"type": "text", "text": "Extract the key clauses in this contract"},
        {"type": "document", "title": "framework-agreement.pdf", "source": {"type": "base64", "media_type": "application/pdf", "data": "JVBERi0xLjQKJaqr..."}}
      ]
    }]
  }'
```

**Text + document (URL)**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions/sesn-20260701120100-klmno/events \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "events": [{
      "type": "user.message",
      "content": [
        {"type": "text", "text": "Summarize this document in 5 points"},
        {"type": "document", "title": "Company annual report", "source": {"type": "url", "url": "https://investor.example.com/annual-2025.pdf"}}
      ]
    }]
  }'
```

**Text + mixed multiple attachments**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions/sesn-20260701120100-klmno/events \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "events": [{
      "type": "user.message",
      "content": [
        {"type": "text", "text": "Give me a complete analysis based on this transcript and the two images"},
        {"type": "document", "source": {"type": "file", "file_id": "file_doc_01"}},
        {"type": "image", "source": {"type": "file", "file_id": "file_img_01"}},
        {"type": "image", "source": {"type": "file", "file_id": "file_img_02"}}
      ]
    }]
  }'
```

The `events` parameter in the request body is an event array, and the server processes it serially in enqueue order.

### Continue sending messages while running

When the session is in the `running` state, you can still continue sending `user.message`. The server first receives the event and writes it to the pending queue, then sends the messages in the queue to subsequent model requests after the current agent reaches a scheduling boundary. Common boundaries include the end of the current model request, the return of tool execution results, and the end of the current turn.

This design is suitable for scenarios such as continuous conversations, appending constraints in real time, and adding supplementary materials during long\-running tasks. The client does not need to poll and wait for `idle` before sending the next message, but it still needs to observe event processing progress through SSE:

* A successful HTTP response only means the event has been received by the server. It does not mean the agent has finished processing this message.

* When the returned event's `processed_at` is `null`, the event is still in the queue, waiting for earlier events to finish processing.

* Multiple queued messages may be processed together in a subsequent model request, and the agent does not necessarily generate a separate reply for each message.

* If the pending queue is full, the API returns an HTTP 409 error. At this point, do not retry quickly. Wait for the agent to consume the queue, or send `user.interrupt` to interrupt the current execution based on your business needs.

### Dynamic system prompt

In addition to the fixed system prompt configured when [defining an agent](define-agent.md), the client can also append a `system.message` in the same request that sends `user.message` to dynamically inject extra instructions for the current turn; the fixed system prompt, runtime `system.message`, and user message are concatenated and sent to the model together.

|Comparison item |Agent\-defined system prompt |`system.message` event of runtime |
|---|---|---|
|Location |Set when creating the agent |Passed when sending events |
|Takes effect |Concatenated when the session is created and fixed throughout the session lifecycle |Appended to the session's system\-role context after each send |

The same request needs to distinguish between the API submission order and the model context order:

|Stage |Order |Note |
|---|---|---|
|The `events` array in the API request |Send `user.message` first, then send `system.message`. |`system.message` must immediately follow `user.message` and be the last element in the array. |
|Context before entering the model |In order: the agent system prompt, historical `system.message`, the current\-turn `system.message`, and the current\-turn `user.message`. |The server first appends the current\-turn `system.message` to the system\-role context, and then initiates the model call for the current turn. |

The model context is actually expanded as follows, with ordinary session history omitted.

|Turn |Context expansion |
|---|---|
|First turn |`[Agent system prompt] [system.message of the first turn] [user.message of the first turn]` |
|Second turn |`[Agent system prompt] [system.message of the first turn] [system.message of the second turn] [user.message of the second turn]` |

`system.message` is written to the session's system\-role context with append\-only semantics. Replacement or clearing is not supported. Each subsequent model call carries all historical `system.message` content in the session. To reset the system prompt, you must create a new session.

Sending `system.message` alone, not immediately followed by `user.message`, or not placing it at the end of the `events` array returns HTTP 400 `InvalidPayload`.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions/sesn-20260701120100-klmno/events \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "events": [
      {"type": "user.message", "content": [{"type": "text", "text": "What is the current position of my order #1234?"}]},
      {"type": "system.message", "content": [{"type": "text", "text": "You are ACME's customer support assistant. You must use available tools to query orders and must never disclose internal customer IDs."}]}
    ]
  }'
```

A successful request returns HTTP 200, and the response `data` array contains the events accepted by the server in the order of the input parameters.

## Receive and handle events

The business application receives complete events through the established SSE connection, and updates state, displays responses, or enters the corresponding branch based on `type`. Complete events such as `agent.message`, `agent.thinking`, and `agent.tool_result` are written to the event history. The client should record event IDs for deduplication during backfill queries after disconnection.

### Display preview content

When you need to update the UI before the complete message is generated, specify the event types to preview through `event_deltas`. Currently, `agent.message` and `agent.thinking` are supported:

Multiple values can be passed in multiple times, such as `?event_deltas=agent.message&event_deltas=agent.thinking`, or passed as a comma\-separated list, such as `?event_deltas=agent.message,agent.thinking`.

> **Note**

> The parameter name must be `event_deltas`, not `event_deltas[]`. The server ignores unknown parameter names and unsupported event types, and continues to return HTTP 200. The client should determine whether the preview subscription has taken effect based on whether the corresponding `event_start` and `event_delta` are received.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/managed-agent/event-preview-assembly-sequence.svg)

* **Preview starts**: The `event_start` event indicates that a preview event has started and provides the ID that will be used by the complete event.

* **Preview delta**: The `event_delta` event appends incremental content by `event_id` and content block `index`. The same event can be returned multiple times.

* **Preview frame identifier**: The top\-level `id` and `processed_at` of each preview frame identify the frame itself. Use `event_start.event.id` or `event_delta.event_id` to associate it with the final complete event.

* **Complete event**: The `agent.message` or `agent.thinking` event returns the final complete content.

Preview frames are not written to the event history and may be incomplete if the connection is interrupted. The UI can use preview frames for real\-time updates, but persistence and business decisions should be based on complete events. For parameter syntax and response format, see [Stream session events](stream-events-api.md).

### Determine the result of the current turn

Do not use a successful HTTP response, `agent.message`, or SSE connection closure as the end condition for the current turn. The client should continue reading status events and handle the following results:

When the session is about to enter `idle`, the server returns `session.usage` first, and then returns `session.status_idle`.`session.usage` provides only the latest cumulative usage and does not provide the processing result for the current turn. Regardless of whether `stop_reason` is `end_turn`, `requires_action`, or `retries_exhausted`, you should continue reading subsequent status events.

|Status event |Stop reason |Client action |
|---|---|---|
|`session.status_idle` |`end_turn` |The current turn ends normally, and you can send the next message. |
|`session.status_idle` |`requires_action` |The current turn has not ended. Return tool confirmations or tool results based on `event_ids`. |
|`session.status_idle` |`retries_exhausted` |The retry count for the current turn has been exhausted. This turn ends, but you can still send new input. |
|`session.status_rescheduled` |Not applicable |ModelArk is automatically recovering. Continue listening and do not resend the original event. |
|`session.status_terminated` |Not applicable |The session has entered a terminal state. Stop sending events. |
|`session.deleted` |Not applicable |The session has been deleted. End the event stream, and do not reconnect or send events. |

When `session.error` is received, you also need to check `error.retry_status.type`:

* **`retrying`**: The server is automatically retrying. Continue listening.

* **`exhausted`**: Retries for the current turn are exhausted. Wait for the corresponding `session.status_idle`.

* **`terminal`**: The session will enter the terminated state. Stop sending new events.

For the complete state transitions, see [Manage session](manage-session.md).

### Handle tool confirmation

When the tool permission policy requires confirmation before execution, follow these steps to resolve `requires_action`:

1. Receive `agent.tool_use` or `agent.mcp_tool_use` events.

2. Receive `session.status_idle`, and confirm that `stop_reason.type=requires_action`.

3. Iterate over `stop_reason.event_ids` and send one `user.tool_confirmation` for each pending event. Enter the event ID in `tool_use_id` unchanged, and set `result` to `allow` or `deny`.

4. After pending confirmation events are handled, the session returns `session.status_running` and continues to process the current task.

When denying a tool call, you can return the specific reason through `deny_message`. The agent will use this to choose another tool, fall back to degraded handling, or explain that it cannot continue. At this stage, do not send a new `user.message` as a substitute for the confirmation.

For how to configure the tool permission policy, see [Tool permission policy](tool-permission-policy.md).

## Reconnect after disconnection and backfill history

The SSE API does not support automatic resume from a breakpoint. To backfill events generated during an interruption, process them in the following order:

1. Call `GET /sessions/{session_id}/events/stream` to open a new event stream, and wait for `: ready`.

2. Call `GET /sessions/{session_id}/events` to query historical events. Omit `page` in the first request.

3. When the response contains `next_page`, use that value unchanged as `page` for the next request until `next_page` is no longer returned.

4. Merge historical events and real\-time events from the new event stream by event ID, skipping events that have already been processed.

When `session.deleted` is received, it indicates that the session has been deleted and is not a recoverable connection interruption. At this point, end the event stream. Do not reconnect or backfill history.

You must establish the new stream before querying history. If you query history before reconnecting, a new listening gap will still remain between the end of the history query and the establishment of the new stream. The [complete example](session-event-stream.md#complete-example) in this topic implements this order and event deduplication.

## Run the complete example

The following `run_session_turn` function connects SSE, sends events, determines results, and backfills history after disconnection. When SSE disconnects unexpectedly, the function first establishes a new stream, then queries historical events by page, and deduplicates them by event ID.

Complete cURL function

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
  local max_reconnects="${ARK_MAX_SSE_RECONNECTS:-3}"
  local max_history_pages="${ARK_MAX_HISTORY_PAGES:-100}"
  local line
  local data

  : "${ARK_BASE_URL:?Set ARK_BASE_URL before calling run_session_turn}"
  : "${ARK_API_KEY:?Set ARK_API_KEY before calling run_session_turn}"
  : "${session_id:?Pass a Session ID as the first argument}"
  : "${user_text:?Pass a user message as the second argument}"
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
      line=${line%$'\r'}
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
      line=${line%$'\r'}
      if [[ $line == ": server draining, reconnect" ]]; then
        break
      fi
      [[ $line == data:* ]] || continue
      data=${line#data:}
      data=${data# }
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

Configure the authentication information and an existing session ID, then call the function:

```bash
export ARK_API_KEY="<ARK_API_KEY>"
export ARK_BASE_URL="https://ark.ap-southeast.bytepluses.com/api/v3"
export SESSION_ID="<SESSION_ID>"

run_session_turn \
  "$SESSION_ID" \
  "Summarize the README in the current working directory and list three key points."
```

When completed normally, the function outputs the complete `agent.message` and exits with status code `0`. Other results use non\-zero status codes:

|status code |Result |Fix |
|---|---|---|
|`0` |`end_turn`, and `agent.message` has been received. |The current turn ends normally. |
|`2` |`requires_action` |Pass back tool confirmations or tool results based on the output event IDs. |
|`3` |`retries_exhausted` |The current turn failed. You can fix the input and send it again. |
|`4` |The session is terminated or deleted, or an unrecoverable error occurs. |Stop sending events to the current session. |
|`5` |The status event is missing a valid stop reason, or the complete response was not received before normal completion. |Query historical events and error information before deciding whether to retry. |

## Interrupt and continue using a session

### Interrupt a session

The client can send a `user.interrupt` event to interrupt the agent's current execution. The interrupt clears pending messages that have not yet been scheduled, preventing old messages from continuing to trigger model calls after the interrupt.

If you are only adding information to the current task, simply continue sending `user.message`, and messages will be processed in queue order. If you want to abandon the current execution and switch to a new task, send `user.interrupt` first, wait for the session to return to `idle`, and then send a new `user.message`:

**Curl**

```Bash
# Step 1: Send an interrupt event
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions/sesn-20260701120100-klmno/events \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"events": [{"type": "user.interrupt"}]}'

# Step 2: After the session returns to idle (after receiving the session.status_idle event), send a new message
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions/sesn-20260701120100-klmno/events \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "events": [{
      "type": "user.message",
      "content": [{"type": "text", "text": "Instead, focus on fixing the bug in line 42."}]
    }]
  }'
```

### Continue using an idle session

When the session is still `idle` and has not been terminated, follow the [send message](session-event-stream.md#send-message) workflow to send a `user.message` that contains real task content. The session changes from `idle` to `running`.

If you need to continue preserving the sandbox state, send a `user.message` with non\-empty `content` before the current period expires. After the session completes the current task and enters `idle` again, the platform recalculates the 14\-day retention period. Omitting `content` or passing an empty array does not refresh the retention period.

If you need to retain events and agent artifacts for a long time, save the necessary data to your own storage. For retention and deletion impacts, see [Data retention and deletion impact](manage-session.md#checkpoint-retention).

## Observability and troubleshooting

### Track usage

Select the corresponding event based on the statistics scope:

* **Cumulative session usage**: Read `usage` from the `session.usage` event. This event returns the latest cumulative snapshot each time before the session enters `idle`. To calculate the increment for the current turn, subtract the previous `session.usage` snapshot from the current snapshot.

* **Usage for a single model request**: Read `model_usage` from the `span.model_request_end` event to analyze the token consumption and inference speed of a specific model call.

The following `session.usage` example includes the session's cumulative runtime, model tokens, and number of server\-side search requests:

```jsonc
{
  "id": "sevt-usg-sevt-20260926143029-e5f6g",
  "type": "session.usage",
  "processed_at": "2026-09-26T14:30:29.220Z",
  "usage": {
    "active_seconds": 147.661,
    "cache_creation": {
      "ephemeral_1h_input_tokens": 0,
      "ephemeral_5m_input_tokens": 0
    },
    "cache_read_input_tokens": 271227,
    "input_tokens": 34,
    "output_tokens": 7774,
    "server_tool_use": {
      "web_fetch_requests": 0,
      "web_search_requests": 2
    }
  }
}
```

`session.usage.id` is generated from the corresponding `session.status_idle` event ID, in the format `sevt-usg-<idle_event_id>`. The two events have the same `processed_at`. The client should preserve the order returned by the server and not sort or deduplicate events only by `processed_at`. The two parameters in `cache_creation` in the current event always return `0`.

To query the latest cumulative usage and duration recorded by the server, call `GET /sessions/{session_id}` to read the session's `usage` and `stats`. To troubleshoot consumption and startup latency by thread, call `GET /sessions/{session_id}/threads/{thread_id}` to read the current thread's `usage` and `stats`. For the statistics integrity boundaries of existing sessions, see [Manage sessions](manage-session.md).

The `model_usage` carried by `span.model_request_end` records only the current model request:

```json
{
  "id": "sevt-mre-01",
  "type": "span.model_request_end",
  "processed_at": "2026-05-31T16:00:02.100Z",
  "model_request_start_id": "sevt-mrs-01",
  "is_error": false,
  "model_usage": {
    "input_tokens": 1820,
    "output_tokens": 42,
    "cache_creation_input_tokens": 1500,
    "cache_read_input_tokens": 0,
    "speed": "standard"
  }
}
```

Meaning of the `model_usage` parameters:

* **`input_tokens`**: Uncached input tokens.

* **`output_tokens`**: All output tokens.

* **`cache_creation_input_tokens`**: Number of input tokens written to the prompt cache in this request.

* **`cache_read_input_tokens`**: Number of input tokens read from the prompt cache in this request.

* **`speed`**: Speed tier of the current request.

### Console observability

The [Managed Agents](https://ai.byteplus.com/ark/region:ap-southeast-1/managed-agents) page in the console provides a visual timeline view for sessions:

* **Session list**: All sessions and their status, creation time, and model.

* **Trace view**: Events within a session are displayed in chronological order and are visible only to developers and administrators.

* **Tool execution**: Each tool call and its result.

### Debugging tips

* **Pay attention to ** **`session.error`**: Use `error.retry_status.type` to determine whether the server will retry, end the current round, or terminate the session.

* **Check tool results**: Failed tool calls usually explain the cause of abnormal agent behavior.

* **Track token usage**: Monitor consumption to optimize prompts and reduce costs.

## Related documents

* Configure built\-in tools, MCP tools, and custom tools. For details, see [Tools](tools.md).

* Observe task delegation between the main thread and child threads. For details, see [Multi Agent](multi-agent.md).

* Query, update, upgrade, or delete a session. For details, see [Manage session](manage-session.md).
