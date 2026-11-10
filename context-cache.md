# Context cache

In scenarios with large amounts of repeated context input such as multi\-turn conversations, tool calls, and role\-playing, context caching reuses computation results to avoid repeated processing of the same content by the model. This eliminates repeated\-loading overhead and can significantly reduce token costs.

> **Tip**

> If you're new to ModelArk, see [Quick start](quick-start.md) to get up and running quickly.

# Cache modes

Context caching supports two working modes, which can be selected based on convenience, determinism, and cost requirements.

* **Implicit cache**: **Automatically enabled**, requires no additional configuration from users, and cannot be disabled. It is suitable for scenarios where ease of integration is more important, or where it is difficult to explicitly manage or fix the prefix. The system automatically identifies common prefixes in requests and caches them.

* **Explicit cache**: A caching mode that needs to be **manually enabled**. It requires manual creation and configuration of cache and supports deterministic hits.

   > **Note**

   > Implicit cache requires no configuration and is automatically enabled by the system. When a request uses explicit cache, the implicit cache does not take effect.

|Comparison item |Implicit cache |Explicit cache |
|---|---|---|
|Supported use cases |Suitable for general scenarios that prioritize ease of integration and do not require explicit cache creation or management. It is especially suitable for scenarios such as multi\-turn conversations and tool calls, where common prefixes continue to grow during interactions and are difficult to stabilize as fixed prefixes. The system automatically identifies common prefixes and caches them. |Suitable for scenarios that require cache creation and configuration, stable reuse of a fixed prefix, and stronger controllability, such as role\-playing and fixed instruction templates, as well as multi\-turn conversations with stable prefixes and few tool calls or a stable structure. |
|Configuration method |No configuration required, cannot be disabled. |Manual configuration required |
|Impact on response quality |No impact |No impact |
|Supported models |See [Supported models](context-cache.md#6024bcd0) |See [Supported models](context-cache.md#e8535e58) |
|Supported APIs |[Responses API](create-model-responses-api.md)<br><br>[Chat API](chat-api.md)<br><br>[Batch API](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/batch-chat-api) |[Responses API](create-model-responses-api.md) |
|Cache hit |The hit rate cannot be guaranteed and is affected by multiple factors. The specific hit probability is determined by the system. |Deterministic hits (higher cache hit rate) |
|Cache lifetime |Dynamically changes. The system periodically clears cache data that has not been used for a long time. |Supports configuring cache expiration time |
|Minimum number of tokens for caching |Default: `1024 tokens`. For model\-specific thresholds, see [How it works](context-cache.md#8c7c47d5). |Prefix cache: `256 tokens`; session cache has no limit on cache block size. |
|Billing for input tokens that hit the cache |Tiered billing based on model input length |Tiered billing based on model input length |
|Cache storage billing |No charge |Charged |

# Implicit cache

## Supported models

Models that support implicit cache are listed below. For details, see [Context caching](model-list.md#ed095742).

* For online inference, Dola Seed 2.0 and later series models support implicit cache, and this feature cannot be disabled.

* For batch inference, implicit cache is enabled for supported models and cannot be disabled.

## Improve the cache hit rate

Implicit cache is an automatic mode that requires no additional configuration from users and cannot be disabled. Whether a cache hit occurs is determined by the system, and hits are not guaranteed.

Recommendations:

* Keep the prefix stable. Place stable content such as fixed role settings, background information, and long text at the beginning, and place variable content such as the current question, timestamp, and request ID at the end. For similar requests, keep the request structure and prefix content as consistent as possible to reduce hit fluctuations caused by frequent prefix changes.

* Keep tool definitions, tool order, structured output schemas, and similar settings consistent.

* Monitor hits and iterate on optimizations. You can record token statistics in responses, continuously observe trends in hit tokens, and optimize prompt organization and prefix stability accordingly.

* Use prompt cache key to configure a stable cache routing identifier and increase the probability of cache reuse. See the configuration method below.

## Use prompt cache key

Prompt cache key is a routing affinity policy for the prompt cache capability. By setting a stable cache routing identifier in the request, the system will try to route requests that share the same prefix to services with a higher cache hit rate, thereby increasing the probability of implicit cache reuse.

### Supported models and APIs

* Supported models: Seed 2.0 and later model series.

* Supported APIs:

   * Chat API: Set `prompt_cache_key` in the request parameters.

   * Responses API: Set `prompt_cache_key` in the request parameters.

### How it works

> **Tip**

> **How it works**: The system uses the cache routing identifier in the request as a session\-level routing hint and isolates routing mappings by endpoint, model, and this value. This capability uses a best\-effort affinity policy. It does not commit to a fixed instance or guarantee that every request will hit the cache.

1. **Initial request**: The system selects an available service based on the regular routing policy and creates a routing mapping for the cache routing identifier value.

2. **Subsequent requests**: Requests with the same endpoint, model, and cache routing identifier value are preferentially routed to the mapped service to increase the probability of cache reuse for the same prefix.

3. **Automatic fallback**: When the mapped service is unavailable, the request rate exceeds the protection threshold, or other protection conditions are triggered, requests fall back to regular routing.

### Limitations

* **Traffic limit**: The total traffic of requests corresponding to the same cache routing identifier should be kept within 15 requests per minute. When the rate is too high, some requests may fall back to regular routing and therefore cannot reuse prefix cache on the original service. For high\-traffic workloads, we recommend steadily distributing traffic across more values.

* **Keep stable**: Requests with continuous context should use the same value and avoid frequent changes within the same session.

* **Isolate sessions**: Different users or parallel sessions should use different values to avoid unrelated requests sharing routing mappings and creating hotspots.

* **Avoid sensitive information**: We recommend using an unreadable internal session ID. Do not use phone numbers, email addresses, access credentials, or the original prompt text.

* **No strong consistency guarantee**: During service scaling, releases, failovers, and mapping expiration, or when protection policies are triggered, requests may be rerouted.

### How to use

When calling a model, Chat API and Responses API can set `prompt_cache_key` in the request parameters. Ensure requests with continuous context use the same value to improve the cache hit rate.

The following example uses Responses API:

**Curl**

```Bash
PREFIX_CHUNK='You are a literary analysis assistant. Answer concisely and clearly. Analyze the story The Gift of the Magi by O. Henry. Della and Jim each sell a treasured possession to buy a Christmas gift for the other. '
LONG_PREFIX=""
for _ in {1..30}; do
    LONG_PREFIX="\\${LONG_PREFIX}\\${PREFIX_CHUNK}"
done

echo "First request: warm the cache"
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d @- <<JSON
{
  "model": "dola-seed-2-1-turbo-260628",
  "input": "\\${LONG_PREFIX} User question: summarize the core plot in five bullet points.",
  "prompt_cache_key": "shared-workflow-v1"
}
JSON

echo "Second request: reuse the same prefix and observe usage"
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d @- <<JSON
{
  "model": "dola-seed-2-1-turbo-260628",
  "input": "\\${LONG_PREFIX} User question: describe the main characters in three bullet points.",
  "prompt_cache_key": "shared-workflow-v1"
}
JSON
```

**Python**

```Python
import os
from arkruntime import Ark

client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)

PREFIX_CHUNK = "You are a literary analysis assistant. Answer concisely and clearly. Analyze the story The Gift of the Magi by O. Henry. Della and Jim each sell a treasured possession to buy a Christmas gift for the other. "
LONG_PREFIX = PREFIX_CHUNK * 30


def ask(question: str):
    response = client.responses.create(
        model="dola-seed-2-1-turbo-260628",
        input=f"{LONG_PREFIX} User question: {question}",
        prompt_cache_key="shared-workflow-v1",
    )
    print(response.usage.model_dump_json())
    return response


ask("summarize the core plot in five bullet points.")
ask("describe the main characters in three bullet points.")
```

**Go**

```Go
package main

import (
    "context"
    "fmt"
    "os"
    "strings"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/responses"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    prefixChunk := "You are a literary analysis assistant. Answer concisely and clearly. Analyze the story The Gift of the Magi by O. Henry. Della and Jim each sell a treasured possession to buy a Christmas gift for the other. "
    longPrefix := strings.Repeat(prefixChunk, 30)

    ask := func(question string) {
        resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
            Model:          "dola-seed-2-1-turbo-260628",
            Input:          responses.NewStringResponsesInput(longPrefix + " User question: " + question),
            PromptCacheKey: responses.NewOptString("shared-workflow-v1"),
        })
        if err != nil {
            fmt.Printf("response error: %v\n", err)
            return
        }
        fmt.Println(resp)
    }

    ask("summarize the core plot in five bullet points.")
    ask("describe the main characters in three bullet points.")
}
```

**Java**

```Java
package com.ark.example;

import com.volcengine.ark.runtime.models.responses.Response;
import com.volcengine.ark.runtime.models.responses.ResponsesInput;
import com.volcengine.ark.runtime.models.responses.ResponsesRequest;
import com.volcengine.ark.runtime.service.ArkService;

class ContextCachePromptCacheKey {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");

        ArkService arkService = ArkService.builder()
                .apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        String prefixChunk = "You are a literary analysis assistant. Answer concisely and clearly. Analyze the story The Gift of the Magi by O. Henry. Della and Jim each sell a treasured possession to buy a Christmas gift for the other. ";
        String longPrefix = prefixChunk.repeat(30);

        Response first = arkService.createResponse(ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofString(longPrefix + " User question: summarize the core plot in five bullet points."))
                .promptCacheKey("shared-workflow-v1")
                .build());
        System.out.println(first);

        Response second = arkService.createResponse(ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofString(longPrefix + " User question: describe the main characters in three bullet points."))
                .promptCacheKey("shared-workflow-v1")
                .build());
        System.out.println(second);

        arkService.shutdownExecutor();
    }
}
```

## Billing

When a request hits implicit cache, the billing rule is as follows:

* Hit input tokens (`cached_tokens`): Billed at the cache hit input unit price.

* Missed input tokens: Billed at the normal input unit price.

* Output tokens: Billed at the normal output unit price.

* Cache storage: No charge.

> **Note**

* > Compared with explicit cache, **implicit cache storage is free of charge**.

* > **The cache hit input unit price may change with the context input length range**. For example, the cache hit input unit price of the Seed 2.0 series models varies when the input length is [0, 32k], [32k, 128k], or [128k, 256k].

## How it works

When sending a request to a model that supports implicit caching, this feature is automatically enabled, and here is how it works:

* **Cache creation**: The system caches based on the **common prefix** of the request input. After a request completes inference, the system may write the reusable **prefix segment** from its input into the cache, so that subsequent requests can reuse it when the **same prefix** appears.

* **Cache hit**: When the input of a subsequent request has the **same prefix** as an existing cache entry, the system will try to reuse the cache result corresponding to this prefix, thereby reducing repeated calculations.

   * **No guarantee of hit**: Cache capacity is limited, and old caches may be evicted; distributed routing also affects the hit probability.

   * **No guarantee of longest prefix hit**: The system comprehensively considers available resources and hit benefits; the hit length may not be the globally longest hit segment.

* **Cache hit condition**: A cache hit can be triggered only after the cache block reaches the minimum number of tokens for the corresponding model. For the specific thresholds, see the table below.

   |Applicable objects |Minimum cache block size |
   |---|---|
   |Default configuration |`1024 tokens` |
   |* `glm-5-2-260617`<br><br>* `deepseek-v4-pro-ga-260813`<br><br>* `deepseek-v4-flash-ga-260731`<br><br>* `deepseek-v4-pro-260425`<br><br>* `deepseek-v4-flash-260425` |`2048 tokens` |
   |`deepseek-v4-1-flash-260910` |`256 tokens` |
   |* `deepseek-v4-1-flash-261017`<br><br>* `deepseek-v4-1-flash-economy-261017` |`128 tokens` |
   |`glm-5-3-flash-260828` |`8960 tokens` |

   > **Tip**

   > You can determine whether implicit cache is hit through the API response parameter `usage.prompt_tokens_details`:

   * > Hit: `cached_tokens > 0`

   * > Missed: `cached_tokens = 0`

# Explicit cache

Compared with implicit cache, explicit cache must be created explicitly and incurs the corresponding overhead, but it provides a higher cache hit rate and greater controllability. Currently, explicit cache supports two types: session cache and prefix cache.

## Supported models

For models and APIs that support explicit cache, see [Context caching](model-list.md#ed095742).

## Limitations

To avoid cache invalidation caused by improper use, the following basic limitations for using explicit cache must be met:

* **store**: The prerequisite for writing to the cache is that storage is enabled, that is, manually set **store** to `true` or keep the default value (the default is `true`).

* **caching**:

   * **You can only write to the cache for the current turn of conversation if cache writing is enabled for the previous turn of conversation request.**  Similarly, if a turn of request needs to write to the cache, you must ensure that cache writing is enabled for all previous turns of requests, that is, all previous turns have `"caching": {"type": "enabled"}` configured.

   * As long as `"caching": {"type": "enabled"}` exists in previous turns, `json_schema` is not supported, but `json_object` is supported.

> **Note**

> During the version switching process, the cache is temporarily unavailable. Requests made using the Responses API will not hit the cache, but the content will be cached and incur cache storage fees. After version switching is completed, caches from historical turns can be hit normally. For details, see [Version switching](manage-inference-endpoints.md#d7c15496).

* **instructions**: To write to the cache, the **instructions** parameter should be empty. If **instructions** is set in the current turn of request, this turn of conversation cannot call the existing cache, nor can it write the current turn of information to the cache.

* **thinking**: The value of the **thinking** parameter in the request must be consistent with that of the previous turn to use or write to the cache.

   * If `"thinking":{"type":"enabled"}` is set in the first turn, the same value must be used in subsequent turns to use or write to the cache.

   * If the **thinking** parameter is not set in the first turn (no value is assigned), subsequent requests must also not set the **thinking** parameter if they need to write to the cache or call the existing cache.

* **tools**: The **tools** parameter can only be set in the first turn of requests, and all subsequent conversations will carry the cached input of the **tools** parameter information by default.

   * Setting the **tools** parameter again in subsequent turns of conversation requests is not supported, which will cause conflicts and return errors.

   * If the first turn of conversation information is deleted, all subsequent turns of conversation will not carry the cached input of the **tools** parameter information, and the **tools** parameter cannot be configured either.

## Quick start

### Prerequisites

Before use, activate the model's "Inference (cache) pricing" capability on the [Model activation page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement).

### Prefix cache

Prefix cache is suitable for scenarios that combine a fixed prefix with a dynamic suffix.

> **Tip**

* > For the first turn of input, you need to set `"store": true` (default is `true`) and `"caching": {"type": "enabled", "prefix": true}` to create prefix caching. In subsequent turns, you can reference the cached information through `previous_response_id`.

* > When creating a prefix cache with the `glm-5-3-flash-260828` model, you must set `role` to `user`.

Restrictions for creating prefix caching: Input tokens must be greater than or equal to 256 tokens, otherwise an error will be reported; the`stream` parameter cannot be set to true.

> When creating prefix caching, `total_tokens = input_tokens` in the returned `usage`, and `output_tokens` is always 0.

**Python**

```Python
# coding=utf-8
import os
from arkruntime import Ark

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY'),
)

prefix_chunk = "The Gift of the Magi follows Della and Jim, who each sell a treasured possession to buy a meaningful Christmas gift for the other."
# Repeat stable content so the prefix is safely above the 256-token minimum.
long_prefix = prefix_chunk * 20

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
            {
             "role": "system",
             "content": long_prefix,
            }
          ],
    caching={"type": "enabled", "prefix": True},
    thinking={"type": "disabled"},
)
print(response)

second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "Write a diary entry from the point of view of Della describing her emotions before selling her hair."}],
    caching={"type": "enabled"},
    thinking={"type": "disabled"},
)
print(second_response)

third_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "Analyze how O. Henry uses irony in this part of the story. Provide a concise explanation."}],
    caching={"type": "enabled"},
    thinking={"type": "disabled"},
)
print(third_response)
```

**Go**

```Go
package main

import (
    "context"
    "fmt"
    "os"
    "strings"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/responses"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        // Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    prefix := true
    prefixChunk := "The Gift of the Magi tells the story of Della and Jim, who, while living in poverty, each sell a treasured possession to prepare a Christmas gift for the other."
    // Repeat stable content so the prefix is safely above the 256-token minimum.
    longPrefix := strings.Repeat(prefixChunk, 20)
    resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            easyMessage(responses.MessageRoleSystem, longPrefix),
        }),
        Caching:  responses.NewOptResponseCaching(responses.ResponseCaching{Type: responses.NewOptCacheMode(responses.CacheModeEnabled), Prefix: responses.NewOptBool(prefix)}),
        Thinking: responses.NewOptThinking(responses.Thinking{Type: responses.NewOptThinkingMode(responses.ThinkingModeDisabled)}),
    })
    if err != nil {
        fmt.Printf("response error: %v", err)
        return
    }
    fmt.Println(resp)

    second_resp, second_err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model:              "dola-seed-2-1-turbo-260628",
        PreviousResponseID: responses.NewOptString(resp.ID),
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            easyMessage(responses.MessageRoleUser, "Write a diary entry from Della's perspective describing how she felt before selling her long hair."),
        }),
        Caching:  responses.NewOptResponseCaching(responses.ResponseCaching{Type: responses.NewOptCacheMode(responses.CacheModeEnabled)}),
        Thinking: responses.NewOptThinking(responses.Thinking{Type: responses.NewOptThinkingMode(responses.ThinkingModeDisabled)}),
    })
    if second_err != nil {
        fmt.Printf("second response error: %v", second_err)
        return
    }
    fmt.Println(second_resp)

    third_resp, third_err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model:              "dola-seed-2-1-turbo-260628",
        PreviousResponseID: responses.NewOptString(resp.ID),
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            easyMessage(responses.MessageRoleUser, "Analyze O. Henry's use of irony in this story excerpt and provide a concise explanation."),
        }),
        Caching:  responses.NewOptResponseCaching(responses.ResponseCaching{Type: responses.NewOptCacheMode(responses.CacheModeEnabled)}),
        Thinking: responses.NewOptThinking(responses.Thinking{Type: responses.NewOptThinkingMode(responses.ThinkingModeDisabled)}),
    })
    if third_err != nil {
        fmt.Printf("second response error: %v", third_err)
        return
    }
    fmt.Println(third_resp)
}

func easyMessage(role responses.MessageRole, content string) responses.InputItem {
    return responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role:    responses.NewOptMessageRole(role),
                Content: responses.NewStringMessageContent(content),
            },
        ),
    }
}
```

**Java**

```Java
package com.ark.sample;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        // The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey).baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3").build();

        String prefixChunk = "The Gift of the Magi follows Della and Jim, who each sell a treasured possession to buy a meaningful Christmas gift for the other.";
        // Repeat stable content so the prefix is safely above the 256-token minimum.
        String longPrefix = String.join("", java.util.Collections.nCopies(20, prefixChunk));

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.SYSTEM).content(
                                MessageContent.ofString(longPrefix)
                        ).build())))
                .caching(ResponseCaching.builder().type(CacheMode.ENABLED).prefix(true).build())
                .thinking(Thinking.builder().type(ThinkingMode.DISABLED).build())
                .build();
        Response resp = arkService.createResponse(request);
        System.out.println(resp);
        System.out.println("---------------------");
        ResponsesRequest request2 = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .previousResponseId(resp.getId())
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofString("Write a diary entry from the point of view of Della describing her emotions before selling her hair.")
                        ).build())))
                .caching(ResponseCaching.builder().type(CacheMode.ENABLED).build())
                .thinking(Thinking.builder().type(ThinkingMode.DISABLED).build())
                .build();
        Response resp2 = arkService.createResponse(request2);
        System.out.println(resp2);
        System.out.println("---------------------");
        ResponsesRequest request3 = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .previousResponseId(resp.getId())
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofString("Analyze how O. Henry uses irony in this part of the story. Provide a concise explanation.")
                        ).build())))
                .caching(ResponseCaching.builder().type(CacheMode.ENABLED).build())
                .thinking(Thinking.builder().type(ThinkingMode.DISABLED).build())
                .build();
        Response resp3 = arkService.createResponse(request3);
        System.out.println(resp3);

        arkService.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
# coding=utf-8
import os
from openai import OpenAI

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY'),
)

prefix_chunk = "The Gift of the Magi follows Della and Jim, who each sell a treasured possession to buy a meaningful Christmas gift for the other."
# Repeat stable content so the prefix is safely above the 256-token minimum.
long_prefix = prefix_chunk * 20

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
            {
             "role": "system",
             "content": long_prefix,
            }
    ],
    extra_body={
        "caching": {"type": "enabled", "prefix": True},
        "thinking":{"type":"disabled"}
    }
)
print(response)

second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "Write a diary entry from the point of view of Della describing her emotions before selling her hair."}],
    extra_body={
        "caching": {"type": "enabled"},
        "thinking":{"type":"disabled"}
    }
)
print(second_response)

third_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "Analyze how O. Henry uses irony in this part of the story. Provide a concise explanation."}],
    extra_body={
        "caching": {"type": "enabled"},
        "thinking":{"type":"disabled"}
    }
)
print(third_response)
```

**Curl**

1. Create a cache and write content to it.

   > **Tip**

   > The input content must exceed 256 tokens; otherwise, prefix cache cannot be created.

   ```Bash
   PREFIX_CHUNK='The Gift of the Magi follows Della and Jim, who each sell a treasured possession to buy a meaningful Christmas gift for the other.'
   LONG_PREFIX=""
   for _ in {1..20}; do
       LONG_PREFIX="\\${LONG_PREFIX}\\${PREFIX_CHUNK}"
   done
   
   curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
   -H "Authorization: Bearer $ARK_API_KEY" \
   -H "Content-Type: application/json;charset=utf-8" \
   -d @- <<EOF
   {
       "model": "dola-seed-2-1-turbo-260628",
       "input":[
                   {
                    "role":"system",
                    "content":"You are a literary analysis assistant. Use the following stable prefix to answer questions: \\${LONG_PREFIX}"
                   }
             ],
       "caching":{
           "type":"enabled",
           "prefix": true
       },
       "thinking": {
           "type": "disabled"
       }
   }
   EOF
   ```

2. In the second round request, read and use the cache via the ID returned from the first round.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
   -H "Authorization: Bearer $ARK_API_KEY" \
   -H "Content-Type: application/json" \
   -d '{
       "model": "dola-seed-2-1-turbo-260628",
       "input": "Write a diary entry from the point of view of Della describing her emotions before selling her hair.",
       "caching":{
           "type":"enabled"
       },
       "thinking": {
           "type": "disabled"
       },
       "previous_response_id":"<THE_ID_FROM_FIRST_CALL>"
   }'
   ```

3. In the third round request, you also use the ID returned from the first round to read and use the cache.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
   -H "Authorization: Bearer $ARK_API_KEY" \
   -H "Content-Type: application/json" \
   -d '{
       "model": "dola-seed-2-1-turbo-260628",
       "input": "Analyze how O. Henry uses irony in this part of the story. Provide a concise explanation.",
       "caching":{
           "type":"enabled"
       },
       "thinking": {
           "type": "disabled"
       },
       "previous_response_id":"<THE_ID_FROM_FIRST_CALL>"
   }'
   ```

### Session caching

The Responses API supports automatically storing historical conversation context and maintaining the cache. You can use cached input and reduce inference costs in scenarios such as multi\-turn conversations by calling `previous_response_id`.

**Python**

```Python
# encoding=utf-8
import os
from arkruntime import Ark

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY'),
)
input_text = "You are a literary analysis assistant. Answer concisely and clearly. Here is an excerpt from The Gift of the Magi by O. Henry. <long excerpt>"
response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "system",
            "content": input_text
        },
        {
            "role": "user",
            "content":"Briefly summarize the story in 5 bullet points."
        }
    ],
    caching={"type": "enabled"},
    thinking={"type": "disabled"},
)
print(response)
print(response.usage.model_dump_json())

# Enter cached information in subsequent requests
second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "Write a diary entry from the point of view of Della describing her emotions before selling her hair."}],
    caching={"type": "enabled"},
    thinking={"type": "disabled"},
)

print(second_response)
print(second_response.usage.model_dump_json())

third_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=second_response.id,
    input=[{"role": "user", "content": "Based on the original excerpt and the diary Della just wrote, imagine how Jame would feel when he read the diary entry."}],
    caching={"type": "enabled"},
    thinking={"type": "disabled"},
)
print(third_response)
print(third_response.usage.model_dump_json())
```

**Go**

```Go
package main

import (
    "context"
    "fmt"
    "os"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/responses"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        // Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    input := "You are a literary analysis assistant. Keep your answers concise and clear. Analyze questions about The Gift of the Magi based on the content below.<The_Gift_of_the_Magi_story_content>"

    resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            easyMessage(responses.MessageRoleSystem, input),
            easyMessage(responses.MessageRoleUser, "Summarize the core plot in 5 brief bullet points."),
        }),
        Caching:  responses.NewOptResponseCaching(responses.ResponseCaching{Type: responses.NewOptCacheMode(responses.CacheModeEnabled)}),
        Thinking: responses.NewOptThinking(responses.Thinking{Type: responses.NewOptThinkingMode(responses.ThinkingModeDisabled)}),
    })
    if err != nil {
        fmt.Printf("response error: %v", err)
        return
    }
    fmt.Println(resp)
    fmt.Println(resp.GetUsage())

    second_resp, second_err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model:              "dola-seed-2-1-turbo-260628",
        PreviousResponseID: responses.NewOptString(resp.ID),
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            easyMessage(responses.MessageRoleUser, "Write a diary entry from Della's perspective describing how she felt before selling her long hair."),
        }),
        Caching:  responses.NewOptResponseCaching(responses.ResponseCaching{Type: responses.NewOptCacheMode(responses.CacheModeEnabled)}),
        Thinking: responses.NewOptThinking(responses.Thinking{Type: responses.NewOptThinkingMode(responses.ThinkingModeDisabled)}),
    })
    if second_err != nil {
        fmt.Printf("second response error: %v", second_err)
        return
    }
    fmt.Println(second_resp)
    fmt.Println(second_resp.GetUsage())
    third_resp, third_err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model:              "dola-seed-2-1-turbo-260628",
        PreviousResponseID: responses.NewOptString(second_resp.ID),
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            easyMessage(responses.MessageRoleUser, "Based on the original excerpt and the diary Della just wrote, imagine how Jame would feel when reading this diary."),
        }),
        Caching:  responses.NewOptResponseCaching(responses.ResponseCaching{Type: responses.NewOptCacheMode(responses.CacheModeEnabled)}),
        Thinking: responses.NewOptThinking(responses.Thinking{Type: responses.NewOptThinkingMode(responses.ThinkingModeDisabled)}),
    })
    if third_err != nil {
        fmt.Printf("third response error: %v", third_err)
        return
    }
    fmt.Println(third_resp)
    fmt.Println(third_resp.GetUsage())
}

func easyMessage(role responses.MessageRole, content string) responses.InputItem {
    return responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role:    responses.NewOptMessageRole(role),
                Content: responses.NewStringMessageContent(content),
            },
        ),
    }
}
```

**Java**

```Java
package com.ark.sample;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        // The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey).baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3").build();
        String input = "You are a literary analysis assistant. Answer concisely and clearly. Here is an excerpt from The Gift of the Magi by O. Henry. <long excerpt>";
        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.SYSTEM).content(
                                MessageContent.ofString(input)
                        ).build(), ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofString("Briefly summarize the story in 5 bullet points.")
                        ).build())))
                .caching(ResponseCaching.builder().type(CacheMode.ENABLED).build())
                .thinking(Thinking.builder().type(ThinkingMode.DISABLED).build())
                .build();
        Response resp = arkService.createResponse(request);
        System.out.println(resp);
        System.out.println(resp.getUsage());
        System.out.println("---------------------");
        ResponsesRequest request2 = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .previousResponseId(resp.getId())
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofString("Write a diary entry from the point of view of Della describing her emotions before selling her hair.")
                        ).build())))
                .caching(ResponseCaching.builder().type(CacheMode.ENABLED).build())
                .thinking(Thinking.builder().type(ThinkingMode.DISABLED).build())
                .build();
        Response resp2 = arkService.createResponse(request2);
        System.out.println(resp2.getOutput());
        System.out.println(resp2.getUsage());
        System.out.println("---------------------");
        ResponsesRequest request3 = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .previousResponseId(resp2.getId())
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofString("Based on the original excerpt and the diary Della just wrote, imagine how would Jame feel when he read the diary entry.")
                        ).build())))
                .caching(ResponseCaching.builder().type(CacheMode.ENABLED).build())
                .thinking(Thinking.builder().type(ThinkingMode.DISABLED).build())
                .build();
        Response resp3 = arkService.createResponse(request3);
        System.out.println(resp3.getOutput());
        System.out.println(resp3.getUsage());

        arkService.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
import os
from openai import OpenAI

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY'),
)

input_text = "You are a literary analysis assistant. Answer concisely and clearly. Here is an excerpt from The Gift of the Magi by O. Henry. <long excerpt>"
response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
            {
                "role": "system",
                "content": input_text
            },
            {
                "role": "user",
                "content":"Briefly summarize the story in 5 bullet points."
            }
          ],
    extra_body={
        "caching": {"type": "enabled"},
        "thinking":{"type":"disabled"}
    }
)
print(response)

second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "Write a diary entry from the point of view of Della describing her emotions before selling her hair."}],
    extra_body={
        "caching": {"type": "enabled"},
        "thinking":{"type":"disabled"}
    }
)
print(second_response)

third_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=second_response.id,
    input=[{"role": "user", "content": "Based on the original excerpt and the diary Della just wrote, imagine how Jame would feel when he read the diary entry."}],
    extra_body={
        "caching": {"type": "enabled"},
        "thinking":{"type":"disabled"}
    }
)
print(third_response)
```

**Curl**

1. Create a cache and write content to it.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
     -H "Authorization: Bearer $ARK_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
       "model": "dola-seed-2-1-turbo-260628",
       "input":[
                   {
                    "role":"system",
                    "content":"You are a literary analysis assistant. Answer concisely and clearly. Here is an excerpt from The Gift of the Magi by O. Henry. <long excerpt>"
                   },
                   {
                    "role": "user",
                    "content":"Briefly summarize the story in 5 bullet points."
                   }
             ],
       "caching":{
           "type":"enabled"
       },
       "thinking": {
           "type": "disabled"
       }
   }'
   ```

2. In the second round request, read and use the cache via the ID returned from the first round.

> If you need to update the cache, configure `"caching":{"type":"enabled"}` and use the ID of the returned request.

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
-H "Authorization: Bearer $ARK_API_KEY" \
-H "Content-Type: application/json" \
-d '{
    "model": "dola-seed-2-1-turbo-260628",
    "input": "Write a diary entry from the point of view of Della describing her emotions before selling her hair.",
    "caching":{
        "type":"enabled"
    },
    "thinking": {
        "type": "disabled"
    },
    "previous_response_id": "<THE_ID_FROM_FIRST_CALL>"
}'
```

3. In the third round request, read and use the cache via the ID returned from the second round.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
   -H "Authorization: Bearer $ARK_API_KEY" \
   -H "Content-Type: application/json" \
   -d '{
       "model": "dola-seed-2-1-turbo-260628",
       "input": "Based on the original excerpt and the diary Della just wrote, imagine how Jame would feel when he read the diary entry.",
       "caching":{
           "type":"enabled"
       },
       "thinking": {
           "type": "disabled"
       },
       "previous_response_id": "<THE_ID_FROM_SECOND_CALL>"
   }'
   ```

## Manage cache

### Control storage/cache lifecycle

You can specify the expiration time for context storage (`store`) and context caching (`caching`) through the `expire_at` parameter. The current maximum storage duration is 7 days, which is the current UTC UNIX timestamp plus 604800. When the `current time` exceeds the `expiration time`, the storage expires; the cache lifecycle is not reset when the cache/storage is used.

When the storage/cache of the Responses API expires, you need to recreate the storage/cache content via the [Responses API](create-model-responses-api.md).

**Python**

```Python
import os
from arkruntime import Ark
import time

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
            {
             "role": "system",
             "content": "Hello"
            }
          ],
    caching={"type": "enabled"},
    thinking={"type": "disabled"},
    expire_at=int(time.time()) + 3600,
)
print(response.model_dump_json())
```

**Go**

```Go
package main

