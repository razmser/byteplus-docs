# Resume deployment

`POST https://ark.ap-southeast.bytepluses.com/api/v3/deployments/{deployment_id}/unpause`

Resume a paused deployment and continue running tasks according to the schedule. When resuming, the server revalidates the agent, environment, vault, and memory store. Whether `type=file` resources are accessible is validated when the next run starts.

The platform continues running from the next scheduled time after resume and does not backfill schedules missed during the pause. Calling the resume API again for a deployment that has already been resumed (`active`) returns an `InvalidAction` error.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**deployment_id** `string` `Required`  |  Deployment ID

ID of the deployment to resume.

## Response parameters

**agent** `object`  |  Agent reference

The agent and version fixed for the deployment.

**id** `string`  |  Agent ID

`agent.id`

Agent ID used by the deployment.

**type** `string`  |  Object type

`agent.type`

Agent reference type, fixed as `agent`.

**version** `integer`  |  Agent version

`agent.version`

Agent version number fixed for the deployment.

**created_at** `string`  |  Creation time

Time when the deployment was created, in RFC 3339 format.

**environment_id** `string`  |  Environment ID

The environment ID used in each run.

**id** `string`  |  Deployment ID

Unique identifier of the deployment. It starts with `depl-` and has the format `depl-<14_digit_timestamp>-<10_random_characters>`.

**initial_events** `object[]`  |  Initial event

Startup event template sent after a new session is created for each run.

User message `object`

User message event. After a session is created for each run, the platform sends this message to the agent as the first user input that starts the task.

**content** `object[]`  |  Message content

`initial_events.content`

List of message content blocks. Supports text, images, documents, and video. The parameter structure is the same as `user.message` in the send session event API. For details, see [Session event structure reference](sessions-events-reference-v2.md).

**type** `string`  |  Content type

`initial_events.content.type`

Content block type. For the complete structure, see [Session event structure reference](sessions-events-reference-v2.md). Options: `text`, `image`, `document`, `video`.

**type** `string`  |  Event type

`initial_events.type`

Event type, fixed as `user.message`.

**name** `string`  |  Name

Deployment name.

**resources** `object[]`  |  Mounted resources

Resource configuration used when a session is created for each run.

**type** `string`  |  Resource type.

`resources.type`

Resource type.`file` indicates a file uploaded through the Files API and mounted to the session. `memory_store` indicates a memory store mounted to the session. `tos` indicates a TOS object directory mounted to the session.

**access** `string`  |  Access permissions

`resources.access`

Access permissions for this resource in the session created for each run.`read_only` means the agent can only read the resource. `read_write` means the agent can read and modify resources that support writes.

**file_id** `string`  |  File ID

`resources.file_id`

When *type* is `file`, this indicates the ID of the file to mount to the session. The ID is obtained after you upload the file through the Files API.

**memory_store_id** `string`  |  Memory store ID

`resources.memory_store_id`

When *type* is `memory_store`, this indicates the ID of the memory store to mount to the session.

**mount_path** `string`  |  Mount path

`resources.mount_path`

Mount path of this resource in the runtime environment of the session created for each run.

**tos_bucket** `string`  |  TOS bucket

`resources.tos_bucket`

When *type* is `tos`, this indicates the name of the TOS bucket that contains the directory to mount.

**tos_key** `string`  |  TOS directory prefix

`resources.tos_key`

When *type* is `tos`, this indicates the object prefix of the directory to mount in TOS. The prefix must end with `/`.

**tos_region** `string`  |  TOS region

`resources.tos_region`

When *type* is `tos`, this indicates the ID of the region where the source bucket is located.

**status** `string`  |  Run status

Scheduling status of the deployment.`paused` does not accept scheduled triggers, but still allows manual runs. When `archived_at` has a value, the deployment is archived and no longer accepts any triggers, regardless of this parameter's value. Options: `active` / `paused`.

**tags** `object[]`  |  Tag

List of deployment tags.

**key** `string`  |  Tag key

`tags.key`

The tag key, used to identify the classification dimension.

**value** `string`  |  Tag value

`tags.value`

The tag value, used to record the specific content of this classification dimension. Only the tag key cannot be empty.

**type** `string`  |  Object type

Object type, fixed as `deployment`.

**updated_at** `string`  |  Update time

Time when the deployment was last updated, in RFC 3339 format.

**vault_ids** `string[]`  |  Vault ID

List of vault IDs bound when a session is created for each run.

**archived_at** `string`  |  Archive time

Time when the deployment was archived, in RFC 3339 format. This parameter is not returned if the deployment is not archived.

**description** `string`  |  Description

Deployment description. This parameter is not returned if it is not set.

**paused_reason** `object`  |  Pause reason

Reason why the deployment is paused. This parameter is not returned when *status* is `active`.

**type** `string`  |  Pause type

`paused_reason.type`

Pause reason type.`manual` indicates that the pause API was called. `error` indicates that the deployment was automatically paused after a run encountered an unrecoverable error.

**error** `object`  |  Pause error

`paused_reason.error`

Error information for automatic pauses. This parameter is not returned when *type* is `manual`.

**message** `string`  |  Error information

`paused_reason.error.message`

Human\-readable information used to locate the cause of the failure.

**type** `string`  |  Error type

`paused_reason.error.type`

Error category.

**schedule** `object`  |  Schedule

Scheduled trigger plan. This parameter is not returned when only manual runs are supported.

**expression** `string`  |  Cron expression

`schedule.expression`

Five\-field cron expression in the format `minute hour day month weekday`. For example, `0 9 * * 1-5` triggers at 09:00 on weekdays in the configured time zone. Seconds, years, and shortcuts such as `@daily` are not supported.

**timezone** `string`  |  Time zone

`schedule.timezone`

IANA time zone used by the cron expression, such as `Asia/Shanghai`, `UTC`, or `America/New_York`. The platform calculates scheduled trigger times based on this time zone.

**type** `string`  |  Schedule type

`schedule.type`

Schedule type, fixed as `cron`.

**upcoming_runs_at** `string[]`  |  Upcoming scheduled times

`schedule.upcoming_runs_at`

Up to the next five theoretical scheduled times, in RFC 3339 format, excluding scheduling jitter. When the deployment is paused, times calculated from the current schedule are still returned. After the deployment is archived, an empty array is returned.

**last_run_at** `string`  |  Last scheduled run time

`schedule.last_run_at`

The theoretical scheduled time of the most recent scheduled run, in RFC 3339 format. This parameter is not returned if no scheduled trigger has occurred. Manual runs do not update this parameter.
