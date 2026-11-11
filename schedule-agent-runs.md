# Schedule agent runs

When the same agent needs to process tasks repeatedly at a fixed cadence, such as generating daily business reports, periodically inspecting data, or summarizing leads each month, you can use a **deployment** to save a reusable session startup configuration.

A deployment does not execute tasks itself. It stores a reusable session startup configuration. When the scheduled time arrives or a manual trigger is invoked, the platform creates a new deployment run and session based on this configuration. Different runs do not reuse conversation context. Each run starts from the same set of initial events.

## Understand runtime objects

After you use a deployment, a recurring task is split into a configuration, startup record, execution session, and initial event template. When creating a schedule, running a one\-off catch\-up task, or troubleshooting results, first identify which layer the issue belongs to. This helps avoid mistaking "startup succeeded" for "task completed":

|Object |Note |
|---|---|
|Deployment |Stores the fixed agent version, environment, initial events, resources, Vault, and trigger schedule. After the configuration is created or updated, it applies only to subsequent new runs. |
|Deployment Run |Records one manual or scheduled startup. A successful run means the platform has created the session and accepted the initial events. To determine whether subsequent agent tasks are complete, continue checking the session. |
|Session |Carries one actual execution. View model outputs, tool calls, task status, and artifacts in the session and its events. |
|Initial events |The first set of events received by each new session. Currently, only `user.message` is supported. |

> **Note**

> Deployment runs only cover the startup stage, not subsequent agent execution. After the API returns `session_id`, continue querying session details and the event stream to confirm model outputs, tool calls, and task status. Do not determine that the business task is complete based only on a successful run.

## Prepare resources

Before you begin, complete the following preparations:

* Create an API key on ModelArk and configure it as `ARK_API_KEY`.

* Create the agent to run and record the agent ID.

* Create an environment and record the environment ID.

* Prepare files, memory stores, and vault as needed.

```Bash
export ARK_API_KEY="<ARK_API_KEY>"
export AGENT_ID="<AGENT_ID>"
export ENVIRONMENT_ID="<ENVIRONMENT_ID>"
```

## Configure a schedule

`schedule.expression` uses a strict five\-field cron expression:

```text
minute hour day-of-month month day-of-week
```

Common configurations:

|Scenario |Expression |Note |
|---|---|---|
|Every hour on the hour |`0 * * * *` |Runs at minute 0 of every hour |
|Every day at 09:00 |`0 9 * * *` |Runs every day in the configured time zone |
|Weekdays at 09:00 |`0 9 * * 1-5` |Runs Monday through Friday |
|Every Monday at 10:30 |`30 10 * * 1` |Runs every Monday |
|The 1st day of every month at 08:00 |`0 8 1 * *` |Runs on the 1st day of the month |

The day\-of\-week field supports numbers `0-7` (`0` and `7` both indicate Sunday, and the others correspond to Monday through Saturday). It also supports English abbreviations `SUN`, `MON`, `TUE`, `WED`, `THU`, `FRI`, and `SAT` (case\-insensitive), and can be combined with ranges and steps, such as `5-7` (Friday through Sunday), `1,7` (Monday and Sunday), and `1-7/2` (Monday, Wednesday, Friday, and Sunday).

`schedule.timezone` must be a valid IANA time zone, such as `Asia/Shanghai`, `UTC`, or `America/New_York`.

> **Note**

> cron does not support seconds, years, shortcuts such as `@daily`, or extended syntax such as `L`, `W`, `#`, and `?`. Passing a six\-field expression returns a parameter error.

## Create deployment

The following example applies to a scenario where a business daily report is generated at 09:00 Beijing time every weekday.`schedule.timezone` is set to `Asia/Shanghai`, which means 09:00 in the cron expression uses Beijing time.

```Bash
curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/deployments" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d @- <<EOF
{
    "name": "Weekday business daily report",
    "agent": "$AGENT_ID",
    "environment_id": "$ENVIRONMENT_ID",
    "initial_events": [
      {
        "type": "user.message",
        "content": [
          {
            "type": "text",
            "text": "Analyze yesterday's business data, generate a daily report, and list anomalous metrics."
          }
        ]
      }
    ],
    "schedule": {
      "type": "cron",
      "expression": "0 9 * * 1-5",
      "timezone": "Asia/Shanghai"
    }
}
EOF
```

The key configuration in the example is as follows:

* `agent`: enter the agent ID. The server fixes the current valid version at the time the request is accepted.

* `environment_id`: specifies the environment used for each run.

* `initial_events`: specifies the first user message received after each session starts.

