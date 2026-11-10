# Deep reasoning (Responses API)

Models with deep reasoning capability can efficiently handle complex tasks. Before generating a response, the model first conducts systematic analysis and logical decomposition of the input question, then generates the response based on the decomposition results to ensure the content is comprehensive and in\-depth.

> **Tip**

> If you're new to ModelArk, see [Quick start](quick-start.md) to get up and running quickly.

## Supported models

For large language models of version 250615 and later, the Responses API is supported by default unless otherwise specified. For details, see [Text and code generation](model-list.md#32e9e053) and find the models with "Deep reasoning" in the Capabilities column.

## API documentation

[Responses API](create-model-responses-api.md)

## Streaming output

Deep reasoning models need to perform thinking before replying. In non\-streaming output scenarios, tasks are prone to failure due to timeout, so streaming output is recommended.

> **Tip**

> If your application uses non\-streaming output, it is recommended to first obtain the complete content via streaming output before returning it. This not only prevents task failure due to timeout, but also meets the output requirements of the application.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "model": "dola-seed-2-1-turbo-260628",
      "input": "",
      "thinking":{"type": "enabled"},
      "stream": true
  }'
```

Output example:

```Plain Text
event: response.created
data: {"type":"response.created","response":{"created_at":1764229579,"id":"resp_021764229578658fe9a0f6cb2cc6c828e7a59adbdb971872aee70","max_output_tokens":32768,"model":"dola-seed-2-1-turbo-260628","object":"response","thinking":{"type":"enabled"},"service_tier":"default","caching":{"type":"disabled"},"store":true,"expire_at":1764488778},"sequence_number":0}

event: response.in_progress
data: {"type":"response.in_progress","response":{"created_at":1764229579,"id":"resp_021764229578658fe9a0f6cb2cc6c828e7a59adbdb971872aee70","max_output_tokens":32768,"model":"dola-seed-2-1-turbo-260628","object":"response","thinking":{"type":"enabled"},"service_tier":"default","caching":{"type":"disabled"},"store":true,"expire_at":1764488778},"sequence_number":1}

event: response.output_item.added
data: {"type":"response.output_item.added","output_index":0,"item":{"id":"rs_02176422957963700000000000000000000ffffac15dd335c9c43","type":"reasoning","status":"in_progress"},"sequence_number":2}

event: response.reasoning_summary_part.added
data: {"type":"response.reasoning_summary_part.added","item_id":"rs_02176422957963700000000000000000000ffffac15dd335c9c43","output_index":0,"summary_index":0,"part":{"type":"summary_text"},"sequence_number":3}

event: response.reasoning_summary_text.delta
data: {"type":"response.reasoning_summary_text.delta","summary_index":0,"delta":"\n","item_id":"rs_02176422957963700000000000000000000ffffac15dd335c9c43","output_index":0,"sequence_number":4}

event: response.reasoning_summary_text.delta
data: {"type":"response.reasoning_summary_text.delta","summary_index":0,"delta":"user ","item_id":"rs_02176422957963700000000000000000000ffffac15dd335c9c43","output_index":0,"sequence_number":5}

event: response.reasoning_summary_text.delta
data: {"type":"response.reasoning_summary_text.delta","summary_index":0,"delta":"asks","item_id":"rs_02176422957963700000000000000000000ffffac15dd335c9c43","output_index":0,"sequence_number":6}
...
```

**Python**

```Python
import os
from arkruntime import Ark
from arkruntime.types.responses.response_completed_event import ResponseCompletedEvent
from arkruntime.types.responses.response_reasoning_summary_text_delta_event import ResponseReasoningSummaryTextDeltaEvent
from arkruntime.types.responses.response_output_item_added_event import ResponseOutputItemAddedEvent
from arkruntime.types.responses.response_text_delta_event import ResponseTextDeltaEvent
from arkruntime.types.responses.response_text_done_event import ResponseTextDoneEvent

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# Create a request
response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="What are some common plants in the Brassicaceae family?",
    thinking={"type": "enabled"},
    stream=True
)

