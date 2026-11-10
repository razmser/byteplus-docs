# List webhook events

`GET https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints/{endpoint_id}/events`

Query the event envelopes associated with a specified webhook endpoint in the last 7 days. The list is sorted by event occurrence time in descending order and supports exact filtering by event ID or event type.

Event retries do not create duplicate list items. Query results come from the frozen envelopes saved during delivery. They do not reread business resources and do not include delivery status or per\-retry details.

You can still query the endpoint after it is disabled. After an endpoint is deleted, you can still query associated events that have not expired within the 7\-day tombstone retention period.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**endpoint_id** `string` `Required`  |  Endpoint ID

Webhook endpoint ID, which starts with `wep-`. You can obtain this ID from the response of the create or list endpoint API.

### Query parameters

> Passed as URL query string.

**event_id** `string`  |  Event ID filter

Filter by exact webhook event ID. Enter an event ID that starts with `whe-`; if omitted, no event ID filter is applied.

**event_type** `string`  |  Event type filter

Filter by exact event type. Supports the business events listed in the webhook event structure reference and the `webhook.test` test event; if omitted, all types are returned. Options: `session.status_run_started` / `session.status_idled` / `session.status_terminated` / `span.outcome_evaluation_end` / `session.error` / `session.thread_status_running` / `session.thread_status_idle` / `session.thread_status_terminated` / `session.updated` / `session.deleted` / `agent.created` / `agent.updated` / `agent.deleted` / `environment.created` / `environment.updated` / `environment.deleted` / `memory_store.created` / `memory_store.deleted` / `vault.created` / `vault.deleted` / `vault_credential.created` / `vault_credential.deleted` / `vault_credential.refresh_failed` / `webhook.test`.

**limit** `integer` `Default 20`  |  Events per page

Number of webhook events returned per page. If omitted, 20 events are returned by default.

**Range**: `[1, 100]`

**page** `string`  |  Next page identifier

Next page identifier. Pass the `next_page` in the response of the previous page as is; if `next_page` is omitted, the query starts from the first page.

## Response parameters

**data** `object[]`  |  Webhook event list

The list of webhook events associated with this endpoint within the retention period, sorted by event occurrence time in descending order. Each item is the complete event envelope saved during delivery; if there are no matching results, an empty array is returned.

**created_at** `string`  |  Occurrence time

`data.created_at`

Event occurrence time in RFC 3339 format.

**data** `object`  |  Event data

`data.data`

The event type, resource ID, and associated context required by the event. For parameter semantics for each event, see [Webhook event structure reference](webhook-events-reference.md).

**id** `string`  |  Resource ID

`data.data.id`

The resource ID that triggered the event. Thread events return the owning session ID; test events return the endpoint ID being tested.

**type** `string`  |  Event type

`data.data.type`

Webhook event type. For supported values and trigger conditions, see [Webhook event structure reference](webhook-events-reference.md).

**session_thread_id** `string`  |  Thread ID.

`data.data.session_thread_id`

The session thread ID whose status changed. This parameter is returned only for thread status events.

**vault_id** `string`  |  Vault ID

`data.data.vault_id`

The vault ID that the credential belongs to. This parameter is returned only for vault credential events.

**id** `string`  |  Event ID

`data.id`

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header in the callback request.

**type** `string`  |  Object type

`data.type`

Object type. Fixed to `agent`.

Value is fixed to `event`.

**next_page** `string / null`  |  Next page identifier

Next page identifier. A string is returned when there are more events; `null` is returned when the last page has been reached.
