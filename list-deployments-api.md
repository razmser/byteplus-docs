# List deployments

`GET https://ark.ap-southeast.bytepluses.com/api/v3/deployments`

List all deployments under the current account and project, for viewing and maintaining scheduled tasks.

The list is sorted in descending order by creation time. If the list is long, pass `next_page` from the previous response unchanged to retrieve the next page.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Query parameters

> Passed as URL query string.

**agent_id** `string`  |  Agent ID

Filter by exact agent ID.

**created_at_gte** `string`  |  Creation time lower limit

Returns only deployments created at or after this value, in RFC 3339 format.

**created_at_lte** `string`  |  Creation time upper limit

Returns only deployments created at or before this value, in RFC 3339 format.

**include_archived** `boolean` `Default false`  |  Include archived items

Specifies whether to include archived deployments.

**limit** `integer` `Default 20`  |  Items per page

Specifies the maximum number of records to return per page.

**Range**: `[1, 100]`

**page** `string`  |  Next page identifier

Omit this parameter for the first query. For subsequent queries, pass the `next_page` value returned in the previous page response as is. Do not parse, modify, or generate it yourself. The last page does not return `next_page`. Stop paging at that point.

**search** `string`  |  Search content

When you pass a complete deployment ID, it matches exactly by ID. Other values are matched fuzzily by name.

**status** `string`  |  Run status

Filter by run status. Only `active` and `paused` are supported. Passing any other value returns a 400 error. Whether archived items are included in the results is controlled separately by `include_archived`.

## Response parameters

**data** `object[]`  |  Deployment list

Contains the list of deployment objects on the current page.

**agent** `object`  |  Agent reference

`data.agent`

The agent and version fixed for the deployment.

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

Time when the deployment was created, in RFC 3339 format.

**environment_id** `string`  |  Environment ID

`data.environment_id`

The environment ID used in each run.

**id** `string`  |  Deployment ID

`data.id`

Unique identifier of the deployment. It starts with `depl-` and has the format `depl-<14_digit_timestamp>-<10_random_characters>`.

**initial_events** `object[]`  |  Initial event

`data.initial_events`

Startup event template sent after a new session is created for each run.

User message `object`

User message event. After a session is created for each run, the platform sends this message to the agent as the first user input that starts the task.

**content** `object[]`  |  Message content

`data.initial_events.content`

List of message content blocks. Supports text, images, documents, and video. The parameter structure is the same as `user.message` in the send session event API. For details, see [Session event structure reference](sessions-events-reference-v2.md).

**type** `string`  |  Content type

`data.initial_events.content.type`

Content block type. For the complete structure, see [Session event structure reference](sessions-events-reference-v2.md). Options: `text`, `image`, `document`, `video`.

**type** `string`  |  Event type

`data.initial_events.type`

Event type, fixed as `user.message`.

**name** `string`  |  Name

`data.name`

Deployment name.

**resources** `object[]`  |  Mounted resources

`data.resources`

Resource configuration used when a session is created for each run.

**type** `string`  |  Resource type.

`data.resources.type`

Resource type.`file` indicates a file uploaded through the Files API and mounted to the session. `memory_store` indicates a memory store mounted to the session. `tos` indicates a TOS object directory mounted to the session.

**access** `string`  |  Access permissions

`data.resources.access`

Access permissions for this resource in the session created for each run.`read_only` means the agent can only read the resource. `read_write` means the agent can read and modify resources that support writes.

**file_id** `string`  |  File ID

`data.resources.file_id`

When *type* is `file`, this indicates the ID of the file to mount to the session. The ID is obtained after you upload the file through the Files API.

**memory_store_id** `string`  |  Memory store ID

`data.resources.memory_store_id`

When *type* is `memory_store`, this indicates the ID of the memory store to mount to the session.

**mount_path** `string`  |  Mount path

`data.resources.mount_path`

Mount path of this resource in the runtime environment of the session created for each run.