for event in response:
    if isinstance(event, ResponseReasoningSummaryTextDeltaEvent):
        print(event.delta, end="")
    if isinstance(event, ResponseOutputItemAddedEvent):
        print("\noutPutItem " + event.type + " start:")
    if isinstance(event, ResponseTextDeltaEvent):
        print(event.delta,end="")
    if isinstance(event, ResponseTextDoneEvent):
        print("\noutPutTextDone.")
    if isinstance(event, ResponseCompletedEvent):
        print("Response Completed. Usage = " + event.response.usage.model_dump_json())
```

Output example:

```Plain Text

outPutItem response.output_item.added start:
The user asked what common cruciferous plants are. First, I need to recall the main characteristics of cruciferous plants, such as cross-shaped corolla, tetradynamous stamens (four long and two short), and mostly silique fruits (long siliques or short silicles). Then I need to list common species. It will be clearer if divided into several categories, such as vegetables, ornamental plants, wild plants, etc.
....
Now organize the content: first summarize the characteristics of cruciferous plants, then list common examples in three categories: vegetables, ornamental plants, and wild weeds, with a brief description for each example. This structure is clear and easy for users to understand.
outPutItem response.output_item.added start:
Cruciferae is a common family of angiosperms, whose core characteristics are **cross-shaped corolla (4 petals arranged in a cross), tetradynamous stamens (4 long and 2 short), and mostly silique fruits** (divided into long siliques such as rapeseed, and short silicles such as shepherd's purse). Here are several categories of common cruciferous plants:
### **1. Common vegetables**
This is the most well-known category of cruciferous plants, and many daily food ingredients belong to this category:
1. **Chinese cabbage category**: Chinese cabbage, bok choy, baby bok choy (all are varieties of Brassica, mainly used for their leaves);
2. ...
...
### **Summary**
Cruciferous plants are closely related to human life - from daily vegetables to ornamental flowers, to field weeds, they have high diversity and practicality. Remember the three characteristics: "cross-shaped flower, tetradynamous stamens, silique", and you can easily identify most cruciferous plants!
outPutTextDone.
Response Completed. Usage = {"input_tokens":42,"input_tokens_details":{"cached_tokens":0},"output_tokens":1089,"output_tokens_details":{"reasoning_tokens":431},"total_tokens":1131,"tool_usage":null,"tool_usage_details":null}
```

**Go**

```Go
package main

import (
    "io"
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

    resp, err := client.CreateResponsesStream(ctx, &responses.ResponsesRequest{
        Model:    "dola-seed-2-1-turbo-260628",

        Input:    responses.NewStringResponsesInput("What are the common cruciferous plants?"),
        Thinking: responses.NewOptThinking(responses.Thinking{Type: responses.NewOptThinkingMode(responses.ThinkingModeEnabled)}),
    })
    if err != nil {
        fmt.Printf("stream error: %v", err)
        return
    }
    for {
        event, err := resp.Recv()
        if err == io.EOF {
            break
        }
        if err != nil {
            fmt.Printf("stream error: %v", err)
            return
        }
        handleEvent(event)
    }
}
func handleEvent(event *responses.ResponseStreamEvent) {
    switch event.OneOf.Type {
    case responses.ResponseReasoningSummaryTextDeltaEventResponseStreamEventSum:
        print(event.OneOf.ResponseReasoningSummaryTextDeltaEvent.Delta.Or(""))
    case responses.ResponseReasoningSummaryTextDoneEventResponseStreamEventSum:
        fmt.Printf("\nAggregated reasoning text: %s\n", event.OneOf.ResponseReasoningSummaryTextDoneEvent.Text.Or(""))
    case responses.ResponseTextDeltaEventResponseStreamEventSum:
        print(event.OneOf.ResponseTextDeltaEvent.Delta.Or(""))
    case responses.ResponseTextDoneEventResponseStreamEventSum:
        fmt.Printf("\nAggregated output text: %s\n", event.OneOf.ResponseTextDoneEvent.Text.Or(""))
    }
}
```

Output example:

```Plain Text
The user asks what common cruciferous plants are. First I need to recall the main characteristics of cruciferous plants, then list common examples, preferably categorized into vegetables, ornamental flowers, wild species and so on, so that the user can read clearly. First, cruciferous plants are characterized by cruciform corolla, tetradynamous stamens (four long and two short), and siliques (long siliques or short silicles).
...
Aggregated reasoning text:
Cruciferous plants are mainly characterized by **cruciform corolla** (four petals arranged in a cross shape), **tetradynamous stamens** (four long and two short) and **siliques** (a type of fruit), and are very common among vegetables and ornamental flowers. The following are several types of typical representatives:
...
**Answer summary:**
Common cruciferous plants include Chinese cabbage, radish, cabbage, broccoli, rapeseed, shepherd's purse, violet, February orchid, etc. (you can list them by vegetable/flower category as needed).
(Note: If you need a more concise version, you can directly list core examples: such as Chinese cabbage, radish, broccoli, shepherd's purse, violet, February orchid, etc.)
Aggregated output text: Cruciferous plants are mainly characterized by **cruciform corolla** (four petals arranged in a cross shape), **tetradynamous stamens** (four long and two short) and **siliques** (a type of fruit), and are very common among vegetables and ornamental flowers. The following are several types of typical representatives:
...
**Answer summary:**
Common cruciferous plants include Chinese cabbage, radish, cabbage, broccoli, rapeseed, shepherd's purse, violet, February orchid, etc. (you can list them by vegetable/flower category as needed).
(Note: If you need a more concise version, you can directly list core examples: such as Chinese cabbage, radish, broccoli, shepherd's purse, violet, February orchid, etc.)
```

**Java**

```Java
package com.ark.example;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
// The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .stream(true)

                .input(ResponsesInput.ofString("The difference between inference models and non-inference models."))
                .thinking(Thinking.builder().type(ThinkingMode.ENABLED).build())
                .build();
        arkService.streamResponse(request)
            .doOnError(Throwable::printStackTrace)
            .blockingForEach(event -> {
                if (event instanceof ResponseReasoningSummaryTextDeltaEvent) {
                    System.out.print(((ResponseReasoningSummaryTextDeltaEvent) event).getDelta());
                }
                if (event instanceof ResponseOutputItemAddedEvent) {
                    System.out.println("OutputItem " + (((ResponseOutputItemAddedEvent) event).getItem().getType()) + " Start: ");
                }
                if (event instanceof ResponseTextDeltaEvent) {
                    System.out.print(((ResponseTextDeltaEvent) event).getDelta());
                }
                if (event instanceof ResponseTextDoneEvent) {
                    System.out.println("OutputText End.");
                }
                if (event instanceof ResponseOutputItemDoneEvent) {
                    System.out.println("OutputItem " + ((ResponseOutputItemDoneEvent) event).getItem().getType() + " End.");
                }
                if (event instanceof ResponseFunctionCallArgumentsDoneEvent) {
                    System.out.println("FunctionCall Arguments: " + ((ResponseFunctionCallArgumentsDoneEvent) event).getArguments());
                }
                if (event instanceof ResponseCompletedEvent) {
                    System.out.println("Response Completed. Usage = " + ((ResponseCompletedEvent) event).getResponse().getUsage());
                }
            });

        arkService.shutdownExecutor();
    }
}
```

Output example:

```Plain Text
OutputItem reasoning Start:
The user asked what common cruciferous plants are. First I need to recall the main characteristics of cruciferous plants, such as cruciform corolla, tetradynamous stamens, etc. But the user probably does not need characteristics, just examples directly. The first thing that comes to mind is vegetables, as many daily edible vegetables are cruciferous.
...
OutputItem message Start:
Cruciferous plants are rich in species, many of which are common daily vegetables, ornamental plants or wild vegetables. Here are some typical examples:
...
These plants are very common in diet, agriculture or landscaping, and there is a high probability that you can find them among the vegetables you usually eat! 🌱🥦🥬OutputText End.
OutputItem message End.
Response Completed. Usage = Usage{inputTokens=42, outputTokens=759, totalTokens=801, inputTokensDetails=InputTokensDetails{cachedTokens=0}, outputTokensDetails=com.volcengine.ark.runtime.model.responses.usage.OutputTokensDetails@73d983ea, toolUsage=null, toolUsageDetails=null}
```

**OpenAI SDK**

```Python
import os
from openai import OpenAI

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# Create a request
response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="",
    stream=True,
    extra_body={
        "thinking": {"type": "enabled"},
    }
)

