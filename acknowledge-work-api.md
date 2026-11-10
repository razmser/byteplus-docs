# Acknowledge work

`POST https://ark.ap-southeast.bytepluses.com/api/v3/environments/{environment_id}/work/{work_id}/ack`

Acknowledge to the server that the work item returned by the pull work item API will be executed by the current worker. After acknowledgment succeeds, the work item state is updated from `queued` to `starting`, and it will not be assigned to other workers.

When acknowledging a work item, note the following:

* Only work items in the `queued` state can be acknowledged.

* When multiple workers concurrently acknowledge the same work item, only one request succeeds.

* The other requests return HTTP 409 `Conflict`, and the error message contains the current state. After receiving 409, do not execute this work item. Continue polling for other work items.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**environment_id** `string` `Required`  |  Environment ID

The environment ID to operate on, starting with `env-`. Enter the ID obtained from the create environment or list environments API.

**work_id** `string` `Required`  |  Work identifier

The unique identifier of the work item to acknowledge, taken from the `id` in the pull work item API response and starting with `sesn-`.

### Header parameters

> Passed in HTTP request headers.

**Ark\-Worker\-ID** `string`  |  Worker instance ID

The worker instance ID that executes the work item. We recommend passing the instance ID used when pulling a work item. If omitted or an empty string is passed, the server handles the acknowledgment request as coming from an anonymous worker.

## Response parameters

**acknowledged_at** `string`  |  Acknowledgment time

The time when this work item was successfully acknowledged, in RFC 3339 format.

**created_at** `string`  |  Creation time

The time recorded when the server created the work item, in RFC 3339 format.

**data** `object`  |  Work payload

Session information corresponding to the work item.

**id** `string`  |  Session ID

`data.id`

The session ID corresponding to the work item. It can be used to call session\-related APIs.

**type** `string`  |  Payload type

`data.type`

Currently fixed as `session`, indicating that `data.id` points to a session.

**environment_id** `string`  |  Environment ID

The ID of the environment to which the work item belongs, starting with `env-`.

**id** `string`  |  Work identifier

Work ID, starting with `sesn-`.

**state** `string`  |  Current state

The work item state after acknowledgment succeeds, fixed as `starting`.

**type** `string`  |  Object type

Type identifier, fixed to `work`.

**updated_at** `string`  |  Update time

The time of the most recent state transition of the work item, in RFC 3339 format.

**latest_heartbeat_at** `string`  |  Latest heartbeat time

The time when the server most recently received a work item heartbeat, in RFC 3339 format; this parameter is not returned if no heartbeat has been received.

**started_at** `string`  |  Start time

The time when the work item started execution, in RFC 3339 format; this parameter is not returned if execution has not started.

**stop_reason** `string`  |  Stop reason

The reason for the most recent stop of the work item. This parameter is returned only after the work item enters the `stopped` state. If no reason was saved for a historical stop record, it is handled as `others`.

* `lease_expired`: The heartbeat lease expired.

* `worker_abnormal`: The worker encountered an exception.

* `user_cancelled`: The task was canceled by the user.

* `admin_stopped`: The task was stopped by an administrator.

* `completed`: The worker completed execution normally.

* `others`: Other stop reasons.

**stop_requested_at** `string`  |  Stop request time

The time when the server first processed the request to stop the work item, in RFC 3339 format. This parameter is not returned if the stop work item API was not called, or if the work item stopped due to a heartbeat lease timeout.

**stopped_at** `string`  |  Stop time

The time when the work item entered the `stopped` state, in RFC 3339 format; this parameter is not returned if the work item has not stopped.

**tags** `object[]`  |  Resource tags

The list of resource tags associated with the current work item.

**key** `string`  |  Tag key

`tags.key`

The key of the resource tag, used to identify the tag associated with the current work item.

**value** `string`  |  Tag value

`tags.value`

The value of the resource tag, used to describe the value of the current work item under this tag key; this parameter is not returned if no value is set.
