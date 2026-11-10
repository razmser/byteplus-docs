# Context management

Model context includes input information (questions) and output information (answers). When deep reasoning is triggered, the output also includes chain\-of\-thought (CoT) content. The chain\-of\-thought content shows the model's problem\-solving process, including splitting the problem into multiple sub\-problems to process, generating multiple responses and synthesizing a better answer. In multi\-turn conversations, context management is very important for maintaining topic consistency. ModelArk provides several methods for managing context.

# Context input

## Input context manually

[Chat API](chat-api.md) requests are independent and stateless, so you need to manage context manually. You need to alternate user messages and assistant messages so that the model can obtain previous conversation information in the request.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
    "messages": [
        {"role": "user", "content": "Differences Between Deep Reasoning Models and Non-Deep-Reasoning Models."},
        {"role": "assistant", "content": "Reasoning models primarily rely on logic, rules, or probabilistic methods to perform analysis, inference, and judgment in order to reach conclusions or make decisions. In contrast, non-reasoning models mainly use pattern recognition, statistical analysis, or simulation to perform tasks such as data description, classification, clustering, or generation, without relying on explicit logical reasoning."},
        {"role": "user", "content": "I want to study the differences between deep reasoning models and non-deep-reasoning models as a research topic. How can I demonstrate my professional expertise in this area?"}
    ]
  }'
```

* Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

**Python**

```Python
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
    # Deep thinking takes longer; set a larger timeout, with 1,800 seconds or more recommended
    timeout=1800,
)

# Create a chat request
completion = client.chat.completions.create(
    # Replace with Model ID
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {"role": "user", "content": "Study the differences between deep reasoning models and non-deep reasoning models"},
        {"role": "assistant", "content": "Reasoning models mainly rely on logic, rules, probability, and similar methods to analyze, infer, and make judgments in order to reach conclusions or decisions, while non-reasoning models use methods such as pattern recognition, statistical analysis, or simulation to perform tasks such as data description, classification, clustering, or generation without relying on explicit logical reasoning."},
        {"role": "user", "content": "I want to study the differences between deep reasoning models and non-deep reasoning models. How can I demonstrate my expertise?"},
    ],
)

if hasattr(completion.choices[0].message, 'reasoning_content'):
    print(completion.choices[0].message.reasoning_content)
print(completion.choices[0].message.content)
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
    "github.com/volcengine/ark-runtime-go/arkruntime/model/chat"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        //Get ARK_API_KEY from environment variables via os.Getenv
        os.Getenv("ARK_API_KEY"),
        // The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
        //Deep reasoning takes longer; set a larger timeout, with 30 minutes or more recommended.
        arkruntime.WithTimeout(30*time.Minute),
    )
    // Create a context, which is usually used to pass request context information such as timeout, cancellation, etc.
    ctx := context.Background()
    // Construct a chat completion request, and set the model and message content for the request
    req := &chat.ChatCompletionRequest{
        // Replace with Model ID
        Model: "dola-seed-2-1-turbo-260628",
        Messages: []chat.ChatCompletionRequestMessage{
userMessage("Research the differences between deep reasoning models and non-deep reasoning models"),
assistantMessage("Reasoning models mainly rely on logic, rules, probability, and similar methods to analyze, infer, and make judgments in order to reach conclusions or decisions, while non-reasoning models use methods such as pattern recognition, statistical analysis, or simulation to perform tasks such as data description, classification, clustering, or generation without relying on explicit logical reasoning."),
userMessage("I want to research the differences between deep reasoning models and non-deep reasoning models. How can I demonstrate my expertise?"),
        },
    }

    // Send the chat completion request, store the result in resp, and store possible errors in err
    resp, err := client.CreateChatCompletion(ctx, req)
    if err != nil {
        // If an error occurs, print the error message and terminate the program
        fmt.Printf("standard chat error: %v\n", err)
        return
    }
    // Check whether deep reasoning is triggered. If triggered, print the chain-of-thought content.
    if resp.Choices[0].Message.ReasoningContent.IsSet() {
        fmt.Println(resp.Choices[0].Message.ReasoningContent.Or(""))
    }
    // Print the response result of the chat completion request
    fmt.Println(resp.Choices[0].Message.Content)
}

