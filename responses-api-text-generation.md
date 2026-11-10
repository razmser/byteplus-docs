# Text generation (Responses API)

Text generation models can generate coherent and clear text content based on the prompt.

The Responses API supports a more concise input and output format and enables more efficient context management. We recommend using it in your project.

> **Tip**

> If you're new to ModelArk, see [Quick start](quick-start.md) to get up and running quickly.

## Supported models

For large language models of version 250615 and later, the Responses API is supported by default unless otherwise specified. For more information, see [Text generation](model-list.md#32e9e053).

## Quick start

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "model": "dola-seed-2-1-turbo-260628",
      "input": "hello"
  }'
```

**Python**

```Python
import os
from arkruntime import Ark

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY'),
)

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="hello"
)

for item in response.output:
    if item.type == "message":
        for content in item.content:
            if content.type == "output_text":
                print(content.text)
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
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewStringResponsesInput("hello"),
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
        //The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofString("hello"))
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

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY'),
)

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="hello"
)

for item in response.output:
    if item.type == "message":
        for content in item.content:
            if content.type == "output_text":
                print(content.text)
```

## API documentation

[Responses API](create-model-responses-api.md)

## Context management

Context state management is easy. By default, the input and response for each request (excluding chain\-of\-thought content) are stored persistently. For subsequent requests, you only need to pass in the ID to include the input and response of the corresponding request. Unlike the Chat API, you do not need to pass in the conversation history for every request. For details, see [How it works and instructions](responses-api-text-generation.md#bdafc901).

### Multi\-turn conversation

In multi\-turn invocation mode, the system can automatically manage context, continuously track and remember previous conversation content, make the conversation more coherent and natural, and improve the intelligent interaction experience.

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

### Regenerate a conversation

The code sample below shows the tree\-like branching structure for regenerating conversations in the Responses API by flexibly calling previous_response_id, and shows how to perform different operations in different branches. In conversation regeneration scenarios, the system can flexibly perform multiple operations based on different branch conditions, enabling more complex conversation logic.

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
    input=[{"role": "user", "content": "Do you know the principle of cosine similarity?"}],
    store=True,  # store the conversation
)
print(response)

# Create the second-round conversation request
second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "I hope you can explain this problem in a way that even elementary school students can understand"}],
    store=True,
)
print(second_response)

# Recreate the second-round conversation request
re_second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "I hope you can explain this problem using a professor's thinking logic"}],
    store=True,
)
print(re_second_response)
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
        Input: responses.NewStringResponsesInput("Do you know the principle of cosine similarity?"),
        Store: responses.NewOptBool(true),
    })
    if err != nil {
        fmt.Printf("response error: %v\n", err)
        return
    }
    fmt.Println(resp)

    fmt.Println("-----------------")
// Create the second-round conversation request
    second_resp, second_err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model:              "dola-seed-2-1-turbo-260628",
        PreviousResponseID: responses.NewOptString(resp.ID),
        Input: responses.NewStringResponsesInput("Explain this in a way that an elementary school student can understand."),
        Store: responses.NewOptBool(true),
    })
    if second_err != nil {
        fmt.Printf("second response error: %v\n", second_err)
        return
    }
    fmt.Println(second_resp)
    fmt.Println("-----------------")
// Recreate the second-round conversation request
    re_second_resp, re_second_err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model:              "dola-seed-2-1-turbo-260628",
        PreviousResponseID: responses.NewOptString(resp.ID),
        Input: responses.NewStringResponsesInput("Explain this problem using a professor's reasoning."),
        Store: responses.NewOptBool(true),
    })
    if re_second_err != nil {
        fmt.Printf("reSecond response error: %v\n", re_second_err)
        return
    }
    fmt.Println(re_second_resp)
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
        //The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();
        // Create the first-round conversation request
        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(

                                 MessageContent.ofString("Do you know the principle of cosine similarity?")
                        ).build())))
                .store(true)
                .build();
        Response resp = arkService.createResponse(request);
        System.out.println(resp);
        // Create the second-round conversation request
        ResponsesRequest request2 = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .previousResponseId(resp.getId())
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(

                                MessageContent.ofString("I hope you can explain this problem in a way that even elementary school students can understand")
                        ).build())))
                .build();
        Response resp2 = arkService.createResponse(request2);
        System.out.println(resp2);
        // Recreate the second-round conversation request
        ResponsesRequest reRequest2 = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .previousResponseId(resp.getId())
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(

                                MessageContent.ofString("I hope you can explain this problem using a professor's thinking logic")
                        ).build())))
                .build();
        Response reResp2 = arkService.createResponse(reRequest2);
        System.out.println(reResp2);

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
    input=[{"role": "user", "content": "Do you know the principle of cosine similarity?"}],
    store=True,  # Set to True to store the conversation
)
print(response)

# Create the second-round conversation request
second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "I hope you can explain this problem in a way that even elementary school students can understand"}],
    store=True,
)
print(second_response)

# Recreate the second-round conversation request
re_second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "I hope you can explain this problem using a professor's thinking logic"}],
    store=True,
)
print(re_second_response)
```

