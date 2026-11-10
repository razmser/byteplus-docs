# Quick start

You can complete your first API call within minutes.

> **Use the documentation retrieval Skill**

> `ark-docs-reader` automatically selects an available method to search and read the latest official documentation.

> For installation, see [ark\-docs\-reader](ark-document-skills.md#ark-docs-reader).

# 1. Obtain and configure an API key

1. Obtain an API key: Switch to the target project in the lower\-left corner of the console, click [API Keys](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) in the left navigation pane, and create or obtain a ModelArk API key.

2. Configure the environment variable: run the following command in the terminal. Replace `your_api_key_here` with your ModelArk API key to configure the API key as an environment variable.

For how to configure persistent environment variables, see the [Environment variable configuration guide](environment-variable-configuration-guide.md).

**macOS**

```Bash
export ARK_API_KEY="your_api_key_here"
```

**Linux**

```Bash
export ARK_API_KEY="your_api_key_here"
```

**Windows_CMD**

```Bash
setx ARK_API_KEY "your_api_key_here"
```

**Windows_PowerShell**

```PowerShell
$env:ARK_API_KEY = "your_api_key_here"
```

# 2. Activate models

Go to the [Model activation page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement) to activate the models you need to use.

# 3. Install the SDK

Install the official ModelArk SDK or a third\-party SDK.

**Python**

> [Python](https://www.python.org/downloads/) version 3.8 or later must be installed in the runtime environment.

* Install the official ModelArk SDK:

   ```Bash
   python -m pip install --upgrade arkruntime
   ```

* Install the OpenAI SDK:

   ```Bash
   pip install openai
   ```

**Go**

> [Go](https://golang.google.cn/doc/install) version 1.20 or later must be installed in the environment.

Run the following command to install the official ModelArk Go SDK:

```Bash
go get github.com/volcengine/ark-runtime-go@latest
```

Import it in your code:

```Go
import "github.com/volcengine/ark-runtime-go/arkruntime"
```

**Java**

> [Java](https://www.java.com/en/download/help/index_installing.html) version 1.8 or later must be installed in the environment.

Add the following dependency configuration to the `pom.xml` file of your project.

```XML
<dependency>
  <groupId>com.volcengine</groupId>
  <artifactId>ark-runtime</artifactId>
  <version><VERSION></version>
</dependency>
```

> **Tip**

> Get the latest stable version from [Maven Central](https://central.sonatype.com/artifact/com.volcengine/ark-runtime), and replace `<VERSION>` with that version number.

# 4. Initiate an API request

The following lists typical tasks by input and output category. Select any code sample to try calling the API and experience the capabilities of large models and the ModelArk platform.

## Text generation

Pass text to the model to perform tasks such as question answering, analysis, rewriting, summarization, programming, and translation, and return text results.

|Input |Output preview |
|---|---|
|Hello |* Reasoning: Got it, let's see. The user said "hello". I need to respond in a friendly and welcoming way. Since the system prompt mentions a professional but friendly tone, I should keep it natural. Maybe something like "Hello! How can I assist you today?" That's simple, polite, and open\-ended to encourage the user to share what they need help with.<br><br>* Answer: Hello! How can I assist you today? Whether you have a question, need help with something specific, or just want to chat, feel free to let me know. 😊 |

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

* Disable deep reasoning: use `"thinking":{"type": "disabled"}` in the configuration.

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

* [Text generation](text-generation.md): Text generation guide.

* [Deep reasoning (Responses API)](responses-api-deep-thinking.md): Deep reasoning capability guide.

* [Migrate to Responses API](responses-api-migration.md): Recommended for new users, with simpler context management and powerful tool calling capabilities.

* [Chat API](chat-api.md): Recommended for iterating existing services; a widely used API.

## Multimodal understanding

Pass images, videos, and PDF files to the model for multimodal understanding tasks such as analysis, content moderation, question answering, and visual grounding, and return text results.

|Input |Output preview |
|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png)<br><br>> Based on the image, how many planets are listed in the table, and which row contains Earth? |* Reasoning: The table lists Mercury, Venus, Earth, Mars, Jupiter, Saturn, Uranus, and Neptune, for a total of eight planets. Earth is in the third data row.<br><br>* Answer: The table lists eight planets. Earth is in the third data row, or the fourth row when the header row is included. |

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
        {
            "role": "user",
            "content": [
                {
                    "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png"
                },
                {
                    "type": "input_text",
                    "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"
                },
            ],
        }
    ]
)

for item in response.output:
    if item.type == "reasoning":
        for summary in item.summary:
            if summary.type == "summary_text":
                print(f"Thoughts: {summary.text}")
    elif item.type == "message":
        for content in item.content:
            if content.type == "output_text":
                print(f"Answer: {content.text}")
```

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
    "input": [
        {
            "role": "user",
            "content": [
                {
                    "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png"
                },
                {
                    "type": "input_text",
                    "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"
                }
            ]
        }
    ]
}'
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
        // Get API Key：https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    inputMessage := responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role: responses.NewOptMessageRole(responses.MessageRoleUser),
                Content: responses.NewContentItemArrayMessageContent(
                    []responses.ContentItem{
            responses.ContentItem{OneOf: responses.NewContentItemImageContentItemSum(responses.ContentItemImage{Type: responses.ContentItemImageTypeInputImage, ImageURL: responses.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png")})},
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Based on the image, how many planets are listed in the table, and which row contains Earth?"})},
                    },
                ),
            },
        ),
    }

    resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{inputMessage}),
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
                MessageContent.ofList(java.util.Arrays.asList(ContentItemImage.builder()
                        .imageUrl("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png").build(), ContentItemText.builder().text("Based on the image, how many planets are listed in the table, and which row contains Earth?").build())))
                .build())))
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
        {
            "role": "user",
            "content": [
                {
                    "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png"
                },
                {
                    "type": "input_text",
                    "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"
                },
            ],
        }
    ]
)

for item in response.output:
    if item.type == "reasoning":
        for summary in item.summary:
            if summary.type == "summary_text":
                print(f"Thoughts: {summary.text}")
    elif item.type == "message":
        for content in item.content:
            if content.type == "output_text":
                print(f"Answer: {content.text}")
```

* [Multimodal understanding (Responses API)](multimodal-understanding.md): Detailed guide to multimodal understanding.

* [Visual grounding](grounding.md): Tasks that find the corresponding target in an image and return its coordinates.

* [Files API tutorial](file-api.md): APIs for passing images, videos, and documents.

## Image generation

Pass images and text to the model for the following use cases and tasks:

* Image generation for ads, posters, image sets, and more;

* Image editing, such as adding or modifying elements and changing colors;

* Style switching, such as oil painting and ink wash.

|Prompt |Output preview |
|---|---|
|A vibrant close\-up editorial portrait, with a model with a piercing gaze, wearing a sculptural hat, rich color blocking, sharp focus on the eyes, shallow depth of field, in the aesthetic style of a Vogue magazine cover, shot on medium format, with dramatic studio lighting. |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/00fb66006eb84b16965b620b6e1f2d78~tplv-goo7wpa0wc-image.image) |

**Python**

```Python
import os
# Install SDK:python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    #The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

imagesResponse = client.images.generate(
    #Replace with Model ID
    model="dola-seedream-5-0-pro-260628",
    prompt="Vibrant close-up editorial portrait, model with piercing gaze, wearing a sculptural hat, rich color blocking, sharp focus on eyes, shallow depth of field, Vogue magazine cover aesthetic, shot in medium format, dramatic studio lighting.",
    size="2K",
    response_format="url",
    watermark=True
)

print(imagesResponse.data[0].url)
```

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seedream-5-0-pro-260628",
    "prompt": "Vibrant close-up editorial portrait, model with piercing gaze, wearing a sculptural hat, rich color blocking, sharp focus on eyes, shallow depth of field, Vogue magazine cover aesthetic, shot on medium format, dramatic studio lighting.",
    "size": "2K",
    "watermark": true
}'
```

* Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.images.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;
import java.util.concurrent.TimeUnit;

public class ImageGenerationsExample {
    public static void main(String[] args) {
// Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        String apiKey = System.getenv("ARK_API_KEY");
        ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
        Dispatcher dispatcher = new Dispatcher();
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3") //The base URL for model invocation
                .dispatcher(dispatcher)
                .connectionPool(connectionPool)
                .apiKey(apiKey)
                .build();

        CreateImageGenerationRequest generateRequest = CreateImageGenerationRequest.builder()
                .model("dola-seedream-5-0-pro-260628") //Replace with Model ID
                .prompt("Vibrant close-up editorial portrait, model with piercing gaze, wearing a sculptural hat, rich color blocking, sharp focus on eyes, shallow depth of field, Vogue magazine cover aesthetic, shot on medium format, dramatic studio lighting.")
                .size("2K")
                .sequentialImageGeneration(SequentialImageGenerationMode.DISABLED)
                .responseFormat(ResponseFormat.URL)
                .stream(false)
                .watermark(true)
                .build();
        ImageGenerationResponse imagesResponse = service.generateImages(generateRequest);
        System.out.println(imagesResponse.getData().get(0).getUrl());

        service.shutdownExecutor();
    }
}
```

