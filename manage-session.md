# Manage session

After you create a session, you can query its status, filter the list, update the title and tags, upgrade the runtime configuration, and archive or permanently delete the session after the task ends. Choose archiving when you need to release runtime resources but keep queryable records. Delete the session only when you are sure the records are no longer needed.

> **Tip**

> For details about how to create a session and send the first event, see [Start session](start-session.md).

## Prerequisites

Before you begin, make sure you have:

* An API key configured as the `ARK_API_KEY` environment variable. For details, see [API key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey).

* A created Agent. For details, see [Define an Agent](define-agent.md).

* A created Environment. For details, see [Configure cloud environments](configure-cloud-environment.md).

For the base URL and authentication used in the examples in this section, see [Base URL and authentication](base-url-and-authentication.md).

## Session state machine

|Status |Note |
|---|---|
|`idle` |The agent is waiting for input, such as a user message or tool confirmation. A standard newly created session starts in this status. |
|`running` |The agent is executing a task. |
|`terminated` |The session has been terminated and no longer accepts new events. |
|`rescheduling` |After a transient error occurs, the system is rescheduling the session. |
|`initializing` |The session is initializing the runtime environment or restoring resources. |
|`failed` |The session failed to initialize or run. |
|`upgrading` |The session is upgrading the agent or environment runtime configuration. |

These statuses and their main transitions are as follows:

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/managed-agent/session-resource-state-machine.svg)

State transition rules:

* After session initialization or resource restoration is complete, the status changes from `initializing` to `idle`.

* If session initialization or resource restoration fails, the status changes from `initializing` to `failed`.

* After a user event such as `user.message` or `user.tool_confirmation` is received, the status changes from `idle` to `running`.

* When the agent completes a turn (`end_turn`) or needs to wait for user input, the status changes from `running` to `idle` (`requires_action`; for details, see [Session event stream](session-event-stream.md)).

* If a transient error occurs while the session is running, the status changes from `running` to `rescheduling`. ModelArk automatically restores and reschedules the session. After restoration is complete, the status changes back to `running`, and you do not need to take any action.

* After the server accepts the runtime configuration upgrade, the status changes from `idle` to `upgrading`. After the upgrade is complete or rolled back, the status returns to `idle`.

* A session can change from any status to `terminated`. After termination, the session no longer accepts events, but the record and event history are retained.

> **Tip**

> The status value of the session resource is `rescheduling`; the corresponding SSE status event name is `session.status_rescheduled`. When you call the query API or use the `status` parameter for filtering, pass `rescheduling` instead of `rescheduled` from the event name.

## Choose a retrieval method

The session details API returns only the latest status, usage statistics, and configuration snapshot of the resource. Real\-time replies and historical events are handled by the Session Events API:

|Content to retrieve |API to use |Returned content and limitations |
|---|---|---|
|Receive agent replies and status in real time; receive cumulative usage as needed. |[Stream session events](stream-events-api.md) |Subscribe to real\-time session events through the SSE (Server\-Sent Events) protocol. Establish the connection and wait for `: ready` before sending events. |
|Complete persisted events |[List session events](list-events-api.md) |Used for historical event queries and catch\-up after disconnection. It does not include preview frames. Get the complete execution process within a child thread through [List thread events](list-thread-events-api.md). |
|Latest status, cumulative usage, and configuration snapshot |[Get session details](get-session-api.md) |Returns information such as the agent, runtime environment, resources, status, and tags. It does not include agent replies or the complete event history. |

During normal interactions, receive agent replies and status in real time through SSE; to display usage or for billing, read the `session.usage` cumulative usage snapshot.`GET /sessions/{session_id}/events` is used only for historical event queries and follow\-up checks after disconnection. Do not determine that the current round succeeded based only on `status=idle`. For the complete process of sending tasks, receiving replies, and determining `stop_reason`, see [Session event stream](session-event-stream.md).

## Retrieve a session

Query the details of a session by using `GET /sessions/{session_id}`, including the agent, session runtime environment, resources, status, cumulative usage, and tags:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions/sesn-20260701120100-klmno \
  -H "Authorization: Bearer $ARK_API_KEY"
```

Response example:

```Plain Text
{
  "id": "<SESSION_ID>",
  "status": "idle",
  "usage": {
    "active_seconds": 147.661,
    "cache_read_input_tokens": 271227,
    "input_tokens": 34,
    "output_tokens": 7774,
    "server_tool_use": {
      "web_fetch_requests": 0,
      "web_search_requests": 2
    }
  },
  "stats": {
    "active_seconds": 147.661,
    "duration_seconds": 1788.0
  },
  "agent": {
    "id": "<AGENT_ID>",
    "version": 1
  },
  "environment_id": "<ENVIRONMENT_ID>"
}
```

Main response parameters:

* **`id`**: The session ID.

* **`status`**: The current status. See the table above.

* **`usage`**: The cumulative runtime, model tokens, and number of server\-side search requests during the session. For details, see [Session event stream § Track usage](session-event-stream.md).

   * `active_seconds` indicates the cumulative duration for which at least one thread in the session is in the `running` status.

   * `cache_read_input_tokens`, `input_tokens`, and `output_tokens` record cache\-read, uncached input, and output tokens, respectively.

   * `server_tool_use` records the cumulative number of requests initiated by server\-hosted tools. For specific parameters, see [Query session details](get-session-api.md).

* **`stats`**: The cumulative runtime and total duration of the session from creation to the current time or termination.

   * `active_seconds` indicates the cumulative duration for which at least one thread in the session is in the `running` status, in seconds.

   * `duration_seconds` indicates the total duration from creation to the query time. After the session is terminated, this value is fixed as the duration from creation to termination.

* **`agent`**: The bound agent object, including parameters such as `id` and `version`.

* **`environment_id`**: The bound cloud environment.

When you need to view the duration and cumulative usage of a thread, call [Get thread details](get-thread-api.md) and read the thread's `stats` and `usage`.

## List sessions

`GET /sessions` supports filtering by `agent_id`, status, tags, and creation time. The response returns a list of sessions in the `data` array.

**Curl**

```Bash
curl "https://ark.ap-southeast.bytepluses.com/api/v3/sessions?agent_id=agent-20260701120000-abcde&limit=20" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

