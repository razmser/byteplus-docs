# Webhook event structure reference

When subscribed events such as agent runtime status changes, resource changes, or credential refreshes occur, Managed Agents webhook pushes a unified event envelope to the endpoint. This topic lists the business events that are currently available for subscription and describes the `webhook.test` test event used to verify the receiving pipeline. Webhook events are independent notifications and are not equivalent to the raw events returned by Session Events queries or SSE.

Each event uses the following unified envelope. The outer `type` is fixed as `event`, and `id` is the webhook event ID that starts with `whe-`; `data.type` is the business event type, and `data.id` is the ID of the resource that triggered the event.

```json
{
  "type": "event",
  "id": "whe-20261012080500-f6g7h",
  "created_at": "2026-10-12T08:05:00Z",
  "data": {
    "type": "session.status_idled",
    "id": "sesn-20261012080000-k1l2m"
  }
}
```

After the event body is generated, it remains unchanged. When the same event is delivered to multiple endpoints or retried, the outer `id`, `created_at`, and body all remain unchanged.

> **Tip**

* > Webhooks do not guarantee strict ordering or exactly\-once delivery. The receiver should deduplicate by the outer `id` and read the current status through the resource query API.

* > `session.status_running` maps to `session.status_run_started`; `session.status_idle` maps to `session.status_idled`. The original session event names remain unchanged.

* > `session.status_idled` only indicates that the session has entered the idle state. It does not indicate that the current turn ended successfully. Use `data.id` to query the historical events of the session, and locate the corresponding `session.status_idle` based on the current\-turn boundary recorded by your business logic. If the correspondence cannot be confirmed, do not automatically advance business logic based only on the most recent `end_turn`. The outer webhook `whe-` ID cannot be used as a session event ID.

* > For thread events, `data.id` is the ID of the session the thread belongs to, and `data.session_thread_id` is the ID of the thread whose status changed.

* > For credential events, `data.id` is the credential ID, and `data.vault_id` is the ID of the vault it belongs to. Delete events retain the pre\-deletion ownership relationship.

* > **Query event envelopes**: Use [List webhook events](list-webhook-events-api.md) or [Get webhook event details](get-webhook-event-api.md) to read immutable event envelopes associated with the endpoint within the last 7 days.

* > **Process callbacks**: For callback signature verification, retry, and disabling rules, see [Use webhooks to receive event notifications](receive-webhook-events.md).

## Server events (responses)

### session.status_run_started

> Triggered when the session status changes to running. An event is generated each time the session enters the running state.

**type** `string`  |  Object type

`session.status_run_started.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`session.status_run_started.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`session.status_run_started.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`session.status_run_started.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`session.status_run_started.data.type`

Event type, fixed as `session.status_run_started`.

**id** `string`  |  Resource ID

`session.status_run_started.data.id`

The ID of the session that triggered the event.

---

### session.status_idled

> Triggered when the session status changes to idle. This event does not include `stop_reason`, nor does it indicate that the current turn ended successfully. To determine the stop reason, query the `session.status_idle` event in the session history based on the current\-turn boundary recorded by your business logic.

**type** `string`  |  Object type

`session.status_idled.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`session.status_idled.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`session.status_idled.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`session.status_idled.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`session.status_idled.data.type`

Event type, fixed as `session.status_idle`.

**id** `string`  |  Resource ID

`session.status_idled.data.id`

The ID of the session that triggered the event.

---

### session.status_terminated

> Triggered when the session enters the terminated state.

**type** `string`  |  Object type

`session.status_terminated.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`session.status_terminated.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`session.status_terminated.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`session.status_terminated.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`session.status_terminated.data.type`

Event type, fixed as `session.status_terminated`.

**id** `string`  |  Resource ID

`session.status_terminated.data.id`

The ID of the session that triggered the event.

---

### span.outcome_evaluation_end

> Triggered when the outcome evaluation for a single iteration ends. This does not indicate that the entire session is complete.

**type** `string`  |  Object type

`span.outcome_evaluation_end.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`span.outcome_evaluation_end.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`span.outcome_evaluation_end.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`span.outcome_evaluation_end.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`span.outcome_evaluation_end.data.type`

Event type, fixed as `span.outcome_evaluation_end`.

**id** `string`  |  Resource ID

`span.outcome_evaluation_end.data.id`

The ID of the session that triggered the event.

---

### session.error

> Triggered when a business error occurs during session execution. This does not indicate delivery failure or that the session has terminated.

**type** `string`  |  Object type

