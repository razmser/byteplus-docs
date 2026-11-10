# Stop work

`POST https://ark.ap-southeast.bytepluses.com/api/v3/environments/{environment_id}/work/{work_id}/stop`

Request to stop the specified work item. The value of `force` determines how the work item is stopped:

* `false`: The server first updates the work item state to `stopping`, allowing the worker to stop the current task within about 60 seconds. After the wait ends, the work item state changes to `stopped`.

* `true`: The server does not wait for the worker to finish the task, and directly updates the work item state to `stopped`.

When `force` is `true`, you can use `reason` to report the stop reason.

This API supports idempotent calls. Duplicate stop requests do not reset the recorded stop state or time. The API returns the latest state of the work item.

When the work item is still in `stopping`, you can pass `force=true` again to stop it immediately.

A running worker will see `state` as `stopping` or `stopped` and `lease_extended` as `false` in subsequent heartbeat responses.

After receiving this result, terminate the current task process and release the local resources occupied by the task.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**environment_id** `string` `Required`  |  Environment ID

The environment ID to operate on, starting with `env-`. Enter the ID obtained from the create environment or list environments API.

**work_id** `string` `Required`  |  Work identifier

The unique identifier of the work item to stop, taken from the `id` in the pull work item API response and starting with `sesn-`.

### Body parameters

**force** `boolean` `Default false`  |  Whether to force the stop

Whether to require the server to stop the work item immediately.`false` means the work item enters `stopping` first and stops after about 60 seconds; `true` means it enters `stopped` immediately.

**reason** `string` `Default others`  |  Stop reason

The stop reason for the work item.

* `lease_expired`: The heartbeat lease expired.

* `worker_abnormal`: The worker encountered an exception.

* `user_cancelled`: The task was canceled by the user.

* `admin_stopped`: The task was stopped by an administrator.

* `completed`: The worker completed execution normally.

* `others`: Other stop reasons.

> **Tip**

> Pass this parameter only when `force` is `true`. Pass `completed` when the worker completes the work item normally.

## Response parameters

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

The current state of the work item.`stopping` indicates that the stop request is being processed; `stopped` indicates that the work item has stopped.

**type** `string`  |  Object type

Type identifier, fixed to `work`.

**updated_at** `string`  |  Update time

The time of the most recent state transition of the work item, in RFC 3339 format.

**acknowledged_at** `string`  |  Acknowledgment time

The time when the work item was successfully acknowledged by the worker, in RFC 3339 format; this parameter is not returned if it has not been acknowledged.

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

The time of the first stop request, in RFC 3339 format. Duplicate calls do not overwrite this value.

**stopped_at** `string`  |  Stop time

The time when the work item enters the `stopped` state, in RFC 3339 format. When `force` is `true`, this parameter is returned immediately. For the first response when `force` is `false`, this parameter is not returned; it is returned when you query again after the work item stops.

**tags** `object[]`  |  Resource tags

The list of resource tags associated with the current work item.

**key** `string`  |  Tag key

`tags.key`

The key of the resource tag, used to identify the tag associated with the current work item.

**value** `string`  |  Tag value

`tags.value`

The value of the resource tag, used to describe the value of the current work item under this tag key; this parameter is not returned if no value is set.
