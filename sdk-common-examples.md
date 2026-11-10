# Common SDK usage examples

When you use the official SDK, see this guide for typical usage such as setting custom headers.

<span id="243f3c5f"></span>
## Set timeout and retry limits

When your network environment is unstable or the response time is long, you can set the timeout and retry limits to improve service availability.


* Timeout setting (`timeout`): Suitable for scenarios with long response times, such as when deep reasoning models take a long time to answer questions. This helps avoid service interruptions caused by request timeouts. We recommend 30 minutes (1800 seconds).

   * Python SDK: The default connection timeout is 60 seconds (the maximum wait time for establishing a network connection), and the socket timeout is 600 seconds (data transfer timeout after the connection is established).

   * Java SDK: The default connection timeout is 60 seconds, and the socket timeout is 600 seconds.

   * Go SDK: The default end\-to\-end total timeout is 600s (the total timeout from when the request is sent to when the response is received).

* Retry limit (`max_retries`): Suitable for unstable network scenarios. Requests that fail due to transient faults, such as network fluctuations, are automatically retried. The default value is 2, meaning that a failed request is retried 2 more times by default. You can configure it as needed.



<Tabs>
<Tab zoneid="VPlRnbc9M5" title="Python">
<TabTitle>Python</TabTitle>

```Python
import os
# Install the ModelArk SDK via System Command python -m pip install --upgrade arkruntime .
from arkruntime import Ark

client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    # Service response timeout settings, in seconds; recommend 1800 seconds or more
    timeout=1800,
    # Retry count settings
    max_retries=2,
    #The base URL for model invocation .
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)
```



</Tab>
<Tab zoneid="C1Z1lTSAJv" title="Go">
<TabTitle>Go</TabTitle>

```Go
import (
    "os"
    "time"
    "github.com/volcengine/ark-runtime-go/arkruntime"
)

client := arkruntime.NewClientWithApiKey(
    os.Getenv("ARK_API_KEY"),
    arkruntime.WithTimeout(30*time.Minute), // Backend overall timeout settings for completing this request, in minutes; recommend 30 minutes or more
    arkruntime.WithRetryTimes(2), //Set the maximum retry count
    arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"), //The base URL for model invocation .
)
```



</Tab>
<Tab zoneid="mG3q0A69YQ" title="Java">
<TabTitle>Java</TabTitle>

```Java
package com.example;

import com.volcengine.ark.runtime.models.chat.*;
import com.volcengine.ark.runtime.service.ArkService;
import java.time.Duration;
import java.util.ArrayList;
import java.util.List;

public class ChatCompletionsExample {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3") //The base URL for model invocation .
                .apiKey(apiKey)
                .timeout(Duration.ofSeconds(1800))//Set the backend's timeout for completing request processing, in seconds; recommended to be 1800 seconds or more
                .connectTimeout(Duration.ofSeconds(20))//Set the connection timeout to 20 seconds
                .retryTimes(2)//Set the retry count to 2
                .build();
        final List<ChatCompletionRequestMessage> messages = new ArrayList<>();
        final ChatCompletionRequestMessage userMessage = ChatCompletionRequestUserMessage.builder().role(ChatCompletionRequestMessageType.USER).content(ChatCompletionMessageContent.ofString("Hello"))
                .build();
        messages.add(userMessage);

        ChatCompletionRequest chatCompletionRequest = ChatCompletionRequest.builder()
                .model("seed-2-0-lite-260228") //Replace with Model ID .
                .messages(messages)
                .build();

        service.createChatCompletion(chatCompletionRequest).getChoices()
                .forEach(choice -> System.out.println(choice.getMessage().getContent()));
        // Shut down the service.
        service.shutdownExecutor();
    }
}
```



</Tab>
</Tabs>


<span id="c8e7ffaf"></span>
## Set custom headers

The custom `header` parameter uses a key\-value structure and is presented as an object. The following is an example format:

```JSON
{
    "key1": "value1",
    "key2": "value2",
    ...,
    "keyN": "valueN"
}
```


It can be used to pass extra information, such as a configuration ID to correlate logs and enable data encryption.

<span id="7493fbd5"></span>
### Troubleshooting

When making requests, you can set the **key** to `X-Client-Request-Id` for multiple requests, configure the **value** as a custom ID, and provide the ID to the ModelArk support team. This helps correlate client\-side and server\-side logs for troubleshooting.


<Tabs>
<Tab zoneid="HGQ93yiusR" title="Python">
<TabTitle>Python</TabTitle>

```Python
import os
# Install the ModelArk SDK using System Command python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    #The base URL for model invocation .
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)

completion = client.chat.completions.create(
    #Replace with Model ID .
    model = "seed-2-0-lite-260228",
    messages = [
        {"role": "user", "content": "Hello"},
    ],
    # Customize request ID
    extra_headers={"X-Client-Request-Id": "My-Request-Id"}
)
print(completion.choices[0].message.content)
```



</Tab>
<Tab zoneid="TXe8g6ljcs" title="Go">
<TabTitle>Go</TabTitle>

```Go
package main

import (
    "context"
    "fmt"
    "os"
    "github.com/volcengine/ark-runtime-go/arkruntime"
    arkmodel "github.com/volcengine/ark-runtime-go/arkruntime/model"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/chat"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"), // The base URL for model invocation .
    )
    ctx := context.Background()
    req := &chat.ChatCompletionRequest{
        Model: "seed-2-0-lite-260228", //Replace with Model ID .
        Messages: []chat.ChatCompletionRequestMessage{
userMessage("Hello"),
        },
    }


    // Custom request ID
    resp, err := client.CreateChatCompletion(
        ctx,
        req,
        arkruntime.WithCustomHeader(arkmodel.ClientRequestHeader, "my-request-id"), // Custom request header
    )
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



</Tab>
<Tab zoneid="qAJJgijS86" title="Java">
<TabTitle>Java</TabTitle>

```Java
package com.example;

import com.volcengine.ark.runtime.Const;
import com.volcengine.ark.runtime.models.chat.*;
import com.volcengine.ark.runtime.service.ArkService;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

public class ChatCompletionsExample3 {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        //The base URL for model invocation .
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();
        final List<ChatCompletionRequestMessage> messages = new ArrayList<>();
        final ChatCompletionRequestMessage userMessage = ChatCompletionRequestUserMessage.builder().role(ChatCompletionRequestMessageType.USER).content(ChatCompletionMessageContent.ofString("What are common cruciferous plants?")).build();
        messages.add(userMessage);
        ChatCompletionRequest chatCompletionRequest = ChatCompletionRequest.builder()
                .model("seed-2-0-lite-260228") //Replace with Model ID .
                .messages(messages)
                .build();
        // Use custom request headers
        arkService.createChatCompletion(chatCompletionRequest, new HashMap<String, String>(){{put(Const.CLIENT_REQUEST_HEADER, "my_request_id");}}).getChoices().forEach(choice -> System.out.println(choice.getMessage().getContent()));
        // Shut down the service.
        arkService.shutdownExecutor();
    }
}
```



</Tab>
</Tabs>


<span id="23274b89"></span>
### Encrypt data

To ensure the security of inference session data during transmission, you can encrypt session data for online inference. After this capability is enabled, the SDK encrypts the content of inference sessions.


* Supported capabilities:

   * Only requests for text generation models and visual understanding or image understanding models are supported.

   * Only Base64 images can be encrypted. Images in the form of URLs cannot be encrypted.

   * Only [Chat API](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1494384) in the official Python SDK and Java SDK is supported.

   * Single\-turn and multi\-turn sessions are supported, as are streaming and non\-streaming, synchronous and asynchronous calls.



<Tabs>
<Tab zoneid="JRWCDi6vSH" title="Python">
<TabTitle>Python</TabTitle>

```Python
import os
# Install the ModelArk SDK via System Command python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    #The base URL for model invocation .
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)