func assistantMessage(content string) chat.ChatCompletionRequestMessage {
    return chat.ChatCompletionRequestMessage{
        OneOf: chat.NewChatCompletionRequestAssistantMessageChatCompletionRequestMessageSum(
            chat.ChatCompletionRequestAssistantMessage{
                Role: chat.ChatCompletionRequestAssistantMessageRoleAssistant,
                Content: chat.NewOptNilChatCompletionMessageContent(chat.NewStringChatCompletionMessageContent(content)),
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
package com.volcengine.ark.runtime;

import com.volcengine.ark.runtime.models.chat.*;
import com.volcengine.ark.runtime.service.ArkService;
import java.time.Duration;
import java.util.Arrays;
import java.util.List;

/*** This is a sample class demonstrating how to use ArkService to complete chat functionality. */
public class ChatCompletionsExample {
    public static void main(String[] args) {
        // Obtain the API key from environment variables
        String apiKey = System.getenv("ARK_API_KEY");
        // Create an ArkService instance
        ArkService arkService = ArkService.builder()
                .apiKey(apiKey)
                .timeout(Duration.ofMinutes(30))// Reasoning takes longer; please set a larger timeout limit, recommended to be 30 minutes or more
                //The base URL for model invocation
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();
        // Multi-turn message list
        final List<ChatCompletionRequestMessage> messages = Arrays.asList(
            ChatCompletionRequestUserMessage.builder().role(ChatCompletionRequestMessageType.USER).content(ChatCompletionMessageContent.ofString("Differences Between Deep Reasoning Models and Non-Deep-Reasoning Models")).build(),
            ChatCompletionRequestAssistantMessage.builder().role(ChatCompletionRequestMessageType.ASSISTANT).content(ChatCompletionMessageContent.ofString("Reasoning models primarily rely on logic, rules, or probabilistic methods to perform analysis, inference, and judgment in order to reach conclusions or make decisions. In contrast, non-reasoning models mainly use pattern recognition, statistical analysis, or simulation to perform tasks such as data description, classification, clustering, or generation, without relying on explicit logical reasoning.")).build(),
            ChatCompletionRequestUserMessage.builder().role(ChatCompletionRequestMessageType.USER).content(ChatCompletionMessageContent.ofString("I want to study the differences between deep reasoning models and non-deep-reasoning models as a research topic. How can I demonstrate my professional expertise in this area?")).build()
        );
        // Create a chat completion request
        ChatCompletionRequest chatCompletionRequest = ChatCompletionRequest.builder()
                .model("dola-seed-2-1-turbo-260628")//Replace with Model ID
                .messages(messages) // Set the message list
                .build();
        // Send the chat completion request and print the response
        try {
            // Obtain the response and print the content of each selected message
            arkService.createChatCompletion(chatCompletionRequest)
                    .getChoices()
                    .forEach(choice -> {
                        // After performing null checks, print the inference content
                        if (choice.getMessage().getReasoningContent() != null) {
                            System.out.println("Reasoning content: " + choice.getMessage().getReasoningContent());
                        } else {
                            System.out.println("Reasoning content is empty");
                        }
                        // Print the message content
                        System.out.println("Message content: " + choice.getMessage().getContent());
                    });
        } catch (Exception e) {
            System.out.println("Request failed: " + e.getMessage());
        } finally {
            // Turn off the service executor
            arkService.shutdownExecutor();
        }
    }
}
```

**OpenAI SDK**

```Python
import os
from openai import OpenAI

client = OpenAI(
    # Read the ModelArk API Key from environment variables
    api_key=os.environ.get("ARK_API_KEY"),
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Reasoning takes longer; to avoid link timeout failures, please set a larger timeout limit, recommended to be 1,800 seconds or more
    timeout=1800,
    )
completion = client.chat.completions.create(
    #Replace with Model ID
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {"role": "user", "content": "Differences Between Deep Reasoning Models and Non-Deep-Reasoning Models"},
        {"role": "assistant", "content": "Reasoning models primarily rely on logic, rules, or probabilistic methods to perform analysis, inference, and judgment in order to reach conclusions or make decisions. In contrast, non-reasoning models mainly use pattern recognition, statistical analysis, or simulation to perform tasks such as data description, classification, clustering, or generation, without relying on explicit logical reasoning."},
        {"role": "user", "content": "I want to study the differences between deep reasoning models and non-deep-reasoning models as a research topic. How can I demonstrate my professional expertise in this area?"},
    ],
)

if hasattr(completion.choices[0].message, 'reasoning_content'):
    print(completion.choices[0].message.reasoning_content)
print(completion.choices[0].message.content)
```

## Input context by ID

[Responses API](create-model-responses-api.md) supports a simpler way to manage context. By default, the input and output of requests are persisted. For subsequent requests, you only need to pass in the ID to include the input and output of the corresponding request.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "model": "dola-seed-2-1-turbo-260628",
      "input": "Hi, tell a joke."    
  }'
```

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "model": "dola-seed-2-1-turbo-260628",
      "previous_response_id":"<id>",
      "input": "What is the punchline of this joke?"    
  }'
```

For the second request, replace `<id>` in the curl command with the Response ID returned by the previous request.

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

# Create the first-round conversation request
response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="Hi, tell a joke."
)
print(response)

# Create the second-round conversation request
second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "What's the punchline of this joke?"}],
)
print(second_response)
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
    // Create the first-round conversation request
    resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",

        Input: responses.NewStringResponsesInput("Hi, tell a joke."),
    })
    if err != nil {
        fmt.Printf("response error: %v\n", err)
        return
    }
    fmt.Println(resp)

    id := resp.ID
    inputMessage := easyMessage(responses.MessageRoleUser, "What's the punchline of this joke?")
    fmt.Println("-----------------")
    // Create the second-round conversation request
    second_resp, second_err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        PreviousResponseID: responses.NewOptString(id),
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            inputMessage,
        }),
    })
    if second_err != nil {
        fmt.Printf("second response error: %v\n", second_err)
        return
    }
    fmt.Println(second_resp)
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
// Create the first-round conversation request
        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")

                .input(ResponsesInput.ofString("Hi, tell a joke."))
                .build();
        Response resp = arkService.createResponse(request);
        System.out.println(resp);
// Create the second-round conversation request
        ResponsesRequest request2 = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .previousResponseId(resp.getId())
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(

                                MessageContent.ofString("What's the punchline of this joke?")
                        ).build())))
                .build();
        Response resp2 = arkService.createResponse(request2);
        System.out.println(resp2);

        arkService.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
from openai import OpenAI
import os

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# Create the first-round conversation request
response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="Hi, tell a joke."
)
print(response)

# Create the second-round conversation request
second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "What's the punchline of this joke?"}],    
)
print(second_response)
```

# Control context length

When you input long content or the number of conversation turns increases, you also need to consider the model output length and context window limits. Model input and output are metered. When the length limit is reached, content is truncated or an error is reported. Control the model output length properly to balance outcome quality, cost, and stability:

* Reduce rate limit triggers (such as TPM limits and burst traffic limits) to ensure service stability.

* Accurately control token usage to balance cost and quality.

* Control inference latency to improve the user interaction experience.

In addition, the platform supports controlling token usage by controlling the length of model output (chain\-of\-thought and answers). The core specifications and parameters are shown in the following figure:

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/1d7c7f33e7b9451893989339e7e7017c~tplv-goo7wpa0wc-image.image)

> The context window, maximum input, and maximum chain\-of\-thought length supported by different models vary. You can query them in the [Model list](model-list.md).

## Control the output (answer + chain\-of\-thought) length

[Chat API](chat-api.md) uses the **max_completion_tokens** parameter to control the model output length. When the model output reaches the configured value, the model stops reasoning.

> [Responses API](create-model-responses-api.md) uses the max_output_tokens parameter to control the model output length. For details, see [Set maximum output length](responses-api-deep-thinking.md#1460ba95).

Supported models: large language models released on 250528 and later. Unless otherwise specified, this parameter is supported by default. For the list of large language models on the ModelArk platform, see [Text and code generation](model-list.md#32e9e053).

> The doubao\-seed\-translation\-250915 model does not support this parameter.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
    "messages": [
        {
            "role": "user",
            "content": "Hello"
        }
    ],
    "max_completion_tokens": 1024
  }'
```

**Python**

```Python
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
    # Deep thinking takes longer; set a larger timeout, with 1,800 seconds or more recommended
    timeout=1800,
)

# Create a chat request
completion = client.chat.completions.create(
    # Replace with Model ID
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {"role": "system", "content": "You are an AI assistant."},
        {"role": "user", "content": "What are common cruciferous plants?"},
    ],
    # Set the model's maximum output length to 1024 tokens. You can adjust it as needed.
    max_completion_tokens = 1024,
)
print(completion.choices[0].message.content)
```

**Go**

```Go
package main

