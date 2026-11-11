# Receive webhook events

If you need to integrate agent run results into audit, alerting, or internal orchestration systems, you do not need to continuously poll the status of each resource. After you configure a Managed Agents webhook, ModelArk proactively calls back to your server when resources such as agents, environments, sessions, memory stores, and vaults change. This guide starts with creating an endpoint and walks through the receiving service, signature verification and deduplication, test events, failure recovery, secret rotation, and resource cleanup.

## Choose a receiving method

Webhooks, SSE, and polling solve different problems. Use webhooks when you need to listen for asynchronous changes across multiple resources. Use SSE when you need to display messages, tool calls, and incremental output for a single session in real time. Use the corresponding query API when you need to proactively check the current status of a resource or look up historical session events.

|Dimension |Webhook |SSE |Polling |
|---|---|---|---|
|Scope |Receive lifecycle and status changes across resources |Receive fine\-grained events for a specified session or thread in real time |Proactively query the current status or history of a resource |
|Connection method |The platform sends an HTTP POST request to a public callback URL |The client maintains a persistent connection |The client sends query requests as needed |
|Success criteria |The receiver returns HTTP 2xx |The client continuously reads the event stream |The query API returns a successful response |
|Reliability handling |Verify signatures, deduplicate, retry, and recover from deactivation |Reconnect after disconnection, and perform lookups based on API capabilities |Control query frequency to avoid high\-frequency polling |
|Recommended scenarios |Auditing, alerting, and asynchronous task orchestration |Real\-time conversations and run progress display |Reconciliation, recovery, and low\-frequency status checks |

Webhooks support subscribing to the business events in the following table. For details about the event body structure, see [Webhook event schema reference](webhook-events-reference.md).

|Resource domain |Event type |
|---|---|
|Sessions and threads |`session.status_run_started`, `session.status_idled`, `session.status_terminated`, `span.outcome_evaluation_end`, `session.error`, `session.thread_status_running`, `session.thread_status_idle`, `session.thread_status_terminated`, `session.updated`, `session.deleted` |
|Agent |`agent.created`, `agent.updated`, `agent.deleted` |
|Environment |`environment.created`, `environment.updated`, `environment.deleted` |
|Memory Store |`memory_store.created`, `memory_store.deleted` |
|Vaults and credentials |`vault.created`, `vault.deleted`, `vault_credential.created`, `vault_credential.deleted`, `vault_credential.refresh_failed` |

> **Note**

> Webhook event queries return frozen event envelopes associated with the endpoint from the last 7 days, along with delivery information for the event for the current endpoint (delivery status, number of attempts, latest failure error code, and delivery creation and completion times). You can use this information to determine whether the event was delivered successfully. The query does not provide per\-retry details or event replay. The receiver should still persist events and processing results itself. If you find that notifications are missing, call the query API for the corresponding resource to check its current status. The session events query API is not a webhook history API.

### Determine session run results

`session.status_idled` only indicates that the session has entered the idle state. It does not indicate that the current turn ended successfully. Webhook events do not include a session event ID or turn ID, so you cannot automatically advance your business process only by querying the latest `session.status_idle`. After receiving this notification, determine the result in the following order:

1. Read the session ID from `data.id` in the webhook event, and deduplicate by the outer webhook `id`.

2. Find the boundary of the current turn recorded on the business side. We recommend saving the event ID returned by the send event API after sending `user.message`, `user.tool_result`, or `user.tool_confirmation` for the current turn. If the receiver has already been continuously consuming session events, you can also save the `processed_at` value of the last processed session event as the watermark.

3. **Query historical events**: Use the session ID to call [List session events](list-events-api.md) and query historical events after the current turn boundary. You can use `created_at[gt]` to narrow the time window and scan in ascending order by `processed_at`. Only after you confirm that the candidate `session.status_idle` is after the current turn boundary should you use it as the status event corresponding to this notification.

