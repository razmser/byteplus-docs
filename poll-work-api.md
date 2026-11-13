# Long-poll for work

`GET https://ark.ap-southeast.bytepluses.com/api/v3/environments/{environment_id}/work/poll`

A self\-hosted worker processes work items in the following order:

1. Retrieve pending work items.

2. Call the acknowledge work item API.

3. Periodically renew the work item during execution.

4. Stop the work item when the task completes or is terminated.

This API long\-polls to retrieve pending work items:

* On a hit, it returns a work item that is still `queued`.

* On a miss, when long\-polling expires, or when the request ends while waiting, it returns HTTP 200 and an empty object `{}`.

* The client determines whether a work item was retrieved based on whether `id` exists in the response.

Retrieving a work item only indicates temporary acquisition. Call the acknowledge work item API immediately.

If it is still not acknowledged after the acknowledgment timeout threshold (`reclaim_older_than_ms`) is exceeded, the work item can be retrieved again by another worker.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**environment_id** `string` `Required`  |  Environment ID

The environment ID to operate on, starting with `env-`. Enter the ID obtained from the create environment or list environments API.

### Query parameters

> Passed as URL query string.

**block_ms** `integer` `Default 0`  |  Long\-polling wait time

The maximum time the server continues to wait when no work item is available for assignment, in milliseconds. If omitted or a non\-positive number is passed, the server does not wait. If there is no work item, it returns HTTP 200 and an empty object `{}`. Valid values: 1–999. Values greater than 999 are treated as 999.

**reclaim_older_than_ms** `integer` `Default 5000`  |  Claim timeout threshold

The time to wait for acknowledgment to complete after retrieving a work item, in milliseconds. If acknowledgment times out, other workers can retrieve the work item again.

> **Tip**

> If omitted or a non\-positive number is passed, the server calculates the acknowledgment timeout as 5000 milliseconds.

### Header parameters

> Passed in HTTP request headers.

**Ark\-Worker\-ID** `string`  |  Worker instance ID

The ID of the worker instance that initiates this work item retrieval request. Before acknowledgment is complete, when the same ID initiates another request, the server preferentially returns the previously retrieved work item.

> **Tip**

> Non\-empty values are also used to calculate `workers_polling`. We recommend that each worker instance use a stable value. If this parameter is omitted or an empty string is passed, work items can still be retrieved, but repeated work item retrievals will not be recognized as being from the same worker, and the number of active workers may also be lower than the actual value.

## Response parameters

**acknowledged_at** `string`  |  Acknowledgment time

The time when the work item was successfully acknowledged by the worker, in RFC 3339 format; this parameter is not returned if it has not been acknowledged.

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

**latest_heartbeat_at** `string`  |  Latest heartbeat time

The time when the server most recently received a work item heartbeat, in RFC 3339 format; this parameter is not returned if no heartbeat has been received.

**started_at** `string`  |  Start time

The time when the work item started execution, in RFC 3339 format; this parameter is not returned if execution has not started.

**state** `string`  |  Current state

Fixed as `queued` when a work item is hit, indicating that the work item is waiting for acknowledgment to complete.

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

**type** `string`  |  Object type

Type identifier, fixed to `work`.

**updated_at** `string`  |  Update time

The time of the most recent state transition of the work item, in RFC 3339 format.