import (
    "time"
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
        //Deep reasoning takes longer; set a larger timeout, with 30 minutes or more recommended.
        arkruntime.WithTimeout(30*time.Minute),
    )

    ctx := context.Background()

    fmt.Println("----- standard request -----")
    req := &chat.ChatCompletionRequest{
        // Replace with Model ID
       Model: "dola-seed-2-1-turbo-260628",
       Messages: []chat.ChatCompletionRequestMessage{
systemMessage("You are an AI assistant."),
userMessage("What are some common cruciferous plants?"),
       },
       MaxCompletionTokens: chat.NewOptInt32(int32(1024)), // Set the maximum output length to 1024 tokens.
    }

    resp, err := client.CreateChatCompletion(ctx, req)
    if err != nil {
       fmt.Printf("standard chat error: %v\n", err)
       return
    }
    fmt.Println(resp.Choices[0].Message.Content)
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
package com.ark.runtime;

import com.volcengine.ark.runtime.models.chat.*;
import com.volcengine.ark.runtime.service.ArkService;
import java.util.ArrayList;
import java.util.List;
import java.time.Duration;

public class ChatCompletionsExample {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        // Create an ArkService instance
        ArkService arkService = ArkService.builder()
                .apiKey(apiKey)
                .timeout(Duration.ofMinutes(30))// Reasoning takes longer; please set a larger timeout limit, recommended to be 30 minutes or more
                //The base URL for model invocation
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();
        System.out.println("\n----- standard request -----");
        final List<ChatCompletionRequestMessage> messages = new ArrayList<>();
        final ChatCompletionRequestMessage systemMessage = ChatCompletionRequestSystemMessage.builder().role(ChatCompletionRequestMessageType.SYSTEM).content(ChatCompletionMessageContent.ofString("You are an AI assistant.")).build();
        final ChatCompletionRequestMessage userMessage = ChatCompletionRequestUserMessage.builder().role(ChatCompletionRequestMessageType.USER).content(ChatCompletionMessageContent.ofString("What are some common cruciferous plants?")).build();
        messages.add(systemMessage);
        messages.add(userMessage);
        ChatCompletionRequest chatCompletionRequest = ChatCompletionRequest.builder()
                .model("dola-seed-2-1-turbo-260628")//Replace with Model ID
                .messages(messages)
                .maxCompletionTokens(1024)// Set the maximum output length to 1,024 tokens
                .build();
        arkService.createChatCompletion(chatCompletionRequest).getChoices().forEach(choice -> System.out.println(choice.getMessage().getContent()));
        // Shut down the service.
        arkService.shutdownExecutor();
    }
}
```

> **Tip**

* > **max_tokens** and **max_completion_tokens** cannot be set at the same time. Otherwise, an error is reported.

## Control response length

[Chat API](chat-api.md) lets you control the model's response length by setting the **max_tokens** parameter. When the response length reaches the configured value, the model stops reasoning.

> **Note**

> `deepseek-v4-1-flash-260910`: `max_tokens` controls the length of the model's answer and chain\-of\-thought content. No error is reported if the length is exceeded. The default value is 128k.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
    "messages": [
        {
            "role": "user",
            "content": "Hello"
        }
    ],
    "max_tokens": 1024
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
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

completion = client.chat.completions.create(
    # Replace with Model ID
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {"role": "system", "content": "You are an AI assistant."},
        {"role": "user", "content": "What are common cruciferous plants?"},
    ],
    # Set the model's maximum output length to 1024 tokens. You can adjust it as needed.
    max_tokens=1024,
)
print(completion.choices[0].message.content)
```