### Window truncation

The code sample below shows how to use the delete API to implement the window truncation feature of the Responses API, allowing the program to manage historical memory at the response granularity for more complex conversations later.

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

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="Tell a pun joke.",
)
print(response)

second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "Tell a philosophical joke"}],
)
print(second_response)

third_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=second_response.id,
    input=[{"role": "user", "content": "Tell a cold joke"}],
)
print(third_response)

deleting_response = client.responses.delete(second_response.id)
print(deleting_response)

fourth_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=third_response.id,
    input=[{"role": "user", "content": "How many jokes did you just tell? What were they all about?"}],
)
print(fourth_response)
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
        Model: "dola-seed-2-1-turbo-260628",

        Input: responses.NewStringResponsesInput("Tell a pun joke."),
    })
    if err != nil {
        fmt.Printf("response error: %v\n", err)
        return
    }
    fmt.Println(resp)

    second_resp, second_err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model:              "dola-seed-2-1-turbo-260628",
        PreviousResponseID: responses.NewOptString(resp.ID),
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            easyMessage(responses.MessageRoleUser, "Tell a philosophical joke"),
        }),
    })
    if second_err != nil {
        fmt.Printf("second response error: %v\n", second_err)
        return
    }
    fmt.Println(second_resp)

    third_resp, third_err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model:              "dola-seed-2-1-turbo-260628",
        PreviousResponseID: responses.NewOptString(second_resp.ID),
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            easyMessage(responses.MessageRoleUser, "Tell a cold joke"),
        }),
    })
    if third_err != nil {
        fmt.Printf("second response error: %v\n", third_err)
        return
    }
    fmt.Println(third_resp)

    deleting_resp := client.DeleteResponse(ctx, &responses.ResponsesRemoveParams{ResponseId: second_resp.ID})
    fmt.Println(deleting_resp)

    forth_resp, forth_err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model:              "dola-seed-2-1-turbo-260628",
        PreviousResponseID: responses.NewOptString(third_resp.ID),
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            easyMessage(responses.MessageRoleUser, "How many jokes did you just tell? What were they all about?"),
        }),
    })
    if forth_err != nil {
        fmt.Printf("second response error: %v\n", forth_err)
        return
    }
    fmt.Println(forth_resp)
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
        //The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")

                .input(ResponsesInput.ofString("Tell a pun joke."))
                .build();
        Response resp = arkService.createResponse(request);
        System.out.println(resp);
        System.out.println("---------------------");
        ResponsesRequest request2 = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .previousResponseId(resp.getId())
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(

                                MessageContent.ofString("Tell me a philosophical joke")
                        ).build())))
                .build();
        Response resp2 = arkService.createResponse(request2);
        System.out.println(resp2);
        System.out.println("---------------------");
        ResponsesRequest request3 = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .previousResponseId(resp2.getId())
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(

                                MessageContent.ofString("Tell a cold joke")
                        ).build())))
                .build();
        Response resp3 = arkService.createResponse(request3);
        System.out.println(resp3);
        System.out.println("---------------------");
        DeleteResponseResponse deleteResp = arkService.deleteResponse(
                DeleteResponseRequest.builder().responseId(resp2.getId()).build()
        );
        System.out.println(deleteResp);
        System.out.println("---------------------");
        ResponsesRequest request4 = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .previousResponseId(resp3.getId())
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(

                                MessageContent.ofString("How many jokes did you just tell? What were they all about?")
                        ).build())))
                .build();
        Response resp4 = arkService.createResponse(request4);
        System.out.println(resp4);

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

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="Tell a pun joke.",
)
print(response)