* `schedule.expression`: sets the trigger to 09:00 on weekdays.

* `schedule.timezone`: specifies the time zone used by the cron expression.

If creation succeeds, a `deployment` object is returned. Record the `id`. You will need it for subsequent queries, runs, and management:

```Bash
export DEPLOYMENT_ID="<DEPLOYMENT_ID>"
```

After you call the create API, the platform only saves this configuration and does not start a session immediately. If `schedule` is configured, the deployment starts triggering from the next scheduled time. If `schedule` is omitted, it can only be triggered manually through the run\-immediately API.

## Run deployment

After you create a schedule, you can usually wait for the platform to trigger it automatically at the scheduled time. Running immediately is mainly used in two scenarios: after creating or updating a deployment, first confirm that the agent, environment, resources, and initial events can start successfully; or run a one\-off catch\-up task outside the schedule.

> **Note**

> The run\-immediately API is not idempotent. Each time it is called, the platform creates a new deployment run and session. If the request times out or returns a server error, first query the latest manual runs for the deployment and confirm that no run or session has already been accepted before deciding whether to retry.

```Bash
curl -sS --fail-with-body -X POST "https://ark.ap-southeast.bytepluses.com/api/v3/deployments/$DEPLOYMENT_ID/run" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

The API waits until the session is created and the initial events are accepted, and writes the final status of this startup to the run. When startup succeeds, a deployment run similar to the following is returned:

```json
{
  "type": "deployment_run",
  "id": "drun-20261027000103-8k2mq",
  "deployment_id": "depl-20261027000103-4rt6u",
  "agent": {
    "type": "agent",
    "id": "agent-20261027000103-2c7v9",
    "version": 3
  },
  "created_at": "2026-10-27T01:00:03Z",
  "trigger_context": {
    "type": "manual"
  },
  "session_id": "sesn-20261027000104-7v5n1"
}
```

Focus on the following parameters:

* `id`: deployment run ID.

* `session_id`: ID of the session created for this run.

* `trigger_context.type`: `manual` for a manual run.

Record the returned values:

```Bash
export DEPLOYMENT_RUN_ID="<DEPLOYMENT_RUN_ID>"
export SESSION_ID="<SESSION_ID>"
```

> **Note**

> If an error occurs during the startup stage, the API returns an HTTP error and does not return a successful `deployment_run` object. The error message contains the run ID that has already been created. After recording this ID, call the run details API to view the failure reason. The platform does not automatically retry this startup.

> If sending the initial events fails, the session may already have been created, but the failed run does not return `session_id`. In this case, you can find the session ID in `error.message`, and then query the actual status of that session. After confirming that the task did not continue, decide whether to run it again.

## Query run results

### Query run details

Query the trigger source and startup result of a specified run:

```Bash
curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/deployment_runs/$DEPLOYMENT_RUN_ID" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

A successfully started run returns `session_id`:

```json
{
  "type": "deployment_run",
  "id": "drun-20261027000103-8k2mq",
  "deployment_id": "depl-20261027000103-4rt6u",
  "created_at": "2026-10-27T01:00:03Z",
  "trigger_context": {
    "type": "schedule",
    "scheduled_at": "2026-10-27T01:00:00Z"
  },
  "session_id": "sesn-20261027000104-7v5n1"
}
```

If startup fails, the run returns `error`:

```json
{
  "type": "deployment_run",
  "id": "drun-20261027000105-3m5n8",
  "deployment_id": "depl-20261027000103-4rt6u",
  "created_at": "2026-10-27T01:00:03Z",
  "trigger_context": {
    "type": "schedule",
    "scheduled_at": "2026-10-27T01:00:00Z"
  },
  "error": {
    "type": "InvalidFile.NotFound",
    "message": "file_id file-xxx not found or not accessible"
  }
}
```

During troubleshooting, determine the status based on the following parameter combinations:

|`session_id` |`error` |Meaning |
|---|---|---|
|Has a value |Not returned |A session has been created, and the initial event has been accepted; continue querying the session to check task progress. |
|Not returned |Has a value |Startup failed. Troubleshoot based on `error.type` and `error.message`; if the failure occurs during the initial event phase, a session might have already been created. |
|Not returned |Not returned |The run is still starting. |

### List runs

List historical runs by deployment:

```Bash
curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/deployment_runs? deployment_id=$DEPLOYMENT_ID&limit=2" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

For scheduled runs, `trigger_context.type` is `schedule`, and `scheduled_at` indicates the originally scheduled trigger time. It does not include the actual scheduling delay. Manual runs have the type `manual` and do not return `scheduled_at`.

If more results are available, the response returns the next page identifier in `next_page`. When continuing the query, pass this value as\-is to `page`. Do not parse, modify, or generate it yourself. The last page does not return `next_page`. Stop paging at that point.

### View session execution results

After the run succeeds, use `session_id` to query the session. Run details only indicate whether the startup phase succeeded; whether the task is complete, the model reported an error, or the tool executed successfully is determined by the session status and events. Sessions created by a deployment record the source deployment ID.

For details, see [Manage sessions](manage-session.md) and [Session event stream](session-event-stream.md).

## Update run configuration

The following example changes the run time to 12:00 on weekdays:

```Bash
curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/deployments/$DEPLOYMENT_ID" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Weekday midday business daily report",
    "schedule": {
      "type": "cron",
      "expression": "0 12 * * 1-5",
      "timezone": "Asia/Shanghai"
    }
  }'
```

The update API performs a partial update; parameters omitted from the request keep their original values:

* `description`: Passing `null` or an empty string clears it.

* `schedule`: Passing `null` removes the schedule while retaining the ability to run manually.

* `resources`, `vault_ids`, and `tags`: Passing `null` or an empty array clears them.

* `initial_events`: Do not pass `null` or an empty array.

The updated configuration is used only by subsequent newly accepted runs. Runs that have already been accepted and sessions that have already been created are not rewritten. They continue to use the configuration snapshot from when they were accepted.

## Manage lifecycle

### Pause scheduled triggers

```Bash
curl -sS --fail-with-body -X POST "https://ark.ap-southeast.bytepluses.com/api/v3/deployments/$DEPLOYMENT_ID/pause" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

Pausing only stops subsequent scheduled triggers. It does not interrupt sessions that have already been accepted or are running. If you need to temporarily make up a missed run, you can still call the run\-now API while the deployment is paused.

### Resume scheduled triggers

```Bash
curl -sS --fail-with-body -X POST "https://ark.ap-southeast.bytepluses.com/api/v3/deployments/$DEPLOYMENT_ID/unpause" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

When resuming, the platform revalidates the pinned agent version, environment, vault, and memory store. Whether `type=file` resources are accessible is still validated when the next run starts. Resuming does not run schedules missed during the pause. The deployment continues triggering from the next scheduled time.

### Archive deployment

```Bash
curl -sS --fail-with-body -X POST "https://ark.ap-southeast.bytepluses.com/api/v3/deployments/$DEPLOYMENT_ID/archive" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

Archiving stops subsequent triggers. After archiving, the deployment cannot be updated, resumed, or run manually, but you can still query the deployment, historical runs, and associated sessions.

> **Warning**

> Archiving cannot be undone. Archive only after you confirm that the deployment will no longer run, and verify the exact deployment ID before the operation.

## Understand configuration limits

|Configuration |Limit |Note |
|---|---|---|
|`initial_events` |50 entries |At least one entry. Currently, only `user.message` is supported. |
|`resources` |500 items |Up to 10 memory stores. Self\-hosted environments do not support mounting resources. |
|`vault_ids` |50 |Each vault must belong to the current account and project. |
|`tags` |50 items |Tag keys can be up to 128 characters, and tag values can be up to 256 characters. |
|Agent tree model resources |1 to 50 |The primary agent and subagents must reference at least one model, and the model provider must be ModelArk. |
|Inference endpoint type |Cannot be mixed |The same agent tree cannot use custom inference endpoints and preset endpoints at the same time. |
|Run configuration snapshot |1 MiB |If this limit is exceeded, the create or update API returns HTTP 413. |

Model resources, file availability, and temporary credentials are all validated when a run starts, so creating a deployment does not guarantee that subsequent runs can start successfully. If the agent configuration becomes invalid, files are inaccessible, or temporary credentials cannot be obtained, the run fails. If the error is unrecoverable, the deployment is also automatically paused.

If `type=file` resources are mounted, the platform validates only the format of `file_id` and `mount_path` when creating, updating, or resuming a deployment. Whether the file exists and whether the current account still has permissions are checked every time a run starts. If validation fails, the current run fails and the deployment is also automatically paused.

## Related documents

* [Create a deployment](create-deployment-api.md)

* [Run a deployment immediately](run-deployment-api.md)

* [List deployment runs](list-deployment-runs-api.md)

* [Start a session](start-session.md)

* [Manage sessions](manage-session.md)

* [Session event stream](session-event-stream.md)

* [Session event structure reference](sessions-events-reference-v2.md)

* [Authenticate with vaults](use-vaults-authentication.md)