import (
    "context"
    "fmt"
    "os"
    "time"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/responses"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            easyMessage(responses.MessageRoleSystem, "Hello"),
        }),
        Caching:  responses.NewOptResponseCaching(responses.ResponseCaching{Type: responses.NewOptCacheMode(responses.CacheModeEnabled)}),
        Thinking: responses.NewOptThinking(responses.Thinking{Type: responses.NewOptThinkingMode(responses.ThinkingModeDisabled)}),
        ExpireAt: responses.NewOptInt64(time.Now().Unix() + 3600),
    })
    if err != nil {
        fmt.Printf("response error: %v", err)
        return
    }
    fmt.Println(resp)
    fmt.Println(resp.GetUsage())
}

func easyMessage(role responses.MessageRole, content string) responses.InputItem {
    return responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role:    responses.NewOptMessageRole(role),
                Content: responses.NewStringMessageContent(content),
            },
        ),
    }
}
```

**Java**

```Java
package com.ark.sample;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
import java.time.Instant;

public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        // The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.SYSTEM).content(
                                MessageContent.ofString("Hello")
                        ).build())))
                .caching(ResponseCaching.builder().type(CacheMode.ENABLED).build())
                .thinking(Thinking.builder().type(ThinkingMode.DISABLED).build())
                .expireAt(Instant.now().getEpochSecond() + 3600)
                .build();
        Response resp = arkService.createResponse(request);
        System.out.println(resp);
        System.out.println(resp.getUsage());

        arkService.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
