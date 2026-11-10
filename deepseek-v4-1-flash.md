# DeepSeek V4.1 Flash

`deepseek-v4-1-flash-260910` is a lightweight flagship model in DeepSeek's new architecture series. With 552B total parameters and MoE, it delivers intelligence beyond its class, outperforming mainstream flagship models including DeepSeek\-V4\-Pro on multiple benchmarks, and natively supports multimodal visual understanding.

> **Important upgrade**

> `deepseek-v4-1-flash-261017` is now available. This version inherits the capabilities of `deepseek-v4-1-flash-260910` on the ModelArk platform, including multimodal understanding, deep reasoning, prefix completion, and context caching.

* [deepseek-v4-1-flash-260910](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?name=deepseek-v4-1-flash): View model information such as capabilities, context and rate limits, and supported APIs.

* [Pricing](model-pricing.md#aa1874cf): View pricing information for peak hours and off\-peak hours.

> **Tip**

> [DeepSeek V4.1 Flash Economy](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?name=deepseek-v4-1-flash-economy) uses exactly the same model weights as the standard edition, with identical capabilities and performance. The economy edition is lower priced, generates responses slightly more slowly, and is suitable for use cases that prioritize cost and do not have high requirements for generation speed.

> **Important**

> The ModelArk `deepseek-v4-1-flash-260910` model has been upgraded to bring more features, and improve the user experience and compatibility. This topic compares the model and API capabilities of ModelArk and DeepSeek to help you make informed decisions.

# What's different

## Capabilities

|Scenario |DeepSeek |ModelArk |
|---|---|---|
|Image understanding |A single image must not exceed `32 MB` |A single image must not exceed `50 MB`, and detail mode (`low`/`high`/`auto`/`xhigh`) is supported.<br><br>> For details, see [Image understanding](deepseek-v4-1-flash.md#multimodal-differences). |
|Video understanding |Not supported |Supported<br><br>A single video file must not exceed `50 MB`. The file ID method is supported.<br><br>> The frame sampling strategy is the same as that of Seed 2.0 and later models. For details, see [Video understanding](deepseek-v4-1-flash.md#video-understanding). |
|Implicit cache |Supported |Supported<br><br>The minimum number of tokens for caching is 256. |
|Explicit cache |Not supported |Supported |
|Prefix completion |Supported<br><br>> Supported in the beta API. |Supported<br><br><br>* Chat API: Aligned with DeepSeek.<br><br>* Responses API: Supported through existing ModelArk API parameters.<br><br>> For details, see [Prefix completion](deepseek-v4-1-flash.md#prefix-continuation). |

## Key API parameters

|Scenario |DeepSeek |ModelArk |
|---|---|---|
|Maximum output length: **max_tokens** |Controls the length of the model response and model chain\-of\-thought content. An error is reported if the length is exceeded. |**Controls the length of the model response and model chain\-of\-thought content.**  No error is reported if the length is exceeded. |
||In the Chat API, the default is 8K in non\-thinking mode and 64K in thinking mode (128K when **reasoning_effort** is `max`). |The default value for the Chat API is 128K. The corresponding parameter for the Responses API is **max_output_tokens**, with a default of 128K. For details, see [Maximum output length](deepseek-v4-1-flash.md#max-tokens-parameter).<br><br>> **Tip**<br><br><br><br>* > `deepseek-v4-1-flash-261017` and `deepseek-v4-1-flash-260910`: **max_tokens** controls the length of the model response and the model chain\-of\-thought content. The default value is 128k. If the chain\-of\-thought content exceeds **max_tokens**, the reasoning is truncated.<br><br><br>* > Other models: **max_tokens** controls the length of the model response, excluding chain\-of\-thought content. The default value is 4k.<br> |
|Adjust reasoning length: **reasoning_effort**/**reasoning.effort**/**output_config.effort** |Chat API / Responses API / Messages API are all supported, with a default value of `high`. |Chat API / Responses API / Messages API are all supported, with a default value of `high`. |
||* OpenAI format / Anthropic format: Valid values are `low`/`high`/`max`.<br><br>* Responses API format: Valid values are `none`/`low`/`high`/`max`. |* Chat API format / Responses API format: Valid values are `none`/`low`/`high`/`max`.<br><br>* Messages API format: Valid values are `low`/`high`/`max`.<br><br>> For details, see [Adjust chain-of-thought length](deepseek-v4-1-flash.md#reasoning-effort). |
|Nucleus sampling threshold: **top_p** |* Thinking mode: The parameter takes effect. If the passed value is `< 0.95`, the system automatically raises it to `0.95`; if the passed value is `≥ 0.95`, the actual passed value is used. If this parameter is not passed, the default value is 1.<br><br>* Non\-thinking mode: The parameter value is fixed at `1.0`, and the passed value is ignored. |* Thinking mode: The parameter takes effect. If the passed value is `< 0.95`, the system automatically raises it to `0.95`; if the passed value is `≥ 0.95`, the actual passed value is used. If this parameter is not passed, the default value is 0.95 when using a tool, and 1 in other cases.<br><br>* Non\-thinking mode: The parameter value is fixed at `1.0`, and the passed value is ignored. |
|Log probabilities: **logprobs** / **top_logprobs** |Supported |Supported<br><br>To ensure consistency with the OpenAI protocol, after **logprobs** is enabled in ModelArk, the API returns the per\-token log probabilities of the model's complete generated sequence in **choices[].logprobs.content**, including chain\-of\-thought content and the final response. |
|Stop sequence: **stop** |Up to `16` strings are supported. |* Chat API default: Up to 4 stop sequences are supported.<br><br>* Chat API for `deepseek-v4-1-flash-261017` and `deepseek-v4-1-flash-260910` model: Up to `16` strings are supported. |
|Whether the value of **role** can be developer |Not supported |Supported |
|**tool_choice** |In thinking mode, **tool_choice** as `required` is not supported, and the API reports an error. |In thinking mode, **tool_choice** as `required` is supported. |
|Parallel tool calls: **parallel_tool_calls** |Not supported. Parallel tool calls are always enabled. |The parameter can be passed normally, but it does not take effect for DeepSeek V4.1 Flash (including the Economy edition): when `false` is passed, the model may still return multiple tool calls at once. |
|Overall compatibility of the Responses API |Weak validation: all unsupported parameters are not validated, and no error will be reported. |Strict validation: unsupported parameters are still validated, and an error is returned.<br><br>> **Tip**<br><br><br>> If the User Agent contains `codex`, weak validation is also used, but **store** is not supported.<br> |

# Model usage notes

## Prefix completion

> For how to configure and use prefill\-based response, see [Prefill-based response](continuation-mode.md#6eb2f599).

The prefix completion behavior supported by `deepseek-v4-1-flash-260910` is as follows:

|API |Chat API |Responses API |
|---|---|---|
|Prefix completion trigger |* The **role** of the last message in the **messages** list is `assistant`.<br><br>* Set the **prefix** parameter of the last message to `true` |* Set the **role** of the last message in the **input** list to assistant.<br><br>* Set **partial** to `true` to enable prefill\-based response (not setting **partial** does not cause an error; it simply does not enable prefill\-based response). |

## Image understanding

> For supported input methods and usages of image understanding, see [Image understanding](image-understanding.md).

The **detail modes and scaling rules** supported by `deepseek-v4-1-flash-260910` for image understanding are as follows:

> The image pixel range in the table refers to the pixel range after the input image is processed according to the detail mode.

|Detail options |Image pixel range |Chat API |Responses API |Messages API |
|---|---|---|---|---|
|low |262144 |The image is automatically scaled while preserving its aspect ratio, and the total pixels after scaling are equivalent to a 512 × 512 image. |The image is automatically scaled while preserving its aspect ratio, and the total pixels after scaling are equivalent to a 512 × 512 image. |* Images whose total pixel count is less than 544 × 544 are enlarged while preserving their aspect ratio, so that the total pixels after enlargement are equivalent to a 544 × 544 image.<br><br>* Images whose total pixel count is greater than 1300 × 1300 are reduced while preserving their aspect ratio, so that the total pixels after reduction are equivalent to a 1300 × 1300 image. |
|high |[295936, 1690000] |* Images whose total pixel count is less than 544 × 544 are enlarged while preserving their aspect ratio, so that the total pixels after enlargement are equivalent to a 544 × 544 image.<br><br>* Images whose total pixel count is greater than 1300 × 1300 are reduced while preserving their aspect ratio, so that the total pixels after reduction are equivalent to a 1300 × 1300 image. |* Images whose total pixel count is less than 544 × 544 are enlarged while preserving their aspect ratio, so that the total pixels after enlargement are equivalent to a 544 × 544 image.<br><br>* Images whose total pixel count is greater than 1300 × 1300 are reduced while preserving their aspect ratio, so that the total pixels after reduction are equivalent to a 1300 × 1300 image. ||
|auto |||||
|xhigh |1690000 |The image is automatically scaled while preserving its aspect ratio, and the total pixels after scaling are equivalent to a 1300 × 1300 image. |The image is automatically scaled while preserving its aspect ratio, and the total pixels after scaling are equivalent to a 1300 × 1300 image. ||

`deepseek-v4-1-flash-260910` has the following **limits** for image understanding:

|Limitations |Requirements |
|---|---|
|Single image size |50 MB |
|Total request body size |64 MB |
|Number of images in a single request |3600 images |
|Total size of image files passed by URL in a single request |200 MB |

## Video understanding

> For supported input methods and usages of video understanding, see [Video understanding](video-understanding.md).

`deepseek-v4-1-flash-260910` uses the same frame sampling strategy as Seed 2.0 and later models in video understanding scenarios, as follows:

|Frame sampling strategy |`deepseek-v4-1-flash-260910` |
|---|---|
|Tokens per frame |Supports continuous dynamic adjustment of tokens within the `[64, 384]` range. The default value is 384.<br><br><br>* The maximum number of tokens for a single frame, used to control the upper limit of single\-frame quality.<br><br>* The minimum number of tokens for a single frame, used to control the lower limit of single\-frame compression. The default value is 64. |
|Maximum pixels per frame |Maximum tokens per frame \* 42 \* 42<br><br>[110K, 670K] |
|Number of sampled frames |* Frame sampling count range: [16 frames, 1280 frames].<br><br><br>```Text```<br>```# Maximum number of sampled frames```<br>```80×1024 tokens ÷ 64 tokens per frame = 1280 frames```<br> |
|Maximum tokens of the video |The default value is 81920 (80 × 1024). |
|Frame sampling scheme |> **Tip**<br><br><br><br>* > It is recommended to evaluate the output quality and adjust the fps parameter configuration or video duration as needed.<br><br><br>* > If the video binary data does not contain frame count encoding information, frames will be evenly sampled according to fps. If the maximum token amount of video in a single request exceeds the model's maximum input length, an error will be reported. If it does not exceed the limit, no error will be reported, but the tokens of the video input may exceed 80K.<br><br><br>* If the fps is too high or the video duration is too long, and the number of processed frame images exceeds 1280 frames, 1280 frames will be evenly sampled at a time interval of `video duration / 1280` with 64 tokens per frame image.<br><br>* If the fps is too low or the video duration is too short, and the number of processed frame images is less than 16, the sampling scheme is as follows:<br><br>   * If the total number of video frames \>= 16, evenly sample 16 frames.<br><br>   * If the total number of video frames < 16, sample all frames of the video. |

**How video understanding works**:

The core method of video processing is "structured concatenation of frames and timestamps", with the following specific rules:

* For each frame image sampled from the video, insert timestamp text before it.

   The timestamp format after processing by `deepseek-v4-1-flash-260910` is `[<timestamp> second]`. For example: `[4.0 second]` and `[4.5 second]`.

* After concatenation, an ordered sequence of "timestamp + image" is formed, and the model understands the temporal logic and content changes of the video through this sequence.

## Pass thinking content back

> For the tutorial on passing thinking content back, see [Best practices of model calling for agents](agent-model-invocation-practice.md).

The DeepSeek V4.1 Flash model supports **passing back the original thinking content** (plaintext). In agent scenarios, you need to add the original thinking content back into the next request.

## Adjust reasoning length: **reasoning_effort**/**reasoning.effort**/**output_config.effort**

> For configuration and usage of adjusting reasoning length in the Chat API and Responses API, see [Adjust chain-of-thought length](deep-thinking.md#fc5eac89).

`deepseek-v4-1-flash-260910` support in the ModelArk API is as follows:

|API |Chat API |Messages API |Responses API |
|---|---|---|---|
|Parameter for adjusting reasoning length |**reasoning_effort** |**output_config.effort** |**reasoning.effort** |
|Default value |`high` |`high` |`high` |
|Compatibility mapping rules |Valid values: `none`, `low`, `high`, `max`.<br><br><br>* If `none` is passed: Reasoning will be turned off.<br><br>* If `minimal` is passed: It will be mapped to `low`.<br><br>* If `medium` / `xhigh` is passed: It will be mapped to `high`.<br><br>* If `ultra` is passed: It will be mapped to `max`. |Valid values: `low`, `high`, `max`.<br><br>> **Note**<br><br><br>> `output_config.effort` takes effect only when `thinking.type` is `adaptive`.<br><br><br><br>* If `minimal` is passed: It will be mapped to `low`.<br><br>* If `medium` / `xhigh` is passed: It will be mapped to `high`.<br><br>* If `ultra` is passed: It will be mapped to `max`. |Valid values: `none`, `low`, `high`, `max`.<br><br><br>* If `none` is passed: Reasoning will be turned off.<br><br>* If `minimal` is passed: It will be mapped to `low`.<br><br>* If `medium` / `xhigh` is passed: It will be mapped to `high`. |

## Maximum output length: **max_tokens**/**max_completion_tokens**/**max_output_tokens**

> For the general configuration method for maximum output length, see [Set maximum output length](deep-thinking.md#cfc7c5a8).

`deepseek-v4-1-flash-260910` support for maximum output length parameters is as follows:

|API |Chat API |Responses API |
|---|---|---|
|Parameter |**max_tokens**/**max_completion_tokens** |**max_output_tokens** |
|Rules |**Controls the length of the model response and model chain\-of\-thought content.**  No error is reported if the length is exceeded.<br><br>> **Note**<br><br><br>> If the chain\-of\-thought content exceeds **max_tokens**, the reasoning will be truncated.<br> |Controls the length of the model response and model chain\-of\-thought content. No error is reported if the length is exceeded. |
|Default value |128K |128K |

# Code samples

Chat API code samples:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
   -H "Content-Type: application/json" \
   -H "Authorization: Bearer $ARK_API_KEY" \
   -d '{
    "model": "deepseek-v4-1-flash-260910",
    "messages": [
        {
            "role": "user",
            "content": [
                {"type": "image_url","image_url": {"url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/ModelArk_demo_img_1.jpg"}},
                {"type": "text", "text": "Which model series supports image input?"}
            ]
        }
    ]
  }'
```

**Python**

```Python
import os
# Install SDK: pip install arkruntime
from arkruntime import Ark

client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

completion = client.chat.completions.create(
    # Replace with Model ID
    model = "deepseek-v4-1-flash-260910",
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "image_url","image_url": {"url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/ModelArk_demo_img_1.jpg"}},
                {"type": "text", "text": "Which model series supports image input?"},
            ],
        }
    ],
)

print(completion.choices[0])
```

**Go**

```Go
package main

import (
    "context"
    "fmt"
    "os"
    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/chat"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        //Use os.Getenv to get ARK_API_KEY
        os.Getenv("ARK_API_KEY"),
        // The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    // Create a context background
    ctx := context.Background()
    // Construct the content of the message
    contentParts := []chat.ChatCompletionContentPart{
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartImageChatCompletionContentPartSum(chat.ChatCompletionContentPartImage{Type: chat.ChatCompletionContentPartImageTypeImageURL, ImageURL: chat.ChatCompletionContentPartImageImageUrl{URL: chat.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/ModelArk_demo_img_1.jpg")}})},
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "Which model series supports image input?"})},
    }
    // Construct chat, specify model and message
    req := &chat.ChatCompletionRequest{
        // Replace with Model ID
       Model: "deepseek-v4-1-flash-260910",
       Messages: []chat.ChatCompletionRequestMessage{
chat.ChatCompletionRequestMessage{OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(chat.ChatCompletionRequestUserMessage{Role: chat.ChatCompletionRequestUserMessageRoleUser, Content: chat.NewChatCompletionContentPartArrayChatCompletionMessageContent(contentParts)})},
       },
    }

    // Send chat, store result in resp and any possible error in err
    resp, err := client.CreateChatCompletion(ctx, req)
    if err!= nil {
       fmt.Printf("standard chat error: %v\n", err)
       return
    }
    // Print response
    fmt.Println(resp.Choices[0].Message.Content)
}
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.chat.*;
import com.volcengine.ark.runtime.models.chat.ChatCompletionContentPart.*;
import com.volcengine.ark.runtime.service.ArkService;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

public class MultiImageSample {
  static String apiKey = System.getenv("ARK_API_KEY");
  static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
  static Dispatcher dispatcher = new Dispatcher();
  static ArkService service = ArkService.builder()
       .dispatcher(dispatcher)
       .connectionPool(connectionPool)
       .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
       // The base URL for model invocation  .
       .apiKey(apiKey)
       .build();

  public static void main(String[] args) throws Exception {

    List<ChatCompletionRequestMessage> messagesForReqList = new ArrayList<>();

    // Construct the content of the message
    List<ChatCompletionContentPart> contentParts = new ArrayList<>();

    // Use builder mode for the image
    contentParts.add(ChatCompletionContentPartImage.builder()
         .type(ChatCompletionContentPartType.IMAGE_URL)
         .imageUrl(ChatCompletionContentPartImageImageUrl.builder().url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/ModelArk_demo_img_1.jpg").build())


         .build());

    // Use builder mode for text
    contentParts.add(ChatCompletionContentPartText.builder()
         .type(ChatCompletionContentPartType.TEXT)
         .text("Which model series supports image input?")
         .build());

    // Create message
    messagesForReqList.add(ChatCompletionRequestUserMessage.builder()
         .role(ChatCompletionRequestMessageType.USER)
         .content(ChatCompletionMessageContent.ofList(contentParts))
         .build());

    ChatCompletionRequest req = ChatCompletionRequest.builder()
         .model("deepseek-v4-1-flash-260910") //Replace with Model ID  .
         .messages(messagesForReqList)
         .build();

    service.createChatCompletion(req)
         .getChoices()
         .forEach(choice -> System.out.println(choice.getMessage().getContent()));
    // shutdown service after all requests are finished
    service.shutdownExecutor();
  }
}
```