4. Read the `stop_reason.type` of the corresponding `session.status_idle` event:

   * **`end_turn`**: The current turn ended successfully, and you can process the business result.

   * **`requires_action`**: The current turn is waiting for tool confirmation or tool results. Continue processing based on `stop_reason.event_ids`.

   * **`retries_exhausted`**: The current turn has exhausted its retry attempts. Do not advance subsequent processes as if the result were successful.

A webhook may arrive before historical session events finish synchronizing, or it may be delivered late. If the query results contain only `session.status_idle` events before the current turn boundary, or you cannot confirm the relationship between a candidate event and the current business task, wait and query again. Do not advance the business process based only on the latest `end_turn`. If you cannot establish the current turn boundary, `session.status_idled` is suitable only as a status check signal. It is not suitable for driving processes that must correspond exactly to a turn, such as automatic publishing, billing, or external notifications.

The outer webhook `id` starts with `whe-` and identifies only the webhook event envelope. Session historical events use independent event IDs. Do not pass a `whe-` ID to the session events query API. For details about session event parameters, see [Session event schema reference](sessions-events-reference-v2.md).

## Prepare the receiver

Before you begin, complete the following preparations:

* Create an API key on ModelArk and configure it as `ARK_API_KEY`.

* Prepare a publicly accessible HTTP or HTTPS callback URL. The platform does not follow 3xx redirects.

* Install Python 3.10 or later.

* Determine the event types that you need to subscribe to. When creating an endpoint, you must specify at least one event type. You can specify all supported event types.

* Prepare a secret management service and persistent deduplication storage for production.

This guide uses `/webhooks/managed-agents` as the callback path. The Python example depends only on the standard library and uses `HTTPServer` to listen on `0.0.0.0:8000`.

> **Note**

> The standard library `HTTPServer` example demonstrates protocol handling. It does not include TLS, access log desensitization, rate limiting, multi\-process concurrency, or persistent storage. In production, run the receiving service behind a reverse proxy or API gateway, and configure the callback URL to use HTTPS.

## Create and test an endpoint

### Step 1: Create an endpoint

Configure `ARK_API_KEY`, replace `url` in the example with the public callback URL, and then create the webhook endpoint. After the endpoint is created successfully, it is in the `ACTIVE` state by default.

```Bash
curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://example.com/webhooks/managed-agents",
    "description": "Receive session idle and termination notifications",
    "events": [
      "session.status_idled",
      "session.status_terminated"
    ]
  }'
```

After creation succeeds, the `id` in the response starts with `wep-`, and `signing_secret` starts with `whsec_`.`signing_secret` is returned only once in this creation response.

Immediately save both values to the server configuration:

```Bash
export WEBHOOK_ENDPOINT_ID="<WEBHOOK_ENDPOINT_ID>"
export WEBHOOK_SIGNING_SECRET="<SIGNING_SECRET>"
```

> **Warning**

> Before closing the creation response, write `signing_secret` to the secret management service. The list and detail APIs will not return this secret again. In production, do not commit secrets to code repositories, images, logs, or shell history.

> **Note**

> You can create up to 20 endpoints in the same region, account, and project. Both enabled and disabled endpoints count toward the quota. The callback URL must be unique within the same project. The management API does not support `Idempotency-Key`. After a create request times out, query the endpoint list by URL first. Do not create it again directly.

### Step 2: Start the receiver

The Python example reads the raw request body first, then parses JSON after completing the time window and signature verification. After the signature verification succeeds, the example deduplicates events in memory by event ID and returns HTTP 204.

