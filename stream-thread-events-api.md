# Stream thread events

`GET https://ark.ap-southeast.bytepluses.com/api/v3/sessions/{session_id}/threads/{thread_id}/stream`

Subscribe to real\-time events received by the specified thread through the SSE (Server\-Sent Events) protocol. When subscribing to the main thread, you may also receive notifications about subthread creation, status changes, and tool requests that need to be handled by the main thread. For the complete event structure, see [Session event structure reference](sessions-events-reference-v2.md).

> **Tip**

* > This API does not support resuming after disconnection. After the connection is interrupted, you can query events generated during the interruption through [List thread events](list-thread-events-api.md), and then re\-establish the streaming connection.

* > After the connection is established, `: ready` is received first, followed by `: heartbeat` about every 15 seconds. The service instance may send `: server draining, reconnect` before exiting. Reconnect after receiving it. The client should ignore these SSE comment lines and set the read timeout to at least two heartbeat intervals.

* > The server sends event JSON through `data:` lines and may omit explicit `event:` lines. When the thread enters `idle`, the connection is not closed automatically; the client must actively disconnect when it no longer needs to listen.

## Authentication

This API supports the following authentication methods. For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**session_id** `string` `Required`  |  Session ID

The session ID is in the form `sesn-...`.

**thread_id** `string` `Required`  |  Thread ID.

The thread ID, starting with `sthr-`.

### Query parameters

> Passed as URL query string.

**event_deltas** `array`  |  Streaming preview event type

Specify the event types to preview through `event_start` and `event_delta`. Currently supports `agent.message` and `agent.thinking`; no preview frames are returned if not provided.

> **Tip**

> Multiple values can be passed in multiple times (`?event_deltas=agent.message&event_deltas=agent.thinking`) or as a comma\-separated list (`?event_deltas=agent.message,agent.thinking`). Unrecognized values are ignored.

## Response parameters

### SSE streaming response. Each event is a SessionEvent object and contains only events related to the current thread.

**data** `string`  |  Event JSON data

The server returns event JSON through `data:` lines. SSE responses may not contain an `event:` line; in this case, the client can still read the `data:` content as the default `message` event.

The `type` parameter in the JSON indicates the specific business event type.

The common parameters of complete events include `id`, `type`, and `processed_at`.`session_thread_id` is returned only when explicitly listed in the corresponding event structure. Other parameters vary by `type`.

**event** `string`  |  SSE event type

SSE event type, fixed as `message`. When the server does not explicitly return an `event:` line, the SSE client uses this default value; the business event type is read from `data.type`.