**Go**

```Go
package main

import (
    "context"
    "fmt"
    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/chat"
    "os"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
// The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )

    ctx := context.Background()

    fmt.Println("----- standard request -----")
    req := &chat.ChatCompletionRequest{
// Replace with Model ID
        Model: "dola-seed-2-1-turbo-260628",
        Messages: []chat.ChatCompletionRequestMessage{
            systemMessage("You are an AI assistant."),
            userMessage("What are some common cruciferous plants?"),
        },
        MaxTokens: chat.NewOptInt32(1024),
    }

    resp, err := client.CreateChatCompletion(ctx, req)
    if err != nil {
        fmt.Printf("standard chat error: %v\n", err)
        return
    }
    fmt.Println(resp.Choices[0].Message.Content)
}

func systemMessage(content string) chat.ChatCompletionRequestMessage {
    return chat.ChatCompletionRequestMessage{
        OneOf: chat.NewChatCompletionRequestSystemMessageChatCompletionRequestMessageSum(
            chat.ChatCompletionRequestSystemMessage{
                Role:    chat.ChatCompletionRequestSystemMessageRoleSystem,
                Content: chat.NewStringChatCompletionMessageContent(content),
            },
        ),
    }
}

func userMessage(content string) chat.ChatCompletionRequestMessage {
    return chat.ChatCompletionRequestMessage{
        OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(
            chat.ChatCompletionRequestUserMessage{
                Role:    chat.ChatCompletionRequestUserMessageRoleUser,
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
        //The base URL for model invocation
        ArkService service = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();
        System.out.println("----- standard request -----");
        final List<ChatCompletionRequestMessage> messages = new ArrayList<>();
        final ChatCompletionRequestMessage systemMessage = ChatCompletionRequestSystemMessage.builder().role(ChatCompletionRequestMessageType.SYSTEM).content(ChatCompletionMessageContent.ofString("You are an AI assistant.")).build();
        final ChatCompletionRequestMessage userMessage = ChatCompletionRequestUserMessage.builder().role(ChatCompletionRequestMessageType.USER).content(ChatCompletionMessageContent.ofString("What are some common cruciferous plants?")).build();
        messages.add(systemMessage);
        messages.add(userMessage);

        ChatCompletionRequest chatCompletionRequest = ChatCompletionRequest.builder()
               .model("dola-seed-2-1-turbo-260628")//Replace with Model ID
               .messages(messages)
               .maxTokens(1024)// Set the maximum output length to 1,024 tokens
               .build();
        service.createChatCompletion(chatCompletionRequest).getChoices().forEach(choice -> System.out.println(choice.getMessage().getContent()));
        // Shut down the service.
        service.shutdownExecutor();
    }
}
```

