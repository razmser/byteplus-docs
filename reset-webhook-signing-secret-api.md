# Reset webhook signing secret

`POST https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints/{endpoint_id}/reset_secret`

Generates a new signing secret for the specified webhook endpoint and immediately invalidates the old secret. It can be called in both the enabled and disabled states.

The reset updates only `updated_at` and does not change the endpoint status, deactivation reason, consecutive failure count, or most recent successful delivery time.

The API does not provide a window during which both the old and new secrets are valid. The new secret is returned only in this successful response. If the response is lost, you must reset it again and synchronize the receiver configuration.

> **The old secret is invalidated immediately**

> After a successful reset, the platform no longer uses the old secret to generate new signatures. In\-flight requests that have already been signed and sent may still arrive with signatures generated using the old secret.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**endpoint_id** `string` `Required`  |  Endpoint ID

Webhook endpoint ID, which starts with `wep-`. You can obtain this ID from the response of the create or list API.

## Response parameters

**signing_secret** `string`  |  Signing secret

The newly generated webhook signing secret, which starts with `whsec_`. You cannot retrieve it again after the response is closed.

**updated_at** `string`  |  Update time

The time when the signing secret replacement was completed, in RFC 3339 format.