`session.error.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`session.error.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`session.error.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`session.error.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`session.error.data.type`

Event type, fixed as `session.error`.

**id** `string`  |  Resource ID

`session.error.data.id`

The ID of the session that triggered the event.

---

### session.thread_status_running

> Triggered when a session thread starts processing a task and enters the running state.

**type** `string`  |  Object type

`session.thread_status_running.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`session.thread_status_running.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`session.thread_status_running.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`session.thread_status_running.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`session.thread_status_running.data.type`

Event type, fixed as `session.thread_status_running`.

**id** `string`  |  Resource ID

`session.thread_status_running.data.id`

The ID of the session that triggered the event.

**session_thread_id** `string`  |  Thread ID.

`session.thread_status_running.data.session_thread_id`

The ID of the session thread whose status changed.

---

### session.thread_status_idle

> Triggered when a session thread finishes processing the current task and enters the idle state.

**type** `string`  |  Object type

`session.thread_status_idle.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`session.thread_status_idle.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`session.thread_status_idle.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`session.thread_status_idle.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`session.thread_status_idle.data.type`

Event type, fixed as `session.thread_status_idle`.

**id** `string`  |  Resource ID

`session.thread_status_idle.data.id`

The ID of the session that triggered the event.

**session_thread_id** `string`  |  Thread ID.

`session.thread_status_idle.data.session_thread_id`

The ID of the session thread whose status changed.

---

### session.thread_status_terminated

> Triggered when a session thread enters the terminated state.

**type** `string`  |  Object type

`session.thread_status_terminated.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`session.thread_status_terminated.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`session.thread_status_terminated.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`session.thread_status_terminated.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`session.thread_status_terminated.data.type`

Event type, fixed as `session.thread_status_terminated`.

**id** `string`  |  Resource ID

`session.thread_status_terminated.data.id`

The ID of the session that triggered the event.

**session_thread_id** `string`  |  Thread ID.

`session.thread_status_terminated.data.session_thread_id`

The ID of the session thread whose status changed.

---

### session.updated

> Triggered when a session is successfully modified through the session update API.

**type** `string`  |  Object type

`session.updated.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`session.updated.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`session.updated.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`session.updated.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`session.updated.data.type`

Event type, fixed as `session.updated`.

**id** `string`  |  Resource ID

`session.updated.data.id`

The ID of the session that triggered the event.

---

### session.deleted

> Triggered when a session is successfully deleted through the session delete API.

**type** `string`  |  Object type

`session.deleted.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`session.deleted.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`session.deleted.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`session.deleted.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`session.deleted.data.type`

Event type, fixed as `session.deleted`.

**id** `string`  |  Resource ID

`session.deleted.data.id`

The ID of the session that triggered the event.

---

### agent.created

> Triggered when an agent is created successfully.

**type** `string`  |  Object type

`agent.created.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`agent.created.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`agent.created.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`agent.created.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`agent.created.data.type`

Event type, fixed as `agent.created`.

**id** `string`  |  Resource ID

`agent.created.data.id`

The ID of the agent that triggered the event.

---

### agent.updated

> Triggered when an agent is updated successfully.

**type** `string`  |  Object type

`agent.updated.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`agent.updated.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`agent.updated.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`agent.updated.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`agent.updated.data.type`

Event type, fixed as `agent.updated`.

**id** `string`  |  Resource ID

`agent.updated.data.id`

The ID of the agent that triggered the event.

---

### agent.deleted

> Triggered when an agent is deleted successfully.

**type** `string`  |  Object type

`agent.deleted.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`agent.deleted.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`agent.deleted.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`agent.deleted.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`agent.deleted.data.type`

Event type, fixed as `agent.deleted`.

**id** `string`  |  Resource ID

`agent.deleted.data.id`

The ID of the agent that triggered the event.

---

### environment.created

> Triggered when an environment is created successfully.

**type** `string`  |  Object type

`environment.created.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`environment.created.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`environment.created.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`environment.created.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`environment.created.data.type`

Event type, fixed as `environment.created`.

**id** `string`  |  Resource ID

`environment.created.data.id`

The ID of the environment that triggered the event.

---

### environment.updated

> Triggered when an environment is updated successfully.

**type** `string`  |  Object type

`environment.updated.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`environment.updated.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`environment.updated.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`environment.updated.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`environment.updated.data.type`

Event type, fixed as `environment.updated`.

**id** `string`  |  Resource ID

`environment.updated.data.id`

The ID of the environment that triggered the event.

---

### environment.deleted

> Triggered when an environment is deleted successfully.

**type** `string`  |  Object type