> **Tip**

* > **max_tokens** and **max_completion_tokens** cannot be set at the same time. Otherwise, an error is reported.

* > [Responses API](create-model-responses-api.md) does not support the **max_tokens** parameter.

## Control chain\-of\-thought length

Set **reasoning_effort** to adjust the degree of deep reasoning and indirectly control the length of chain\-of\-thought content. Currently, seven levels are available:

* `none`: Turn off reasoning.

* `minimal`: Turns off reasoning and answers directly.

* `low`: Light reasoning, focused on fast responses.

* `medium`: Balanced mode, considering both speed and depth.

* `high`: Deep analysis for handling complex problems.

* `xhigh`: Higher level of reasoning.

* `max`: Maximum level of reasoning, suitable for highly difficult reasoning tasks.

Relationship with **thinking.type**:

* When **thinking.type** is set to `enabled`: **reasoning_effort** can be configured. When **reasoning_effort** is set to `minimal`, reasoning is turned off and the model answers directly.

* When **thinking.type** is set to `disabled`: **reasoning_effort** only supports `minimal`. An error is returned when **reasoning_effort** is set to any other value.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
     "messages": [
         {
             "role": "user",
             "content": [
                {
                    "image_url": {
                        "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/view.jpeg"
                    },
                    "type": "image_url"
                },
                {
                    "text": "What is in the image",
                    "type": "text"
                }
            ]
         }
     ],
     "thinking":{"type":"enabled"},
     "reasoning_effort": "low"
}'
```

**Python**

```Python
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
    # Deep thinking takes longer; set a larger timeout, with 1,800 seconds or more recommended
    timeout=1800,
)

