# Text generation

Generate coherent and clear text content based on the prompt.

> **Tip**

> If you're new to ModelArk, see [Quick start](quick-start.md) to get up and running quickly.

# Quick start

|Input |Output preview |
|---|---|
|```Plain```<br>```Please structure the following content: ModelArk is a large model service platform launched by BytePlus. It provides comprehensive functions and services such as model training, inference, evaluation, and fine-tuning, and focuses on supporting the large model ecosystem. Through stable and reliable secure mutual trust solutions, ModelArk ensures the model security of model providers and the information security of model users, accelerates the adoption of large model capabilities across industries, and helps model providers and users achieve new business growth.```<br> |```Plain```<br>```# Structured information about the ModelArk large model service platform```<br>``````<br>```## I. Basic attributes```<br>```1. **Launched by**: BytePlus```<br>```2. **Platform positioning**: Large model service platform```<br>``````<br>```## II. Core functions and services```<br>```Provides comprehensive large model-related functions and services such as model training, inference, evaluation, and fine-tuning. Core positioning includes:```<br>```1.  Support large model ecosystem development```<br>```2.  Promote the adoption of large model capabilities across industries```<br>``````<br>```## III. Security assurance solution```<br>```Provides two-way information security protection through stable and reliable secure mutual trust solutions:```<br>```1.  Model providers: The platform ensures the security of their models```<br>```2.  Model users: The platform ensures the security of their information```<br>``````<br>```## IV. Value goals```<br>```Help model providers and users achieve new business growth and build a win-win business model in the large model ecosystem.```<br> |

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
    "messages": [
        {"role": "user", "content": "Please structurally organize the following content: ModelArk is a large model service platform launched by BytePlus. It provides comprehensive functions and services such as model training, inference, evaluation, and fine-tuning, and focuses on supporting the large model ecosystem. Through stable and reliable secure mutual trust solutions, ModelArk ensures the model security of model providers and the information security of model users, accelerates the penetration of large model capabilities into various industries, and helps model providers and users achieve new business growth."}
    ],
     "thinking":{
         "type":"disabled"
     }
  }'
```

* Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

**Python**

```Python
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

# Initialize Ark client
client = Ark(
# The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
# Get API Key:https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

completion = client.chat.completions.create(
# Replace with Model ID
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {"role": "user", "content": "Please structurally organize the following content: ModelArk is a large model service platform launched by BytePlus. It provides comprehensive functions and services such as model training, inference, evaluation, and fine-tuning, and focuses on supporting the large model ecosystem. Through stable and reliable secure mutual trust solutions, ModelArk ensures the model security of model providers and the information security of model users, accelerates the penetration of large model capabilities into various industries, and helps model providers and users achieve new business growth."},
    ],
# thinking={"type": "disabled"}, #  Manually disable deep thinking
)
print(completion.choices[0].message.content)
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
        os.Getenv("ARK_API_KEY"),
        // The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )

    ctx := context.Background()
    req := &chat.ChatCompletionRequest{
        // Replace with Model ID
       Model: "dola-seed-2-1-turbo-260628",
       Messages: []chat.ChatCompletionRequestMessage{
userMessage("Please structurally organize the following content: Volcano Ark is a large model service platform launched by Volcano Engine. It provides comprehensive functions and services such as model training, inference, evaluation, and fine-tuning, and focuses on supporting the large model ecosystem. Through stable and reliable secure mutual trust solutions, Volcano Ark ensures the model security of model providers and the information security of model users, accelerates the penetration of large model capabilities into various industries, and helps model providers and users achieve new business growth."),
       },
       Thinking: chat.NewOptThinking(chat.Thinking{Type: chat.NewOptThinkingMode(chat.ThinkingModeDisabled)}),
    }

    resp, err := client.CreateChatCompletion(ctx, req)
    if err != nil {
       fmt.Printf("standard chat error: %v\n", err)
       return
    }
    fmt.Println(resp.Choices[0].Message.Content)
}

