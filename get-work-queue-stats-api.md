# Get work queue statistics

`GET https://ark.ap-southeast.bytepluses.com/api/v3/environments/{environment_id}/work/stats`

Query the number of work items to be pulled and acknowledged, the number of active workers, and the earliest enqueue time in a self\-hosted environment.

This API is used only to observe the work queue and does not pull, claim, renew leases for, or stop work items.

`queued` work items consist of the following two parts:

* `depth`: The number of work items that have not yet been pulled.

* `pending`: The number of work items that have been pulled but not yet acknowledged.

For other statuses, query the work list.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**environment_id** `string` `Required`  |  Environment ID

The environment ID to operate on, starting with `env-`. Enter the ID obtained from the create environment or list environments API.

## Response parameters

**depth** `integer`  |  Number of items to be pulled

The number of `queued` work items that have not yet been obtained by any worker through the pull work API.

**pending** `integer`  |  Number of items to be claimed

The number of `queued` work items that have been returned to a worker by the pull API but have not yet been confirmed through the acknowledge work item API.

**type** `string`  |  Object type

Type identifier, fixed to `work_queue_stats`.

**workers_polling** `integer`  |  Number of active workers

The number of workers that initiated a work pull request with a non\-empty `Ark-Worker-ID` in the last 30 seconds. Workers that do not include this request header are not counted in this parameter, so this value may be lower than the actual number of running workers.

**oldest_queued_at** `string`  |  Earliest enqueue time

The creation time of the earliest `queued` work item, in UTC RFC 3339 format. This parameter is not returned when the queue has no pending work items.