second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "Tell a philosophical joke"}],
)
print(second_response)

third_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=second_response.id,
    input=[{"role": "user", "content": "Tell a cold joke"}],
)
print(third_response)

deleting_response = client.responses.delete(second_response.id)
print(deleting_response)

fourth_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=third_response.id,
    input=[{"role": "user", "content": "How many jokes did you just tell? What were they all about?"}],
)
print(fourth_response)
```

## Streaming output

Streaming output supports dynamic real\-time content presentation. It can not only ease users' waiting anxiety, but also avoid client timeout failures caused by long inference time for complex tasks, ensuring the smooth request process.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "model": "dola-seed-2-1-turbo-260628",
      "input": "What are the common cruciferous plants?",
      "thinking":{"type": "enabled"},
      "stream": true
  }'
```

Output example:

```Text
event: response.created
data: {"type":"response.created","response":{"created_at":1765508936,"id":"resp_021765508935818e7a9c000dd92b8b7b879cbc284c59191ff1d92","max_output_tokens":32768,"model":"seed-2-0-lite-260228","object":"response","thinking":{"type":"enabled"},"service_tier":"default","caching":{"type":"disabled"},"store":true,"expire_at":1765768135},"sequence_number":0}

event: response.in_progress
data: {"type":"response.in_progress","response":{"created_at":1765508936,"id":"resp_021765508935818e7a9c000dd92b8b7b879cbc284c59191ff1d92","max_output_tokens":32768,"model":"seed-2-0-lite-260228","object":"response","thinking":{"type":"enabled"},"service_tier":"default","caching":{"type":"disabled"},"store":true,"expire_at":1765768135},"sequence_number":1}

event: response.output_item.added
data: {"type":"response.output_item.added","output_index":0,"item":{"id":"rs_02176550893661800000000000000000000ffffac150a0f16e055","type":"reasoning","status":"in_progress"},"sequence_number":2}

event: response.reasoning_summary_part.added
data: {"type":"response.reasoning_summary_part.added","item_id":"rs_02176550893661800000000000000000000ffffac150a0f16e055","output_index":0,"summary_index":0,"part":{"type":"summary_text"},"sequence_number":3}

event: response.reasoning_summary_text.delta
data: {"type":"response.reasoning_summary_text.delta","summary_index":0,"delta":"\n","item_id":"rs_02176550893661800000000000000000000ffffac150a0f16e055","output_index":0,"sequence_number":4}

event: response.reasoning_summary_text.delta
data: {"type":"response.reasoning_summary_text.delta","summary_index":0,"delta":"Got","item_id":"rs_02176550893661800000000000000000000ffffac150a0f16e055","output_index":0,"sequence_number":5}

event: response.reasoning_summary_text.delta
data: {"type":"response.reasoning_summary_text.delta","summary_index":0,"delta":" it","item_id":"rs_02176550893661800000000000000000000ffffac150a0f16e055","output_index":0,"sequence_number":6}

event: response.reasoning_summary_text.delta
data: {"type":"response.reasoning_summary_text.delta","summary_index":0,"delta":",","item_id":"rs_02176550893661800000000000000000000ffffac150a0f16e055","output_index":0,"sequence_number":7}

event: response.reasoning_summary_text.delta
data: {"type":"response.reasoning_summary_text.delta","summary_index":0,"delta":" let","item_id":"rs_02176550893661800000000000000000000ffffac150a0f16e055","output_index":0,"sequence_number":8}
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
    input="What are the common cruciferous plants?",
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

```Text

