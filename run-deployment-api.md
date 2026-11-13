# Run deployment

`POST https://ark.ap-southeast.bytepluses.com/api/v3/deployments/{deployment_id}/run`

Run a task immediately without waiting for the schedule. The API waits until the session is created and the initial event is accepted, and then writes the final startup result for this run.

A successful response only indicates that the session and initial event have been accepted. It does not indicate that the model, tools, or agent task has been executed successfully. You can still run a deployment manually when it is paused. You cannot run it after it is archived.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**deployment_id** `string` `Required`  |  Deployment ID

The deployment ID to run immediately.

## Response parameters

**agent** `object`  |  Agent reference

The agent and version fixed at the time this run was accepted.

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

The time when the run record was created, in RFC 3339 format.

**deployment_id** `string`  |  Deployment ID

The deployment ID that triggered this run.

**id** `string`  |  Run ID

The unique identifier of the deployment run. It starts with `drun-` and uses the format `drun-<14-digit_timestamp>-<10-random_characters>`.

**trigger_context** `object`  |  Trigger information

The trigger method and scheduled execution time for this run.

**type** `string`  |  Trigger method

`trigger_context.type`

How the run is triggered: `manual` indicates that it is triggered manually through the run\-immediately API; `schedule` indicates that it is triggered automatically by a schedule.

**scheduled_at** `string`  |  Scheduled execution time

`trigger_context.scheduled_at`

The time when a scheduled run is expected to start according to the schedule, in RFC 3339 format, excluding delays caused by actual scheduling. This parameter is not returned for manually triggered runs.

**type** `string`  |  Object type

Object type, fixed as `deployment_run`.

**error** `object`  |  Failure information

Error information returned when the run fails to start. This parameter is not returned when the run succeeds or has not finished.

**message** `string`  |  Error information

`error.message`

Human\-readable information used to locate the cause of the failure.

**type** `string`  |  Error type

`error.type`

Error category.

**session_id** `string`  |  Session ID

The session ID returned when both the session and the initial event are accepted successfully. This parameter is not returned if the run fails or has not finished.
