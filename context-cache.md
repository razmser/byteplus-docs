# Context cache

When an application repeatedly sends the same long prefix in multi\-turn conversations, tool calls, or fixed prompts, repeated input continues to consume compute power and iccur cost. Context cache reuses the computed results of these prefixes. Implicit cache is automatically enabled for supported models, while explicit cache is manually enabled by developers and requires lifecycle management. The two cannot take effect at the same time. An actual hit is determined by whether cached tokens are greater than 0. HTTP 200 or configuration echo alone cannot prove a hit. If you call the model only once and do not have a stable repeated prefix, caching is usually not needed. This topic covers mode selection, integration, observation, billing, and management.

> **Tip**

> If you're new to ModelArk, see [Quick start](quick-start.md) to get up and running quickly.

## Quick start

Context cache lets supported models reuse repeated input prefixes. You do not need to choose a mode first or enable an additional cache switch. The first request warms up the cache. In the second request, keep the same long prefix and replace only the question at the end. You can directly compare the `usage` of the two requests to see the cache benefit. The following example uses implicit cache in the Responses API.

|Input |Output preview (`usage`) |Description |
|---|---|---|
|First request: fixed prefix (repeated 30 times) + first\-round question |`{"usage": {"input_tokens_details": {"cached_tokens": 0}}}` |The first request has no reusable cached input. All input is billed at the regular input unit price and written to the cache. |
|Second request: same fixed prefix + second\-round question |`{"usage": {"input_tokens_details": {"cached_tokens": 1336}}}` |The prefix hit is billed at the cached input unit price, and the repeated prefix is no longer computed. Compared with not using cache, this can reduce model usage costs and improve response speed when the prefix is long and hits are stable. |

The values in the table come from an actual run (`dola-seed-2-1-turbo-260628`, with the fixed prefix repeated 30 times). The actual number of tokens depends on the input, and the speed benefit depends on the model, prefix length, and service load.

`cached_tokens` greater than 0 indicates a hit for this request. For complete hit determination, zero\-value troubleshooting, and billing criteria, see [Implicit cache > Observation and billing](context-cache.md#implicit-observation-billing).

### Minimal example

All of the following tabs are examples with the Responses API. The examples repeat the fixed prefix 30 times to exceed the default threshold. In actual use, adjust it based on the threshold of the corresponding model and determine whether a hit occurred through the second\-round `usage`.

**cURL**

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

## Select a cache mode

The key difference between implicit cache and explicit cache is whether active control is required. Implicit cache is automatically enabled by the system, requires no additional configuration, and does not incur cache storage fees. Explicit cache must be manually enabled, continues through response IDs or reuses fixed prefixes, and is billed based on cache storage duration. Choose explicit cache when you need to actively control the reuse method, lifecycle, or deletion timing. Use implicit cache first when you only need to reduce the cost of repeated prefixes.

### Decision flowchart

First determine whether you need to actively control the cache, then choose a path based on model support and whether a stable repeated prefix exists. Implicit cache is automatically enabled even without a stable prefix, but the hit probability is unstable and the hit rate cannot be guaranteed.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/advanced-usage/context-cache-mode-selection-v1.svg)

### Mode comparison

|Comparison item |Implicit cache |Explicit cache |
|---|---|---|
|Applies to |Chat, Responses, Messages, Batch (subject to the model list) |Responses |
|Enabling and reuse methods |Automatically enabled for supported models, with no cache parameter required. Chat API and Responses API support an optional routing identifier. Messages API passes a session identifier through `metadata.user_id.session_id` to optimize affinity. |Manually enable it for each request; session cache uses response IDs to continue turn by turn, and prefix cache is reused by multiple requests after a fixed prefix is created. |
|Supported use cases |Multi\-turn conversations, tool calls, and fixed long system prompts |Scenarios where you need to actively control the reuse method, lifecycle, or deletion timing |
|Management and billing |No need to manage the cache lifecycle. No separate cache storage fee is currently charged. |You can set an expiration time. Storage fees for newly added cached tokens are charged by natural\-hour billing cycles. |