`environment.deleted.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`environment.deleted.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`environment.deleted.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`environment.deleted.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`environment.deleted.data.type`

Event type, fixed as `environment.deleted`.

**id** `string`  |  Resource ID

`environment.deleted.data.id`

The ID of the environment that triggered the event.

---

### memory_store.created

> Triggered when a memory store is created successfully.

**type** `string`  |  Object type

`memory_store.created.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`memory_store.created.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`memory_store.created.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`memory_store.created.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`memory_store.created.data.type`

Event type, fixed as `memory_store.created`.

**id** `string`  |  Resource ID

`memory_store.created.data.id`

The memory store ID that triggered the event.

---

### memory_store.deleted

> Triggered when a memory store is deleted successfully.

**type** `string`  |  Object type

`memory_store.deleted.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`memory_store.deleted.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`memory_store.deleted.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`memory_store.deleted.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`memory_store.deleted.data.type`

Event type, fixed as `memory_store.deleted`.

**id** `string`  |  Resource ID

`memory_store.deleted.data.id`

The memory store ID that triggered the event.

---

### vault.created

> Triggered when a vault is created successfully.

**type** `string`  |  Object type

`vault.created.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`vault.created.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`vault.created.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`vault.created.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`vault.created.data.type`

Event type, fixed as `vault.created`.

**id** `string`  |  Resource ID

`vault.created.data.id`

The vault ID that triggered the event.

---

### vault.deleted

> Triggered when a vault is deleted successfully.

**type** `string`  |  Object type

`vault.deleted.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`vault.deleted.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`vault.deleted.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`vault.deleted.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`vault.deleted.data.type`

Event type, fixed as `vault.deleted`.

**id** `string`  |  Resource ID

`vault.deleted.data.id`

The vault ID that triggered the event.

---

### vault_credential.created

> Triggered when a vault credential is created successfully.

**type** `string`  |  Object type

`vault_credential.created.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`vault_credential.created.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`vault_credential.created.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`vault_credential.created.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`vault_credential.created.data.type`

Event type, fixed as `vault_credential.created`.

**id** `string`  |  Resource ID

`vault_credential.created.data.id`

The credential ID that triggered the event.

**vault_id** `string`  |  Vault ID

`vault_credential.created.data.vault_id`

The vault ID that the credential belongs to. Delete events retain the ownership relationship from before deletion.

---

### vault_credential.deleted

> Triggered when a vault credential is deleted directly or when its vault is deleted.

**type** `string`  |  Object type

`vault_credential.deleted.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`vault_credential.deleted.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`vault_credential.deleted.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`vault_credential.deleted.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`vault_credential.deleted.data.type`

Event type, fixed as `vault_credential.deleted`.

**id** `string`  |  Resource ID

`vault_credential.deleted.data.id`

The credential ID that triggered the event.

**vault_id** `string`  |  Vault ID

`vault_credential.deleted.data.vault_id`

The vault ID that the credential belongs to. Delete events retain the ownership relationship from before deletion.

---

### vault_credential.refresh_failed

> Triggered when the platform fails to refresh an OAuth credential; this event is not generated if no refresh is performed.

**type** `string`  |  Object type

`vault_credential.refresh_failed.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`vault_credential.refresh_failed.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`vault_credential.refresh_failed.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`vault_credential.refresh_failed.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`vault_credential.refresh_failed.data.type`

Event type, fixed as `vault_credential.refresh_failed`.

**id** `string`  |  Resource ID

`vault_credential.refresh_failed.data.id`

The credential ID that triggered the event.

**vault_id** `string`  |  Vault ID

`vault_credential.refresh_failed.data.vault_id`

The vault ID that the credential belongs to. Delete events retain the ownership relationship from before deletion.

---

### webhook.test

> Generated when the API for sending a webhook test event is called. This event cannot be subscribed to and is delivered only once to the target endpoint.

**type** `string`  |  Object type

`webhook.test.type`

Object type, fixed as `event`.

**id** `string`  |  Event ID

`webhook.test.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header and is used for idempotent deduplication.

**created_at** `string`  |  Occurrence time

`webhook.test.created_at`

Event occurrence time in RFC 3339 format. For test events, this value is the generation time.

**data** `object`  |  Event data

`webhook.test.data`

The event type, resource ID, and associated context required by the event.

**type** `string`  |  Event type

`webhook.test.data.type`

Event type, fixed as `webhook.test`.

**id** `string`  |  Resource ID

`webhook.test.data.id`

The endpoint ID of the webhook being tested.

---