completion = client.chat.completions.create(
    #Replace with Model ID .
    model = "seed-2-0-lite-260228",
    messages = [
        {"role": "user", "content": "Hello"},
    ],
    # Use the code below for custom header settings to enable inference session application-layer encryption for free
    extra_headers={'x-is-encrypted': 'true'}
)
print(completion.choices[0].message.content)
```



</Tab>
<Tab zoneid="oQ136iC1OG" title="Java">
<TabTitle>Java</TabTitle>

Add the following configuration to the `pom.xml` file:

```XML
<dependencies>
    <!-- BouncyCastle 核心加密库（bcpkix依赖此包） -->
    <dependency>
        <groupId>org.bouncycastle</groupId>
        <artifactId>bcprov-jdk18on</artifactId>
        <version>1.78.1</version>
    </dependency>
    <!-- 包含PEMParser的模块（处理PEM格式证书） -->
    <dependency>
        <groupId>org.bouncycastle</groupId>
        <artifactId>bcpkix-jdk18on</artifactId>
        <version>1.78.1</version>
    </dependency>
</dependencies>
```


The code example is as follows:

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.chat.*;
import com.volcengine.ark.runtime.service.ArkService;
import java.time.Duration;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.HashMap;

public class ChatCompletionsExample {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService service = ArkService.builder()
                .apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .timeout(Duration.ofSeconds(1800)) // Set the backend request processing timeout in seconds, recommended to be 1800 seconds or longer
                .connectTimeout(Duration.ofSeconds(20)) // Set the connection timeout to 20 seconds
                .retryTimes(2)// Set the retry count to 2
                .build();
        final List<ChatCompletionRequestMessage> messages = new ArrayList<>();
        final ChatCompletionRequestMessage userMessage = ChatCompletionRequestUserMessage.builder().role(ChatCompletionRequestMessageType.USER).content(ChatCompletionMessageContent.ofString("Hello"))
                .build();
        messages.add(userMessage);

        ChatCompletionRequest chatCompletionRequest = ChatCompletionRequest.builder()
                .model("seed-2-0-lite-260228") //Replace with Model ID
                .messages(messages)
                .build();

        // Set custom headers with the code below to enable application-layer encryption for inference sessions at no cost
        Map<String, String> customHeaders = new HashMap<>();
        customHeaders.put("x-is-encrypted", "true");
        service.createChatCompletion(chatCompletionRequest, customHeaders).getChoices()
                .forEach(choice -> System.out.println(choice.getMessage().getContent()));
        // shutdown service
        service.shutdownExecutor();
    }
}
```



</Tab>
</Tabs>


<span id="063cdc02"></span>
## Singleton requests

Use a singleton to request the model service. Do not repeatedly create instances, as this causes extra resource consumption.


<Tabs>
<Tab zoneid="iZTZi6QYX7" title="Python">
<TabTitle>Python</TabTitle>

```Python
import os
# Install the ModelArk SDK via System Command python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.environ.get("ARK_API_KEY"),
    #The base URL for model invocation .
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)

# Request 1
completion = client.chat.completions.create(
    model="seed-2-0-lite-260228",
    messages=[
        {"role": "user", "content": "Nice day"},
    ],
)
print(completion.choices[0].message.content)


# Request 2
completion = client.chat.completions.create(
    model="seed-2-0-lite-260228",
    messages=[
        {"role": "user", "content": "Hello"},
    ],
)
print(completion.choices[0].message.content)
```



