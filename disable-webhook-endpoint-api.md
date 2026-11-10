# Disable webhook endpoint

`POST https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints/{endpoint_id}/disable`

Disable the specified webhook endpoint to pause subsequent business event delivery. Disabling the endpoint cancels deliveries that have not started and deliveries waiting to be retried; requests that have already been sent may still arrive.

When the endpoint changes from `ACTIVE` to `DISABLED`, `disabled_reason` is set to `manual`. Events that occur while the endpoint is disabled are not redelivered; later disable operation attempts preserve the original disabled reason and `updated_at`.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**endpoint_id** `string` `Required`  |  Endpoint ID

Webhook endpoint ID, which starts with `wep-`. You can obtain this ID from the response of the create or list API.

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

**status** `string`  |  Endpoint status

The enablement status of the webhook endpoint.`ACTIVE` indicates that business events are received; `DISABLED` indicates that business event delivery is paused.

**updated_at** `string`  |  Update time

The time when the webhook endpoint configuration, signing key, or enablement status last changed, in RFC 3339 format. Changes to business delivery statistics do not update this parameter; automatic disabling updates this parameter.

**url** `string`  |  Callback URL

The public HTTP or HTTPS address to which the platform sends webhook requests. The platform does not follow redirects.