for event in response:
    if event.type == "response.reasoning_summary_text.delta":
        print(event.delta, end="")
    if event.type == "response.output_item.added":
        print("\noutPutItem " + event.type + " start:")
    if event.type == "response.output_text.delta":
        print(event.delta,end="")
    if event.type == "response.output_item.done":
        print("\noutPutTextDone.")
    if event.type == "response.completed":
        print("\nResponse Completed. Usage = " + event.response.usage.model_dump_json())
```

Output example:

```Plain Text

outPutItem response.output_item.added start:
The user asked what common cruciferous plants are. First I need to recall the main characteristics of cruciferous plants, such as cruciform corolla, tetradynamous stamens, etc. But the user probably does not need characteristics, just examples directly. Then I need to divide them into common categories, such as vegetables, which should be the most numerous, plus ornamental plants, and possibly wild weeds?
...
outPutTextDone.
outPutItem response.output_item.added start:
Cruciferous is a large family of angiosperms, characterized by **cruciform corolla** (4 petals arranged in a cross shape) and **tetradynamous stamens** (6 stamens, 4 long and 2 short). Many species are common vegetables, ornamental plants or wild vegetables. Here are several types of typical common cruciferous plants:
...
**Answer summary:**
Common cruciferous plants include Chinese cabbage, radish, brassica (cabbage/broccoli/cauliflower), rapeseed, mustard, shepherd's purse, violet orychophragmus, stock, etc., covering three categories: vegetables, ornamental plants, and wild vegetables.
...
outPutTextDone.
Response Completed. Usage = {"input_tokens":42,"input_tokens_details":{"cached_tokens":0},"output_tokens":1467,"output_tokens_details":{"reasoning_tokens":574},"total_tokens":1509}
```

## Control deep reasoning

You can control whether the model enables deep reasoning through the **thinking** parameter.

* `enabled`: Force enable. Forcibly turn on the deep reasoning capability.

* `disabled`: Force disable. Forcibly turn off the deep reasoning capability.

The following models support using the **thinking** parameter in the Responses API to control deep reasoning:

* dola\-seed\-2\-1\-turbo\-260628: supports `enabled` (default) and `disabled`.

* seed\-2\-0\-lite\-260428: supports `enabled` (default) and `disabled`.

* seed\-2\-0\-mini\-260428: supports `enabled` (default) and `disabled`.

* seed\-2\-0\-pro\-260328: supports `enabled` (default) and `disabled`.

* seed\-2\-0\-lite\-260228: supports `enabled` (default) and `disabled`.

* seed\-2\-0\-mini\-260215: supports `enabled` (default) and `disabled`.

* seed\-2\-0\-code\-preview\-260328: supports `enabled` (default) and `disabled`.

* seed\-1\-8\-251228: supports `enabled` (default) and `disabled`.

* glm\-5\-3\-flash\-260828: supports `enabled` (default). (**Thinking is always enabled, and disabling thinking is no longer supported.** )

* glm\-5\-2\-260617: supports `enabled` (default) and `disabled`.

* seed\-1\-6\-250615: supports `enabled` (default) and `disabled`.

* seed\-1\-6\-250915: supports `enabled` (default) and `disabled`.

* seed\-1\-6\-flash\-250715: supports `enabled` (default) and `disabled`.

* seed\-1\-6\-flash\-250615: supports `enabled` (default) and `disabled`.

* deepseek\-v4\-1\-flash\-261017: supports `enabled` (default) and `disabled`.

* deepseek\-v4\-1\-flash\-260910: supports `enabled` (default) and `disabled`.

* deepseek\-v4\-1\-flash\-economy\-261017: supports `enabled` (default) and `disabled`.

* deepseek\-v4\-pro\-ga\-260813: supports `enabled` (default) and `disabled`.

* deepseek\-v4\-flash\-ga\-260731: supports `enabled` (default) and `disabled`.

* deepseek\-v4\-pro\-260425: supports `enabled` (default) and `disabled`.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "model": "dola-seed-2-1-turbo-260628",
      "input": "The difference between inference models and non-inference models.",
      "thinking":{"type": "enabled"}
  }'
```

