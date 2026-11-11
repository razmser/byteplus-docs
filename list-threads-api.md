# List threads

`GET https://ark.ap-southeast.bytepluses.com/api/v3/sessions/{session_id}/threads`

Query the main thread and child threads under the specified session with pagination. A main thread is available only after the session starts executing. Each time the agent delegates a subtask, a child thread is added.

The main thread is listed first, and child threads are returned in ascending order by creation time.

> **Tip**

* > For a newly created session, the thread list may be empty before it starts processing any events.

* > Sessions that support dynamic subagents return thread information recorded by the platform. Historical sessions do not have separate thread records. The API reconstructs the thread list and statuses based on existing events.

* > This API supports only `limit` and `page` pagination. It does not support filtering by status, agent, or parent thread, or reversing the sort order. It returns up to 1000 items each time.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**session_id** `string` `Required`  |  Session ID

The session ID is in the form `sesn-...`.

### Query parameters

> Passed as URL query string.

**limit** `integer` `Default 1000`  |  Threads per page

The number of threads returned per page. Valid range: 0–1000.

> **Tip**

> If `limit` is omitted or set to 0, it is treated as 1000; if an integer greater than 1000 is passed, it is treated as 1000. Negative values or values that cannot be parsed as integers return a parameter error.

**Min**: `0`

**page** `string`  |  Next page identifier

Next page identifier. Pass the `next_page` in the response of the previous page as is; if `next_page` is omitted, pagination starts from the first page.

## Response parameters

**data** `object[]`  |  Thread list

Thread list. A newly created session may return an empty array before it starts processing any events; the main thread can be returned only after the first execution starts.

**agent_name** `string`  |  Agent name

`data.agent_name`

Name of the agent associated with the thread

**created_at** `string`  |  Creation time

`data.created_at`

The creation time of the thread in RFC 3339 format. In historical sessions, this value indicates when the thread was first observed.

**id** `string`  |  Thread ID.

`data.id`

The unique thread identifier is in the form `sthr-...`.

**parent_thread_id** `string / null`  |  Parent thread ID

`data.parent_thread_id`

The parent thread ID of a child thread. This parameter is not returned for the main thread.

**session_id** `string`  |  Session ID

`data.session_id`

The owning session ID is in the form `sesn-...`.

**status** `string`  |  Thread status

`data.status`

Thread status:

Statuses in which processing can continue:

* `running`: The agent is processing the current work.

* `idle`: The current work is paused or finished, and you can continue dispatching tasks to this thread.

Retry or terminal states:

* `rescheduling`: Automatically recovering from a temporary error.

* `archived`: After the session is archived, its child threads enter this state; they can no longer receive tasks, but can still be queried.

* `terminated`: The thread has terminated and can no longer receive any input; this status appears when a historical collaboration\-mode session restores threads from events.

* `unknown`: There are not enough current events to determine the thread status; use thread events for further troubleshooting.

No public API is provided to archive or delete individual threads; `archived`, `terminated`, or `unknown` threads may still appear in the list.

**type** `string`  |  Object type

`data.type`

Object type. Fixed to `session_thread`.

**updated_at** `string`  |  Update time

`data.updated_at`

The last update time of thread information in RFC 3339 format.

**stats** `object`  |  Thread duration statistics

`data.stats`

Runtime duration statistics for the thread.

**active_seconds** `number`  |  Active duration

`data.stats.active_seconds`

The cumulative number of seconds that the thread was actually running. The system adds the duration of each run only after the thread returns from `running` to `idle` or `terminated`; any time for which the thread is still running is not counted yet.

**duration_seconds** `number`  |  Total duration

`data.stats.duration_seconds`

Total duration of the thread from creation to now or termination, in seconds.

**startup_seconds** `number`  |  Startup duration

`data.stats.startup_seconds`

Preparation time for the session's main thread from creation until it first enters `running`, in seconds; child agent threads return 0.

**usage** `object`  |  Thread usage

`data.usage`

The cumulative model and server\-side tool usage for the thread.

**active_seconds** `number`  |  Active duration

`data.usage.active_seconds`

The cumulative number of seconds that the thread was actually running. The system adds the duration of each run only after the thread returns from `running` to `idle` or `terminated`; any time for which the thread is still running is not counted yet.

**cache_read_input_tokens** `integer`  |  Cache read tokens

`data.usage.cache_read_input_tokens`

The cumulative number of input tokens read from the cache by the thread.

**input_tokens** `integer`  |  Total input tokens

`data.usage.input_tokens`

The cumulative number of input tokens consumed by the thread.

**output_tokens** `integer`  |  Total output tokens

`data.usage.output_tokens`

The cumulative number of output tokens generated by the thread.

**server_tool_use** `object`  |  Server tool usage

`data.usage.server_tool_use`

The cumulative number of server\-side tool calls made by the thread.

**general_search_requests** `integer`  |  General search count

`data.usage.server_tool_use.general_search_requests`

Number of `general_search` tool calls.

**visual_search_requests** `integer`  |  Visual search count

`data.usage.server_tool_use.visual_search_requests`

Number of `visual_search` tool calls.

**web_fetch_requests** `integer`  |  Web fetch count

`data.usage.server_tool_use.web_fetch_requests`

Number of `web_fetch` tool calls.

**web_search_requests** `integer`  |  Web search count

`data.usage.server_tool_use.web_search_requests`

Number of `web_search` tool calls.

**next_page** `string`  |  Next page identifier

Next page identifier. If it is empty or the parameter is not returned, it indicates that this is the last page.