outPutItem response.output_item.added start:
Got it, let's tackle this question: "What are the common cruciferous plants?" First, I need to remember what cruciferous plants are—they belong to the Brassicaceae family (formerly Cruciferae), named after their four-petaled flowers that look like a cross.
...
1. Leafy greens: Kale, spinach? Wait no, spinach isn't cruciferous. Oh right, kale is, collard greens, mustard greens, arugula (rocket), watercress, bok choy (pak choi), Swiss chard? No, Swiss chard is Amaranthaceae. Wait, bok choy is Brassica rapa, yes. Also, Brussels sprouts—those are small cabbages.
...
That should cover the common ones. Let me check if any are missing—maybe kohlrabi is included, yes. Also, make sure the categories are logical. This list is comprehensive but not too long, focusing on the ones people are likely to encounter.
outPutItem response.output_item.added start:
Cruciferous plants belong to the **Brassicaceae family** (formerly Cruciferae), named for their distinctive four-petaled flowers that form a cross shape. They are widely valued for their nutritional richness (high in fiber, vitamins C/K/A, and glucosinolates—compounds linked to antioxidant and anti-inflammatory benefits). Below are common examples, grouped by type:
...
**Answer:** Common cruciferous plants include broccoli, cauliflower, cabbage, Brussels sprouts, kale, collard greens, bok choy, radish, turnip, kohlrabi, arugula, watercress, mustard plants, and rapeseed (canola). They are known for their cross-shaped flowers and high nutritional value.
\boxed{Broccoli, cauliflower, cabbage, Brussels sprouts, kale, radish, bok choy, turnip}
outPutTextDone.
Response Completed. Usage = {"input_tokens":41,"input_tokens_details":{"cached_tokens":0},"output_tokens":2204,"output_tokens_details":{"reasoning_tokens":1433},"total_tokens":2245,"tool_usage":null,"tool_usage_details":null}
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

```Text
Got it, let's tackle this question: "What are the common cruciferous plants?" First, I need to remember what cruciferous plants are—they belong to the Brassicaceae family (formerly Cruciferae), named after their four-petaled flowers that look like a cross.
...
Many of these are rich in vitamins (C, K), fiber, and compounds like glucosinolates, which are linked to potential health benefits.
Aggregated reasoning text:
Cruciferous plants belong to the **Brassicaceae family** (formerly called Cruciferae), named for their distinctive four-petaled flowers that form a cross shape. They are widely cultivated for food, with many common vegetables falling into this category. Below are some of the most familiar examples, grouped by type:
...
Aggregated output text: Cruciferous plants belong to the **Brassicaceae family** (formerly called Cruciferae), named for their distinctive four-petaled flowers that form a cross shape. They are widely cultivated for food, with many common vegetables falling into this category. Below are some of the most familiar examples, grouped by type:

### **1. Floret/Head Vegetables**
These are characterized by dense flower heads or layered leaves:
- **Broccoli**: Green, tree-like flower buds on thick stalks.
...
**Answer:** Common cruciferous plants include broccoli, cauliflower, cabbage, Brussels sprouts, kale, bok choy, arugula, radish, turnip, rutabaga, horseradish, kohlrabi, watercress, and mustard plants. These belong to the Brassicaceae family, named for their cross-shaped flowers, and are valued for their nutritional content.
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

                .input(ResponsesInput.ofString("What are the common cruciferous plants?"))
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

```Text
OutputItem reasoning Start:
Got it, let's tackle this question: "What are the common cruciferous plants?" First, I need to remember what cruciferous plants are—they belong to the Brassicaceae family (formerly Cruciferae), named after their four-petaled flowers that look like a cross.
...
OutputItem message Start:
Cruciferous plants belong to the **Brassicaceae family** (formerly called Cruciferae), named for their distinctive four-petaled flowers that form a cross shape. They are widely cultivated for food, known for their nutrient density (rich in fiber, vitamins C/K/A, and glucosinolates—compounds linked to potential health benefits). Common examples include:
...
\boxed{Broccoli, cauliflower, cabbage, kale, Brussels sprouts, bok choy, radish, turnip, arugula, watercress, kohlrabi, mustard greens, horseradish, wasabi, rapeseed}OutputText End.
OutputItem message End.
Response Completed. Usage = Usage{inputTokens=41, outputTokens=1315, totalTokens=1356, inputTokensDetails=InputTokensDetails{cachedTokens=0}, outputTokensDetails=com.volcengine.ark.runtime.model.responses.usage.OutputTokensDetails@6892b3b6, toolUsage=null, toolUsageDetails=null}
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
    input="What are the common cruciferous plants?",
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