**Python**

```Python
import os
from arkruntime import Ark

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# Create a request
response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="The difference between inference models and non-inference models.",
    thinking={"type": "enabled"},
)

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
        // Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model:    "dola-seed-2-1-turbo-260628",
        Input:    responses.NewStringResponsesInput("The difference between inference models and non-inference models."),
        Thinking: responses.NewOptThinking(responses.Thinking{Type: responses.NewOptThinkingMode(responses.ThinkingModeEnabled)}),
    })
    if err != nil {
        fmt.Printf("response error: %v\n", err)
        return
    }
    fmt.Println(resp)
}
```

**Java**

```Java
package com.ark.example;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        // The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")

                .input(ResponsesInput.ofString("The difference between inference models and non-inference models."))
                .thinking(Thinking.builder().type(ThinkingMode.ENABLED).build())
                .build();
        Response resp = arkService.createResponse(request);
        System.out.println(resp);
        arkService.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
import os
from openai import OpenAI

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# Create a request
response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="The difference between inference models and non-inference models.",
    extra_body={
        "thinking": {"type": "enabled"},
    }
)

print(response)
```

## Output thinking summary

### Supported models

* dola\-seed\-2\-1\-turbo\-260628

* seed\-2\-0\-lite\-260428

### Instructions

By default, the thinking summary capability is enabled. The model's original thinking content is not output. Instead, the **summary** parameter for the model's thinking summary and the **encrypted_content** parameter for the encrypted original thinking content are returned.

