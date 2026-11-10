# Delete webhook endpoint

`DELETE https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints/{endpoint_id}`

Delete specified webhook endpoint. A successful deletion returns 204 and no response body.

Deletion cancels deliveries that have not started or are waiting to be retried, and immediately clears the signing key. Requests that have already been sent might still arrive. A deleted endpoint cannot be restored.

A tombstone is retained for 7 days after deletion. During the tombstone retention period, repeated delete requests still return 204, and you can still query associated events that have not expired; after the tombstone expires, deleting the endpoint again returns 404.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**endpoint_id** `string` `Required`  |  Endpoint ID

Webhook endpoint ID, which starts with `wep-`. You can obtain this ID from the response of the create or list API.

## Response parameters

On success, the HTTP status is `204 No Content`.

This API does not return a response body.
