# Get webhook event

`GET https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints/{endpoint_id}/events/{event_id}`

Query the event envelope associated with a specified webhook endpoint in the last 7 days. The response is the frozen envelope saved at delivery time. It is consistent with the callback request body and includes the delivery information for this event for the current endpoint.

The API does not reread business resources or provide per\-retry details. Events can still be queried after the endpoint is disabled; after the endpoint is deleted, associated events that have not expired can still be queried during the 7\-day tombstone retention period.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**endpoint_id** `string` `Required`  |  Endpoint ID

Webhook endpoint ID, which starts with `wep-`. You can obtain this ID from the response of the create or list endpoint API.

**event_id** `string` `Required`  |  Event ID

Webhook event ID, starting with `whe-`. The event must have been associated with the specified endpoint and must still be within the 7\-day retention period.

## Response parameters

**created_at** `string`  |  Occurrence time

Event occurrence time in RFC 3339 format.

**data** `object`  |  Event data

The event type, resource ID, and associated context required by the event. For parameter semantics for each event, see [Webhook event structure reference](webhook-events-reference.md).

**id** `string`  |  Resource ID

`data.id`

The resource ID that triggered the event. Thread events return the owning session ID; test events return the endpoint ID being tested.

**type** `string`  |  Event type

`data.type`

Webhook event type. For supported values and trigger conditions, see [Webhook event structure reference](webhook-events-reference.md).

**session_thread_id** `string`  |  Thread ID.

`data.session_thread_id`

The session thread ID whose status changed. This parameter is returned only for thread status events.

**vault_id** `string`  |  Vault ID

`data.vault_id`

The vault ID that the credential belongs to. This parameter is returned only for vault credential events.

**id** `string`  |  Event ID

Webhook event ID, starting with `whe-`. This value is the same as the `webhook-id` request header in the callback request.

**type** `string`  |  Object type

Object type, fixed as `event`.

**delivery** `object`  |  Delivery information

Delivery information for this event for the current endpoint. This parameter is returned only by the query API. This parameter is omitted if the delivery record has been cleaned up or was never created.

**status** `string`  |  Delivery status

`delivery.status`

Delivery status.`RUNNING` indicates that the delivery has not completed; `SUCCEEDED` indicates that the delivery succeeded; `FAILED` indicates that the delivery ultimately failed; `CANCELED` indicates that deliveries that have not started or are waiting to be retried were canceled because the endpoint was disabled or a subscribed event type was removed; `EXPIRED` indicates that the delivery did not complete within the 24\-hour delivery window.

**attempt_count** `integer`  |  Attempt count

`delivery.attempt_count`

Number of delivery attempts made to the callback URL.

**created_at** `string`  |  Delivery creation time

`delivery.created_at`

Time when the delivery record was created, in RFC 3339 format.

**finished_at** `string`  |  Delivery completion time

`delivery.finished_at`

Time when the delivery completed (succeeded, ultimately failed, was canceled, or expired), in RFC 3339 format. This parameter is omitted if the delivery has not completed.

**last_error_code** `string`  |  Latest failure error code

`delivery.last_error_code`

Error code of the most recent delivery failure.