completion = client.chat.completions.create(
    # Replace with Model ID  .
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {
            "role": "user",
             "content": [
                {"type": "image_url","image_url": {"url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/view.jpeg"}},
                {"type": "text", "text": "What is the image mainly about?"},
            ],
        }
    ],
    thinking={"type":"enabled"},
    reasoning_effort="low"
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
        // Use os.Getenv to obtain ARK_API_KEY from environment variables
        os.Getenv("ARK_API_KEY"),
        //The base URL for model invocation  .
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    // Create a context, usually used to pass contextual information for requests, such as timeout, cancel, and so on
    ctx := context.Background()
    contentParts := []chat.ChatCompletionContentPart{
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartImageChatCompletionContentPartSum(chat.ChatCompletionContentPartImage{Type: chat.ChatCompletionContentPartImageTypeImageURL, ImageURL: chat.NewChatCompletionContentPartImageImageUrlChatCompletionContentPartImageImageURL(chat.ChatCompletionContentPartImageImageUrl{URL: chat.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/view.jpeg")})})},
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "What's in the image"})},
    }
    effort := chat.ReasoningEffortLow
    req := &chat.ChatCompletionRequest{
        //Replace with Model ID
       Model: "dola-seed-2-1-turbo-260628",
       Messages: []chat.ChatCompletionRequestMessage{
chat.ChatCompletionRequestMessage{OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(chat.ChatCompletionRequestUserMessage{Role: chat.ChatCompletionRequestUserMessageRoleUser, Content: chat.NewOptNilChatCompletionMessageContent(chat.NewChatCompletionContentPartArrayChatCompletionMessageContent(contentParts))})},
       },
       Thinking:        chat.NewOptThinking(chat.Thinking{Type: chat.NewOptThinkingMode(chat.ThinkingModeEnabled)}),
        ReasoningEffort: chat.NewOptReasoningEffort(effort),
    }

    // Send the chat completion request, store the result in resp, and store any possible errors in err
    resp, err := client.CreateChatCompletion(ctx, req)
    if err != nil {
       // If an error occurs, print the error message and terminate the program
       fmt.Printf("standard chat error: %v\n", err)
       return
    }
    // Print the response result of the chat completion request
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
  static ArkService arkService = ArkService.builder()
       .dispatcher(dispatcher)
       .connectionPool(connectionPool)
       .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
       // The base URL for model invocation  .
       .apiKey(apiKey)
       .build();

  public static void main(String[] args) throws Exception {

    List<ChatCompletionRequestMessage> messagesForReqList = new ArrayList<>();

    // Construct message content
    List<ChatCompletionContentPart> contentParts = new ArrayList<>();

    // Use builder pattern for the first image
    contentParts.add(ChatCompletionContentPartImage.builder()
         .type(ChatCompletionContentPartType.IMAGE_URL)
.imageUrl(ChatCompletionContentPartImageImageUrl2.ofChatCompletionContentPartImageImageUrl(ChatCompletionContentPartImageImageUrl.builder().url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/view.jpeg").build()))
         .build());

    contentParts.add(ChatCompletionContentPartText.builder()
         .type(ChatCompletionContentPartType.TEXT)
.text("What is the image mainly about?")
         .build());

    messagesForReqList.add(ChatCompletionRequestUserMessage.builder()
         .role(ChatCompletionRequestMessageType.USER)
         .content(ChatCompletionMessageContent.ofList(contentParts))
         .build());

    ChatCompletionRequest req = ChatCompletionRequest.builder()
         .model("dola-seed-2-1-turbo-260628") //Replace with Model ID  .
         .messages(messagesForReqList)
         .thinking(Thinking.builder().type(ThinkingMode.ENABLED).build())
         .reasoningEffort(ReasoningEffort.LOW)
         .build();

    arkService.createChatCompletion(req)
         .getChoices()
         .forEach(choice -> System.out.println(choice.getMessage().getContent()));
    // shutdown service after all requests are finished
    arkService.shutdownExecutor();
  }
}
```

**OpenAI SDK**

```Python
import os
from openai import OpenAI

client = OpenAI(
    #The base URL for model invocation  .
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

completion = client.chat.completions.create(
    #Replace with Model ID  .
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {
            "role": "user",
             "content": [
                {"type": "image_url","image_url": {"url":  "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/view.jpeg"}},
                {"type": "text", "text": "What's in the image"},
            ],
        }
    ],
    extra_body={"thinking":{"type":"enabled"}},
    reasoning_effort="low"
)

print(completion.choices[0])
```

For the complete code samples and usage, see [Adjust chain-of-thought length](responses-api-deep-thinking.md#dc4c1547) (Responses API) and [Adjust chain-of-thought length](deep-thinking.md#fc5eac89) (ChatCompletion API).

# Context passing logic

## Multi\-turn conversations

|Flowchart |Note |
|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/advanced-usage/context-management-02.svg) |* During each turn of the conversation, the deep reasoning model outputs chain\-of\-thought (CoT) content and the final answer.<br><br>* In the next conversation turn, the previously output chain\-of\-thought content will not be spliced into the context.<br><br>> The chain\-of\-thought content shows the model's problem\-solving process, including splitting the problem into multiple sub\-problems to process, generating multiple responses and synthesizing a better answer, etc. |

## Tool calling

The deep reasoning execution logic is optimized for tool calling scenarios. The platform determines on its own whether to input the chain\-of\-thought content to the model so that it participates in reasoning in subsequent turns, ensuring that the output is coherent, accurate, and explainable.

|Flowchart |Note |
|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/advanced-usage/context-management-03.svg) |* When answering question 1 (requests 1.1 \- 1.2), the model gives the answer after multiple thinking processes and tool calls. ModelArk will input the complete context including the chain\-of\-thought content to the model for processing.<br><br>* When starting to answer question 2 (request 2.1), ModelArk will independently judge and delete the chain\-of\-thought in the previous context before inputting it to the model. |

> **Tip**

> It is recommended to use previous_response_id in Responses API, and then ModelArk automatically saves the historical conversation context and passes it back to the inference service during multi\-turn interactions.

Request example:

**Chat API**

**Request of the first turn: trigger tool calling**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "seed-2-0-lite-260228",
    "messages": [
        {
            "role": "system",
            "content": "You are an AI assistant."
        },
        {
            "role": "user",
            "content": "What is the weather like in Beijing today?"
        }
    ],
    "tools": [
        {
            "type": "function",
            "function": {
                "name": "get_weather",
                "description": "Weather query",
                "parameters": {
                    "properties": {
                        "location": {
                            "description": "The location for which to query the weather.",
                            "type": "string"
                        }
                    },
                    "required": [
                        "location"
                    ],
                    "type": "object"
                }
            }
        }
    ]
  }'
```

**Response of the first turn: return the tool call instruction**

The model returns key parameters such as `reasoning_content` and `tool_calls`.

```Plain Text
{
    "choices": [
        {
            "finish_reason": "tool_calls",
            "index": 0,
            "logprobs": null,
            "message": {
                "content": "",
                "reasoning_content": "<the reasoning content from the previous request>",
                "role": "assistant",
                "tool_calls": [
                    {
                        "function": {
                            "arguments": " {\"location\": \"Beijing\"}",
                            "name": "get_weather"
                        },
                        "id": "call_wiezxeyae8jzxl3jx8nhfgb5",
                        "type": "function"
                    }
                ]
            }
        }
    ],
    ...
 }
```

**Request of the second turn: pass back the full context and generate the final response**

The second\-turn request also needs to pass back the chain\-of\-thought information and tool call results, and the model generates a response.

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "seed-2-0-lite-260228",
    "messages": [
        {
            "role": "system",
            "content": "You are an AI assistant."
        },
        {
            "role": "user",
            "content": "What is the weather like in Beijing today?"
        },
        {
            "role": "assistant",
            "reasoning_content": "<the reasoning content from the previous request>",
            "tool_calls": [
                {
                    "function": {
                        "arguments": " {\"location\": \"Beijing\"}",
                        "name": "get_weather"
                    },
                    "id": "call_wiezxeyae8jzxl3jx8nhfgb5",
                    "type": "function"
                }
            ]
        },
        {
            "role": "tool",
            "tool_call_id":"call_wiezxeyae8jzxl3jx8nhfgb5",
            "content": "5 degree"
        }
    ],
    "tools": [
        {
            "type": "function",
            "function": {
                "name": "get_weather",
                "description": "Weather query",
                "parameters": {
                    "properties": {
                        "location": {
                            "description": "The location for which to query the weather.",
                            "type": "string"
                        }
                    },
                    "required": [
                        "location"
                    ],
                    "type": "object"
                }
            }
        }
    ]
  }'
```

**Responses API**

**Request of the first turn: trigger tool calling**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d '{
        "model": "seed-2-0-lite-260228",
        "input": [
            {
                "role": "system",
                "content": "You are an AI assistant."
            },
            {
                "role": "user",
                "content": "What is the weather like in Beijing today?"
            }
        ],
        "thinking":{"type": "enabled"},
        "tools": [
            {
                "type": "function",
                "name": "get_weather",
                "description": "Weather query",
                "parameters": {
                    "type": "object",
                    "properties": {
                        "location": {
                            "type": "string",
                            "description": "The location for which to query the weather.",                    
                        }
                    },
                    "required": ["location"]
                }
            }
        ]
    }'
```

**Response of the first turn: return the tool call instruction**

The information returned by the model contains key parameters such as `id`, `call_id`, and `arguments`.

```Plain Text
{
    "created_at": 1766126702,
    "id": "resp_0217661267019147d8950efa0e2f7c9d9cc7a1cc971272cf4548c",
    "max_output_tokens": 32768,
    "model": "seed-2-0-lite-260228",
    "object": "response",
    "output": [
        {
            "id": "rs_02176612670248500000000000000000000ffffac154e10754f5c",
            "type": "reasoning",
            "summary": [
                {
                    "type": "summary_text",
                    "text": "Got it, let's see. The user is asking about the weather in Beijing today. I need to use the get_weather function here, with location set to \"Beijing\". Let me make sure to format it correctly with the FunctionCallBegin and End tags. Yep, that's straightforward. Alright, time to write the function call."
                }
            ],
            "status": "completed"
        },
        {
            "arguments": " {\"location\": \"Beijing\"}",
            "call_id": "call_t885uulopdd499rn0pioze7l",
            "name": "get_weather",
            "type": "function_call",
            "id": "fc_02176612670345400000000000000000000ffffac154e10a6753e",
            "status": "completed"
        }
    ],
    ....
 }
```

**Request of the second turn: pass back the results and generate the final response**

Pass in information from the previous turn such as the response_id and tool calling results, then the model generates a response in natural language.

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d '{
        "model": "seed-2-0-lite-260228",
        "input": [
            {
                "type": "function_call_output",
                "call_id": "call_t885uulopdd499rn0pioze7l",
                "output": "5 degree"
            }
        ],
        "previous_response_id": "resp_0217661267019147d8950efa0e2f7c9d9cc7a1cc971272cf4548c",
        "thinking":{"type": "enabled"},
        "tools": [
            {
                "type": "function",
                "name": "get_weather",
                "description": "Weather query",
                "parameters": {
                    "type": "object",
                    "properties": {
                        "location": {
                            "type": "string",
                            "description": "The Location for which to query the weather."
                        }
                    },
                    "required": ["location"]
                }
            }
        ]
    }'
