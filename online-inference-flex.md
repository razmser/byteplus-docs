# Online inference (Flex)

**Online inference (Flex)**  uses an independent Flex TPM quota and provides best\-effort inference at a lower price for latency\-insensitive workloads. To use lower\-priority compute resources for inference, set `service_tier=flex` in the request.

> **tip**

* > Online inference (Flex) does not guarantee SLA metrics, including but not limited to TTFT, TPOT, success rate, or completion time. It is suitable for retryable batch or background workloads that can tolerate waiting.

## Advantages

* **Lower price**: Input and output token prices for Online inference (Flex) are approximately 50% of the prices for online inference (standard). Cached input token prices are the same as those for online inference (standard).

* **Independent quota and higher off\-peak limits**: Online inference (Flex) has an independent TPM quota for each account\-model pair. You can request a higher quota for off\-peak hours.

* **No migration overhead**: Online inference (Flex) uses the same Chat, Responses, and Messages APIs and the same inference endpoints as the `default` tier. You only need to add `service_tier=flex` to the request. You do not need to create another endpoint or maintain a separate invocation workflow.

* **Complements Batch Jobs**: Online inference (Flex) supports high\-volume processing through synchronous APIs when workloads cannot be converted to task\-based Batch Jobs, such as when client\-side data streams cannot be uploaded to TOS.

## Use cases

* **Background agents and non\-critical online workflows**: Latency\-insensitive workloads that can tolerate minute\-level queues and occasional timeouts during peak periods.

* **Large\-scale processing and evaluation**: Retryable workloads such as model evaluation, data augmentation, offline labeling, and data feedback.

* **Off\-peak processing**: Cost\-sensitive batch workloads that can use higher rate limits requested for off\-peak hours.

> **tip**