```Python
import base64
import binascii
import hashlib
import hmac
import json
import os
import time
from http.server import BaseHTTPRequestHandler, HTTPServer


WEBHOOK_PATH = "/webhooks/managed-agents"
MAX_TIMESTAMP_SKEW_SECONDS = 5 * 60
PROCESSED_EVENT_IDS: set[str] = set()


def decode_signing_secret(value: str) -> bytes:
    if not value.startswith("whsec_"):
        raise ValueError("WEBHOOK_SIGNING_SECRET must start with whsec_")

    encoded_secret = value.removeprefix("whsec_")
    encoded_secret += "=" * (-len(encoded_secret) % 4)
    try:
        secret = base64.b64decode(encoded_secret, validate=True)
    except (binascii.Error, ValueError) as exc:
        raise ValueError("WEBHOOK_SIGNING_SECRET contains invalid Base64 data") from exc
    if len(secret) != 32:
        raise ValueError("WEBHOOK_SIGNING_SECRET must decode to 32 bytes")
    return secret


def verify_signature(
    secret: bytes,
    webhook_id: str,
    timestamp: str,
    raw_body: bytes,
    signature_header: str,
) -> bool:
    signed_content = (
        webhook_id.encode("utf-8")
        + b"."
        + timestamp.encode("utf-8")
        + b"."
        + raw_body
    )
    digest = hmac.new(secret, signed_content, hashlib.sha256).digest()
    expected_signature = base64.b64encode(digest).decode("ascii")

    version, separator, signature = signature_header.partition(",")
    return bool(
        separator
        and version == "v1"
        and hmac.compare_digest(signature, expected_signature)
    )


class WebhookHandler(BaseHTTPRequestHandler):
    signing_secret: bytes

    def send_status(self, status_code: int, message: str | None = None) -> None:
        response_body = b""
        if message is not None:
            response_body = json.dumps({"error": message}).encode("utf-8")

        self.send_response(status_code)
        if response_body:
            self.send_header("Content-Type", "application/json")
        self.send_header("Content-Length", str(len(response_body)))
        self.end_headers()
        if response_body:
            self.wfile.write(response_body)

    def do_POST(self) -> None:
        if self.path != WEBHOOK_PATH:
            self.send_status(404, "Webhook path not found")
            return

        content_length = self.headers.get("Content-Length")
        try:
            body_length = int(content_length or "")
        except ValueError:
            self.send_status(400, "Invalid Content-Length")
            return
        if body_length < 0:
            self.send_status(400, "Invalid Content-Length")
            return

        raw_body = self.rfile.read(body_length)
        webhook_id = self.headers.get("webhook-id", "")
        timestamp = self.headers.get("webhook-timestamp", "")
        signature = self.headers.get("webhook-signature", "")
        if not webhook_id or not timestamp or not signature:
            self.send_status(400, "Missing webhook signature headers")
            return

        try:
            timestamp_seconds = int(timestamp)
        except ValueError:
            self.send_status(400, "Invalid webhook timestamp")
            return

        if abs(time.time() - timestamp_seconds) > MAX_TIMESTAMP_SKEW_SECONDS:
            self.send_status(401, "Webhook timestamp is outside the allowed window")
            return

        if not verify_signature(
            self.signing_secret,
            webhook_id,
            timestamp,
            raw_body,
            signature,
        ):
            self.send_status(401, "Invalid webhook signature")
            return

        try:
            event = json.loads(raw_body)
        except (json.JSONDecodeError, UnicodeDecodeError):
            self.send_status(400, "Invalid JSON body")
            return

        if not isinstance(event, dict):
            self.send_status(400, "Webhook body must be a JSON object")
            return

        event_id = event.get("id")
        if (
            not isinstance(event_id, str)
            or not event_id.startswith("whe-")
            or event_id != webhook_id
        ):
            self.send_status(400, "Webhook event ID does not match the request header")
            return

        if event_id in PROCESSED_EVENT_IDS:
            self.send_status(204)
            return

        data = event.get("data")
        event_type = data.get("type") if isinstance(data, dict) else None
        print(f"Received event {event_id}: {event_type}", flush=True)
        PROCESSED_EVENT_IDS.add(event_id)
        self.send_status(204)


def main() -> None:
    secret_value = os.environ.get("WEBHOOK_SIGNING_SECRET")
    if not secret_value:
        raise SystemExit("Set WEBHOOK_SIGNING_SECRET before starting the server")

    try:
        WebhookHandler.signing_secret = decode_signing_secret(secret_value)
    except ValueError as exc:
        raise SystemExit(str(exc)) from exc

    server = HTTPServer(("0.0.0.0", 8000), WebhookHandler)
    print(f"Listening on http://0.0.0.0:8000{WEBHOOK_PATH}", flush=True)
    try:
        server.serve_forever()
    except KeyboardInterrupt:
        pass
    finally:
        server.server_close()


if __name__ == "__main__":
    main()
```