func userMessage(content string) chat.ChatCompletionRequestMessage {
    return chat.ChatCompletionRequestMessage{
        OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(
            chat.ChatCompletionRequestUserMessage{
                Role: chat.ChatCompletionRequestUserMessageRoleUser,
                Content: chat.NewOptNilChatCompletionMessageContent(chat.NewStringChatCompletionMessageContent(content)),
            },
        ),
    }
}
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.chat.*;
import com.volcengine.ark.runtime.service.ArkService;
import java.util.ArrayList;
import java.util.List;

public class ChatCompletionsExample {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        // The base URL for model invocation
        ArkService service = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();
        final List<ChatCompletionRequestMessage> messages = new ArrayList<>();

        final ChatCompletionRequestMessage userMessage = ChatCompletionRequestUserMessage.builder().role(ChatCompletionRequestMessageType.USER).content(ChatCompletionMessageContent.ofString("Please structurally organize the following content: ModelArk is a large model service platform launched by BytePlus. It provides comprehensive functions and services such as model training, inference, evaluation, and fine-tuning, and focuses on supporting the large model ecosystem. Through stable and reliable secure mutual trust solutions, ModelArk ensures the model security of model providers and the information security of model users, accelerates the penetration of large model capabilities into various industries, and helps model providers and users achieve new business growth.")).build();
        messages.add(userMessage);

        ChatCompletionRequest chatCompletionRequest = ChatCompletionRequest.builder()
               .model("dola-seed-2-1-turbo-260628")//Replace with Model ID
               .messages(messages)
               // .thinking(Thinking.builder().type(ThinkingMode.DISABLED).build()) // Manually disable deep thinking
               .build();
        service.createChatCompletion(chatCompletionRequest).getChoices().forEach(choice -> System.out.println(choice.getMessage().getContent()));
        // shutdown service
        service.shutdownExecutor();
    }
}
```

> **Tip**

> For an example of using the Responses API to implement a single\-turn conversation, see [Quick start](responses-api-text-generation.md#17377051).

# Models and APIs

Supported models: [Text and code generation](model-list.md#32e9e053)

Supported APIs:

* [Responses API](create-model-responses-api.md): A newly launched API with simplified context management, enhanced tool calling capabilities, and caching capabilities to reduce costs. Recommended for new businesses and users.

* [Chat API](chat-api.md): A widely used API with low migration costs for existing businesses.

# Usage examples

## Multi\-turn conversation

To implement a multi\-turn conversation, combine the conversation history that includes system messages, assistant messages, and user messages into a list, so that the model can understand the context and continue the previous topic for Q&A.

|Input method |Manage context manually |Manage context by ID |
|---|---|---|
|Usage examples |```JSON```<br>```...```<br>```    "model": "dola-seed-2-1-turbo-260628",```<br>```    "messages":[```<br>```        {"role": "user", "content": "Hi, tell a joke."},```<br>```        {"role": "assistant", "content": "Why did the math book look sad? Because it had too many problems! 😄"},```<br>```        {"role": "user", "content": "What's the punchline of this joke?"}```<br>```    ]```<br>```...```<br> |```JSON```<br>```...```<br>```    "model": "dola-seed-2-1-turbo-260628",```<br>```    "previous_response_id":"<id>",```<br>```    "input": "What is the punchline of this joke?"```<br>```...```<br> |
|API |[Chat API](chat-api.md) |[Responses API](create-model-responses-api.md) |

> For more information and complete examples, see [Context management](context-management.md).

## Streaming output

|Demo |Advantages |
|---|---|
|[video](https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/0b0ed47ec1b94b20a4f4966aa80130e6)<br> |* **Improved waiting experience**: You can process in\-progress content immediately without waiting for the full content to be generated.<br><br>* **Real\-time feedback**: In multi\-turn interactions, you can track the current processing stage of the task in real time.<br><br>* **Higher fault tolerance**: If an error occurs midway, you can still obtain the generated content, avoiding the case where non\-streaming output fails and returns nothing.<br><br>* **Simplified timeout management**: Keeps the client–server connection active and reduces timeouts for long\-running or complex tasks. |

Enable streaming output by setting **stream** to `true`.

```JSON
...
    "model": "dola-seed-2-1-turbo-260628",
    "messages": [
        {"role": "user", "content": "Differences between deep reasoning models and non-deep reasoning models"}
    ],
    "stream": true
 ...
