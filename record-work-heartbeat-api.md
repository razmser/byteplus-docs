# Renew work lease

`POST https://ark.ap-southeast.bytepluses.com/api/v3/environments/{environment_id}/work/{work_id}/heartbeat`

During work item execution, the worker should call this API every 30–60 seconds to renew the lease.

The first heartbeat after the work item is acknowledged updates the work item state from `starting` to `active`.

The worker should pass the same non\-empty `Ark-Worker-ID` when claiming and renewing the lease. Omitting this request header maintains compatibility with legacy clients that did not set a worker ID, but cannot restore stopped work items.

Subsequent requests should pass the `last_heartbeat` from the previous response unchanged to `expected_last_heartbeat`.

If `Ark-Worker-ID` does not match the worker that claimed the work item, multiple workers renew the lease at the same time, or `expected_last_heartbeat` is not the most recent returned value, the server returns HTTP 412 `PreconditionFailed`.

After receiving this error, first query the latest state of the work item.

After confirming that it is still being executed by the current worker, send the heartbeat again.

If heartbeats are interrupted, the work item enters `stopped` after the lease from the most recent successful heartbeat expires and a 60\-second grace period passes.

After the work item stops because the lease expires, the original worker that claimed the work item can send another heartbeat with the same non\-empty `Ark-Worker-ID`. After the server verifies ownership, it restores the work item directly to `active` and renews the lease. The work item is not requeued or handed off to another worker. Work items in the `stopping` state or stopped for other reasons cannot be restored through heartbeats.

If `lease_extended` in the response is `false`, the worker should stop execution and exit the heartbeat loop.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**environment_id** `string` `Required`  |  Environment ID

The environment ID to operate on, starting with `env-`. Enter the ID obtained from the create environment or list environments API.

**work_id** `string` `Required`  |  Work identifier

The unique identifier of the work item for which to renew the lease, taken from the `id` in the pull work item API response and starting with `sesn-`.

### Query parameters

> Passed as URL query string.

**desired_ttl_seconds** `integer` `Default 300`  |  Desired lease TTL

The lease TTL that the worker wants to use for this renewal, in seconds.

> **Tip**

> If a positive number is passed, the request value is used; if omitted or a non\-positive number is passed, the server treats it as 300 seconds.

**expected_last_heartbeat** `string`  |  Optimistic concurrency validation value

This is used to prevent multiple workers from renewing the lease for the same work item at the same time. For the first heartbeat, you can omit this parameter or pass `NO_HEARTBEAT`; for subsequent requests, you must pass the `last_heartbeat` from the previous response unchanged. Default: `NO_HEARTBEAT`

### Header parameters

> Passed in HTTP request headers.

**Ark\-Worker\-ID** `string`  |  Worker instance ID

The worker instance ID that executes the work item. It should exactly match the non\-empty `Ark-Worker-ID` passed when the work item was claimed. If it is omitted or an empty string is passed, the server handles the heartbeat as coming from an anonymous worker, but cannot restore a stopped work item.

## Response parameters

**last_heartbeat** `string`  |  Latest heartbeat time

This is the time of this heartbeat recorded by the server, in RFC 3339 format. When sending the next heartbeat, pass this value unchanged to `expected_last_heartbeat`.

**lease_extended** `boolean`  |  Whether the lease was extended

Indicates whether this heartbeat extended the work item lease.`true` indicates that the lease was renewed normally, or that the original worker restored a work item stopped because its lease expired to `active` and renewed the lease; `false` indicates that the current state does not allow lease renewal. Use this value together with `state` to determine whether to continue execution.

**state** `string`  |  Current state

The current state of the work item. Options: `queued`, `active`, `stopping`, `stopped`.

> **Tip**

* > `queued`: The work item is still waiting to be acknowledged. Call the claim work item API first, and do not start execution.

* > `active`: The work item is being executed, or the original worker has restored it from a stopped state caused by lease expiration. You can continue sending heartbeats.

* > `stopping`: A stop request has been received. Stop the current task.

* > `stopped`: This heartbeat did not restore the work item. Do not continue execution or send heartbeats.

**ttl_seconds** `integer`  |  Effective TTL

This is the lease TTL recorded by the server for the current work item, in seconds.

**type** `string`  |  Object type

Type identifier, fixed to `work_heartbeat`.