> **Tip**

> When thinking summary is enabled, there may be relatively high inter\-packet latency. Please increase the request timeout period (**timeout**) and complete compatibility adaptation.

Parameter explanation:

* **reasoning.effort**: Only applies to the original thinking content of the model, not to the thinking summary.

* **usage.output_tokens_details.reasoning_tokens**: Tokens of the original thinking content. Billing is still calculated based on the tokens of the original thinking content.

### Pass thinking content back

To return the thinking content when calling tools with multiple turns, you can use the following parameters. For an example of passing thinking content back, see [Pass encrypted thinking content back](deep-thinking.md#8cfd447b).

* **previous_response_id** (recommended): Automatically obtains the original thinking content and passes it back to the model for reasoning.

* Manually pass back **encrypted_content**: **Pass back the encrypted thinking content** in the complete format as received. If the content is tampered with, the thinking content cannot be restored.

## Adjust chain\-of\-thought length

Use **reasoning.effort** to adjust the chain\-of\-thought length and balance the requirements for performance, latency, and cost in different scenarios. Valid values are:

* `none`: Turn off reasoning.

* `minimal`: Turns off reasoning and answers directly.

* `low`: Light reasoning, focused on fast responses.

* `medium`: Balanced mode, considering both speed and depth.

* `high`: Deep analysis for handling complex problems.

* `xhigh`: Higher level of reasoning.

* `max`: Maximum level of reasoning, suitable for highly difficult reasoning tasks.

The following table shows the models that support adjusting the chain\-of\-thought length, and the supported values, default values, and compatibility mapping rules for **reasoning.effort** for each model. All models that support this parameter accept all seven values. Some values are automatically mapped to equivalent levels according to the rules in the table.

|Supported models |Default value |Compatibility mapping |
|---|---|---|
|\* `dola-seed-2-1-turbo-260628` | | |
|* `seed-2-0-lite-260428`<br><br>* `seed-2-0-mini-260428`<br><br>* `seed-2-0-pro-260328`<br><br>* `seed-2-0-lite-260228`<br><br>* `seed-2-0-mini-260215`<br><br>* `seed-2-0-code-preview-260328`<br><br>* `seed-1-8-251228`<br><br>* `seed-1-6-250915`<br><br>   `glm-5-3-flash-260828` |`medium`<br><br>`max` |* If `minimal` is passed: Reasoning will be turned off.<br><br>* If `none` is passed: It will be mapped to `minimal`.<br><br>* If `xhigh` / `max` is passed: it will be mapped to `high`.<br><br>* If `none` / `minimal` / `low` is passed: It will be mapped to `low`.<br><br>* If `medium` / `high` is passed: It will be mapped to `high`.<br><br>* If `xhigh` / `max` is passed: It will be mapped to `max`. |
|`glm-5-2-260617` |`high` |* If `none` / `minimal` is passed: Reasoning will be turned off.<br><br>* If `low` / `medium` is passed: It will be mapped to `high`.<br><br>* If `xhigh` is passed: It will be mapped to `max`. |
|* `deepseek-v4-1-flash-261017`<br><br>* `deepseek-v4-1-flash-260910`<br><br>* `deepseek-v4-1-flash-economy-261017` |`high` |* If `none` is passed: Reasoning will be turned off.<br><br>* If `minimal` is passed: It will be mapped to `low`.<br><br>* If `medium` / `xhigh` is passed: It will be mapped to `high`. |
|* `deepseek-v4-pro-ga-260813`<br><br>* `deepseek-v4-flash-ga-260731` |`high` |* If `none` / `minimal` is passed: Reasoning will be turned off.<br><br>* If `medium` is passed: It will be mapped to `low`<br><br>* If `xhigh` is passed: It will be mapped to `high` |
|\* `deepseek-v4-pro-260425` |`high` |\* If `minimal` is passed: Reasoning will be turned off. |

The instructions for **thinking.type** and **reasoning.effort** are as follows:

* When **thinking.type** is set to `enabled`: **reasoning.effort** can be configured. When **reasoning.effort** is set to `minimal`, reasoning is turned off and the model answers directly.

* When **thinking.type** is set to `disabled`: **reasoning.effort** only supports `minimal`. An error is returned when **reasoning.effort** is set to any other value.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
    "input": [
        {
            "role": "user",
            "content": [
                {
                    "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/ark_demo_img_1.png"
                },
                {
                    "type": "input_text",
                    "text": "What do you see?"
                }
            ]
        }
    ],
    "thinking":{"type":"enabled"},
    "reasoning":{"effort": "low"}
}'
```

**Python**

```Python
import os
from arkruntime import Ark