```

> For complete examples and more information, see [Streaming output](streaming-output.md).

## Set maximum response length

To control costs or response time, you can limit the model's response length. When the response is long, such as when translating long text, you can set `max_tokens` to a larger value to avoid truncation.

> **Note**

> `deepseek-v4-1-flash-260910`: `max_tokens` controls the length of the model's answer and chain\-of\-thought content. No error is reported if the length is exceeded. The default value is 128k.

```JSON
...
    "model": "dola-seed-2-1-turbo-260628",
    "messages": [
        {"role": "user","content": "What are some common cruciferous plants?"}
    ],
    "max_tokens": 300
...
```

> For the complete code sample, see [Control response length](context-management.md#c7fbdbe3).

## Asynchronous output

For complex tasks or scenarios with multiple concurrent tasks, you can use the Asyncio API to make concurrent calls, improving program efficiency and user experience.

* Chat API code sample:

**Python**

```Python
import asyncio
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import AsyncArk

# Initialize Ark client
client = AsyncArk(
# The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
# Get API Key:https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

async def main() -> None:
    stream = await client.chat.completions.create(
# Replace with Model ID
        model = "dola-seed-2-1-turbo-260628",
        messages=[
            {"role": "system", "content": "You are an AI assistant."},
            {"role": "user", "content": "What are common cruciferous plants?"},
        ],
        stream=True
    )
    async for completion in stream:
        print(completion.choices[0].delta.content, end="")
    print()

if __name__ == "__main__":
    asyncio.run(main())
```

* Responses API code sample:

**Python**

```Python
import asyncio
import os
from arkruntime import AsyncArk
from arkruntime.types.responses.response_completed_event import ResponseCompletedEvent
from arkruntime.types.responses.response_reasoning_summary_text_delta_event import ResponseReasoningSummaryTextDeltaEvent
from arkruntime.types.responses.response_output_item_added_event import ResponseOutputItemAddedEvent
from arkruntime.types.responses.response_text_delta_event import ResponseTextDeltaEvent
from arkruntime.types.responses.response_text_done_event import ResponseTextDoneEvent


client = AsyncArk(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY')
)

async def main():
    stream = await client.responses.create(
        model="dola-seed-2-1-turbo-260628",
        input=[
            {"role": "system", "content": "You are an AI assistant."},
            {"role": "user", "content": "What are common cruciferous plants?"},
        ],
        stream=True
    )
    async for event in stream:
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


if __name__ == "__main__":
    asyncio.run(main())
```

# More usage

## Deep reasoning

Before generating a response, the model first conducts systematic analysis and logical decomposition of the input question, then generates the response based on the decomposition results.

This can significantly improve response quality, but increases token consumption. For details, see [Deep reasoning](deep-thinking.md).

## Prompt engineering

Designing and writing prompts correctly, such as providing instructions, examples, and good specifications, can improve the quality and accuracy of model output. The work of optimizing prompts is also called prompt engineering. For details, see [Prompt engineering](prompt-engineering.md).

## Tool calling

By integrating custom tools or connecting to remote MCP servers, you can extend the model's capabilities so it can better answer questions or perform tasks.

## Prefill\-based response

By prefilling part of the **assistant** role's content, you can guide and control the model to continue generating from existing text chunks, and control the model to maintain consistency in role\-playing scenarios.

* [Prefill-based response](continuation-mode.md): Use the [Chat API](chat-api.md) to implement prefill\-based response.

* [Prefill-based response](responses-api-text-generation.md#a1384090): Use the [Responses API](create-model-responses-api.md) to implement prefill\-based response.

## Structured output (beta)

Control the model to output standard formats that programs can process (mainly JSON) instead of natural language, making standardized processing or display easier.

* [Structured output (beta)](structured-output-beta.md): Use the [Chat API](chat-api.md) to implement structured output.

* [Structured output (beta)](structured-output-beta.md): Use the [Responses API](create-model-responses-api.md) to implement structured output.

## Batch inference

ModelArk provides batch inference capabilities. When you have large\-scale data processing tasks, you can use batch inference to achieve higher throughput and lower costs. For details and usage, see [Batch inference](batch-inference.md).

## Exception handling

Add exception handling for diagnosis.

**Python**

```Python
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark
from arkruntime._exceptions import ArkAPIError

# Initialize the Ark client
client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.getenv('ARK_API_KEY'),
)