import os
from openai import OpenAI
import time

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
            {
             "role": "system",
             "content": "Hello"
            }
          ],
    extra_body={
        "thinking":{"type":"disabled"},
        "caching":{"type":"enabled"},
        "expire_at": int(time.time()) + 3600 # The expiration time for storage and cache is 1 hour from the current time.
    }
)
print(response.model_dump_json())
```

**Curl**

```Bash
EXPIRE_AT=$(($(date +%s) + 3600))

curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  --data-binary @- <<JSON
{
  "model": "dola-seed-2-1-turbo-260628",
  "input": [
    {
      "role": "system",
      "content": "Hello"
    }
  ],
  "expire_at": \\${EXPIRE_AT},
  "caching": {
    "type": "enabled"
  },
  "thinking": {
    "type": "disabled"
  }
}
JSON
```

### Delete cache

The Responses API supports deleting caches by ID, which works the same way as deleting historical conversations. This allows you to independently control the amount of cached information based on your business needs, such as deleting unnecessary cached content to reduce redundant input and lower costs.

**Python**

```Python
import os
from arkruntime import Ark

api_key = os.getenv('ARK_API_KEY')
client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

response = client.responses.delete("<RESPONSE_ID>")
print(response)
```

**Go**

```Go
package main

import (
    "context"
    "fmt"
    "os"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/responses"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    resp := client.DeleteResponse(ctx, &responses.ResponsesRemoveParams{ResponseId: "<RESPONSE_ID>"})
    fmt.Println()
    fmt.Println(resp)
}
```

**Java**

```Java
package com.ark.sample;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");

        // The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();
        DeleteResponseResponse deleteResult = arkService.deleteResponse(
                DeleteResponseRequest.builder().responseId("<RESPONSE_ID>").build()
        );

        System.out.println(deleteResult);

        arkService.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