**Go**

```Go
package main

import (
    "context"
    "fmt"
    "os"
    "github.com/volcengine/ark-runtime-go/arkruntime"
    model "github.com/volcengine/ark-runtime-go/arkruntime/model/images"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"), // Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"), //The base URL for model invocation
    )
    ctx := context.Background()

    generateReq := &model.CreateImageGenerationRequest{
       Model:          "dola-seedream-5-0-pro-260628", //Replace with Model ID

       Prompt:         model.NewOptString("Vibrant close-up editorial portrait, model with piercing gaze, wearing a sculptural hat, rich color blocking, sharp focus on eyes, shallow depth of field, Vogue magazine cover aesthetic, shot on medium format, dramatic studio lighting."),
       Size:           model.NewOptString("2K"),
       ResponseFormat: model.NewOptResponseFormat(model.ResponseFormatURL),
       Watermark:      model.NewOptBool(true),
    }

    imagesResponse, err := client.GenerateImages(ctx, generateReq)
    if err != nil {
       fmt.Printf("generate images error: %v\n", err)
       return
    }

    fmt.Printf("%s\n", imagesResponse.Data[0].URL.Or(""))
}
```

**OpenAI**

```Python
import os
from openai import OpenAI

client = OpenAI(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3", #The base URL for model invocation
    api_key=os.getenv('ARK_API_KEY'),  # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
)

imagesResponse = client.images.generate(
    #Replace with Model ID
    model="dola-seedream-5-0-pro-260628",
    prompt="Vibrant close-up editorial portrait, model with piercing gaze, wearing a sculptural hat, rich color blocking, sharp focus on eyes, shallow depth of field, Vogue magazine cover aesthetic, shot on medium format, dramatic studio lighting.",
    size="2K",
    response_format="url",
    extra_body={
        "watermark": True,
    },
)

print(imagesResponse.data[0].url)
```