# Get your API key from environment variables. See configuration method at: https://ai.byteplus.com/ark/region:ap-southeast-1/docs/quick-start
api_key = os.getenv('ARK_API_KEY')

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "user",
            "content": [

                {
                    "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/ark_demo_img_1.png"
                },
                {
                    "type": "input_text",
                    "text": "What do you see?"
                },
            ],
        }
    ],
    thinking={"type": "enabled"},
    reasoning={"effort": "low"}
)

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
//Get ARK_API_KEY from environment variables via os.Getenv
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
// Create a context, which is usually used to pass request context information such as timeout, cancellation, etc.
    ctx := context.Background()

    inputMessage := responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role: responses.NewOptMessageRole(responses.MessageRoleUser),
                Content: responses.NewContentItemArrayMessageContent(
                    []responses.ContentItem{
            responses.ContentItem{OneOf: responses.NewContentItemImageContentItemSum(responses.ContentItemImage{Type: responses.ContentItemImageTypeInputImage, ImageURL: responses.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/ark_demo_img_1.png")})},
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "What do you see?"})},
                    },
                ),
            },
        ),
    }

    resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{inputMessage}),
        Thinking:  responses.NewOptThinking(responses.Thinking{Type: responses.NewOptThinkingMode(responses.ThinkingModeEnabled)}),
        Reasoning: responses.NewOptReasoning(responses.Reasoning{Effort: responses.NewOptReasoningEffort(responses.ReasoningEffortLow)}),
    })
    if err != nil {
        fmt.Printf("response error: %v", err)
        return
    }
    fmt.Println(resp)
}
```

**Java**

```Java
package com.ark.example;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
// Create an ArkService instance
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemImage.builder().imageUrl("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/ark_demo_img_1.png").build(), ContentItemText.builder().text("What do you see?").build()))
                        ).build())))
                .thinking(Thinking.builder().type(ThinkingMode.ENABLED).build())
                .reasoning(Reasoning.builder().effort(ReasoningEffort.LOW).build())
                .build();
        Response resp = arkService.createResponse(request);
        System.out.println(resp);

        arkService.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