```Text

outPutItem response.output_item.added start:
Got it, let's tackle this question: "What are the common cruciferous plants?" First, I need to remember what cruciferous plants are—they belong to the Brassicaceae family (formerly Cruciferae), named after their four-petaled flowers that look like a cross.
...
1. Leafy greens: Kale, spinach? Wait no, spinach isn't cruciferous. Oh right, kale is, collard greens, mustard greens, arugula (rocket), watercress, bok choy (pak choi), Swiss chard? No, Swiss chard is Amaranthaceae. Wait, bok choy is Brassica rapa, yes. Also, Brussels sprouts—those are small cabbages.
...
That should cover the common ones. Let me check if any are missing—maybe kohlrabi is included, yes. Also, make sure the categories are logical. This list is comprehensive but not too long, focusing on the ones people are likely to encounter.
outPutItem response.output_item.added start:
Cruciferous plants belong to the **Brassicaceae family** (formerly Cruciferae), named for their distinctive four-petaled flowers that form a cross shape. They are widely valued for their nutritional richness (high in fiber, vitamins C/K/A, and glucosinolates—compounds linked to antioxidant and anti-inflammatory benefits). Below are common examples, grouped by type:
...
**Answer:** Common cruciferous plants include broccoli, cauliflower, cabbage, Brussels sprouts, kale, collard greens, bok choy, radish, turnip, kohlrabi, arugula, watercress, mustard plants, and rapeseed (canola). They are known for their cross-shaped flowers and high nutritional value.
\boxed{Broccoli, cauliflower, cabbage, Brussels sprouts, kale, radish, bok choy, turnip}
outPutTextDone.
Response Completed. Usage = {"input_tokens":41,"input_tokens_details":{"cached_tokens":0},"output_tokens":2204,"output_tokens_details":{"reasoning_tokens":1433},"total_tokens":2245,"tool_usage":null,"tool_usage_details":null}
```

## Supplement system prompts

In the Responses API, the instructions parameter is used to supplement system prompts in a specific turn. Its core mechanism is to add a system prompt message to the beginning of the conversation context, enabling more flexible output.

> After the instructions parameter is configured, you cannot write to or use the cache. That is, you cannot set the **caching** parameter to {"type": "enabled"}, and cache hits are not possible.

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

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "system",
            "content": "You are a math teacher and able to clearly explain the corresponding math problems."
        },
    ]
)
print(response)

second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "Please explain the principle of cosine similarity"}],
)
print(second_response)

third_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    instructions="Add a requirement: I hope you can explain this problem in a way that elementary school students can understand.",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "Please explain the principle of cosine similarity"}],
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
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            easyMessage(responses.MessageRoleSystem, "You are a math teacher and able to clearly explain the corresponding math problems."),
        }),
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
            easyMessage(responses.MessageRoleUser, "Please explain the principle of cosine similarity"),
        }),
    })
    if second_err != nil {
        fmt.Printf("second response error: %v", second_err)
        return
    }
    fmt.Println(second_resp)


    str := "Add a requirement: I hope you can explain this problem in a way that elementary school students can understand."
    third_resp, third_err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model:              "dola-seed-2-1-turbo-260628",
        Instructions: responses.NewOptString(str),
        PreviousResponseID: responses.NewOptString(resp.ID),
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            easyMessage(responses.MessageRoleUser, "Please explain the principle of cosine similarity"),
        }),
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
        //The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.SYSTEM).content(

                                MessageContent.ofString("You are a math teacher and able to clearly explain the corresponding math problems.")
                        ).build())))
                .build();
        Response resp = arkService.createResponse(request);
        System.out.println(resp);
        System.out.println("---------------------");
        ResponsesRequest request2 = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .previousResponseId(resp.getId())
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(

                                MessageContent.ofString("Please explain the principle of cosine similarity")
                        ).build())))
                .build();
        Response resp2 = arkService.createResponse(request2);
        System.out.println(resp2);
        System.out.println("---------------------");
        ResponsesRequest request3 = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")

                .instructions("Add a requirement: I hope you can explain this problem in a way that elementary school students can understand.")
                .previousResponseId(resp.getId())
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(

                                MessageContent.ofString("Please explain the principle of cosine similarity")
                        ).build())))
                .build();
        Response resp3 = arkService.createResponse(request3);
        System.out.println(resp3);

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

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "system",
            "content": "You are a math teacher and able to clearly explain the corresponding math problems."
        },
    ]
)
print(response)

second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "Please explain the principle of cosine similarity"}],
)
print(second_response)