* [Image generation tutorial](image-generation-tutorial.md): Capabilities of mainstream image generation models and how to call them through the API.

* [Seedream 4.0-4.5 prompt guide](seedream-4-0-5-0-prompt-guide.md): Learn how to write prompts when using image generation models.

## Video generation

Quickly generate high\-quality video content in diverse styles from text descriptions, image assets, and video assets.

|Prompt |Output video preview |
|---|---|
|A woman in a green sequin gown stands in front of a pink background, surrounded by falling colorful confetti. |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/aae3d0c636954bdd9e66e7a23e98c480~tplv-goo7wpa0wc-image.image) |

**Python**

```Python
import os
import time
# Install SDK:python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3", #The base URL for model invocation
    api_key=os.environ.get("ARK_API_KEY"), # Get API Key：https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
)

if __name__ == "__main__":
    print("----- create request -----")
    create_result = client.content_generation.tasks.create(
        model="dreamina-seedance-2-5-260628", #Replace with Model ID
        content=[
            {
                # Combination of text prompt and parameters
                "type": "text",
                "text": "Photorealistic style: Under a clear blue sky, a vast expanse of white daisy fields stretches out. The camera gradually zooms in and finally fixates on a close - up of a single daisy, with several glistening dewdrops resting on its petals.  --ratio 16:9  --resolution 720p  --duration 5 --camerafixed false"
            }
        ],
        watermark=True,
    )
    print(create_result)

    # Polling query section
    print("----- polling task status -----")
    task_id = create_result.id
    max_wait = 600  # Maximum wait time in seconds
    interval = 10   # Initial polling interval
    start = time.time()

    while time.time() - start < max_wait:
        get_result = client.content_generation.tasks.get(task_id=task_id)
        status = get_result.status
        if status == "succeeded":
            print("----- task succeeded -----")
            print(get_result)
            break
        elif status == "failed":
            print("----- task failed -----")
            print(f"Error: {get_result.error}")
            break
        else:
            print(f"Current status: {status}, retrying after {interval:.0f}s...")
            time.sleep(interval)
            interval = min(interval * 1.5, 30)
    else:
        raise TimeoutError(f"Task {task_id} timed out after {max_wait}s. "
                            f"Task may still be running - check status manually.")
```

**Java**