# Streaming
try:
    stream = client.chat.completions.create(
    # Replace with Model ID
    model = "dola-seed-2-1-turbo-260628",
        messages=[
            {"role": "system", "content": "You are an AI assistant."},
            {"role": "user", "content": "What are common cruciferous plants?"},
        ],
        stream=True
    )
    for chunk in stream:
        if not chunk.choices:
            continue

        print(chunk.choices[0].delta.content, end="")
    print()
except ArkAPIError as e:
    print(e)
```

**Go**

```Go
package main

import (
    "context"
    "errors"
    "fmt"
    "io"
    "os"
    "github.com/volcengine/ark-runtime-go/arkruntime"
    arkmodel "github.com/volcengine/ark-runtime-go/arkruntime/model"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/chat"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        // The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    fmt.Println("----- streaming request -----")
    req := &chat.ChatCompletionRequest{
        // Replace with Model ID
       Model: "dola-seed-2-1-turbo-260628",
       Messages: []chat.ChatCompletionRequestMessage{
systemMessage("You are an AI assistant."),
userMessage("What are some common cruciferous plants?"),
       },
    }
    stream, err := client.CreateChatCompletionStream(ctx, req)
    if err != nil {
       apiErr := &arkmodel.APIError{}
       if errors.As(err, &apiErr) {
          fmt.Printf("stream chat error: %v\n", apiErr)
       }
       return
    }
    defer stream.Close()

    for {
       recv, err := stream.Recv()
       if err == io.EOF {
          return
       }
       if err != nil {
          apiErr := &arkmodel.APIError{}
          if errors.As(err, &apiErr) {
             fmt.Printf("stream chat error: %v\n", apiErr)
          }
          return
       }

       if len(recv.Choices) > 0 {
          fmt.Print(recv.Choices[0].Delta.Content.Or(""))
       }
    }
}

func systemMessage(content string) chat.ChatCompletionRequestMessage {
    return chat.ChatCompletionRequestMessage{
        OneOf: chat.NewChatCompletionRequestSystemMessageChatCompletionRequestMessageSum(
            chat.ChatCompletionRequestSystemMessage{
                Role: chat.ChatCompletionRequestSystemMessageRoleSystem,
                Content: chat.NewStringChatCompletionMessageContent(content),
            },
        ),
    }
}
func userMessage(content string) chat.ChatCompletionRequestMessage {
    return chat.ChatCompletionRequestMessage{
        OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(
            chat.ChatCompletionRequestUserMessage{
                Role: chat.ChatCompletionRequestUserMessageRoleUser,
                Content: chat.NewOptNilChatCompletionMessageContent(chat.NewStringChatCompletionMessageContent(content)),
            },
        ),
    }
}
```

**Java**

```Java
package com.byteplus.ark.runtime;

import com.volcengine.ark.runtime.models.chat.*;
import com.volcengine.ark.runtime.exception.ArkHttpException;
import com.volcengine.ark.runtime.service.ArkService;
import java.util.ArrayList;
import java.util.List;


