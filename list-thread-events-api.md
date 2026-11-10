# List thread events

`GET https://ark.ap-southeast.bytepluses.com/api/v3/sessions/{session_id}/threads/{thread_id}/events`

Queries the messages, tool calls, and status events that have been generated in the specified thread, with pagination. When querying events in the main session thread, use [List session events](list-events-api.md); to receive events from a specified thread in real time, use [Stream thread events](stream-thread-events-api.md). Returned parameters vary by event type. For details, see [Session event structure reference](sessions-events-reference-v2.md).

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**session_id** `string` `Required`  |  Session ID

Session ID. A string that starts with `sesn-`.

**thread_id** `string` `Required`  |  Thread ID.

The thread ID is in the form `sthr-xxxxx`.

### Query parameters

> Passed as URL query string.

**created_at[gt]**  `string`  |  Start time (exclusive)

The start boundary (exclusive) of the event processing time, in RFC 3339 format. At most two of the four `created_at` boundaries can be used to form a range.

**created_at[gte]**  `string`  |  Start time (inclusive)

The start boundary (inclusive) of the event processing time, in RFC 3339 format. At most two of the four `created_at` boundaries can be used to form a range.

**created_at[lt]**  `string`  |  End time (exclusive)

The end boundary (exclusive) of the event processing time, in RFC 3339 format. At most two of the four `created_at` boundaries can be used to form a range.

**created_at[lte]**  `string`  |  End time (inclusive)

The end boundary (inclusive) of the event processing time, in RFC 3339 format. At most two of the four `created_at` boundaries can be used to form a range.

**limit** `integer` `Default 50`  |  Events per page

Number of events returned per page.

> **Tip**

> If `limit` is 0, the default value 50 is used; if it is greater than 200, 200 is used. Negative values or values that cannot be parsed as integers return a parameter error.

**Range**: `[0, 200]`

**order** `string` `Default asc`  |  Sort order

Event sort order.

> **Tip**

> `desc` indicates descending order; if omitted, or if `asc` or any other value is passed, events are returned in ascending order.

**page** `string`  |  Next page identifier

Next page identifier. Pass the `next_page` returned from the previous page as is; if omitted, pagination starts from the first page.

**types** `string[]`  |  Event type filter

Filter by event type. You can pass this parameter multiple times, for example, `types=user.message&types=agent.message`. For supported values, see [Session event structure reference](sessions-events-reference-v2.md).

## Response parameters

**data** `object[]`  |  Event list

Event list for the specified thread, used to query messages and tool calls within a sub\-agent thread.

Each event object contains `id`, `type`, `processed_at`, and business parameters that vary by `type`.

* **Query main thread events**: Use [List session events](list-events-api.md).

* **View the complete parameter structure**: See [Session event structure reference](sessions-events-reference-v2.md).

**id** `string`  |  Event ID

`data.id`

Unique event identifier, in the format `sevt-...`.

**processed_at** `string`  |  Processing time

`data.processed_at`

Event processing time in RFC 3339 format

**type** `string`  |  Event type

`data.type`

Event type (such as `user.message`, `agent.message`, `session.status_idle`, and so on).

**next_page** `string`  |  Next page identifier

Next page identifier. Pass it as is to the `page` parameter of the next request. This parameter is not returned when there is no more data.
