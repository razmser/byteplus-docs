# Send webhook test event

`POST https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints/{endpoint_id}/test`

Generates and asynchronously delivers a `webhook.test` test event. This API can be called when the endpoint is enabled or disabled, and test events are not filtered by the subscribed event list.

A 200 response indicates that the test event has been generated and accepted, but does not indicate that the callback URL has received the event. A test event is delivered only once. A normal delivery failure does not change the endpoint status, update business delivery statistics, or automatically enable the endpoint.

Each call generates a new test event. Retrying directly after a request times out may cause the receiver to receive test notifications with multiple different event IDs.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**endpoint_id** `string` `Required`  |  Endpoint ID

Webhook endpoint ID, which starts with `wep-`. You can obtain this ID from the response of the create or list API.

## Response parameters

**created_at** `string`  |  Generation time

Generation time of the webhook test event, in RFC 3339 format.

**data** `object`  |  Event data

Data for the webhook test event.

**id** `string`  |  Endpoint ID

`data.id`

The ID of the webhook endpoint being tested. It is the same as `endpoint_id` in the request path.

**type** `string`  |  Event type

`data.type`

Event type, fixed as `webhook.test`.

**id** `string`  |  Event ID

Webhook test event ID, starting with `whe-`. The callback body and the `webhook-id` request header use the same value.

**type** `string`  |  Object type

Object type. Fixed to `agent`.

Value is fixed to `event`.