> Models released after Dola Seed 2.1 no longer support [Batch Chat](batch-inference.md#075b8a07) (`ep-bi-*`). For batch traffic that previously submitted requests individually through Batch Chat, migrate to an online inference endpoint and specify `service_tier=flex` in each request.

## Supported scope

### Supported models

The supported model versions are listed below. Actual availability is subject to the ModelArk console and API responses.

* `deepseek-v4-pro-ga-260813`

* `deepseek-v4-flash-ga-260731`

> **tip**

> Fine\-tuned models and branched models are not supported.

### Supported APIs

* [Chat API](chat-api.md)

* [Responses API](create-model-responses-api.md)

* [Messages API](messages-api.md)

### Supported inference endpoints

* **Preset** online inference endpoints

* **Custom** online inference endpoints

> **tip**

> Batch inference endpoints (`ep-bi-*`) are not supported. Setting `service_tier=flex` in a batch inference request does not take effect.

## Use Online inference (Flex)

### Prerequisites

Activate the service and obtain an API key:

* Activate a model: On the [Model activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement) page, select the target model and activate the model service in the **Actions** column.

* Create or obtain an API key on the [API key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Configure the API key as the `ARK_API_KEY` environment variable. For details, see [Environment variable configuration guide](environment-variable-configuration-guide.md).

Use either of the following methods to enable Online inference (Flex).

### Method 1: Specify the service tier in an API request

Both preset and custom inference endpoints support this method. Use it to select a service tier for each request sent to the same inference endpoint.

* For a preset inference endpoint, specify the model ID.

* For a custom inference endpoint, [create an inference endpoint](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint/create) and obtain its endpoint ID.

**Chat API**

Set `service_tier` to `flex`.

```Python
import os
from arkruntime import Ark

client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    )
completion = client.chat.completions.create(
    # For a custom inference endpoint, replace this value with its endpoint ID.
    model="deepseek-v4-pro-ga-260813",
    messages=[
        {"role": "user", "content": "Hello"},
    ],
    service_tier="flex",  # Default is auto; set to flex explicitly.
)
print(completion.choices[0].message)
```

**Responses API**

Set `service_tier` to `flex`.

```Python
import os
from arkruntime import Ark

client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    )

response = client.responses.create(
    # For a custom inference endpoint, replace this value with its endpoint ID.
    model="deepseek-v4-pro-ga-260813",
    input="hello",
    service_tier="flex",  # Default is auto; set to flex explicitly.
)
print(response)
```

**Messages API**

Set `service_tier` to `flex`.

```Python
import os
import requests

response = requests.post(
    "https://ark.ap-southeast.bytepluses.com/api/compatible/v1/messages",
    headers={
        "Authorization": f"Bearer {os.environ['ARK_API_KEY']}",
        "Content-Type": "application/json",
    },
    json={
        # For a custom inference endpoint, replace this value with its endpoint ID.
        "model": "deepseek-v4-pro-ga-260813",
        "max_tokens": 1024,
        "messages": [
            {"role": "user", "content": "Hello"},
        ],
        "service_tier": "flex",
    },
    timeout=1800,
)
response.raise_for_status()
print(response.json())
```

### Method 2: Configure the default service tier for an inference endpoint

Only custom inference endpoints support this method. Use it when an entire application should use Online inference (Flex) by default without modifying its invocation code.

1. [Create an inference endpoint](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint/create). Under **Default parameter**, set `service_tier` to `flex`.

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/resources/image-service-tier-flex.png)

2. Save the configuration and invoke the model with the endpoint ID. Requests that do not explicitly specify `service_tier` use Online inference (Flex) by default.

> **warning**

> The `service_tier` value explicitly specified in a request takes precedence over the default service tier configured for the inference endpoint.

## Billing

### Token pricing

Input and output token prices for Online inference (Flex) are approximately 50% of the prices for the `default` tier. For model\-specific prices, see [Pricing](model-pricing.md#flex).

### Token counting

* TPM (Tokens Per Minute) is the rate\-limit unit. Input and output tokens are not counted separately.

* For differences in unit prices by input\-length range, see the token pricing for the corresponding model.

* The response reports the service tier that processed the request in `response_service_tier`. Billing is based on this reported tier.

## Rate limits

### Rate\-limit scope

* TPM rate limits for Online inference (Flex) and online inference (standard) are independent. Using Online inference (Flex) does not consume the TPM quota for the standard tier.

* Inference endpoints share the Online inference (Flex) rate limit. Inference endpoints for the same model under the same primary account share the model's Flex TPM limit. You can also configure a Flex TPM limit for an individual endpoint on the endpoint details page. No endpoint\-specific limit is configured by default, so each endpoint can consume the entire shared quota.

### Rate\-limit values

To view a model's default and off\-peak Online inference (Flex) quotas, go to [Model activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement) \> **Model** \> **Model\-level rate limits**.

* **Default quota**: Online inference (Flex) has an independent base TPM quota for each account\-model pair. This quota applies 24 hours a day, seven days a week.

* **Off\-peak quota**: If you need a higher quota during off\-peak hours, such as 24:00\-08:00, submit a [Ticket](https://console.byteplus.com/workorder/create?step=2&SubProductID=P00001514).

### Queueing and over\-limit behavior

* Requests within the TPM quota are scheduled based on available compute resources. The server waits for resources for up to 20 minutes. If the request times out, an error is returned and no fee is charged.

* The Flex TPM quota is an admission limit, not guaranteed capacity. Requests that exceed the quota are rate limited and return HTTP status code 429.

## Service availability and performance

* Online inference (Flex) is a best\-effort service. It does not guarantee SLA metrics, including but not limited to TTFT, TPOT, success rate, or completion time.

* The platform schedules requests based on available compute resources. Service latency and availability vary with resource load. Queue times can increase during peak periods, while availability is typically higher during off\-peak periods.

* When traffic in the `default` tier consumes available resources, admission priority for Online inference (Flex) requests decreases. In extreme cases, an in\-progress request might be interrupted. Implement retries based on the returned error code.

* The platform does not automatically switch a request to the `default` tier when the request exceeds its quota, the service is overloaded, or the model does not support `flex`. To switch tiers, explicitly change `service_tier` before retrying.

## Best practices

### Use streaming output

Use streaming output to receive generated content incrementally and detect connection status promptly.

### Configure timeouts

The server waits for resources for up to 20 minutes. `X-Ark-Max-Wait-Timeout-Ms` specifies how long a request can wait for server resources, in milliseconds. The total timeout configured for clients and intermediate gateways must cover server\-side waiting, model generation, and network transmission.

|Configuration |Purpose |Recommendation |
|---|---|---|
|`X-Ark-Max-Wait-Timeout-Ms` |Maximum time that a request can wait for server resources, in milliseconds |Set it based on the application's time limit. The value cannot exceed 20 minutes. |
|SDK or HTTP client timeout |Total time from sending a request to receiving the complete response |Set it higher than the sum of the server\-side wait time, expected maximum generation time, and network buffer. The recommended default is 30 minutes. |

For example, if the application can wait for up to 10 minutes and the expected maximum generation time is 3 minutes, set `X-Ark-Max-Wait-Timeout-Ms` to `600000` and the total timeout for clients and intermediate gateways to at least 14\-15 minutes. If the application can wait for only 2 minutes, set this header to `120000` instead of using the platform's upper limit value.

### Retry by error type

* For temporary capacity errors such as `ServerOverloaded`, use exponential backoff with random jitter. Limit both the maximum number of retries and the total retry duration.

* For `TooManyRequests.ModelAccountFlexTpmRateLimitExceeded` or `TooManyRequests.RateLimitExceeded.EndpointFlexTPMExceeded`, do not retry immediately. Reduce concurrency, wait for the quota window to reset, or request a higher quota.

* `InvalidParameter.UnsupportedParameter` indicates an invalid parameter or unsupported model capability and must not be retried. Check the request parameters and verify that the model version supports Online inference (Flex).

### Configure a client fallback strategy

For a small number of requests that must complete, remove `service_tier` or set it to `auto` before resubmitting, based on cost and latency requirements. The platform does not automatically switch a Flex request to the `default` tier.

## Monitoring and alerts

To view inference endpoint metrics, go to [Online inference](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint), select an endpoint, and open the **Monitoring** tab.

The dashboard displays metrics separately for the `default` and `flex` tiers. Monitor the following metrics, subject to the actual fields available in the console:

* TPM and RPM usage and error rates for Online inference (Flex)

* TTFT, TPOT, and end\-to\-end latency percentiles for Online inference (Flex)

## Limitations

Online inference (Flex) does not support:

* Fine\-tuned models and branched models

* Explicit caching

* Batch inference endpoints (`ep-bi-*`)

* Data Collaboration Rewards Campaign data collection

* Coding Plan

## Error codes

For details, see [Error codes](error-codes.md).

## FAQs

### How do I choose between Online inference (Flex) and Batch Jobs?

* **Online inference (Flex)** : Uses synchronous online APIs and is suitable for lower\-priority workloads whose requests are generated and submitted individually and cannot easily be converted to file\-based tasks. The client remains responsible for connections, concurrency, and retries.

* **Batch Jobs**: Use asynchronous managed tasks and are suitable for large\-scale processing that can tolerate longer completion times or requires task status tracking, result files, and failure recovery.

Use Online inference (Flex) to retain the existing online invocation method. Use Batch Jobs when the platform should manage and complete an entire batch.

### Does the Flex TPM quota guarantee the same throughput?

No. The Flex TPM quota is the maximum request rate allowed for an account. It does not mean that the platform reserves the same amount of compute capacity. Actual throughput depends on current Flex capacity, request length, and client concurrency.

### Does the service automatically switch to the `default` tier when the TPM quota is exceeded or capacity is unavailable?

No. The platform does not automatically switch service tiers because doing so could charge a request at a higher price without the user's knowledge. If the request must complete, explicitly retry it from the client based on your cost and latency requirements.

### Can the same inference endpoint use both online inference (standard) and Online inference (Flex)?

Yes. A `service_tier` value explicitly specified in a request takes precedence over the inference endpoint's default service tier. If the request omits `service_tier`, the endpoint's configured default applies. For multi\-turn calls that do not depend on server\-side state, each request can independently select a service tier. When you use `previous_response_id` to chain context through the Responses API, follow the context management requirements for the Responses API.

### Is Online inference (Flex) available only during off\-peak hours, and how does the off\-peak TPM quota take effect?

No. Online inference (Flex) is available at all times. The effective TPM quota and time window are displayed in the console.
