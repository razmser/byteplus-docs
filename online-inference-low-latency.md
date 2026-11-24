# Online inference (low latency)

**Online inference (low latency)**  provides better time per output token (TPOT) performance for latency\-sensitive scenarios with pay\-as\-you\-go billing. Set `service_tier=fast` in a request to reduce latency. When rate limits or traffic spike protection are triggered, the request automatically falls back to **Online inference (standard)** .

> **tip**

* > The [Model comparison](https://ai.byteplus.com/ark/region:ap-southeast-1/model/comparison) page displays latency observations from the last 24 hours to help you evaluate the actual latency reduction.

## **Key benefits**

* **Low integration cost**: Billing is based on token usage.

* **Lower output latency (TPOT)** : Designed for scenarios sensitive to output generation speed, such as outbound calls, smart devices, and real\-time interactions.

* **Automatic fallback**: When a request exceeds the `fast` rate limit or triggers QoS protection, the system falls back to Online inference (standard) to keep the request available whenever possible.

## **Use cases**

* Applications highly sensitive to interaction latency, such as outbound calls, customer service assistants, smart devices, and real\-time companion applications.

* Workloads with fluctuating request traffic that require pay\-as\-you\-go billing and better latency during traffic peaks.

## **Supported features**

### **Supported models**

* seed\-2\-0\-lite\-260428

> **tip**

> Fine\-tuned models and branched models are not supported. For the actual supported models, see the console and API responses.

### **Supported APIs**

* [Chat API](chat-api.md)

* [Responses API](create-model-responses-api.md): For tools, only custom tools (Function Calling) are supported. For context caching, only implicit caching is supported.

## Procedure

### Prerequisites

Activate the service and obtain an API key.

* Activate the model and low\-latency inference service: On the [Model activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement) page, select the target model, activate the model service and turn on the switch in the **Low latency** column.

   > **tip**

   > After you activate low\-latency service for a model, low latency is enabled by default for all inference endpoints of that model. If you do not want to use low\-latency inference for an endpoint, manually turn off its low\-latency switch.

* On the [API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page, create or obtain an API key and configure it as the `ARK_API_KEY` environment variable. For details, see [Environment variable configuration guide](environment-variable-configuration-guide.md).

Use either of the following methods to access Online inference (low latency).

### Method 1: Specify the service tier in an API request

Both preset and custom inference endpoints support this method. Use it to switch the service tier for each request on the same inference endpoint.

* For a preset inference endpoint, invoke the model by model ID.

* For a custom inference endpoint, first [create an inference endpoint](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint/create) and obtain its endpoint ID. Keep the **Low\-latency (Fast) inference** switch turned on. Otherwise, low\-latency inference is unavailable.

#### Chat API

Set **service_tier** to `fast`.

```Python
# For SDK installation, see https://ai.byteplus.com/ark/region:ap-southeast-1/docs/1541595.
import os
from arkruntime import Ark

client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    )
completion = client.chat.completions.create(
    # For custom inference endpoints, configure this as the endpoint ID.
    model="seed-2-0-lite-260428",
    messages=[
        {"role": "user", "content": "Hello"},
    ],
    service_tier="fast",
)
print(completion.choices[0].message)
```

#### Responses API

Set **service_tier** to `fast`.

```Python
# For SDK installation, see https://ai.byteplus.com/ark/region:ap-southeast-1/docs/1541595.
import os
from arkruntime import Ark

client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    )

response = client.responses.create(
    # For custom inference endpoints, configure this as the endpoint ID.
    model="seed-2-0-lite-260428",
    input="hello",
    service_tier="fast",
)
print(response)
```

### Method 2: Configure the default service tier for an inference endpoint

Only custom inference endpoints support this method. Use it when an entire workload should use Online inference (low latency) by default without requiring changes to the invocation implementation.

1. [Create an inference endpoint](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint/create) and obtain its endpoint ID.

   1. Keep the low\-latency (fast) inference switch turned on.

   2. Under **Default parameters**, set `service_tier` to `fast`.

2. Save the configuration and invoke the model by using the endpoint ID. Requests that do not explicitly specify `service_tier` use low\-latency online inference by default.

   > **warning**

   > An explicit `service_tier` value in a request takes precedence over the default service tier of the inference endpoint.

## **Billing**

### Token pricing

See [Model pricing](model-pricing.md).

### **Token measurement**

* Rate limits are measured in TPM (tokens per minute), **with no separate input and output quotas**.

* Unit price differences across input length ranges follow the token\-based pricing of the corresponding model.

* Rate\-limit deductions for cache hits are the same as those for **Online inference (standard)** .

## **Rate limits and fallback**

### **Rate\-limit isolation and sharing**

* **Online inference (low latency) and Online inference (standard) do not share rate limits**. Online inference (low latency) has an independent TPM quota. View the default quota on the [Model activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement) page. To adjust the quota, submit a request through [Ticket](https://console.byteplus.com/workorder/create?step=2&SubProductID=P00001514).

* The rate\-limit quota for **Online inference (low latency)**  is shared across inference endpoints. In other words, inference endpoints for the same model under a primary account share the TPM quota. On the inference endpoint details page, you can configure an independent low\-latency TPM limit for an endpoint. By default, no endpoint\-specific limit is set, so each endpoint can consume the entire quota. Requests exceeding the quota fall back at random.

### **Dynamic rate limiting**

To ensure fairness, the platform dynamically limits accounts that exceed the default low\-latency TPM quota of a model and automatically reclaims quotas with persistently low utilization. The following rules apply:

* If the **P95 low\-latency TPM** over the past 14 days is less than **1/3** of the allocated low\-latency quota and the peak low\-latency TPM over the past 14 days is less than **1/2** of that quota, the platform reduces the quota to **1/3** of its allocated value, but not below the default low\-latency quota.

* If low\-latency inference for the model has not been used in the past 14 days, the platform reduces the quota to the default low\-latency quota.

### **Automatic fallback behavior**

* Rate limit:

   * The platform reserves quota based on the configured estimated output length. When a request exceeds the rate limit, it automatically falls back to **Online inference (standard)** .

   * Calculation interval: 10 to 15 seconds per interval. For a 15\-second interval, traffic exceeding one\-fourth of the TPM quota within 15 seconds falls back to **Online inference (standard)** . Evaluate the quota for **Online inference (low latency)**  against peak TPM rather than only average traffic per minute.

* Ramp\-rate limit: When platform load is high, traffic that grows by more than 20% per minute falls back to **Online inference (standard)**  to protect the overall user experience. The threshold may vary with resource load and request length.

* Fallback processing: After fallback, the request is subject to the rate\-limit rules of **Online inference (standard)** . A low\-latency request that exceeds its quota might still fail because of standard rate limits.

* Protection against attacks and long requests: When the platform detects risks such as unusually high concurrency or excessively long requests, it falls back to **Online inference (standard)**  to protect service stability.

> **warning**

* > **Online inference (low latency)** is not recommended for requests with long outputs.

* > If a traffic spike causes a rate\-limit failure, follow [Best practices for handling burst traffic](traffic-burst-handling-best-practices.md).

## **Monitoring and alerts**

The console displays invocation data for inference endpoints. Go to [Online inference](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint) \> endpoint details page (select the endpoint name in the endpoint list) \> **Monitoring**.

Focus on the following metrics. The metrics actually displayed in the console and monitoring panel take precedence.

* TPM and error rate in `fast` mode

* Latency (TPOT) curve

* Fallback volume (`fast` \-\> `default`) and fallback ratio

## **Limitations**

Online inference (low latency) does not support:

* Data Collaboration Rewards Campaign

* Model and endpoint usage limits

* Free Tokens Only mode: Online inference (low latency) and the Free Tokens Only mode cannot be enabled at the same time.

* Batch inference: Setting **service_tier** in a batch inference request body has no effect.

## **Error codes**

See [Error codes](error-codes.md).
