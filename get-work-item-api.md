# Get a work item

`GET https://ark.ap-southeast.bytepluses.com/api/v3/environments/{environment_id}/work/{work_id}`

Query detailed information about the specified work item, including its current state, associated session, resource tags, and lifecycle timestamps.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**environment_id** `string` `Required`  |  Environment ID

The environment ID to operate on, starting with `env-`. Enter the ID obtained from the create environment or list environments API.

**work_id** `string` `Required`  |  Work identifier

The unique identifier of the work item to query, obtained from the `id` in the response of the pull work API or list work items API, starting with `sesn-`.

## Response parameters

**created_at** `string`  |  Creation time

The time recorded when the server created the work item, in RFC 3339 format.

**data** `object`  |  Payload

Session information corresponding to the work item.

**id** `string`  |  Session ID

`data.id`

The session ID corresponding to the work item. It can be used to call session\-related APIs.

**type** `string`  |  Type

`data.type`

Currently fixed as `session`, indicating that `data.id` points to a session.

**environment_id** `string`  |  Environment ID

The ID of the environment to which the work item belongs, starting with `env-`.

**id** `string`  |  Work identifier

Work ID, starting with `sesn-`.

**state** `string`  |  Current state

The current state of the work item: `queued`, `starting`, `active`, `stopping`, `stopped`.

**type** `string`  |  Object type

Type identifier, fixed to `work`.

**updated_at** `string`  |  Update time

The time of the most recent state transition of the work item, in RFC 3339 format.

**acknowledged_at** `string`  |  Acknowledgment time

The time when the work item was successfully acknowledged by the worker, in RFC 3339 format; this parameter is not returned if the work item has not been acknowledged.

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

The time when the server first processed the request to stop the work item, in RFC 3339 format. This parameter is not returned in any of the following cases: the stop work API was not called; the work item stopped because the heartbeat lease timed out.

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