Configure the signing secret from the create response, and then start the service:

```Bash
export WEBHOOK_SIGNING_SECRET="<SIGNING_SECRET>"
python code/managed-agents/managed-agents-receive-webhook-python.py
```

After startup succeeds, the terminal displays the following address:

```text
Listening on http://0.0.0.0:8000/webhooks/managed-agents
```

Deploy this service to the public callback URL you entered when creating the endpoint. The callback path must remain consistent.

### Step 3: Send a test event

Send a `webhook.test` event to verify the public route, request headers, signature verification, and response status.

```Bash
curl -sS --fail-with-body -X POST \
  "https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints/$WEBHOOK_ENDPOINT_ID/test" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

An HTTP 200 response from the test API indicates that the platform has generated and accepted the test event. It does not indicate that the receiver has received the event. A test event is delivered once through an asynchronous task. It is not affected by the subscription list or endpoint enabled/disabled status, and it does not trigger retries. Ordinary receiver failures do not change the endpoint status.

Each call generates a new test event. Retrying directly after a request times out may cause the receiver to receive test notifications with multiple different event IDs.

After the receiver receives the event, it outputs:

```Plain Text
Received event whe-20261012080500-f6g7h: webhook.test
```

Confirm that the `whe-` ID in the logs is the same as the `id` in the test API response. The Python example returns HTTP 204 after successful processing.

## Verify signatures and process events

Each callback includes the following signature request headers:

|Request headers |Note |
|---|---|
|`Content-Type` |Fixed as `application/json`; the body uses UTF\-8 encoding. |
|`User-Agent` |Fixed as `ArkManagedAgents-Webhook/1.0` |
|`webhook-id` |Webhook event ID, starts with `whe-`, and is the same as the top\-level `id` in the body. |
|`webhook-timestamp` |Unix timestamp in seconds used to generate the signature |
|`webhook-signature` |Signature in `v1,<BASE64_SIGNATURE>` format |

The signature content is concatenated in the following order:

```text
signed_content = webhook-id + "." + webhook-timestamp + "." + raw_body
signature = Base64(HMAC-SHA256(Base64Decode(signing_secret after removing the whsec_ prefix), signed_content))
```

Here, `webhook-id` and `webhook-timestamp` are encoded in UTF\-8.`raw_body` must be the original bytes received over the network.

Process the request in the following order:

1. Read `webhook-id`, `webhook-timestamp`, `webhook-signature`, and the raw request body.

2. Parse `webhook-timestamp` as Unix seconds, and reject requests that differ from the current server time by more than 5 minutes.

3. Remove the `whsec_` prefix from `signing_secret`, and then Base64\-decode the remaining content. The decoded result must be 32 bytes.

4. Use `webhook-id.timestamp.raw_body` to calculate the HMAC\-SHA256 and Base64 signature.

5. Verify that the signature version is `v1`, and use a constant\-time comparison function such as `hmac.compare_digest` to compare the Base64 signatures.

6. After the signature verification passes, parse JSON and confirm that the top\-level `id` in the body is the same as `webhook-id`.

7. Deduplicate by the top\-level `id`. When the same `whe-` ID is received repeatedly, do not repeat business operations that have side effects.

8. After you reliably save the event or business processing result, return HTTP 2xx.

> **Note**

> Do not parse JSON first and then reserialize the body for signature verification. Changes in spaces, parameter order, or escaping will change the byte sequence, causing signature verification to fail for valid requests.

The Python example uses an in\-memory set for deduplication, and records are lost after the process restarts. In production, enforce a unique constraint on event IDs in a database or cache, and retain deduplication records for at least the business traceability period.

### Delivery and retries

Business events do not guarantee strict ordering or exactly\-once delivery. When the same event is retried, `webhook-id`, `created_at`, and the raw body remain unchanged. The platform regenerates `webhook-timestamp` and `webhook-signature` for each attempt.

|Rules |Behavior |
|---|---|
|Successful response |After the receiver returns any HTTP 2xx, this delivery succeeds; the platform ignores the response body |
|Single\-attempt timeout |The total time for DNS, connection, TLS, sending, and reading is at most 5 seconds. |
|Maximum attempts |The same business event is delivered at most 3 times. |
|Retry interval |Wait 5 seconds after the first failure and 30 seconds after the second failure. |
|Deadline |The platform stops initiating delivery 24 hours after the event is generated. |
|Retryable failures |HTTP 408, 425, 429, and 5xx, and DNS, connection, or read/write errors |
|Non\-retryable failures |Other 4xx, 3xx, URL security check failures, and HTTPS certificate verification failures |

Return 2xx only after the event has been reliably written to storage or the business result has been persisted. If asynchronous processing is required, write the event to an internal queue first, and then return 204. Returning 3xx does not cause the platform to follow redirects.

## Query event records

Use the event list API to check events associated with the specified endpoint in the last 7 days. The list is sorted in descending order by event time and supports exact filtering by `event_id` or `event_type`:

```Bash
#!/usr/bin/env bash
curl -sS --fail-with-body \
  "https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints/$WEBHOOK_ENDPOINT_ID/events? limit=20&event_type=session.status_idled" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

