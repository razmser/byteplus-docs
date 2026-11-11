# List deployment runs

`GET https://ark.ap-southeast.bytepluses.com/api/v3/deployment_runs`

List run records for all deployments to track the startup status each time a scheduled task is triggered.

Run records indicate only the session startup result, not the final execution result of the agent task. The list is sorted in descending order by creation time.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Query parameters

> Passed as URL query string.

**created_at_gt** `string`  |  Creation time lower limit

Returns only runs whose creation time is strictly later than this value, in RFC 3339 format. Cannot be used together with `created_at_gte`.

**created_at_gte** `string`  |  Creation time lower limit

Returns only runs whose creation time is later than or equal to this value, in RFC 3339 format. Cannot be used together with `created_at_gt`.

**created_at_lt** `string`  |  Creation time upper limit

Returns only runs whose creation time is strictly earlier than this value, in RFC 3339 format. Cannot be used together with `created_at_lte`.

**created_at_lte** `string`  |  Creation time upper limit

Returns only runs whose creation time is earlier than or equal to this value, in RFC 3339 format. Cannot be used together with `created_at_lt`.

**deployment_id** `string`  |  Deployment ID

Filter by exact deployment ID.

**has_error** `boolean`  |  Whether it failed

`true` returns only records where startup failed, and `false` returns only records where startup succeeded; if omitted, all records are returned.

**limit** `integer` `Default 20`  |  Items per page

Specifies the maximum number of records to return per page.

**Range**: `[1, 1000]`

**page** `string`  |  Next page identifier

Omit this parameter for the first query. For subsequent queries, pass the `next_page` value returned in the previous page response as is. Do not parse, modify, or generate it yourself. The last page does not return `next_page`. Stop paging at that point.

**trigger_type** `string`  |  Trigger type

Filter by trigger source. Options: `manual`, `schedule`.

## Response parameters

**data** `object[]`  |  Run list

Contains the list of deployment run objects on the current page.

**agent** `object`  |  Agent reference

`data.agent`

The agent and version fixed at the time this run was accepted.

**id** `string`  |  Agent ID

`data.agent.id`

Agent ID used by the deployment.

**type** `string`  |  Object type

`data.agent.type`

Agent reference type, fixed as `agent`.

**version** `integer`  |  Agent version

`data.agent.version`

Agent version number fixed for the deployment.

**created_at** `string`  |  Creation time

`data.created_at`

The time when the run record was created, in RFC 3339 format.

**deployment_id** `string`  |  Deployment ID

`data.deployment_id`

The deployment ID that triggered this run.

**id** `string`  |  Run ID

`data.id`

The unique identifier of the deployment run. It starts with `drun-` and uses the format `drun-<14-digit_timestamp>-<10-random_characters>`.

**trigger_context** `object`  |  Trigger context

`data.trigger_context`

The trigger source and scheduled time of this run.

**type** `string`  |  Trigger type

`data.trigger_context.type`

Trigger type.`manual` indicates that the run\-now API was called; `schedule` indicates that it was triggered by a schedule.

**scheduled_at** `string`  |  Scheduled time

`data.trigger_context.scheduled_at`

The theoretical scheduled time of the scheduled run, in RFC 3339 format, excluding scheduling jitter. This parameter is not returned for manual runs.

**type** `string`  |  Object type

`data.type`

Object type, fixed as `deployment_run`.

**error** `object`  |  Failure information

`data.error`

Error information returned when the run fails to start. This parameter is not returned when the run succeeds or has not finished.

**message** `string`  |  Error information

`data.error.message`

Human\-readable information used to locate the cause of the failure.

**type** `string`  |  Error type

`data.error.type`

Error category.

**session_id** `string`  |  Session ID

`data.session_id`

The session ID returned when both the session and the initial event are accepted successfully. This parameter is not returned if the run fails or has not finished.

**next_page** `string`  |  Next page identifier

This parameter is returned when there are more results. For subsequent queries, pass this value to `page` as is. Do not parse, modify, or generate it yourself. This parameter is not returned on the last page.