public class ChatCompletionsExample {
    public static void main(String[] args) {

        String apiKey = System.getenv("ARK_API_KEY");
// The base URL for model invocation
        ArkService service = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        System.out.println("----- streaming request -----");
        final List<ChatCompletionRequestMessage> streamMessages = new ArrayList<>();
        final ChatCompletionRequestMessage streamSystemMessage = ChatCompletionRequestSystemMessage.builder().role(ChatCompletionRequestMessageType.SYSTEM).content(ChatCompletionMessageContent.ofString("You are an AI assistant.")).build();
        final ChatCompletionRequestMessage streamUserMessage = ChatCompletionRequestUserMessage.builder().role(ChatCompletionRequestMessageType.USER).content(ChatCompletionMessageContent.ofString("What are common cruciferous plants?")).build();
        streamMessages.add(streamSystemMessage);
        streamMessages.add(streamUserMessage);

        ChatCompletionRequest streamChatCompletionRequest = ChatCompletionRequest.builder()
               .model("dola-seed-2-1-turbo-260628")//Replace with Model ID
               .messages(streamMessages)
               .build();

        try {
            service.streamChatCompletion(streamChatCompletionRequest)
                   .doOnError(Throwable::printStackTrace)
                   .blockingForEach(
                            choice -> {
                                if (choice.getChoices().size() > 0) {
                                    System.out.print(choice.getChoices().get(0).getDelta().getContent());
                                }
                            }
                    );
        } catch (ArkHttpException e) {
            System.out.print(e.toString());
        }

// shutdown service
        service.shutdownExecutor();
    }

}
```

## Conversation encryption

In addition to default network\-layer encryption, ModelArk also provides free application\-layer encryption to offer stronger security protection for your inference session data. You only need to add one line of code to enable it. For the complete code sample, see [Encrypt data](sdk-common-examples.md#23274b89); for more information about how it works, see [Inferential session data application layer encryption scheme](application-layer-encryption-for-inference-session-data.md).

# Instructions

* Key model limits:

   * Maximum context length (context window): The length of content that the model can process in a single request, including user input and model output, measured in tokens. If the content exceeds the maximum context length, it will be truncated and output will stop. If content is truncated due to the context limit, you can choose a model that supports a larger context length.

   * Maximum output length (max tokens): The maximum length of content that the model can output in a single request. If this happens, you can see [Prefill-based response](continuation-mode.md) and generate multiple continued responses to assemble the complete content.

   * Tokens processed per minute (TPM): The limit on the amount of content that the same model under an account (regardless of version) can process per minute, measured in tokens. If the default TPM limit cannot meet your business needs, you can contact support ([Ticket](https://console.byteplus.com/workorder/create?step=2&SubProductID=P00001514)) to increase the quota. Example: If a model's TPM is 5 million, all endpoint versions of this model created under the same primary account share this quota.

   * Requests processed per minute (RPM): The maximum number of requests that the same model under an account (regardless of version) can process per minute. This is similar to TPM above. If the default RPM limit cannot meet your business needs, you can contact support ([Ticket](https://console.byteplus.com/workorder/create?step=2&SubProductID=P00001514)) to increase the quota.

   * For detailed specifications of each model, see [Model list](model-list.md).

* Usage query:

   * Token usage for a request: You can view it in the returned **usage** object.

   * Token usage for input/output content: You can use the [Tokenization API](tokenization-api.md) or [Token calculator](https://ai.byteplus.com/ark/region:ap-southeast-1/tokenCalculator) to estimate it.

   * Token usage by account/project/endpoint: You can view it on the [Usage](https://ai.byteplus.com/ark/region:ap-southeast-1/usageTracking) page.

# FAQs

[FAQs](modelark-faq.md) \- [Online inference](modelark-faq.md#aa45e6c0): FAQs about online inference. If you encounter an error, you can try finding a solution here.