## Implicit cache

Implicit cache is automatically enabled for supported models and cannot be disabled. You can use it by sending a regular request. No cache switch or special configuration is required. `prompt_cache_key` only provides optional routing affinity optimization. If it is omitted, implicit cache still takes effect. A successful request only means the call succeeded. It does not mean the cache was hit.

### Supported models and use cases

The specific model scope for implicit cache is subject to the "Context caching" section and the "Implicit caching" column in the [Model list](model-list.md#text-code-context-caching).

|API |Automatically enabled |Optional optimization |Hit observation |Boundary |
|---|---|---|---|---|
|[Chat API](chat-api.md) |Automatically enabled for supported models and cannot be disabled |`prompt_cache_key` is an optional string and is not set by default. If it is omitted, implicit cache is still enabled, but routing affinity optimization using this identifier is not used. |`usage.prompt_tokens_details.cached_tokens` |Cached tokens greater than 0 indicate a hit for this request. A value of 0 indicates no hit for this request and does not mean the capability is disabled. |
|[Responses API](create-model-responses-api.md) |Automatically enabled for supported models and cannot be disabled |`prompt_cache_key` is an optional string and is not set by default. If it is omitted, implicit cache is still enabled. |`usage.input_tokens_details.cached_tokens` |When explicit cache is enabled, do not interpret the same response as implicit cache. |
|[Messages API](messages-api.md) |Automatically enabled for supported models |Supports passing a session identifier through `metadata.user_id.session_id` to achieve cache affinity similar to `prompt_cache_key`. There is no direct `prompt_cache_key` input parameter, and the hidden parameter in `metadata` is not a public configuration. |`usage.cache_read_input_tokens` |`usage.cache_creation_input_tokens` currently returns 0. This parameter indicates the amount read from implicit cache and does not indicate that a configurable explicit cache entry exists. |
|[Batch API](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/batch-chat-api) |Automatically used for supported models |The current public Batch Chat request does not require additional cache parameters. |`usage.prompt_tokens_details.cached_tokens` is currently fixed as 0. |A fixed value of 0 is the current observation boundary of the API, not a cache miss signal. |

For specific model IDs and cache length thresholds, see [How to use > Cache length threshold](context-cache.md#implicit-usage).

### How to use

Implicit cache does not require any setup steps. You can send a standard request.

#### Cache length threshold

* **Cache creation**: After the input reaches the threshold for the corresponding model, the system automatically caches the reusable **prefix segment** in the request. You do not need to explicitly declare a common prefix. After the cache is created, the input of subsequent requests is automatically matched against existing caches.

* **Cache hit**: When the input beginning of a subsequent request has the **same prefix** as an existing cache entry, the system will try to reuse the corresponding cache result, thereby reducing repeated calculations.

   * **No guaranteed cache hit**: Cache capacity, eviction policies, distributed routing, prefix stability, and model thresholds all affect actual hits. The system cannot guarantee a hit every time.

   * **No guarantee of longest prefix hit**: The system comprehensively considers available resources and hit benefits; the hit length may not be the globally longest hit segment.

The input is written to the cache only after it reaches the minimum number of tokens for the corresponding model. Subsequent requests with the same prefix may then hit the cache. If the input is below the threshold, the capability remains enabled, but nothing is written for that request. The thresholds for different models are as follows:

* **128 tokens**: `deepseek-v4-1-flash-261017`, `deepseek-v4-1-flash-economy-261017`.

* **256 tokens**: `deepseek-v4-1-flash-260910`.

* **Default threshold**: At least 1024 tokens.

* **2048 tokens**: `dola-seed-2-1-turbo-260628`, `glm-5-2-260617`, `deepseek-v4-pro-ga-260813`, `deepseek-v4-flash-ga-260731`, `deepseek-v4-pro-260425`, and `deepseek-v4-flash-260425`.

* **8960 tokens**: `glm-5-3-flash-260828`.

#### Optimize cache hits

We recommend the following order:

1. **Prepare a stable prefix**: Put stable role settings, background knowledge, tool definitions, or long text at the beginning of the request, put dynamic questions at the end, and keep tool definitions, tool order, and structured output schemes stable.

2. **Send a request**: Use a model that supports implicit cache. Chat, Responses, and Messages all automatically enable implicit cache. Chat and Responses can optionally set `prompt_cache_key` to improve routing affinity for requests with the same prefix. Messages supports passing a session identifier through `metadata.user_id.session_id` to achieve cache affinity and does not have a direct `prompt_cache_key` input parameter. Batch API automatically uses implicit cache for supported models, but `cached_tokens` is fixed as 0, so you cannot use it to calculate the hit rate.

3. **Verify hits**: Send a limited number of repeated requests with the same common prefix. For the complete determination procedure, see [Observation and billing](context-cache.md#implicit-observation-billing).

#### prompt_cache_key configuration

`prompt_cache_key` is used to improve routing affinity for requests with the same prefix. It does not change whether caching is enabled. The Chat and Responses examples in the quick start already include this parameter. Set it according to the following constraints:

* **Session isolation**: Requests that continue the same context should use the same value; different users or parallel sessions should use different values.

* **Traffic control**: We recommend limiting the total traffic for the same value to 15 requests per minute. If this limit is exceeded, some requests may fall back to regular routing.

* **Sensitive information**: Use an unreadable internal session ID. Do not pass phone numbers, email addresses, access credentials, or raw prompt text.

* **Rerouting boundaries**: Rerouting may occur when the service scales, a release is deployed, failover occurs, mappings expire, or protection policies are triggered. A hit cannot be guaranteed every time.

### Observation and billing

First prepare a stable prefix as described in [Optimize cache hits](context-cache.md#implicit-usage), then verify it in the following order:

1. **First request**: Complete the first request with the same prefix and make sure the input reaches the threshold for the corresponding model.

2. **Repeated request**: Send another request with the same prefix, and read Chat's `usage.prompt_tokens_details.cached_tokens`, Responses' `usage.input_tokens_details.cached_tokens`, or Messages' `usage.cache_read_input_tokens`.

3. **Determine whether a hit occurred**: A cached token count greater than 0 indicates a hit for this request. If it is 0, check the input threshold, prefix stability, and routing conditions, and then perform bounded validation.

4. **Exclude indirect signals**: Do not treat HTTP 200, a response ID, or a one\-time latency change as evidence of a hit. For factors that affect hits, see [Cache length threshold](context-cache.md#cache-threshold).

The cost of a request consists of:

```text
Standard input fee = (input_tokens - cached_tokens) × input unit price
Cached input fee = cached_tokens × cached input unit price
Output fee = output_tokens × output unit price
Request fee = standard input fee + cached input fee + output fee
```

Implicit cache currently does not charge a separate cache storage fee. The input unit price, cached input unit price, and output unit price are subject to [Model pricing](model-pricing.md).

## Explicit cache

Explicit cache is available only through the Responses API. Before use, you must [activate inference (cache) pricing](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenTokenDrawer=false). When using or continuing to write to explicit cache, each request must set `caching.type=enabled`. `store` defaults to `true`, but response storage does not mean explicit cache is enabled. Explicit cache and implicit cache cannot take effect at the same time.

### Use session cache

Session cache continues turn by turn and is suitable for multi\-turn conversations and consecutive tool calls:

1. **Enable in the first turn**: In the first turn, set `caching.type=enabled`, keep `store` set to `true`, and save the `id` returned in the response.

2. **Continue in the next turn**: In the next turn, set `caching.type=enabled`, and pass the response `id` from the immediately preceding turn through `previous_response_id`.

3. **Consecutive turns**: Repeat step 2 for each subsequent turn. We recommend adding a delay of about 100 milliseconds between consecutive multi\-turn requests. If the interval is too short, the call may fail.

4. **Missing previous turn**: If any previous turn was not written to the cache, subsequent turns cannot backfill the context for that turn. In this case, rebuild the session chain from a still\-valid response or a new request.

#### How session cache works

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/advanced-usage/context-cache-session-principle-v1.svg)

1. **Cache not enabled**: When session cache is not enabled, the initial information, the first N turns of conversation, and the question in turn N+1 all need to be recalculated and generate tokens.

2. **Cache enabled**: When session cache is enabled, the initial information and the first N turns of conversation are directly reused as cached tokens. The question in turn N+1 is calculated separately.

3. **Write to the next turn**: The model generates the response for turn N+1 based on the cached tokens and the current input, and adds the current turn content to the session cache for reuse in the next turn.

#### Code samples

The following example shows the complete calls for creating the first turn, continuing from it, and handling consecutive turns.

**cURL**

Step 1: Create the first\-turn response and save the response ID.

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

Step 2: Continue using the previous turn's response ID.

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

Step 3: Continue again using the previous turn's response ID.

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

**OpenAI Python SDK**

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

### Use prefix cache

Prefix cache writes a fixed\-length prefix to the cache for reuse by multiple requests. It is suitable for fixed knowledge, fixed role settings, or fixed tool definitions. The following examples all call the Responses API. The fixed prefix must be at least 256 tokens:

1. **Create a fixed prefix**: Prepare a fixed prefix of at least 256 tokens. In the creation request, set `caching.type=enabled` and `caching.prefix=true`, and do not set `stream=true`.

2. **Save the response ID**: The creation request only writes the prefix and does not generate model output. `output_tokens` is 0. Save the `id` returned in the creation response.

3. **Reuse in subsequent requests**: Multiple subsequent requests set `caching.type=enabled` and use the same `previous_response_id` to reference the creation response ID. The fixed prefix itself is not updated with each turn. We recommend adding a delay of about 100 milliseconds between consecutive multi\-turn requests.

4. **Creation failure**: If the creation response ID is not saved or the wrong ID is passed, the prefix cache cannot be reused. If creation fails, first check the token threshold, `stream`, `store`, `caching`, and combination restrictions.

#### How prefix cache works

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/advanced-usage/context-cache-prefix-principle-v1.svg)

1. **Cache not enabled**: When prefix cache is not enabled, the initial information, the first N turns of history, and the input for turn N+1 are all included in computation.

2. **Cache enabled**: When prefix cache is enabled, the initial information is reused directly as fixed\-prefix cached tokens and is not recomputed; the first N turns of history and the input for turn N+1 are still computed.

3. **Generate a response**: The model generates a response based on the fixed\-prefix cache, historical tokens, and the input for the current turn; the fixed prefix itself is not updated across turns.

#### Code samples

The following examples show the complete calls for creating a fixed prefix, reusing it for the first time, and reusing it again.

**cURL**

Step 1: Create a fixed\-prefix cache.

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

Step 2: Reuse the prefix cache for the first time.

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

Step 3: Reuse the same prefix cache again.

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

**OpenAI Python SDK**

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

### Management and cleanup

#### Set lifecycle

`expire_at` is an optional UTC Unix timestamp in seconds that takes effect for both response storage and explicit cache. Using the cache does not reset the lifecycle; for the default value and value range, see the "Custom expiration" row in [Supported models and usage limits](context-cache.md#explicit-support-limits).

**cURL**

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

**OpenAI Python SDK**

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

The example keeps the default `store` value of `true`; after creation, check the `expire_at` returned in the response. If a parameter is out of range, correct the timestamp and retry; after the cache expires, stop referencing the old response ID and create a new cache chain.

#### Delete cache

Call `DELETE /responses/{responseId}` to delete the specified response and its cache resources. Determine the deletion result from the returned `id`, `object=response`, and `deleted`; `deleted=true` indicates that deletion succeeded.

**cURL**

```Bash
curl "https://ark.ap-southeast.bytepluses.com/api/v3/responses/<RESPONSE_ID>" \
  -X DELETE \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json"
```

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

**OpenAI Python SDK**

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

The following diagram shows how information in turns 4, 5, and 6 is recomputed and cached again after the cache for turn 3 is deleted.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/advanced-usage/context-cache-delete-principle-v1.svg)

After deletion, check the returned `id`, `object=response`, and `deleted`, and check `cached_tokens` and the recomputed input in subsequent requests. State changes after deletion:

* **Deletion succeeded**: The same `id`, `object=response`, and `deleted=true` are returned, and the response and its cache resources are no longer available.

* **Deleting an intermediate turn**: Subsequent turns are recomputed and written to a new cache on the next request, and the recomputed content is billed as regular input.

* **Deletion failed**: If `deleted=false` is returned or the request reports an error, check `responseId` and the error message first. Do not rebuild the chain directly.

### Observation and billing

A cache hit is determined by `usage.input_tokens_details.cached_tokens` being greater than 0; the echoed `caching.type=enabled` only indicates that the configuration for the current turn took effect.

When continuing across turns, the previous turn's response, chain\-of\-thought, and tool content may be included in the input of subsequent requests, increasing `input_tokens`; you can use [context editing](responses-api-context-editing.md) to control the chain\-of\-thought and tool content carried over to subsequent requests.

Explicit cache fees consist of two parts:

1. **Request fees**: Regular input, cached input, and output. The formula is the same as for implicit cache.

2. **Storage fees**: After explicit cache is enabled, newly cached tokens are accumulated for storage fees by calendar\-hour billing period.

#### Storage fees and turns

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/advanced-usage/context-cache-storage-rounds-v1.svg)

In the diagram, orange indicates the cache that was hit. Turn 2 reuses the system prompt and question from turn 1; turn 3 continues to reuse the cached content from the first two turns and adds only the content from the current turn. Only the newly cached content added in each turn is included in the storage fees for that hour.

Cache storage billing is accumulated by calendar hour:

```text
Cache storage fee = Σ(number of newly cached tokens in each calendar-hour billing period × storage unit price)
```

When the cache validity period spans multiple billing periods, each billing period is calculated separately; billing periods shorter than 1 hour are rounded up to 1 hour. For example, if a cache is created at 10:57 and expires at 12:27, it is billed for three billing periods: 10:00\-11:00, 11:00\-12:00, and 12:00\-13:00. Storage unit prices and cached input unit prices are subject to [model pricing](model-pricing.md).

### Supported models and usage limits

Explicit cache applies only to Responses models whose model list entry indicates support for explicit caching. The default value is `disabled`, and the allowed values are session cache and prefix cache. The following table summarizes the required configuration, default behavior, direct verification, and key boundaries by task.

|Task |Required configuration |Default behavior |Direct verification |Key boundaries |
|---|---|---|---|---|
|Applies to |Use the Responses API; pass the model ID of a model that supports explicit cache, or the corresponding model's inference endpoint ID, to `model`. |Chat, Messages, and Batch do not provide the explicit cache integration contract described on this page. |The request returns a Responses response ID. |Explicit cache applies only to the Responses API. |
|Enable session cache |`caching.type=enabled`; `store` is actually `true`. |`caching.type` defaults to `disabled`, so explicit cache is not enabled or written; `store` defaults to `true`, but only indicates that the response is stored. |The response echoes `caching.type=enabled`, and `usage.input_tokens_details.cached_tokens` is greater than 0. |`enabled` is a necessary condition but not a sufficient condition for hitting an existing cache. |
|Disable explicit cache |`caching.type=disabled` |The current turn neither reads from nor writes to the explicit cache; subsequent turns cannot backfill this turn's context. |The response does not enable explicit cache. |The default is disabled; this does not indicate that the request failed. |
|Session continuation |Set `caching.type=enabled` in each turn; pass the immediately preceding turn's response ID in `previous_response_id`. |Starts a new response chain and does not reference the created explicit cache. |The response ID chain is complete in each turn, and `cached_tokens` is greater than 0. |If any previous turn was not written, it cannot be backfilled; for multiple consecutive turns, use an interval of about 100 ms; session cache has no separate minimum input token threshold. |
|Prefix creation |`caching.type=enabled`; `caching.prefix=true`; at least 256 input tokens; `stream` cannot be `true`. |`caching.prefix` defaults to `false`, so the response is generated normally and written to the session cache. |The creation response returns `id`, and `output_tokens` is 0. |Successful creation does not mean subsequent requests will automatically hit the cache. |
|Prefix reuse |`caching.type=enabled`; pass the creation response ID in `previous_response_id`. |If the ID is not saved or the wrong ID is passed, this prefix cache cannot be reused. |`cached_tokens` is greater than 0 in subsequent requests. |A fixed prefix is not updated with each turn; multiple requests reference the same creation ID. |
|Custom expiration |`expire_at` is a UTC Unix timestamp in seconds, later than the creation time and no later than the creation time plus 604800 seconds. |Use the default expiration time: creation time plus 259200 seconds. |The response returns the actual `expire_at`. |`expire_at` applies to both `store` and `caching`; using the cache does not reset its lifecycle. |
|Combined usage |In multi\-turn requests, `thinking` must be consistent with the first turn; `tools` is set only in the first turn. |If the configuration is inconsistent, the cache cannot be used or written. |The request succeeds and `cached_tokens` is greater than 0. |`caching.type=enabled` conflicts with `instructions`; `json_schema` is not supported when a previous turn has cache enabled, so use `json_object` instead; setting `tools` again in subsequent turns causes a conflict. |

The following combinations will fail or not take effect:

* **Prefix too short**: The fixed prefix in the prefix creation request has fewer than 256 tokens.

* **Expiration time out of range**: `expire_at` is not later than the creation time, or is later than the creation time plus 604800 seconds.

* **thinking configuration changes**: `thinking` in multi\-turn requests must remain consistent with the first turn; do not change parameters that were set or not set in the first turn in subsequent turns.

* **Streaming creation**: The prefix creation request sets `stream=true`; in this case, you can create the prefix cache with a non\-streaming request first.

* **Instructions conflict**: `caching.type=enabled` is used together with `instructions`.

* **json_schema conflict**: A previous turn with cache enabled uses `json_schema`; in this case, you can use `json_object` instead.

* **Tools set repeatedly**: `tools` is set again in subsequent turns.

* **Cache pricing not activated**: Inference (cache) pricing is not activated before explicit cache is used, and the request returns an error.

Request failures usually come from model or endpoint configuration errors, inference (cache) pricing not being activated, or parameter combinations that do not meet the preceding restrictions. There are currently no confirmed cache parameters that are silently ignored; known conflicts return an error or appear as `cached_tokens=0`.

When a request fails, refer to the returned `error.code` and `error.message`. For error code descriptions, see [Error codes](error-codes.md).

## How it works

The client is responsible for organizing a stable prefix, saving the response ID, and continuing from it in subsequent requests; the ModelArk service is responsible for writing the cache, matching by prefix, and returning cache token usage. Response storage is only a prerequisite for explicit cache. `store=true` does not mean explicit cache is enabled; the `caching.type=enabled` echo only indicates that the configuration has taken effect, and an actual hit is still determined by whether cache tokens are greater than 0.

### Overall collaboration

Implicit cache does not have a separate comparison diagram. For detailed diagrams of session and prefix in explicit cache, see [Use session cache integration](context-cache.md#session-cache) and [Use prefix cache](context-cache.md#prefix-cache). This section only describes the common collaboration sequence.

Stage descriptions:

* **Organize input**: The client places stable content at the beginning and dynamic questions at the end; session mode retains the response chain, and prefix mode prepares a fixed prefix.

* **Create or write**: Implicit cache is handled automatically; explicit cache requires `caching.type=enabled` and `store` to be actually `true`, and returns a response ID.

* **Reuse and observe**: Implicit mode relies on reusable identical prefixes in the input, which the system matches automatically; explicit mode keeps `caching.type=enabled` in subsequent requests, passes the correct `previous_response_id`, and then determines hits by cache tokens.

* **Terminate**: The current flow ends when the response is returned, the cache expires or is deleted, or the request fails.

### Hits and invalidation

* **Hit**: Cache tokens are greater than 0, indicating that this request reused part of the cached input.

* **Miss**: Cache tokens are 0, indicating that this request did not perform any measurable cache read; this does not mean the cache capability is permanently unavailable.

* **Expiration or deletion**: After expiration, the old response ID can no longer be referenced; after an intermediate turn is deleted, subsequent content is recalculated and stored on the next request.

* **Model version switching**: Hits may be temporarily unavailable during the switching window, but requests may still write to the cache and incur storage fees; verify historical turns again after the switch is complete.

For hit parameters and mode\-specific conditions for different APIs, see [implicit cache](context-cache.md#implicit-cache) and [explicit cache](context-cache.md#explicit-cache).

## FAQs

### Can explicit cache and implicit cache take effect at the same time?

No. When a request uses explicit cache, implicit cache does not take effect. First choose a mode based on your active control requirements, and then interpret the results according to that mode's parameters and observability contract.

### What are the common reasons why prefix cache creation fails?

Check whether the fixed prefix reaches 256 tokens, whether the creation request incorrectly sets `stream=true`, whether `caching.type` is `enabled`, whether `store` is actually `true`, and whether parameter combination restrictions involving `instructions`, `tools`, `json_schema`, and others are met.

### Will implicit cache still be enabled if `prompt_cache_key` is not provided?

Yes. Implicit cache is enabled automatically. `prompt_cache_key` is only used to optimize routing affinity; it is not a switch.

### Why is implicit cache request successful but cached_tokens is 0?

Check whether the input reaches the threshold for the corresponding model, whether the common prefix is stable, and whether the endpoint, model, and value of the optional `prompt_cache_key` remain consistent; do not switch to checking explicit cache parameters.

### Why does explicit cache still miss after previous_response_id is provided?

Confirm that the first turn and every subsequent request set `caching.type=enabled`, `store` is actually `true`, and `previous_response_id` corresponds to the correct prefix creation ID or the previous session turn ID. Also check the cache lifecycle and model version switching window. For the criteria, see [Hits and invalidation](context-cache.md#hit-expiry).

### Why do costs increase after an intermediate turn cache is deleted?

Subsequent turns are recalculated and stored on the next request. The recalculated content is first billed as normal input and also incurs new cache storage fees.

## Related documents

* [Quick start](quick-start.md): Complete API key configuration and your first model call.

* [Model list](model-list.md#text-code-context-caching): Check whether the current model ID supports implicit or explicit cache.

* [Chat API](chat-api.md): View the `prompt_cache_key` and `cached_tokens` parameters.

* [Responses API](create-model-responses-api.md): View explicit cache parameters, response IDs, and lifecycle parameters.

* [Messages API](messages-api.md): View the `cache_read_input_tokens` parameter.

* [Batch API](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/batch-chat-api): View the current fixed zero\-value boundary for `cached_tokens` in Batch.

* [Context editing](responses-api-context-editing.md): Control the chain\-of\-thought and tool content carried by subsequent requests.
