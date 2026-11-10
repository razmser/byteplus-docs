# List session tasks

`GET https://ark.ap-southeast.bytepluses.com/api/v3/sessions/{session_id}/tasks`

Paginate through background tool tasks in a specified session. You can filter by task status or session thread.

The list returns only task summaries and does not return the execution result `result`. To read the task result, log location, or artifact path, use [Query session task](get-session-task-api.md).

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**session_id** `string` `Required`  |  Session ID

The session ID for querying background tool tasks. It is a string starting with `sesn-`.

### Query parameters

> Passed as URL query string.

**cursor** `integer`  |  Next page identifier

Next page identifier. Page as follows:

1. Omit it for the first query.

2. For subsequent queries, pass the `next_cursor` from the previous page response as is.

3. Stop querying when `next_cursor` is no longer returned in the response.

Do not parse, generate, or modify this value.

> **Tip**

> When `0` is passed, it is treated as the first query.

**Min**: `0`

**limit** `integer` `Default 20`  |  Tasks per page

Number of tasks returned per page.

> **Tip**

> If `limit` is omitted or `0` is passed, it is treated as 20. If an integer greater than 50 is passed, it is treated as 50. Negative values or values that cannot be parsed as integers return a parameter error.

**Min**: `0`

**session_thread_id** `string`  |  Session thread ID

The session thread ID that initiated the task. After this is passed, only background tool tasks created by this thread are returned. If omitted, tasks for the entire session are returned.

**state** `string`  |  Task status

Filter results by task status. Multiple statuses separated by English commas are supported.

> **Tip**

* > If omitted, results are not filtered by status.

* > If the status value does not match any task, an empty list is returned.

> The following task statuses are supported:

* > `queued`: The task is waiting to run.

* > `running`: The task is running, or artifacts are being saved to the session workspace.

* > `succeeded`: Task succeeded.

* > `failed`: Task failed.

* > `cancelled`: The task has been cancelled.

* > `unknown`: The task status cannot currently be determined. Keep the `task_id` and query again later. The status may return to `running` or change to the actual final status, so do not treat this status as success.

## Response parameters

**items** `object[]`  |  Task list

A summary list of background tool tasks, returned in reverse creation order. A normally executed `bash` command appears in the list only after it is converted into a background task. List items do not include the task execution result `result`. To read the result, query a single task.

**created_at** `integer`  |  Creation time

`items.created_at`

The task submission time, as a Unix timestamp in milliseconds.

**session_id** `string`  |  Session ID

`items.session_id`

The session ID that the task belongs to.

**session_thread_id** `string`  |  Session thread ID

`items.session_thread_id`

The session thread ID that initiated this task.

**state** `string`  |  Task status

`items.state`

The current status of the task.

`unknown` means that the task status cannot currently be determined. It is not a stable final status. Keep the `task_id` and query again later. The status may return to `running` or change to the actual final status, so do not treat this status as success. Options: `queued` / `running` / `succeeded` / `failed` / `cancelled` / `unknown`.

**task_id** `string`  |  Task ID

`items.task_id`

The unique identifier of the background tool task. It is a string starting with `task-`.

**tool** `object`  |  Tool information

`items.tool`

Information about the tool call that generated this task.

**name** `string`  |  Tool name

`items.tool.name`

The name of the tool that generated this task, such as `bash` or `video_generate`.

**description** `string`  |  Tool call description

`items.tool.description`

A description of the purpose of the tool call. This parameter is not returned when no description is available.

**tool_use_id** `string`  |  Tool call ID

`items.tool.tool_use_id`

The tool call ID generated when the agent initiated this tool call. This parameter is not returned when no ID is available.

**error_code** `string`  |  Failure type

`items.error_code`

The failure type when the task has failed, been cancelled, or has an unknown status, and the server has recorded the failure reason. Options: `upstream`, `timeout`, `fetch`, `killed`, `internal`.

**error_message** `string`  |  Failure information

`items.error_message`

The reason description when the task has failed, been cancelled, or has an unknown status, and the server has recorded the failure reason.

**finished_at** `integer`  |  End time

`items.finished_at`

The time when the task entered a final status, as a Unix timestamp in milliseconds. This parameter is not returned when the task has not finished.

**started_at** `integer`  |  Start time

`items.started_at`

The time when the task started running, as a Unix timestamp in milliseconds. This parameter is not returned when the task has not started.

**summary** `string`  |  Task summary

`items.summary`

A summary of the tool execution content. This parameter is not returned when the server has not generated a summary.

**next_cursor** `integer`  |  Next page identifier

Next page identifier. When this parameter is returned, pass it as is in the `cursor` of the next request. If it is not returned, the last page has been reached.