third_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    instructions="Add a requirement: I hope you can explain this problem in a way that elementary school students can understand.",
    previous_response_id=response.id,
    input=[{"role": "user", "content": "Please explain the principle of cosine similarity"}],
)
print(third_response)
```

## Prefill\-based response

By prefilling part of the **assistant** role's content, you can guide and control the model to continue generating from existing text chunks, and control the model to maintain consistency in role\-playing scenarios.

### Supported models

* dola\-seed\-2\-1\-turbo\-260628

* seed\-2\-0\-lite\-260428

* seed\-2\-0\-mini\-260428

* seed\-2\-0\-pro\-260328

* seed\-2\-0\-lite\-260228

* seed\-2\-0\-mini\-260215

* seed\-2\-0\-code\-preview\-260328

* doubao\-seed\-character\-260628

* deepseek\-v4\-1\-flash\-261017

* deepseek\-v4\-1\-flash\-260910

* deepseek\-v4\-1\-flash\-economy\-261017

### Core configuration

In the **input** list, set the **role** of the last message to `assistant` and set **partial** to `true` to enable prefill\-based response. The model will continue generating based on the **content** value.

```Python
"input": [
    {"role": "user", "content": "Please write bubble sort code without any additional content."},
    {"role": "assistant", "content": "def bubble_sort(arr):", "partial": true}
]
```

> **Note**

> When using prefill\-based response, note the following:

* > When using the `deepseek-v4-1-flash-261017`, `deepseek-v4-1-flash-260910`, or `deepseek-v4-1-flash-economy-261017` model, not setting `partial` does not cause an error; it simply does not enable prefill\-based response.

* > For a new round of input, you can set the **content** of the `assistant` role to empty for prefill, so that the model can generate continuous responses across multiple rounds.

* > Prefix caching and **session** caching are supported. When using **session** caching, both cache storage and cache hit in the next round are calculated as **tokens** of the current round of input.

* > Structured output (**json_object** mode and **json_schema** mode) is not supported.

* > It is not recommended to use it together with built\-in tools,because the prefill\-based response will fail to generate as the **content** cannot be used as the starting content of the final output if large model calls and tool calls are intertwined.

### Usage examples

#### Code completion

The following is an example of using prefill to continue the code for the bubble sort algorithm. After enabling prefill\-based response, the model will continue to write after `"def bubble_sort(arr):"`.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "model": "seed-1-8-251228",
      "input": [
          {"role": "user", "content": "Please write bubble sort code without any additional content."},
          {"role": "assistant", "content": "def bubble_sort(arr):", "partial": true}
      ],
      "caching": {"type": "disabled"}
  }'
```

**Python**

```Python
import os
from arkruntime import Ark

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY'),
)

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {"role": "user", "content": "Please write bubble sort code without any additional content."},
        {"role": "assistant", "content": "def bubble_sort(arr):", "partial": True}
    ],
    caching={"type": "disabled"}
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
    input := responses.NewInputItemArrayResponsesInput([]responses.InputItem{
        easyMessage(
            responses.MessageRoleUser,
            "Please write bubble sort code without any additional content.",
            false,
        ),
        easyMessage(responses.MessageRoleAssistant, "def bubble_sort(arr):", true),
    })
    response, err := client.CreateResponses(context.Background(), &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: input,
        Caching: responses.NewOptResponseCaching(responses.ResponseCaching{
            Type: responses.NewOptCacheMode(responses.CacheModeDisabled),
        }),
    })
    if err != nil {
        fmt.Printf("response error: %v\n", err)
        return
    }
    fmt.Println(response)
}

func easyMessage(
    role responses.MessageRole,
    content string,
    partial bool,
) responses.InputItem {
    return responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role:    responses.NewOptMessageRole(role),
                Content: responses.NewStringMessageContent(content),
                Partial: responses.NewOptBool(partial),
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
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofString("Please write bubble sort code without any additional content.")
                        ).build(), ItemEasyMessage.builder().role(MessageRole.ASSISTANT).content(
                                MessageContent.ofString("def bubble_sort(arr):")
                        ).partial(true).build())))
                .caching(ResponseCaching.builder().type(CacheMode.DISABLED).build())
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

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY'),
)

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {"role": "user", "content": "Please write bubble sort code without any additional content."},
        {"role": "assistant", "content": "def bubble_sort(arr):", "partial": True}
    ],
    extra_body={
        "caching": {"type": "disabled"}
    }
)
print(response)
```

