# Send session event

`POST https://ark.ap-southeast.bytepluses.com/api/v3/sessions/{session_id}/events`

Sends user\-side events to the specified session.

When you need to receive agent output and status changes in real time, after creating a session, establish a [streaming event connection](stream-events-api.md) before calling this API to send events. If you do not need to receive events in real time, you can query the results through [List session events](list-events-api.md) after sending the events.

A successful API response only indicates that the event has been accepted; it does not indicate that processing for this round is complete. For the event parameter structure, see [Session event structure reference](sessions-events-reference-v2.md).

If the client needs to send back `user.tool_result` or `user.custom_tool_result`, set a stable event ID for each tool result. After a request times out, retry with the same event ID and the same content. The server returns the first accepted event; if the content is different, it returns 400. If the content is the same but a new event ID is used, it returns 409.

> **Tip**

> The `content` array of a `user.message` event supports multiple content blocks, including text, images, documents, and video. For image, document, and video blocks, you can set `source.type` to `file` and reference a file uploaded through the Files API by using `source.file_id`. Files with `purpose` set to `user_data` or `agent` can be reused. Whether a file can be passed directly to the current model is determined by both the model capabilities and the content type; if the requirements are not met, you can mount the file to the session runtime environment for processing through session `resources`. For details about the parameter structure, see [Session event structure reference](sessions-events-reference-v2.md).

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**session_id** `string` `Required`  |  Session ID

Specify the ID of the session from which you want to receive events, in the format `sesn-...`.

### Body parameters

**events** `object[]` `Required`  |  Event list

The list of user\-side events to send. It must contain at least 1 event. Each event object must include at least the `type` parameter. Other parameters vary by event type.

For supported event types, parameter structures, and batch order, see [Session event structure reference](sessions-events-reference-v2.md).

When sending `user.tool_result` or `user.custom_tool_result`, if you need to retry after the request times out, pass a stable event ID for each tool result.

The server stores only the first accepted result for each tool call. For duplicate submissions, the server first compares the result content, and then compares the event ID:

* Different result content: HTTP 400 is returned. The same tool call has two different results. Changing the event ID cannot overwrite the first result. Treat this as a business conflict and do not retry.

* Same result content and same event ID: HTTP 200 is returned, and the first accepted event is replayed.

* Same result content but different event ID: HTTP 409 is returned. Retry with the event ID used in the first submission.

If no event ID is passed, the server generates a new ID, so retries cannot be deduplicated as the same request.

> **Tip**

> The entire request body must not exceed 10 MiB.

**type** `string` `Required`  |  Event type

`events.type`

Identifier of the event type. For supported values and the corresponding parameter structures, see [Session event structure reference](sessions-events-reference-v2.md).

## Response parameters

**data** `object[]`  |  List of received events

The list of events accepted by the server. It corresponds one\-to\-one to `events` in the request. The parameters of each element vary by `type`.

For details about the parameter structure, see [Session event structure reference](sessions-events-reference-v2.md).

A successful API response only indicates that the event has been accepted; it does not indicate that processing for this round is complete.

You can choose one of the following query methods as needed:

* To receive results in real time: call [Stream session events](stream-events-api.md).

* To query generated events: call [List session events](list-events-api.md).

**type** `string`  |  Event type

`data.type`

Event type, consistent with the `type` passed in the request, such as `user.message`.

**id** `string`  |  Event ID

`data.id`

The event ID in the receipt. For user events that do not provide an ID, the server generates an ID in the format `sevt-...`; events such as `system.message` may return an empty string. The final event ID is determined by the event list or streaming events.