from openai import OpenAI
import os

api_key = os.getenv('ARK_API_KEY')
client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

response = client.responses.delete("<RESPONSE_ID>")
print(response)
```

**Curl**

```Bash
curl "https://ark.ap-southeast.bytepluses.com/api/v3/responses/<RESPONSE_ID>" \
  -X DELETE \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json"
```

Note that after you delete the cache for a specific turn, the cached information for subsequent turns will be recalculated and stored the next time a request is made. As shown in the following figure (the scenario in the figure is that the API is called to delete the conversation information of the 3rd turn after the 5th turn of request).

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/e82470faec7f446c94dfcb55810fe08e~tplv-goo7wpa0wc-image.image)

> **Note**

> After you delete the cache for a specific turn, the cached information for subsequent turns will be recalculated and stored again on the next request.

When you make the 6th turn of request, the information from the 4th and 5th turns will be recalculated and cached, and this information will be billed as regular input instead of cached input.

## Billing

Billing items for explicit cache include input, cached input, cache storage, and output. For specific unit prices, see [Model pricing](model-pricing.md).

* **Input (USD per thousand tokens)** : New text in the ongoing conversation, that is, historical conversation information that needs to be recalculated and cached after deletion.

* **Cached input (USD per thousand tokens)** : The input is preprocessed and cached content, and the billing rate is significantly lower than that for new input content.

* **Storage (USD per thousand tokens per hour)** : Historical conversations are stored in the cache and incur storage fees. The fees are accumulated by multiplying the amount of cache used in each natural hour by the unit price.

> **Note**

* > Storage fees are incurred as soon as the cache is created, and billing stops when the cache is manually deleted or expires.

* > Storage fees are billed at each natural hour. Any duration less than 1 hour is counted as 1 hour.

* **Output (USD per thousand tokens)** : Content generated by the model based on input information. The billing method is the same as the inference that does not use session cache.

The billed usage for each request can be viewed in the returned `usage` structure. See [Responses API](create-model-responses-api.md) for details.

### Input tokens

It can be calculated by `input_tokens - cached_tokens`.

> **Tip**

> The model input includes the chain\-of\-thought content from the previous turn, which increases the number of input tokens. You can enable [Context editing (Responses API)](responses-api-context-editing.md) to manage chain\-of\-thought content and tool call content and control input tokens. The cache hits are the intersection of the input content and the cached content after context editing.

### Storage fees

Storage fees are only incurred after the cache is enabled in the request, that is, the parameter is configured as `"caching": {"type": "enabled"}`. Calculated by natural hour, the storage fee is calculated by accumulating the number of newly added cache tokens generated by each turn of requests in that hour.

> Storage is calculated by natural hour, and any duration less than 1 hour is counted as 1 hour.

|Dimension |Note |
|---|---|
|Cached content |Caches the input content. |
|Request diagram |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/responses-api/new-202607221313.svg) |
|Calculation for a single request |* **Cached content**: `input tokens`<br><br>* **Newly added cached content**: `cached content of the current turn of requests - cached content from the previous turn`<br><br>* **Cache storage fee**: `Within the cache validity period, the hourly storage fee is newly added cached content tokens × storage unit price` |

### Cost calculation

The cost of one request within 1 hour after enabling cache includes: token fee generated by the request and cache storage fee. The calculation formulas are as follows.

```plaintext
= Input cost + cached input cost + output cost
= (input_tokens - cached_tokens) x input unit price + cached_tokens x cached input unit price + output_tokens x output unit price
```

```plaintext
= Newly added cache storage fee
= Newly added cached content tokens × storage unit price
= (Cached content of the current turn of requests - cached content from the previous turn) × storage unit price
```

## How it works

Explicit cache supports two types: prefix cache and session cache.

### Prefix cache

Stores initial information and does not need to be updated during each conversation. You can store initialization information, such as role settings and background knowledge, in the cache in advance. Subsequent calls can directly reuse it through a cache reference without repeatedly sending the same content, reducing repeated computation and lowering usage costs. Suitable for repeated use scenarios of static prompt templates such as standardized conversation openings, specific task instructions, rule\-based templates, and in\-depth analysis of ultra\-long texts.

|Diagram |Note |
|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/advanced-usage/principles-and-selection-03.svg) |1. When a user creates a cache, ModelArk processes the information into tokens that can be directly used for model inference, stores them in the cache, and generates an ID as the key.<br><br>2. ModelArk receives a new request, calculates the tokens of the new input, retrieves the corresponding information tokens based on the cache ID in the request, concatenates them, and inputs them to the model for inference.<br><br>3. The model outputs response information, and there is no need to update the information in the cache. |

### Session caching

**Stores initial information** and **dynamically updates the cache with each turn of conversation**. In a new turn of requests, the cache information and input information are sent to the model together for inference. It is suitable for scenarios such as multi\-turn conversations and multi\-turn tool calls.

|Diagram |Note |
|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/advanced-usage/principles-and-selection-02.svg) |1. When a user creates a cache, ModelArk processes the information into tokens that can be directly used for model inference, stores them in the cache, and generates an ID as the key.<br><br>2. ModelArk receives a new request, calculates the tokens of the new input, retrieves the corresponding information tokens based on the cache ID in the request, concatenates them, and inputs them to the model for inference.<br><br>3. The model returns information, and ModelArk **stores the tokens of the response information into the cache** for use in the next request. The content actually written to the cache varies by model version. See the following description for details. |

> **Tip**

> The cached content consists only of the **input** of the current request; the model response is not written directly to the cache. The model response and chain\-of\-thought content from the previous turn are included as part of the input for the next request. After cache writing is enabled for that turn, they enter the cache, so the number of input tokens increases. You can manage chain\-of\-thought content and tool call content through the context editing feature. For details, see [Input tokens](context-cache.md#input-token-count).

# FAQs

## Can explicit cache and implicit cache take effect at the same time?

No. The explicit cache and implicit cache are mutually exclusive; when a request uses the explicit cache, the implicit cache does not take effect.

## What are the common reasons why prefix cache creation fails?

There are three common reasons: first, the first turn is not configured as required with `"caching": {"type": "enabled", "prefix": true}`; second, the input tokens are fewer than 256; third, `stream=true` is set in the request. In addition, the first turn of the prefix cache depends on `store=true` by default (that is, the default value is true).

## What happens after a cache for a turn is deleted?

After you delete the cache for a specific turn, the cached information for subsequent turns will be recalculated and stored again on the next request. The corresponding content will be billed as input rather than cache input until a new cache is established again.

## How do I tell whether the implicit cache was hit?

Check the `usage.prompt_tokens_details.cached_tokens` parameter in the response. A value greater than 0 indicates a hit; a value equal to 0 indicates a miss.