Response example:

> The model continues to write after "def bubble_sort(arr):" in the similar format and writing style.

```Plain Text
n = len(arr)
for i in range(n):
    swapped = False
    for j in range(0, n-i-1):
        if arr[j] > arr[j+1]:
            arr[j], arr[j+1] = arr[j+1], arr[j]
            swapped = True
    if not swapped:
        break
return arr
```

#### Context caching

The following is a cURL code sample about how to use context caching in prefill\-based response. For other SDK code samples, see [Context cache](context-cache.md).

* Create prefix cache while using prefill\-based response.

>    * The input content must be at least 256 tokens, otherwise the prefix cache cannot be created.\*

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
    "input":[
        {
            "role": "system",
            "content": "You are Dola, <prompt_content>"
        },
        {
            "role": "user",
            "content": "What is your favorite food?"
        },
        {
            "role": "assistant",
            "content": "Tomatoes"
        },
        {
            "role": "user",
            "content": "What else?"
        },
        {
            "role": "assistant",
            "content": "Hamburgers",
            "partial":true
        }
    ],
    "caching":{
        "type":"enabled",
        "prefix": true
    },
    "thinking": {
        "type": "disabled"
    }
  }'
```

* Create session cache while using prefill\-based response. Read and use the prefix cache via the ID returned by the previous request.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
     -H "Authorization: Bearer $ARK_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
   *    "model": "dola-seed-2-1-turbo-260628",
   *    "input":[
   *        {
   *            "role": "assistant",
   *            "content": "Apple pies"
   *        },
   *        {
   *            "role": "assistant",
   *            "content": "",
   *            "partial":true
   *        }
   *    ],
   *    "previous_response_id": "<id>",
   *    "caching":{
   *        "type":"enabled"
   *    },
   *    "thinking": {
   *        "type": "disabled"
   *    }
     }'
   ```

## How it works and instructions

### How context management works

The context management process is as follows:

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/eb56a7e5a22646278e195c8ced97ec06~tplv-goo7wpa0wc-image.image)![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/responses-api/text-generation-02.svg)

* item: A message from any role, including system messages, user messages, model messages, chain\-of\-thought messages, tool messages, and more.

* response_id: Represents the input item + answer item of the current request.

* Input item: The item referred to by previous_response_id + the new input item of the current request. Previous rounds of conversation are linked in the form of a linked list to form new input.

### Instructions

The store feature is enabled by default. You can enable it by setting `"store": true` or disable it by setting `"store": false`.

* Storage conditions: The current round of records is stored when the response to this request is completed and successful (the returned **status** parameter is `completed`), or when the response is truncated because it hits the length limit (the **status** parameter is `incomplete`). When chain\-of\-thought content is truncated because it hits the length limit, **output** is returned as empty when querying the model response.

* Input limits:

   * When `store: true` (storage enabled), the input limits are constrained by both the model context window/maximum input length and the number of stored items. The actual available input upper limit is determined by whichever limit is reached first.

      * Model length limit: See the length limit for the corresponding model in the [Model list](model-list.md).

      * Item number limit: Up to 1000 items are supported. A message from any role counts as 1 item. One question and one answer are considered two items. After this limit is reached, the conversation cannot continue. You can manually clean up records through the [Delete a model response](delete-model-responses-api.md).

   * When `store: false` (storage disabled), the input limits are constrained only by the model context window/maximum input length.

* Storage duration: Records are stored for 3 days by default. You can customize this through the **expire_at** parameter, with a maximum of 7 days.

* Stored content: Information in the **input** and **output** parameters is stored. Chain\-of\-thought content is not stored.

* Other notes: The store feature is currently free of charge. Data is encrypted to ensure information security and privacy compliance.

## QPS rate limits

The QPS rate limits for the Responses API are as follows. If you need to increase the rate limit, submit [Ticket](https://console.byteplus.com/workorder/create?step=2&SubProductID=P00001514).

|API name |QPS rate limits per account |
|---|---|
|Create model response |None |
|Query model response |20 |
|List input items |20 |
|Delete model response |20 |