</Tab>
<Tab zoneid="WPiVQLGLyf" title="Go">
<TabTitle>Go</TabTitle>

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
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"), // The base URL for model invocation .
    )
    ctx := context.Background()
    // First request
    req1 := &chat.ChatCompletionRequest{
       // Replace with Model ID .
       Model: "seed-2-0-lite-260228",
       Messages: []chat.ChatCompletionRequestMessage{
userMessage("Nice day"),
       },
    }

    resp1, err := client.CreateChatCompletion(ctx, req1)
    if err != nil {
       fmt.Printf("standard chat error: %v\n", err)
       return
    }
    fmt.Println(resp1.Choices[0].Message.Content)

    // Second request
    req2 := &chat.ChatCompletionRequest{
       // Replace with Model ID .
       Model: "seed-2-0-lite-260228",
       Messages: []chat.ChatCompletionRequestMessage{
userMessage("Hello"),
       },
    }

    resp2, err := client.CreateChatCompletion(ctx, req2)
    if err != nil {
       fmt.Printf("standard chat error: %v\n", err)
       return
    }
    fmt.Println(resp2.Choices[0].Message.Content)
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



</Tab>
<Tab zoneid="sVPTfh21gI" title="Java">
<TabTitle>Java</TabTitle>

```Java
package com.example;

import com.volcengine.ark.runtime.models.chat.*;
import com.volcengine.ark.runtime.service.ArkService;
import java.util.ArrayList;
import java.util.List;

public class ChatCompletionsExample4 {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        //The base URL for model invocation .
        ArkService service = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        // First request
        final List<ChatCompletionRequestMessage> messages1 = new ArrayList<>();
        final ChatCompletionRequestMessage userMessage1 = ChatCompletionRequestUserMessage.builder().role(ChatCompletionRequestMessageType.USER).content(ChatCompletionMessageContent.ofString("Nice weather today?")).build();
        messages1.add(userMessage1);
        ChatCompletionRequest chatCompletionRequest1 = ChatCompletionRequest.builder()
                .model("seed-2-0-lite-260228")//Replace with Model ID .
                .messages(messages1)
                .build();
        service.createChatCompletion(chatCompletionRequest1).getChoices().forEach(choice -> System.out.println(choice.getMessage().getContent()));
        // Second request
        final List<ChatCompletionRequestMessage> messages2 = new ArrayList<>();
        final ChatCompletionRequestMessage userMessage2 = ChatCompletionRequestUserMessage.builder().role(ChatCompletionRequestMessageType.USER).content(ChatCompletionMessageContent.ofString("Hello")).build();
        messages2.add(userMessage2);
        ChatCompletionRequest chatCompletionRequest2 = ChatCompletionRequest.builder()
                .model("seed-2-0-lite-260228")//Replace with Model ID .
                .messages(messages2)
                .build();
        service.createChatCompletion(chatCompletionRequest2).getChoices().forEach(choice -> System.out.println(choice.getMessage().getContent()));
        // Shut down the service.
        service.shutdownExecutor();
    }
}
```



</Tab>
</Tabs>


<span id="065e9d3e"></span>
## Set base URL

When initializing `Ark` or `AsyncArk` instances, you usually do not need to configure the `base_url` parameter. Use it when you need to connect to a service endpoint in a non\-default region, as shown in the following example.


<Tabs>
<Tab zoneid="oGRIRSD7s7" title="Python">
<TabTitle>Python</TabTitle>

```Python
import os
# Install the ModelArk SDK using a System Command: python -m pip install --upgrade arkruntime .
from arkruntime import Ark

client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    #The base URL for model invocation .
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)
```



</Tab>
<Tab zoneid="SluUTYIjVq" title="Go">
<TabTitle>Go</TabTitle>

```Go
import (
    "os"
    "github.com/volcengine/ark-runtime-go/arkruntime"
)

client := arkruntime.NewClientWithApiKey(
    os.Getenv("ARK_API_KEY"),
    arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"), //The base URL for model invocation .
)
```



</Tab>
<Tab zoneid="sRJTK3m4ic" title="Java">
<TabTitle>Java</TabTitle>

```Java
import com.volcengine.ark.runtime.service.ArkService;

public class CreateArkClientExample {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService service = ArkService.builder()
            .apiKey(apiKey)
            .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
            //The base URL for model invocation .
            .build();
    }

    // do your operation...
}
```



</Tab>
</Tabs>