```

# Length limit logic

The model output = model response + model chain\-of\-thought (if any).

* **max_tokens**: Controls the length of the model response. The default value is 4096. If a length limit is triggered, the model stops responding, and the **finish_reason** parameter in the returned struct is `length`.

* **max_completion_tokens**: Controls the total length of the model response and model chain\-of\-thought. If the limit is triggered, the model stops responding, and the **finish_reason** parameter in the returned struct is `length`. If **max_completion_tokens** is configured, the default value of max_tokens becomes invalid. It is often used for ultra\-long text output or to control the length returned by the model.

## Configure only max_tokens (default logic)

The length limits for each object are described below.

|Object |Key parameter |Note |Configurable |
|---|---|---|---|
|Problem |Maximum input length |`context window` \- `chain-of-thought window`, shares the quota with the response.<br><br>> This can be understood as the question\-answering quota. |Model specification, not configurable |
|Chain\-of\-thought |Chain\-of\-thought window |Exclusive and not shared. That is, even if there is unused quota in a single request, it is not shared with the question or response. |Model specification, not configurable |
|Response |Maximum response length |Maximum response length, configured by the **max_tokens** parameter.<br><br>In addition, as the input length increases, the response quota is also affected by the remaining quota of **maximum input length (question\-answering quota)** .<br><br>Response quota<br><br>=`min(maximum response length, maximum input length - actual input length)`<br><br>=`min(maximum response length, context window - chain-of-thought window - actual input length)` |API parameter, configurable |
||Maximum input length ||Model specification, not configurable |

Content truncation logic example: model A has the following properties: context window 96k, chain\-of\-thought window 32k, maximum input length (question\-answering quota) 64k, and configured maximum response length 16k.

* When the user input question is 56k, the model outputs a 16k chain\-of\-thought, and the model output response reaches 8k, `question + response = 56k+8k = 64k`, triggering the **maximum input length (question\-answering quota)**  limit, and the model stops reasoning.

* When the user input question is 22k, the model outputs a 16k chain\-of\-thought, and the model output response reaches 16k, the **maximum response length** limit is triggered, and the model stops reasoning.

* When the user input question is 22k and the model outputs a 32k chain\-of\-thought, the **maximum chain\-of\-thought length** limit is triggered, and the model stops reasoning.

## Configure only **max_completion_tokens**

|Affected/controlled object |Key parameter |Note |Configurable |
|---|---|---|---|
|Problem |Maximum input length |Shares the quota with the response; that is, unused quota is shared with the response content. |Model specification, not configurable |
|Chain\-of\-thought |Maximum output length |Shares the quota with the response; that is, unused quota is shared with the response content. |API parameter, configurable |
| |Chain\-of\-thought window |The maximum chain\-of\-thought length that the model can output. |Model specification, not configurable |
|Response |Maximum output length |Shares the quota with the chain\-of\-thought; that is, the output length cannot exceed `maximum output length` \- `length of the chain-of-thought content already output`. |API parameter, configurable |

Content truncation logic example: model A has the following properties: context window 96k, maximum input length 64k, and maximum chain\-of\-thought length 32k. After setting the maximum output length (**max_completion_tokens**) to 32k, the default limit (`4096`) of the maximum response length (**max_tokens**) becomes invalid:

* When the length of the user input content is 72k, the **maximum input length** limit is triggered, and an error is reported directly.

* When the content output by the model exceeds 32k, the **maximum chain\-of\-thought length** limit is triggered, and the model stops reasoning.

* When the length of the user's input question is 26k, the length of the model output chain\-of\-thought is 16k, and the length of the model output response reaches 16k, `chain-of-thought length + response length=16k+16k=32k`, triggering the **maximum output length** limit, and the model stops reasoning.
