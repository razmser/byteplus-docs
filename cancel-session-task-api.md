# Cancel session task

`POST https://ark.ap-southeast.bytepluses.com/api/v3/sessions/{session_id}/tasks/{task_id}/cancel`

Request cancellation of the specified background tool task. A successful response from the API only indicates that the cancellation request has been processed. It does not necessarily mean that the task has stopped. The `state` parameter in the response indicates the task status after the cancellation request is processed, as follows:

* `cancelled`: The cancellation request has taken effect, and the task has been canceled.

* `succeeded`: When the cancellation request was received, the task had already succeeded, so it was not canceled.

* `failed`: When the cancellation request was received, the task had already failed, so it was not canceled.

* `unknown`: The task status cannot be determined. This does not indicate that the cancellation succeeded. We recommend querying the task status later, or checking the command execution result yourself.

* `queued`: The task is still queued. If `message` is not returned, the cancellation request has been accepted. If `message` is returned, the cancellation request was not accepted.

* `running`: The task is running. If `message` is not returned, the cancellation request has been accepted. If `message` is returned, the task cannot be canceled at the current stage.

When `state` is queued or running, continue to use [Query session task](get-session-task-api.md) to query the status.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**session_id** `string` `Required`  |  Session ID

The ID of the session to which the task belongs. It is a string that starts with `sesn-`.

**task_id** `string` `Required`  |  Task ID

The ID of the background tool task to cancel. It is a string that starts with `task-`. You can obtain it from the `task_id` parameter returned by the tool call or in the task list response.

## Response parameters

**state** `string`  |  Task status

The task status after the cancellation request is processed. Use this parameter to determine whether the cancellation has taken effect, whether the task has already ended, or whether you need to continue querying:

* `cancelled`: The cancellation request has taken effect, and the task has been canceled.

* `succeeded`: When the cancellation request was received, the task had already succeeded, so it was not canceled.

* `failed`: When the cancellation request was received, the task had already failed, so it was not canceled.

* `unknown`: The task status cannot be determined. This does not indicate that the cancellation succeeded. We recommend querying the task status later, or checking the command execution result yourself.

* `queued`: The task is still queued. If `message` is not returned, the cancellation request has been accepted. If `message` is returned, the cancellation request was not accepted. For the specific reason, see `message`. Continue querying the task status.

* `running`: The task is running. If `message` is not returned, the cancellation request has been accepted. If `message` is returned, the task cannot be canceled at the current stage. For the specific reason, see `message`. Continue querying the task status.

**message** `string`  |  Cancellation result description

Returned when the cancellation request is not accepted, to explain the reason.

When `state` is `queued` or `running` and this parameter is returned, handle it according to the description and continue querying the task status. If this parameter is not returned, the cancellation request has been accepted.

When `state` is `succeeded`, `failed`, `cancelled`, or `unknown`, this parameter is not returned.
