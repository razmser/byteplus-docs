# Online inference (standard)

Online inference (standard) runs in a public resource pool and uses token\-based billing, providing flexible, usage\-based pricing. It is designed for individual developers and small businesses. This topic covers its key features, supported models, use cases, and workflow.

# Key features

* Postpaid billing based on the number of tokens consumed during inference. No charges apply when you do not send requests.

* Moderate latency and concurrency. All endpoints for the same model under an account share the model\-level rate limits.

* Public resource pool deployment. Latency and concurrency can vary with platform resource availability.

# Use cases

* New ModelArk users

* Individual developers or small businesses

* Cost\-sensitive workloads without strict latency or concurrency requirements

* Workloads with unpredictable traffic that can tolerate occasional errors caused by limited resource availability

# Supported models

|Endpoint type |Invocation method |Supported models |
|---|---|---|
|Preset inference endpoint |Call a model directly by model ID; no endpoint creation is required |All foundation models available on ModelArk |
|Custom inference endpoint |Call a model only by endpoint ID; create the endpoint first |All foundation models available on ModelArk and models fine\-tuned with LoRA |

# Procedure

## 1. Create an inference endpoint (optional)

Create a custom inference endpoint in the target project. If you plan to use a preset inference endpoint, skip to step 2.

1. Go to [Create inference endpoint](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint/create) and follow the on\-screen instructions. For **Purchase option**, select **Standard**. You can specify other settings for the endpoint based on your actual need.

2. Click **Create**. The endpoint first enters the **Scheduling** state. When its state changes to **Healthy**, it is ready to receive requests.

   > **tip**

   > By default, ModelArk creates inference endpoints in the `default` project. To use another project, switch projects in the lower\-left corner of the console navigation bar.

## 2. Get the ID for model calls

* For a custom inference endpoint, go to [Online inference](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint), then view and copy the endpoint ID.

* For a preset inference endpoint, get the model ID from [Model list](model-list.md).

## 3. Call a model by endpoint ID or model ID

The following example shows a typical model call. For more examples, see [Text generation](text-generation.md).

> Before running the code, configure the API key in the `ARK_API_KEY` environment variable and install the SDK by running `python -m pip install --upgrade arkruntime`. For environment variable instructions, see [Environment variable configuration guide](environment-variable-configuration-guide.md).

```Python
import os
from arkruntime import Ark

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY'),
)

response = client.responses.create(
    model="<ENDPOINT_ID>",
    input="What is the largest lake on Earth?"
)
print(response)
```
