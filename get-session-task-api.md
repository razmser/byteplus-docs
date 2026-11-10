# Query session task

`GET https://ark.ap-southeast.bytepluses.com/api/v3/sessions/{session_id}/tasks/{task_id}`

Query the latest status of the specified background tool task, as well as the execution result and artifact location information after it ends.

When querying a single task, if the task status is `succeeded`, `failed`, `cancelled`, or `unknown`, the response includes `result`; it is not returned when the task status is `queued` or `running`.

`unknown` means that the task status cannot currently be determined. It is not a stable final status. Keep the `task_id` and query again later. The status may return to `running` or change to the actual final status, so do not treat this status as success.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**session_id** `string` `Required`  |  Session ID

The ID of the session to which the task belongs. It is a string that starts with `sesn-`.

**task_id** `string` `Required`  |  Task ID

The ID of the background tool task to query. It is a string that starts with `task-`. You can obtain it from the `task_id` parameter returned by the tool call or in the task list response.

## Response parameters

**created_at** `integer`  |  Creation time

The time when the task record was created, as a Unix timestamp in milliseconds.

**session_id** `string`  |  Session ID

The session ID that the task belongs to.

**session_thread_id** `string`  |  Session thread ID

The session thread ID that initiated this task.

**state** `string`  |  Task status

The current status of the task:

* `queued`: The task is waiting to run.

* `running`: The task is running, or artifacts are being saved to the session workspace.

* `succeeded`: Task succeeded.

* `failed`: Task failed.

* `cancelled`: The task has been cancelled.

* `unknown`: The task status cannot currently be determined. It is not a stable final status. Keep the `task_id` and query again later. The status may return to `running` or change to the actual final status, so do not treat this status as success.

**task_id** `string`  |  Task ID

The unique identifier of the background tool task. It is a string starting with `task-`.

**tool** `object`  |  Tool information

Information about the tool call that generated this task.

**name** `string`  |  Tool name

`tool.name`

The name of the tool that generated this task, such as `bash` or `video_generate`.

**description** `string`  |  Tool call description

`tool.description`

A description of the purpose of the tool call. This parameter is not returned when no description is available.

**tool_use_id** `string`  |  Tool call ID

`tool.tool_use_id`

The tool call ID generated when the agent initiated this tool call. This parameter is not returned when no ID is available.

**error_code** `string`  |  Failure type

The failure type when the task has failed, been cancelled, or has an unknown status, and the server has recorded the failure reason:

* `upstream`: The service that the tool depends on failed.

* `timeout`: The task execution timed out.

* `fetch`: Failed to fetch or save the task result.

* `killed`: The task execution was terminated.

* `internal`: An error occurred while the platform was processing the task. The underlying error cause is not returned. Contact technical support with the `task_id`.

**error_message** `string`  |  Failure information

The reason description when the task has failed, been cancelled, or has an unknown status, and the server has recorded the failure reason. When `error_code` is `internal`, a platform\-side failure description is returned, and the underlying error cause is not returned.

**finished_at** `integer`  |  End time

The time when the task entered a final status, as a Unix timestamp in milliseconds. This parameter is not returned when the task has not finished.

**result** `object`  |  Execution result

Task execution result.

When querying a single task, if the task status is `succeeded`, `failed`, `cancelled`, or `unknown`, the response includes `result`; it is not returned when the task status is `queued` or `running`.

**content** `object[]`  |  Result content

`result.content`

The list of result content blocks returned by the tool.

**text** `string`  |  Text content

`result.content.text`

A text description of the tool execution result or failure reason.

**type** `string`  |  Content type

`result.content.type`

Content block type, fixed as `text`.

**status** `string`  |  Result status

`result.status`

Tool execution result status:

* `ok`: The task ended successfully.

* `error`: The task failed, was cancelled, or has an unknown status.

**meta_info** `object`  |  Result metadata

`result.meta_info`

Information for reading logs, locating artifacts in the session workspace, or associating upstream generation tasks. This parameter is not returned when no metadata is available.

**exit_code** `integer`  |  Exit code

`result.meta_info.exit_code`

The exit code of the Bash command. This parameter is not returned when the execution environment does not return an exit code.

**output_path** `string`  |  Artifact path

`result.meta_info.output_path`

The file path of the tool artifact or compatibility\-mode output in the session workspace.

**stderr_path** `string`  |  Standard error path

`result.meta_info.stderr_path`

The file path of Bash standard error in the session workspace.

**stdout_path** `string`  |  Standard output path

`result.meta_info.stdout_path`

The file path of Bash standard output in the session workspace.

**task_id** `string`  |  Generation task ID

`result.meta_info.task_id`

The task ID of the video generation task in the content generation service.

**video_url** `string`  |  Video URL

`result.meta_info.video_url`

The temporary video access URL returned after successful generation. This parameter is not returned when the URL is not retained or is unavailable.

**started_at** `integer`  |  Start time

The time when the task started running, as a Unix timestamp in milliseconds. This parameter is not returned when the task has not started.

**summary** `string`  |  Task summary

A summary of the tool execution content. This parameter is not returned when the server has not generated a summary.