import os
from openai import OpenAI

# Get your API key from environment variables. See configuration method at: https://ai.byteplus.com/ark/region:ap-southeast-1/docs/quick-start
api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "user",
            "content": [

                {
                    "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/ark_demo_img_1.png"
                },
                {
                    "type": "input_text",
                    "text": "What do you see?"
                },
            ],
        }
    ],
    extra_body={
        "thinking": {"type": "enabled"},
    },
    reasoning={"effort": "low"}
)

print(response)
```

## Set maximum output length

Model output length = Chain\-of\-thought length (if any) + Answer length

Adjust the model's output length by setting the **max_output_tokens** parameter to control costs or generate output of a specified length.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "model": "dola-seed-2-1-turbo-260628",
      "input": "The difference between inference models and non-inference models.",
      "max_output_tokens": 1024
  }'
```

**Python**

```Python
import os
from arkruntime import Ark

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# Create a request
response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="The difference between inference models and non-inference models.",
    max_output_tokens=1024
)

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
        // Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model:    "dola-seed-2-1-turbo-260628",
        Input:    responses.NewStringResponsesInput("The difference between inference models and non-inference models."),
        MaxOutputTokens: responses.NewOptInt64(int64(1024)),
    })
    if err != nil {
        fmt.Printf("response error: %v\n", err)
        return
    }
    fmt.Println(resp)
}
```

**Java**

```Java
package com.ark.example;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        // The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")

                .input(ResponsesInput.ofString("The difference between inference models and non-inference models."))
                .maxOutputTokens(1024L)
                .build();
        Response resp = arkService.createResponse(request);
        System.out.println(resp);
        arkService.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
import os
from openai import OpenAI

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# Create a request
response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="The difference between inference models and non-inference models.",
    max_output_tokens=1024
)

print(response)
```

## How it works

* Multi\-turn conversations

   |Flowchart |Note |
   |---|---|
   |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/advanced-usage/deep-thinking-01.svg) |* During each turn of the conversation, the deep reasoning model outputs chain\-of\-thought (CoT) content and the final answer.<br><br>* In the next conversation turn, the previously output chain\-of\-thought content will not be spliced into the context.<br><br>> The chain\-of\-thought content shows the model's problem\-solving process, including splitting the problem into multiple sub\-problems to process, generating multiple responses and synthesizing a better answer, etc. |

* Tool calling

   After enabling deep reasoning in the tool use scenario, the chain\-of\-thought content will not be discarded directly to provide more detailed and accurate answers. The chain\-of\-thought content from historical turns will participate in inference as needed (judged independently by the model). During the entire request process, users only need to send back the complete context. The server will independently judge whether to retain the chain\-of\-thought content. The chain\-of\-thought content that is not input to the model will not be counted in the token usage. For code samples, see [Pass thinking content back](deep-thinking.md#3e8661f7).

   |Flowchart |Note |
   |---|---|
   |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/advanced-usage/deep-thinking-02.svg) |* When answering question 1 (requests 1.1 \- 1.2), the model gives the answer after multiple thinking processes and tool calls. ModelArk will input the complete context including the chain\-of\-thought content to the model for processing.<br><br>* When starting to answer question 2 (request 2.1), ModelArk will independently judge and delete the chain\-of\-thought in the previous context before inputting it to the model. |

## QPS rate limits

The QPS rate limits for the Responses API are as follows. If you need to increase the rate limit, submit [Ticket](https://console.byteplus.com/workorder/create?step=2&SubProductID=P00001514).

|API name |QPS rate limits per account |
|---|---|
|Create model response |None |
|Query model response |20 |
|List input items |20 |
|Delete model response |20 |