`data` in the response is an array of frozen event envelopes. When there are more results, `next_page` returns the identifier for the next page; pass this value as\-is to `page` to query the next page. The last page returns `next_page: null`.

To read a single event, set the event ID from the list or receiver logs:

```Bash
export WEBHOOK_EVENT_ID="<WEBHOOK_EVENT_ID>"
```

Then query the event details:

```Bash
#!/usr/bin/env bash
curl -sS --fail-with-body \
  "https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints/$WEBHOOK_ENDPOINT_ID/events/$WEBHOOK_EVENT_ID" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

Event details include the original callback request body plus delivery information for the current endpoint (delivery status, number of attempts, latest failure error code, and delivery creation and completion times), which you can use to determine whether the event was delivered successfully. The API does not re\-read business resources and does not support replaying events. Events can still be queried after the endpoint is disabled; after the endpoint is deleted, associated events that have not expired can still be queried during the 7\-day tombstone retention period.

## Maintaining endpoints

### Pause and update

When you need to migrate the callback URL or perform maintenance, disable the endpoint first:

```Bash
curl -sS --fail-with-body -X POST \
  "https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints/$WEBHOOK_ENDPOINT_ID/disable" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

Disabling cancels deliveries that have not started and those waiting for retry. Requests that have already been sent may still arrive, and events generated while the endpoint is disabled are not replayed after it is re\-enabled.

Update the callback URL, description, or subscription list. The callback URL can be modified only when the endpoint is disabled:

```Bash
curl -sS --fail-with-body -X PUT \
  "https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints/$WEBHOOK_ENDPOINT_ID" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "description": "Receive session status change notifications",
    "events": [
      "session.status_idled",
      "session.status_terminated"
    ]
  }'
```

Updating the subscription list replaces the original list entirely. Removing event types cancels corresponding deliveries that have not started or are waiting for retry; adding event types does not backfill historical events. After the update, send a test event before enabling the endpoint:

```Bash
curl -sS --fail-with-body -X POST \
  "https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints/$WEBHOOK_ENDPOINT_ID/enable" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

After it is enabled successfully, the platform clears the disabled reason and consecutive failure count, but does not backfill business events generated while it was disabled. The enable API does not send a probe request to the callback URL first, so you should send a test event and confirm that it is received successfully before enabling the endpoint.

### Recover from automatic disabling

If a business event still does not succeed after all attempts, it is counted as only one final delivery failure. When 20 consecutive business events fail final delivery, the platform automatically disables the endpoint and sets `disabled_reason` to `consecutive_failures`.

The following issues also cause the endpoint to be automatically disabled:

* If the callback URL returns 3xx, `disabled_reason` is `redirect`.

* If the callback URL fails the public address security check, `disabled_reason` is `unsafe_url`.

* If HTTPS certificate or hostname verification fails, `disabled_reason` is `invalid_certificate`.

During recovery, first query the endpoint details and check `disabled_reason`. After you fix the callback service, perform the following steps:

1. Update the callback URL or subscription list while the endpoint is disabled.

2. Send a test event and confirm that the receiver returns 2xx.

3. Call the enable API to resume business event delivery.

4. Use the resource query API to check state changes during the disabled period.

### Reset the signing secret

Reset the signing secret if you suspect a secret leak or need to rotate it periodically. The platform does not provide an overlap window where the old and new secrets are both valid. After the reset succeeds, subsequent requests are immediately signed with the new secret. Choose one of the following methods depending on whether your business allows a brief pause in notifications.

#### Rotate during a maintenance window

The Python receiver provided in this guide reads only one `WEBHOOK_SIGNING_SECRET` at a time, so it is suitable for rotation during a maintenance window:

1. Disable webhook endpoint. Disabling cancels deliveries that have not started and those waiting for retry, and events generated while the endpoint is disabled are not replayed after it is re\-enabled.

2. **Reset the signing secret**:

```Bash
curl -sS --fail-with-body -X POST \
  "https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints/$WEBHOOK_ENDPOINT_ID/reset_secret" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

3. Save the new secret from the response, update `WEBHOOK_SIGNING_SECRET` on the receiver, and then restart the receiving service.

4. Send a test event and confirm that signature verification succeeds. Test events can also be delivered while the endpoint is disabled.

5. Enable the endpoint to resume business event delivery.

6. Use the webhook event query API and the corresponding resource query API to verify status changes during the maintenance window.

The new secret is returned only in this successful response. Before you close the response, write the new secret to your secret management service.

#### Rotate while continuously receiving

If you need to continuously receive business notifications, do not directly follow the sequence of resetting the secret, updating environment variables, and restarting. First deploy a receiver that supports controlled rotation, and then follow these steps:

1. **Enter rotation mode**: Keep using the old secret on the receiver for signature verification. Only during this controlled window, return HTTP 503 for requests whose signatures cannot be verified so that business events enter the platform retry process.

2. **Reset and save the new secret**: Call the reset secret API and save the new secret.

3. **Load both secrets**: Atomically load the new secret on the receiver while retaining the old secret. During the transition period, verify signatures with the old and new secrets in sequence, and continue to deduplicate by event ID.

4. **Send a test event**: Confirm that signature verification with the new secret succeeds. Business events are delivered up to 3 times. Load the new secret before the retry attempts are exhausted.

5. **Remove the old secret**: Keep the old secret for at least the signature time window allowed by the receiver. The example in this guide uses 5 minutes. Then remove the old secret and exit rotation mode.

> **Warning**

> The platform does not sign with both the new and old secrets at the same time. If the receiver has not loaded the new secret, requests with the new signature will fail verification with the old secret. After the receiver removes the old secret, old signed requests that have already been sent will also fail verification. Return 503 for requests whose signatures temporarily cannot be verified only after explicitly entering controlled rotation mode. During normal operation, you should still return 401 to prevent attack requests from triggering platform retries.

## Clean up resources

After confirming that you no longer need event notifications, delete the endpoint by using the exact `WEBHOOK_ENDPOINT_ID`:

```Bash
curl -sS --fail-with-body -X DELETE \
  "https://ark.ap-southeast.bytepluses.com/api/v3/webhook_endpoints/$WEBHOOK_ENDPOINT_ID" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

A successful deletion returns HTTP 204. Deletion cancels deliveries that have not started or are waiting to be retried, and immediately clears the signing key saved on the server. Requests that have already been sent might still arrive. Repeated calls to the delete API within 7 days after deletion still return 204.

After deleting the endpoint, remove the secret and local environment variables from the receiving service:

```Bash
unset WEBHOOK_SIGNING_SECRET
unset WEBHOOK_ENDPOINT_ID
unset WEBHOOK_EVENT_ID
```

> **Warning**

> A deleted webhook endpoint cannot be restored. Delete only the endpoint created in this procedure for which you recorded the exact ID. Do not bulk delete endpoints by fuzzy matching on name or callback URL.
