# Create webhook endpoint

`POST https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints`

Create a webhook endpoint to subscribe to Managed Agents business events in the current account and project. If creation succeeds, 200 is returned and the endpoint is enabled by default.

You can create up to 20 endpoints in the same region, account, and project. Both enabled and disabled endpoints count toward the quota. The callback URL must be unique within the same project.

> **Save the signing secret**

> The `signing_secret` in the creation response is returned only once. Before closing the response, save the secret in secure server\-side configuration; it cannot be retrieved again through the list or detail APIs.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Body parameters

**events** `string[]` `Required`  |  Subscribed events

The list of subscribed business event types. Specify at least one event. Wildcards or resource ID filters are not supported. Options: `session.status_run_started`, `session.status_idled`, `session.status_terminated`, `span.outcome_evaluation_end`, `session.error`, `session.thread_status_running`, `session.thread_status_idle`, `session.thread_status_terminated`, `session.updated`, `session.deleted`, `agent.created`, `agent.updated`, `agent.deleted`, `environment.created`, `environment.updated`, `environment.deleted`, `memory_store.created`, `memory_store.deleted`, `vault.created`, `vault.deleted`, `vault_credential.created`, `vault_credential.deleted`, `vault_credential.refresh_failed`.

**url** `string` `Required`  |  Callback URL

The public HTTP or HTTPS address that receives webhook requests. The platform does not follow redirects.

**description** `string / null`  |  Endpoint description

A description of the webhook endpoint's purpose, up to 256 characters. If this parameter is omitted, passed as `null`, or set to an empty string, it is saved as `null`.

## Response parameters

**consecutive_failures** `integer`  |  Consecutive failures

The number of business events whose final delivery failed consecutively. This is reset to zero when a business event is delivered successfully or the endpoint is re\-enabled; multiple attempts for a single event are counted only once.

**Min**: `0`

**created_at** `string`  |  Creation time

The time when the webhook endpoint was created, in RFC 3339 format.

**description** `string / null`  |  Endpoint description

A description of the webhook endpoint's purpose. Returns `null` if not set.

**disabled_reason** `string / null`  |  Disabled reason

The current disabled reason for the webhook endpoint. Returns `null` when the endpoint is in the `ACTIVE` state.

> **Tip**

> Disabled reason values:

* > `manual`: Manually disabled by the caller.

* > `consecutive_failures`: Final delivery failed for 20 consecutive business events.

* > `redirect`: The callback URL returned 3xx, and the platform does not follow redirects.

* > `unsafe_url`: The callback URL failed the public address security check.

* > `invalid_certificate`: HTTPS certificate or hostname validation failed.

**events** `string[]`  |  Subscribed events

The list of business event types subscribed to by this endpoint. The platform delivers only the events in the list to this endpoint. Options: `session.status_run_started`, `session.status_idled`, `session.status_terminated`, `span.outcome_evaluation_end`, `session.error`, `session.thread_status_running`, `session.thread_status_idle`, `session.thread_status_terminated`, `session.updated`, `session.deleted`, `agent.created`, `agent.updated`, `agent.deleted`, `environment.created`, `environment.updated`, `environment.deleted`, `memory_store.created`, `memory_store.deleted`, `vault.created`, `vault.deleted`, `vault_credential.created`, `vault_credential.deleted`, `vault_credential.refresh_failed`.

**id** `string`  |  Endpoint ID

Webhook endpoint ID, which starts with `wep-`. Subsequent management operations use this ID to locate the endpoint.

**last_error_code** `string / null`  |  Last failure category

The error category for the most recent final business event delivery failure. Returns `null` if there has been no final failure; test events do not update this parameter.

> **Tip**

> Failure categories include:

* > `redirect`: The callback URL returned 3xx.

* > `unsafe_url`: The callback URL failed the public address security check.

* > `invalid_certificate`: HTTPS certificate or hostname validation failed.

* > `http_rejected`: A non\-retryable HTTP 4xx was received.

* > `http_retryable`: A retryable HTTP status code was received, and all attempts failed.

* > `network_error`: A network error occurred, and all attempts failed.

**last_failure_at** `string / null`  |  Last failure time

The time when final delivery of the most recent business event failed. Returns `null` if there has been no final failure; test events do not update this parameter.

**last_http_status** `integer / null`  |  Last failure status code

The HTTP status code received when final delivery of the most recent business event failed. Returns `null` if no response was received due to a network error or there has been no final failure; test events do not update this parameter.

**Range**: `[100, 599]`

**last_success_at** `string / null`  |  Last success time

The time when the most recent business event was delivered successfully. Returns `null` if no delivery has succeeded; test events do not update this parameter.

**signing_secret** `string`  |  Signing secret

The secret used to verify webhook request signatures, which starts with `whsec_`. This parameter is returned only once in the successful creation response. It cannot be retrieved again through the list or detail APIs.

**status** `string`  |  Endpoint status

The enablement status of the webhook endpoint.`ACTIVE` indicates that business events are received; `DISABLED` indicates that business event delivery is paused.

**updated_at** `string`  |  Update time

The time when the webhook endpoint configuration, signing key, or enablement status last changed, in RFC 3339 format. Changes to business delivery statistics do not update this parameter; automatic disabling updates this parameter.

**url** `string`  |  Callback URL

The public HTTP or HTTPS address to which the platform sends webhook requests. The platform does not follow redirects.