```Java
package com.ark.sample;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;
import com.volcengine.ark.runtime.models.content_generation.*;
import com.volcengine.ark.runtime.service.ArkService;

public class ContentGenerationTaskExample {
  public static void main(String[] args) {
    String apiKey = System.getenv("ARK_API_KEY");
    ArkService service = ArkService.builder()
        .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
        //The base URL for model invocation
        .apiKey(apiKey)
        .build();

    System.out.println("----- create request -----");
    List<ContentItem> contents = new ArrayList<>();
    contents.add(ContentItem.builder()
        .type(ContentType.TEXT)

        .text("Photorealistic style: Under a clear blue sky, a vast expanse of white daisy fields stretches out. The camera gradually zooms in and finally fixates on a close - up of a single daisy, with several glistening dewdrops resting on its petals.  --ratio 16:9  --resolution 720p  --duration 5 --camerafixed false")
        .build());

    // Create a video generation task
    CreateContentGenerationTaskRequest createRequest = CreateContentGenerationTaskRequest.builder()
        .model("dreamina-seedance-2-5-260628") //Replace with Model ID
        .content(contents)
        .watermark(true)
        .build();

    CreateContentGenerationTaskResponse createResult = service.createContentGenerationTask(createRequest);
    System.out.println(createResult);

    // Get the details of the task
    String taskId = createResult.getId();
    String getRequest = taskId;

    System.out.println("----- polling task status -----");
    while (true) {
      try {
        ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
        String status = getResponse.getStatus().toString();
        if ("succeeded".equalsIgnoreCase(status)) {
          System.out.println("----- task succeeded -----");
          System.out.println(getResponse);
          service.shutdownExecutor();
          break;
        } else if ("failed".equalsIgnoreCase(status)) {
          System.out.println("----- task failed -----");
          System.out.println("Error: " + getResponse.getStatus());
          service.shutdownExecutor();
          break;
        } else {
          System.out.printf("Current status: %s, Retrying in 3 seconds...\n", status);
          TimeUnit.SECONDS.sleep(3);
        }
      } catch (InterruptedException ie) {
        Thread.currentThread().interrupt();
        System.err.println("Polling interrupted");
        service.shutdownExecutor();
        break;
      }
    }
  }
}
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
    model "github.com/volcengine/ark-runtime-go/arkruntime/model/contentgeneration"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"), // Get API Key：https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"), //The base URL for model invocation
    )
    ctx := context.Background()
    //Replace with Model ID
    modelEp := "dreamina-seedance-2-5-260628"

    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model: modelEp,
        Content: []model.ContentItem{
            {
                Type: model.ContentTypeText,

                Text: model.NewOptString("Photorealistic style: Under a clear blue sky, a vast expanse of white daisy fields stretches out. The camera gradually zooms in and finally fixates on a close - up of a single daisy, with several glistening dewdrops resting on its petals.  --ratio 16:9  --resolution 720p  --duration 5 --camerafixed false"),
            },
        },
        Watermark:     model.NewOptBool(true),
    }
    createResp, err := client.CreateContentGenerationTask(ctx, createReq)
    if err != nil {
        fmt.Printf("create content generation error: %v", err)
        return
    }
    taskID := createResp.ID
    fmt.Printf("Task Created with ID: %s", taskID)

    // Polling query section
    fmt.Println("----- polling task status -----")
    for {
        getReq := taskID
        getResp, err := client.GetContentGenerationTask(ctx, getReq)
        if err != nil {
            fmt.Printf("get content generation task error: %v", err)
            return
        }

        status := getResp.Status
        if status == "succeeded" {
            fmt.Println("----- task succeeded -----")
            fmt.Printf("Task ID: %s \n", getResp.ID)
            fmt.Printf("Model: %s \n", getResp.Model)
            fmt.Printf("Video URL: %s \n", getResp.Content.Or(model.TaskContent{}).VideoURL.Or(""))
            fmt.Printf("Completion Tokens: %d \n", getResp.Usage.Or(model.TaskUsage{}).CompletionTokens)
            fmt.Printf("Created At: %d, Updated At: %d", getResp.CreatedAt.Or(0), getResp.UpdatedAt.Or(0))
            return
        } else if status == "failed" {
            fmt.Println("----- task failed -----")
            if getResp.Error.IsSet() {
                fmt.Printf("Error Code: %s, Message: %s", getResp.Error.Value.Code, getResp.Error.Value.Message)
            }
            return
        } else {
            fmt.Printf("Current status: %s, Retrying in 3 seconds... \n", status)
            time.Sleep(3 * time.Second)
        }
    }
}
```

* [Video generation tutorial](video-generation-tutorial.md): Learn how to use the model's video generation capabilities, including text\-to\-video, first\-and\-last\-frame\-to\-video, and first\-frame\-to\-video.

* [Dreamina Seedance 2.5 prompt guide](seedance-2-5-prompt-guide.md): Learn how to write prompts when using video generation models.

## Tool use

Use tools or plugins to enable the model to read external data and call functions, including:

* [Function call](function-calling.md): Learn how to let the model call custom tools.

* [Cloud-deployed MCP / remote MCP](mcp-remote-mcp.md): Learn how to let the model use MCP services.

# 5. Next steps

Now that you have completed your first API call to the ModelArk models, you can explore more model capabilities, including:

* [Model list](model-list.md): Quickly browse the full list of models provided by ModelArk and the capabilities of each model, and quickly find a suitable model for your actual business requirements.