## Update and upgrade sessions

|Requirement |API |Status change |
|---|---|---|
|Modify the title or tags |[Update session title and tags](update-session-api.md) |Does not change the runtime status of the session |
|Modify the agent or environment runtime configuration |[Upgrade session](upgrade-session-api.md) |First changes from `idle` to `upgrading`, then returns to `idle` after completion. |

The upgrade API keeps the original `session_id` unchanged and does not support rebinding to another agent or environment. Before you start an upgrade, the session must be in `idle`, and the previous task turn must have ended with `end_turn`. After the API returns `upgrading`, poll `GET /sessions/{session_id}`. When the status returns to `idle`, the upgrade process has ended.

> **Note**

> Do not use the [Update session API](update-session-api.md) to modify the agent, environment, `tools`, or `mcp_servers`. This API only updates the title and tags. To change runtime configuration, you must use the [Upgrade Session API](upgrade-session-api.md). Sessions whose `archived_at` already has a value or whose `status` is `terminated` cannot be updated or upgraded.

## Archive a session

When a task has ended and you need to release runtime resources but still keep the session configuration and history for querying, call [Archive session](archive-session-api.md).

Before archiving, make sure the session is `idle`. If the session is running, send an interrupt event first and wait for the current task to end. Even if the session is already idle, the server rejects archiving if it detects tool tasks in `queued` or `running`; wait for the tool tasks to finish or cancel them, and then try again.

```Bash
curl -sS --fail-with-body \
  "https://ark.ap-southeast.bytepluses.com/api/v3/sessions/<SESSION_ID>/archive" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{}'
```

After archiving succeeds, the platform starts reclaiming session runtime resources, but the session configuration and history remain queryable.

After a session whose `status` is `idle` is archived successfully, `status` changes to `terminated`, and `archived_at` records the time when the session first entered that status. After a session is archived, it can no longer be updated, upgraded, or have events written to it.

The archive API supports idempotent calls. When an already terminated session is archived again, the existing `archived_at` is retained; for historical sessions that were terminated before the archive feature was released and that do not have `archived_at`, the server does not backfill it with the time of this call. Repeated archiving does not extend the retention period of events, trace records, or media data such as images and videos. If you need long\-term storage, export the data to your own storage before the corresponding data expires.

## Delete a session

> **Note**

> **Deleting a session is irreversible.** Deletion permanently removes the session record, all events, and the associated sandbox. Sessions whose `status` is `idle` or `terminated` can be deleted directly. For other statuses, the API returns `InvalidAction`. If `status` is `running`, send an [interrupt event](session-event-stream.md) first, wait until `status` returns to `idle`, and then delete the session.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions/sesn-20260701120100-klmno \
  -X DELETE \
  -H "Authorization: Bearer $ARK_API_KEY"
```

## Data retention and deletion impact

Session resource records, complete events, trace records, media data such as images and videos, sandbox state, and agent artifacts use independent retention policies. Archiving only changes the session's availability and runtime resource status, and does not uniformly extend the retention periods of this data:

|Object |Default retention and recovery rules |After session archiving |After session deletion |
|---|---|---|---|
|Session resource record |Always retained unless explicitly deleted |Session configuration records remain queryable. |The resource record can no longer be queried. |
|Session event history |Each persisted complete event is retained for 6 months by default. `event_start` and `event_delta` preview frames are not written to history. |The data continues to be stored for the original retention period; archiving does not extend it. |All events for this session are deleted. |
|Trace records and media data such as images and videos |Follow the retention periods of the corresponding features |The data continues to follow its respective retention periods; archiving does not make it permanently retained. |Deleting a session does not mean the related data in external storage is cleaned up immediately. The retention rules of the corresponding features still apply. |
|Sandbox state |When a session is `idle` and has not been terminated, the file system, installed packages, and temporary files in the sandbox can be restored. After a session remains continuously `idle` for 14 days, it enters `terminated`. |The session enters `terminated`, and the platform starts reclaiming runtime resources. |The associated sandbox and its temporary state are cleaned up. |
|Agent artifacts |ModelArk default storage is used only to temporarily store associated artifacts. Objects written to your own TOS bucket follow the bucket lifecycle you configure. |Associated artifacts in default storage are reclaimed along with runtime resources. Objects in your own TOS bucket are not affected. |Associated artifacts in default storage are cleaned up. Objects in your own TOS bucket are not affected. |

> **Data retention tips**

* > **Independent resources are not affected by session deletion**: When you delete a session, ModelArk does not delete agents, environments, memory, vaults, skills, or input files independently uploaded through the Files API. These objects are independent resources and must be managed according to their respective lifecycles.

* > **Retain agent artifacts long term**: If you need to retain agent artifacts long term, write the artifacts to your own TOS bucket before the associated artifacts in ModelArk default storage are cleaned up. If you also need to retain event records, save them to your own storage before the events expire. For details about how to configure artifact storage, see [Configure artifact storage](configure-cloud-environment.md#configure-output-storage).

* > **Extend the sandbox state retention period**: To continue retaining the sandbox state, send a `user.message` with non\-empty `content` before the current 14\-day retention period expires. After the session completes the current task and enters `idle` again, the platform recalculates the 14\-day retention period. Omitting `content` or passing an empty array does not refresh the retention period.