**tos_bucket** `string`  |  TOS bucket

`data.resources.tos_bucket`

When *type* is `tos`, this indicates the name of the TOS bucket that contains the directory to mount.

**tos_key** `string`  |  TOS directory prefix

`data.resources.tos_key`

When *type* is `tos`, this indicates the object prefix of the directory to mount in TOS. The prefix must end with `/`.

**tos_region** `string`  |  TOS region

`data.resources.tos_region`

When *type* is `tos`, this indicates the ID of the region where the source bucket is located.

**status** `string`  |  Run status

`data.status`

Scheduling status of the deployment.`paused` does not accept scheduled triggers, but still allows manual runs. When `archived_at` has a value, the deployment is archived and no longer accepts any triggers, regardless of this parameter's value. Options: `active` / `paused`.

**tags** `object[]`  |  Tag

`data.tags`

List of deployment tags.

**key** `string`  |  Tag key

`data.tags.key`

The tag key, used to identify the classification dimension.

**value** `string`  |  Tag value

`data.tags.value`

The tag value, used to record the specific content of this classification dimension.

**type** `string`  |  Object type

`data.type`

Object type, fixed as `deployment`.

**updated_at** `string`  |  Update time

`data.updated_at`

Time when the deployment was last updated, in RFC 3339 format.

**vault_ids** `string[]`  |  Vault ID

`data.vault_ids`

List of vault IDs bound when a session is created for each run.

**archived_at** `string`  |  Archive time

`data.archived_at`

Time when the deployment was archived, in RFC 3339 format. This parameter is not returned if the deployment is not archived.

**description** `string`  |  Description

`data.description`

Deployment description. This parameter is not returned if it is not set.

**paused_reason** `object`  |  Pause reason

`data.paused_reason`

Reason why the deployment is paused. This parameter is not returned when *status* is `active`.

**type** `string`  |  Pause type

`data.paused_reason.type`

Pause reason type.`manual` indicates that the pause API was called. `error` indicates that the deployment was automatically paused after a run encountered an unrecoverable error.

**error** `object`  |  Pause error

`data.paused_reason.error`

Error information for automatic pauses. This parameter is not returned when *type* is `manual`.

**message** `string`  |  Error information

`data.paused_reason.error.message`

Human\-readable information used to locate the cause of the failure.

**type** `string`  |  Error type

`data.paused_reason.error.type`

Error category.

**schedule** `object`  |  Schedule

`data.schedule`

Scheduled trigger plan. This parameter is not returned when only manual runs are supported.

**expression** `string`  |  Cron expression

`data.schedule.expression`

Five\-field cron expression in the format `minute hour day month weekday`. For example, `0 9 * * 1-5` triggers at 09:00 on weekdays in the configured time zone. Seconds, years, and shortcuts such as `@daily` are not supported.

**timezone** `string`  |  Time zone

`data.schedule.timezone`

IANA time zone used by the cron expression, such as `Asia/Shanghai`, `UTC`, or `America/New_York`. The platform calculates scheduled trigger times based on this time zone.

**type** `string`  |  Schedule type

`data.schedule.type`

Schedule type, fixed as `cron`.

**upcoming_runs_at** `string[]`  |  Upcoming scheduled times

`data.schedule.upcoming_runs_at`

Up to the next five theoretical scheduled times, in RFC 3339 format, excluding scheduling jitter. When the deployment is paused, times calculated from the current schedule are still returned. After the deployment is archived, an empty array is returned.

**last_run_at** `string`  |  Last scheduled run time

`data.schedule.last_run_at`

The theoretical scheduled time of the most recent scheduled run, in RFC 3339 format. This parameter is not returned if no scheduled trigger has occurred. Manual runs do not update this parameter.

**next_page** `string`  |  Next page identifier

This parameter is returned when there are more results. For subsequent queries, pass this value to `page` as is. Do not parse, modify, or generate it yourself. This parameter is not returned on the last page.
