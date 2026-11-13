# Image understanding

Some large models with image understanding capabilities support image input via local files and image URLs, and are suitable for scenarios such as image description, classification, and visual grounding.

# Quick start

Experiment with the following Responses API code samples, and have a glance at how image understanding works by passing images via URL.

|Input |Output preview |
|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png)<br><br>> Based on the image, how many planets are listed in the table, and which row is Earth in? |* Thought: The table lists Mercury, Venus, Earth, Mars, Jupiter, Saturn, Uranus, and Neptune, for a total of 8 planets. Earth is in the 3rd data row.<br><br>* Answer: The table lists 8 planets. Earth is in the 3rd data row; if the header is counted as row 1, Earth is in row 4. |

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

# Supported models

* See [Capabilities](model-list.md#76df97c8).

# APIs

* [Responses API](create-model-responses-api.md): Supports images as input for analysis. Accepts file ID as an input for image understanding. For instructions, see [Upload via Files API (recommended)](image-understanding.md#2c38c01b).

* [Chat API](chat-api.md): Supports images as input for analysis. Accepts file ID as an input for image understanding. For instructions, see [Upload via Files API (recommended)](image-understanding.md#2c38c01b).

# Image input methods

The supported image input methods are as follows:

* Upload local files:

   * [Upload via Files API (recommended)](image-understanding.md#2c38c01b): Upload image files through the Files API. The file size cannot exceed 512 MB.

   * [Pass Base64 string](image-understanding.md#477e51ce): Suitable for images smaller than 10 MB, and the request body cannot exceed 64 MB.

* [Passing image URL](image-understanding.md#d86010f4): Suitable for scenarios where the image file already has a publicly accessible URL. A single image must be smaller than 10 MB.

> **Tip**

> Chat API is stateless. If you want the model to perform multi\-turn understanding on an image, you must pass that image in each request.

## Upload local files

### Upload via Files API (recommended)

Uploading local files via the filepath is recommended, because it supports files up to 512 MB. After you upload an image through the Files API and the file status changes to `active`, both the Responses API and Chat API support passing the image by using `file_id`.

The following examples first call the Files API to upload a local file and wait for processing to complete, and then call the Responses API or Chat API by using the file ID for image analysis:

> * If you need to obtain analysis in real time or avoid client timeout failures caused by complex tasks, you can use the streaming output. For usage, see [Chat API](streaming-output.md#9346c907).

> * You can also upload local files directly using the Files API. For details, see [Files API tutorial](file-api.md).

* Responses API code samples:

**Python**

```Python
import os
from pathlib import Path

from arkruntime import Ark

client = Ark(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.environ.get("ARK_API_KEY"),
)
file_path = Path(os.getenv("FILE_PATH", "/Users/doc/demo.png"))

uploaded_file = client.files.create(file=file_path, purpose="user_data")
client.files.wait_for_processing(uploaded_file.id)

response = client.responses.create(
    model=os.getenv("MODEL_ID", "dola-seed-2-1-turbo-260628"),
    input=[
        {
            "role": "user",
            "content": [
                {"type": "input_image", "file_id": uploaded_file.id},
                {"type": "input_text", "text": "Please describe the main content of the image."},
            ],
        }
    ],
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
    "github.com/volcengine/ark-runtime-go/arkruntime/model/file"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/responses"
)

func main() {
    ctx := context.Background()
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    filePath := os.Getenv("FILE_PATH")
    if filePath == "" {
        filePath = "/Users/doc/demo.png"
    }
    data, err := os.Open(filePath)
    if err != nil {
        panic(err)
    }
    defer data.Close()

    uploaded, err := client.UploadFile(ctx, &file.FileCreateRequest{Purpose: file.PurposeUserData}, data)
    if err != nil {
        panic(err)
    }
    uploaded, err = client.WaitForFileProcessing(ctx, uploaded.ID, arkruntime.WaitForFileProcessingOptions{})
    if err != nil || uploaded.Status != file.StatusActive {
        panic(fmt.Sprintf("file processing failed: %v, status=%s", err, uploaded.Status))
    }

    media := responses.ContentItem{OneOf: responses.NewContentItemImageContentItemSum(
        responses.ContentItemImage{Type: responses.ContentItemImageTypeInputImage, FileID: responses.NewOptString(uploaded.ID)},
    )}
    text := responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(
        responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Please describe the main content of the image."},
    )}
    message := responses.InputItem{OneOf: responses.NewItemEasyMessageInputItemSum(
        responses.ItemEasyMessage{
            Role: responses.NewOptMessageRole(responses.MessageRoleUser),
            Content: responses.NewContentItemArrayMessageContent([]responses.ContentItem{media, text}),
        },
    )}
    modelID := os.Getenv("MODEL_ID")
    if modelID == "" {
        modelID = "dola-seed-2-1-turbo-260628"
    }
    response, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model: modelID,
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{message}),
    })
    if err != nil {
        panic(err)
    }
    fmt.Println(response)
}
```

**Java**

```Java
import com.volcengine.ark.runtime.models.file.FileCreateRequest;
import com.volcengine.ark.runtime.models.file.FileObject;
import com.volcengine.ark.runtime.models.file.Purpose;
import com.volcengine.ark.runtime.models.file.Status;
import com.volcengine.ark.runtime.models.responses.*;
import com.volcengine.ark.runtime.service.ArkService;

import java.io.File;
import java.util.Arrays;

public class Main {
    public static void main(String[] args) {
        ArkService service = ArkService.builder()
                .apiKey(System.getenv("ARK_API_KEY"))
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();
        String filePath = System.getenv().getOrDefault("FILE_PATH", "/Users/doc/demo.png");
        String modelId = System.getenv().getOrDefault("MODEL_ID", "dola-seed-2-1-turbo-260628");

        FileObject uploaded = service.uploadFile(
                FileCreateRequest.builder().purpose(Purpose.USER_DATA).build(),
                new File(filePath)
        );
        uploaded = service.waitForFileProcessing(uploaded.getId());
        if (uploaded.getStatus() != Status.ACTIVE) {
            throw new IllegalStateException("File processing failed: " + uploaded.getStatus());
        }

        ContentItem media = ContentItemImage.builder()
                .type(ContentItemType.INPUT_IMAGE)
                .fileId(uploaded.getId())
                .build();
        ContentItem text = ContentItemText.builder()
                .type(ContentItemType.INPUT_TEXT)
                .text("Please describe the main content of the image.")
                .build();
        ResponsesRequest request = ResponsesRequest.builder()
                .model(modelId)
                .input(ResponsesInput.ofList(Arrays.asList(
                        ItemEasyMessage.builder()
                                .role(MessageRole.USER)
                                .content(MessageContent.ofList(Arrays.asList(media, text)))
                                .build()
                )))
                .build();
        Response response = service.createResponse(request);
        System.out.println(response);
        service.shutdownExecutor();
    }
}
```

* Chat API code samples:

**Python**

```Python
import os
from pathlib import Path

from arkruntime import Ark

client = Ark(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.environ.get("ARK_API_KEY"),
)
file_path = Path(os.getenv("FILE_PATH", "/Users/doc/demo.png"))

uploaded_file = client.files.create(
    file=file_path,
    purpose="user_data",
)
client.files.wait_for_processing(uploaded_file.id)

completion = client.chat.completions.create(
    model=os.getenv("MODEL_ID", "dola-seed-2-1-turbo-260628"),
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "image_url", "image_url": {"file_id": uploaded_file.id}},
                {"type": "text", "text": "Please describe the main content of the image."},
            ],
        }
    ],
)
print(f"id={completion.id}")
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
    "github.com/volcengine/ark-runtime-go/arkruntime/model/file"
)

func main() {
    ctx := context.Background()
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    filePath := os.Getenv("FILE_PATH")
    if filePath == "" {
        filePath = "/Users/doc/demo.png"
    }
    data, err := os.Open(filePath)
    if err != nil {
        panic(err)
    }
    defer data.Close()

    uploaded, err := client.UploadFile(ctx, &file.FileCreateRequest{
        Purpose: file.PurposeUserData,
    }, data)
    if err != nil {
        panic(err)
    }
    uploaded, err = client.WaitForFileProcessing(
        ctx,
        uploaded.ID,
        arkruntime.WaitForFileProcessingOptions{},
    )
    if err != nil || uploaded.Status != file.StatusActive {
        panic(fmt.Sprintf("file processing failed: %v, status=%s", err, uploaded.Status))
    }

    media := chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartImageChatCompletionContentPartSum(chat.ChatCompletionContentPartImage{Type: chat.ChatCompletionContentPartImageTypeImageURL, ImageURL: chat.NewChatCompletionContentPartImageImageUrlChatCompletionContentPartImageImageURL(chat.ChatCompletionContentPartImageImageUrl{FileID: chat.NewOptString(uploaded.ID)})})}
    text := chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(
        chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "Please describe the main content of the image."},
    )}
    message := chat.ChatCompletionRequestMessage{OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(
        chat.ChatCompletionRequestUserMessage{
            Role: chat.ChatCompletionRequestUserMessageRoleUser,
            Content: chat.NewOptNilChatCompletionMessageContent(chat.NewChatCompletionContentPartArrayChatCompletionMessageContent(
                []chat.ChatCompletionContentPart{media, text},
            )),
        },
    )}
    modelID := os.Getenv("MODEL_ID")
    if modelID == "" {
        modelID = "dola-seed-2-1-turbo-260628"
    }
    response, err := client.CreateChatCompletion(ctx, &chat.ChatCompletionRequest{
        Model:    modelID,
        Messages: []chat.ChatCompletionRequestMessage{message},
    })
    if err != nil {
        panic(err)
    }
    fmt.Printf("id=%s\n%s\n", response.ID, response.Choices[0].Message.Content)
}
```

**Java**

```Java
import com.volcengine.ark.runtime.models.chat.*;
import com.volcengine.ark.runtime.models.file.FileCreateRequest;
import com.volcengine.ark.runtime.models.file.FileObject;
import com.volcengine.ark.runtime.models.file.PreprocessConfigs;
import com.volcengine.ark.runtime.models.file.Purpose;
import com.volcengine.ark.runtime.models.file.Status;
import com.volcengine.ark.runtime.models.file.Video;
import com.volcengine.ark.runtime.service.ArkService;

import java.io.File;
import java.util.Arrays;

public class Main {
    public static void main(String[] args) {
        ArkService service = ArkService.builder()
                .apiKey(System.getenv("ARK_API_KEY"))
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();
        String filePath = System.getenv().getOrDefault("FILE_PATH", "/Users/doc/demo.png");
        String modelId = System.getenv().getOrDefault("MODEL_ID", "dola-seed-2-1-turbo-260628");

        FileObject uploaded = service.uploadFile(
                FileCreateRequest.builder()
                        .purpose(Purpose.USER_DATA)

                        .build(),
                new File(filePath)
        );
        uploaded = service.waitForFileProcessing(uploaded.getId());
        if (uploaded.getStatus() != Status.ACTIVE) {
            throw new IllegalStateException("File processing failed: " + uploaded.getStatus());
        }

        ChatCompletionContentPart media = ChatCompletionContentPartImage.builder().type(ChatCompletionContentPartType.IMAGE_URL).imageUrl(ChatCompletionContentPartImageImageUrl2.ofChatCompletionContentPartImageImageUrl(ChatCompletionContentPartImageImageUrl.builder().fileId(uploaded.getId()).build())).build();
        ChatCompletionContentPart text = ChatCompletionContentPartText.builder()
                .type(ChatCompletionContentPartType.TEXT)
.text("Please describe the main content of the image.")
                .build();
        ChatCompletionRequestMessage message = ChatCompletionRequestUserMessage.builder()
                .role(ChatCompletionRequestMessageType.USER)
                .content(ChatCompletionMessageContent.builder()
                        .value(Arrays.asList(media, text))
                        .build())
                .build();
        ChatCompletionRequest request = ChatCompletionRequest.builder()
                .model(modelId)
                .messages(Arrays.asList(message))
                .build();
        ChatCompletionResponse response = service.createChatCompletion(request);
        System.out.println("id=" + response.getId());
        System.out.println(response.getChoices().get(0).getMessage().getContent());
        service.shutdownExecutor();
    }
}
```

### Pass Base64 string

Convert the local file to a Base64 encoded string and submit it to the large model. This method is suitable for images smaller than 10 MB, and the request body cannot exceed 64 MB. (Supported by both Responses API and Chat API.)

> **Note**

> Convert the image file to a Base64 encoded string, concatenate it in the format `data:{mime_type};base64,{base64_data}`, and pass it to the model.

* > `{mime_type}`: The media type of the file, which must be identical with the file's mime_type. For details about supported video formats, see [Supported image formats](image-understanding.md#51efc45f).

* > `{base64_data}`: The Base64 encoded string of the file.

|[Chat API](chat-api.md) |[Responses API](create-model-responses-api.md) |
|---|---|
|```Python```<br>```...```<br>```model="dola-seed-2-1-turbo-260628",```<br>```messages=[```<br>```    {```<br>```        "role": "user",```<br>```        "content": [```<br>```            {```<br>```                "type": "image_url",```<br>```                "image_url": {```<br>```                    "url": f"data:image/png;base64,{base64_image}"```<br>```                }```<br>```            },```<br>```            {```<br>```                "type": "text",```<br>```                "text": "Based on the image, how many planets are listed in the table, and which row is Earth in?"```<br>```            }```<br>```        ]```<br>```    }```<br>```]```<br>```...```<br> |```Python```<br>```...```<br>```model="dola-seed-2-1-turbo-260628",```<br>```input=[```<br>```    {```<br>```        "role": "user",```<br>```        "content": [```<br>```            {```<br>```                "type": "input_image",```<br>```                "image_url": f"data:image/png;base64,{base64_image}"```<br>```            },```<br>```            {```<br>```                "type": "input_text",```<br>```                "text": "Based on the image, how many planets are listed in the table, and which row is Earth in?"```<br>```            }```<br>```        ]```<br>```    }```<br>```]```<br>```...```<br> |

* Responses API code sample:

**Curl**

```Bash
BASE64_IMAGE=$(base64 < planet_comparison.png) && curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
       -H "Content-Type: application/json"  \
       -H "Authorization: Bearer $ARK_API_KEY"  \
       -d @- <<EOF
       {
        "model": "dola-seed-2-1-turbo-260628",
        "input": [
          {
            "role": "user",
            "content": [
              {
                "type": "input_image",
                "image_url": "data:image/png;base64,$BASE64_IMAGE"
              },
              {
                "type": "input_text",
                "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"
              }
            ]
          }
        ]
      }
    EOF
```

**Python**

```Python
import os
from arkruntime import Ark
import base64
# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)
# Convert local files to Base64-encoded strings.
def encode_file(file_path):
  with open(file_path, "rb") as read_file:
    return base64.b64encode(read_file.read()).decode('utf-8')
base64_file = encode_file("/Users/doc/planet_comparison.png")  # Replace with the actual path to your local image.

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "user",
            "content": [

                {
                    "type": "input_image",
                    "image_url": f"data:image/png;base64,{base64_file}"
                },
                {
                    "type": "input_text",
                    "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"
                },
            ],
        }
    ]
)

print(response)
```

**Go**

```Go
package main

import (
    "context"
    "encoding/base64"
    "fmt"
    "os"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/responses"
)

func main() {
// Convert local files to Base64-encoded strings.
    fileBytes, err := os.ReadFile("/Users/doc/planet_comparison.png")  // Replace it with the actual path to your local image.
    if err != nil {
        fmt.Printf("read file error: %v\n", err)
        return
    }
    base64File := base64.StdEncoding.EncodeToString(fileBytes)
    client := arkruntime.NewClientWithApiKey(
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
            responses.ContentItem{OneOf: responses.NewContentItemImageContentItemSum(responses.ContentItemImage{Type: responses.ContentItemImageTypeInputImage, ImageURL: responses.NewOptString(fmt.Sprintf("data:image/png;base64,%s", base64File))})},
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
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.Base64;
import java.io.IOException;

public class demo {
    private static String encodeFile(String filePath) throws IOException {
        byte[] fileBytes = Files.readAllBytes(Paths.get(filePath));
        return Base64.getEncoder().encodeToString(fileBytes);
    }
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();
        // Convert local files to Base64-encoded strings.
        String base64Data = "";
        try {
            base64Data = "data:image/png;base64," + encodeFile("/Users/planet_comparison.png");
        } catch (IOException e) {
            System.err.println("encode error: " + e.getMessage());
        }
        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemImage.builder().imageUrl(base64Data).build(), ContentItemText.builder().text("Based on the image, how many planets are listed in the table, and which row contains Earth?").build()))
                        ).build())))
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
import base64
api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)
# Convert local files to Base64-encoded strings.
def encode_file(file_path):
  with open(file_path, "rb") as read_file:
    return base64.b64encode(read_file.read()).decode('utf-8')
base64_file = encode_file("/Users/doc/planet_comparison.png")  # Replace with the actual path to your local image.

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "user",
            "content": [

                {
                    "type": "input_image",
                    "image_url": f"data:image/png;base64,{base64_file}",
                },
                {
                    "type": "input_text",
                    "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"
                },
            ],
        }
    ]
)

print(response)
```

* Chat API code samples:

**Curl**

```Bash
BASE64_IMAGE=$(base64 < planet_comparison.png) && curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
       -H "Content-Type: application/json"  \
       -H "Authorization: Bearer $ARK_API_KEY"  \
       -d @- <<EOF
       {
        "model": "dola-seed-2-1-turbo-260628",
        "messages": [
          {
            "role": "user",
            "content": [
              {
                "type": "image_url",
                "image_url": {
                  "url": "data:image/png;base64,$BASE64_IMAGE"
                }
              },
              {
                "type": "text",
                "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"
              }
            ]
          }
        ],
        "max_tokens": 300
      }
    EOF
```

**Python**

```Python
import base64
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

# Define a method to convert the image at the specified path to Base64 string
def encode_image(image_path):
  with open(image_path, "rb") as image_file:
    return base64.b64encode(image_file.read()).decode('utf-8')

# Image to be passed to the large model
image_path = "planet_comparison.png"

# Convert the image to Base64 string
base64_image = encode_image(image_path)

completion = client.chat.completions.create(
  # Replace with Model ID
  model = "dola-seed-2-1-turbo-260628",
  messages=[
    {
      "role": "user",
      "content": [
        {
          "type": "image_url",
          "image_url": {
          # Note: the Base64 string passed in must follow the format data:image/<IMAGE_FORMAT>;base64,{base64_image}:
          # PNG images: "url":  f"data:image/png;base64,{base64_image}"
          # JPEG images: "url":  f"data:image/jpeg;base64,{base64_image}"
          # WEBP images: "url":  f"data:image/webp;base64,{base64_image}"
            "url":  f"data:image/<IMAGE_FORMAT>;base64,{base64_image}"
          },
        },
        {
          "type": "text",
          "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?",
        },
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
    "encoding/base64"
    "fmt"
    "os"
    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/chat"
)

func main() {
// Read local image file
    imageBytes, err := os.ReadFile("planet_comparison.png") // Replace with the actual image path
    if err != nil {
        fmt.Printf("Failed to read image: %v\n", err)
        return
    }
    base64Image := base64.StdEncoding.EncodeToString(imageBytes)

    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
// The base URL for model invocation  .
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
        )
    ctx := context.Background()
    req := &chat.ChatCompletionRequest{
// Replace with Model ID
        Model: "dola-seed-2-1-turbo-260628",
        Messages: []chat.ChatCompletionRequestMessage{
chat.ChatCompletionRequestMessage{OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(chat.ChatCompletionRequestUserMessage{Role: chat.ChatCompletionRequestUserMessageRoleUser, Content: chat.NewOptNilChatCompletionMessageContent(chat.NewChatCompletionContentPartArrayChatCompletionMessageContent(
                []chat.ChatCompletionContentPart{
                    chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartImageChatCompletionContentPartSum(chat.ChatCompletionContentPartImage{Type: chat.ChatCompletionContentPartImageTypeImageURL, ImageURL: chat.NewChatCompletionContentPartImageImageUrlChatCompletionContentPartImageImageURL(chat.ChatCompletionContentPartImageImageUrl{URL: chat.NewOptString(fmt.Sprintf("data:image/png;base64,%s", base64Image))})})},
                    chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "Based on the image, how many planets are listed in the table, and which row contains Earth?"})},
                },
            ))})},
        },
    }

    resp, err := client.CreateChatCompletion(ctx, req)
    if err != nil {
        fmt.Printf("standard chat error: %v\n", err)
        return
    }
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
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.Base64;
import java.io.IOException;

public class Sample {
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

// Base64 encoding method
    private static String encodeImage(String imagePath) throws IOException {
        byte[] imageBytes = Files.readAllBytes(java.nio.file.Paths.get(imagePath));
        return Base64.getEncoder().encodeToString(imageBytes);
    }

    public static void main(String[] args) throws Exception {

        List<ChatCompletionRequestMessage> messagesForReqList = new ArrayList<>();

// Local image path (replace with actual path)
        String imagePath = "planet_comparison.png";

// Generate Base64 URL
        String base64Data = "data:image/png;base64," + encodeImage(imagePath);

// Construct message content (fixed the construction method of the content part)
        List<ChatCompletionContentPart> contentParts = new ArrayList<>();

// Use builder pattern for the video part
        contentParts.add(ChatCompletionContentPartImage.builder()
                 .type(ChatCompletionContentPartType.IMAGE_URL)
                 .imageUrl(ChatCompletionContentPartImageImageUrl2.ofChatCompletionContentPartImageImageUrl(ChatCompletionContentPartImageImageUrl.builder().url(base64Data).build()))
                 .build());

// Use builder pattern for the text part
        contentParts.add(ChatCompletionContentPartText.builder()
                 .type(ChatCompletionContentPartType.TEXT)
                 .text("Based on the image, how many planets are listed in the table, and which row contains Earth?")
                 .build());

// Create message
        messagesForReqList.add(ChatCompletionRequestUserMessage.builder()
                 .role(ChatCompletionRequestMessageType.USER)
                 .content(ChatCompletionMessageContent.ofList(contentParts))
                 .build());

        ChatCompletionRequest req = ChatCompletionRequest.builder()
                 .model("dola-seed-2-1-turbo-260628") //Replace with Model ID  .
                 .messages(messagesForReqList)
                 .maxTokens(300)
                 .build();

        service.createChatCompletion(req)
                 .getChoices()
                 .forEach(choice -> System.out.println(choice.getMessage().getContent()));
// shutdown service after all requests are finished
        service.shutdownExecutor();
    }
}
```

## Passing image URL

If the image already has a publicly accessible URL, you can directly fill in the public URL of the image in the request. A single image cannot exceed 10 MB. (Supported by both Responses API and Chat API.)

> **Tip**

> If using a URL, it is recommended to store the image using BytePlus Torch Object Storage (TOS) and generate an access link. This not only ensures stable storage of the image but also leverages the internal network communication advantage between ModelArk and TOS, effectively reducing model response latency and public network traffic costs.

|[Chat API](chat-api.md) |[Responses API](create-model-responses-api.md) |
|---|---|
|```Python```<br>```...```<br>```model="dola-seed-2-1-turbo-260628",```<br>```messages=[```<br>```    {```<br>```        "role": "user",```<br>```        "content": [```<br>```            {```<br>```                "type": "image_url",```<br>```                "image_url": {```<br>```                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png"```<br>```                }```<br>```            },```<br>```            {```<br>```                "type": "text",```<br>```                "text": "Based on the image, how many planets are listed in the table, and which row is Earth in?"```<br>```            }```<br>```        ]```<br>```    }```<br>```]```<br>```...```<br> |```Python```<br>```...```<br>```model="dola-seed-2-1-turbo-260628",```<br>```input=[```<br>```    {```<br>```        "role": "user",```<br>```        "content": [```<br>```            {```<br>```                "type": "input_image",```<br>```                "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png"```<br>```            },```<br>```            {```<br>```                "type": "input_text",```<br>```                "text": "Based on the image, how many planets are listed in the table, and which row is Earth in?"```<br>```            }```<br>```        ]```<br>```    }```<br>```]```<br>```...```<br> |

* Responses API code sample: [Quick start](image-understanding.md#18cf565a)

* Chat API code samples:

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
                {"type": "image_url","image_url": {"url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png"}},
                    {"type": "text", "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"}
                ]
            }
        ],
        "max_tokens": 300
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
)

completion = client.chat.completions.create(
# Replace with Model ID
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "image_url","image_url": {"url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png"}},
                {"type": "text", "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"},
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
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartImageChatCompletionContentPartSum(chat.ChatCompletionContentPartImage{Type: chat.ChatCompletionContentPartImageTypeImageURL, ImageURL: chat.NewChatCompletionContentPartImageImageUrlChatCompletionContentPartImageImageURL(chat.ChatCompletionContentPartImageImageUrl{URL: chat.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png")})})},
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "Based on the image, how many planets are listed in the table, and which row contains Earth?"})},
    }
    // Construct chat, specify model and message
    req := &chat.ChatCompletionRequest{
        // Replace with Model ID
       Model: "dola-seed-2-1-turbo-260628",
       Messages: []chat.ChatCompletionRequestMessage{
chat.ChatCompletionRequestMessage{OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(chat.ChatCompletionRequestUserMessage{Role: chat.ChatCompletionRequestUserMessageRoleUser, Content: chat.NewOptNilChatCompletionMessageContent(chat.NewChatCompletionContentPartArrayChatCompletionMessageContent(contentParts))})},
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
         .imageUrl(ChatCompletionContentPartImageImageUrl2.ofChatCompletionContentPartImageImageUrl(ChatCompletionContentPartImageImageUrl.builder().url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png").build()))


         .build());

    // Use builder mode for text
    contentParts.add(ChatCompletionContentPartText.builder()
         .type(ChatCompletionContentPartType.TEXT)
         .text("Based on the image, how many planets are listed in the table, and which row contains Earth?")
         .build());

    // Create message
    messagesForReqList.add(ChatCompletionRequestUserMessage.builder()
         .role(ChatCompletionRequestMessageType.USER)
         .content(ChatCompletionMessageContent.ofList(contentParts))
         .build());

    ChatCompletionRequest req = ChatCompletionRequest.builder()
         .model("dola-seed-2-1-turbo-260628") //Replace with Model ID  .
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

# Use cases

## Multi\-image input

The API supports accepting and processing multiple image inputs. These images can be passed to the model via accessible URLs or converted Base64 strings. The model will combine information from all passed images to answer questions.

* Responses API code samples:

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
                        "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/country_list.png"
                    },
                    {
                        "type": "input_text",
                    "text": "Based on the image, how many planets are listed in the table, and which row contains Earth? In the other image, how many countries are listed in the table?"
                    }
                ]
            }
        ]
      }'
```

**Python**

```Python
import os
from arkruntime import Ark

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY')
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
                    "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/country_list.png"
                },
                {
                    "type": "input_text",
                    "text": "Based on the image, how many planets are listed in the table, and which row contains Earth? In the other image, how many countries are listed in the table?"
                }
            ]
        }
    ]
)

print(response.output)
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

    inputMessage := responses.InputItem{
    OneOf: responses.NewItemEasyMessageInputItemSum(
        responses.ItemEasyMessage{
            Role: responses.NewOptMessageRole(responses.MessageRoleUser),
            Content: responses.NewContentItemArrayMessageContent(
                []responses.ContentItem{
            responses.ContentItem{OneOf: responses.NewContentItemImageContentItemSum(responses.ContentItemImage{Type: responses.ContentItemImageTypeInputImage, ImageURL: responses.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png")})},
            responses.ContentItem{OneOf: responses.NewContentItemImageContentItemSum(responses.ContentItemImage{Type: responses.ContentItemImageTypeInputImage, ImageURL: responses.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/country_list.png")})},
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Based on the image, how many planets are listed in the table, and which row contains Earth? In the other image, how many countries are listed in the table?"})},
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
import com.volcengine.ark.runtime.models.responses.ContentItemImage;
import com.volcengine.ark.runtime.models.responses.ContentItemText;
import com.volcengine.ark.runtime.models.responses.ItemEasyMessage;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
import com.volcengine.ark.runtime.models.responses.Response;
import com.volcengine.ark.runtime.models.responses.MessageContent;


public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                    MessageContent.ofList(java.util.Arrays.asList(ContentItemImage.builder().imageUrl("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png").build(), ContentItemImage.builder().imageUrl("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/country_list.png").build(), ContentItemText.builder().text("Based on the image, how many planets are listed in the table, and which row contains Earth? In the other image, how many countries are listed in the table?").build()))
                        ).build())))
                .build();
        Response resp = arkService.createResponse(request);
        System.out.println(resp);

        arkService.shutdownExecutor();
    }
}
```

* Chat API code samples:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
       -H "Content-Type: application/json"  \
       -H "Authorization: Bearer $ARK_API_KEY"  \
       -d '{
        "model": "dola-seed-2-1-turbo-260628",
        "messages": [
            {
                "role": "user",
                "content": [
                {"type": "image_url","image_url": {"url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png"}},
                {"type": "image_url","image_url": {"url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/country_list.png"}},
                {"type": "text", "text": "Based on the image, how many planets are listed in the table, and which row contains Earth? In the other image, how many countries are listed in the table?"}
                ]
            }
        ],
        "max_tokens": 300
      }'
```

**Python**

```Python
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    #The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

completion = client.chat.completions.create(
    #Replace with Model ID
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "image_url","image_url": {"url":  "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png"}},
                {"type": "image_url","image_url": {"url":  "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/country_list.png"}},
                {"type": "text", "text": "Based on the image, how many planets are listed in the table, and which row contains Earth? In the other image, how many countries are listed in the table?"},
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
    // Create context background
    ctx := context.Background()
    // Construct message, including 2 images and a text
    contentParts := []chat.ChatCompletionContentPart{
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartImageChatCompletionContentPartSum(chat.ChatCompletionContentPartImage{Type: chat.ChatCompletionContentPartImageTypeImageURL, ImageURL: chat.NewChatCompletionContentPartImageImageUrlChatCompletionContentPartImageImageURL(chat.ChatCompletionContentPartImageImageUrl{URL: chat.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/ModelArk_demo_img_1.jpg")})})},
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartImageChatCompletionContentPartSum(chat.ChatCompletionContentPartImage{Type: chat.ChatCompletionContentPartImageTypeImageURL, ImageURL: chat.NewChatCompletionContentPartImageImageUrlChatCompletionContentPartImageImageURL(chat.ChatCompletionContentPartImageImageUrl{URL: chat.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/ModelArk_demo_img_2.jpg")})})},
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "Which model series supports image input? Also, what are the advantages of Skylark-Pro?"})},
    }
    // Construct chat request
    req := &chat.ChatCompletionRequest{
        // Replace with Model ID
       Model: "dola-seed-2-1-turbo-260628",
       Messages: []chat.ChatCompletionRequestMessage{
chat.ChatCompletionRequestMessage{OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(chat.ChatCompletionRequestUserMessage{Role: chat.ChatCompletionRequestUserMessageRoleUser, Content: chat.NewOptNilChatCompletionMessageContent(chat.NewChatCompletionContentPartArrayChatCompletionMessageContent(contentParts))})},
       },
       MaxTokens: chat.NewOptInt32(int32(300)), // Set max output token count
    }

    // Send the chat completion request
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
       // The base URL for model invocation
       .apiKey(apiKey)
       .build();

  public static void main(String[] args) throws Exception {

    List<ChatCompletionRequestMessage> messagesForReqList = new ArrayList<>();

    // Construct content of the message
    List<ChatCompletionContentPart> contentParts = new ArrayList<>();

    // Use builder mode for the first image
    contentParts.add(ChatCompletionContentPartImage.builder()
         .type(ChatCompletionContentPartType.IMAGE_URL)
         .imageUrl(ChatCompletionContentPartImageImageUrl2.ofChatCompletionContentPartImageImageUrl(ChatCompletionContentPartImageImageUrl.builder().url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/ModelArk_demo_img_1.jpg").build()))


         .build());

    //
    contentParts.add(ChatCompletionContentPartImage.builder()
         .type(ChatCompletionContentPartType.IMAGE_URL)
         .imageUrl(ChatCompletionContentPartImageImageUrl2.ofChatCompletionContentPartImageImageUrl(ChatCompletionContentPartImageImageUrl.builder().url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/ModelArk_demo_img_2.jpg").build()))


         .build());

    contentParts.add(ChatCompletionContentPartText.builder()
         .type(ChatCompletionContentPartType.TEXT)
         .text("Which model series supports image input? Also, what are the advantages of Skylark-Pro?")

         .build());

    messagesForReqList.add(ChatCompletionRequestUserMessage.builder()
         .role(ChatCompletionRequestMessageType.USER)
         .content(ChatCompletionMessageContent.ofList(contentParts))
         .build());

    ChatCompletionRequest req = ChatCompletionRequest.builder()
         .model("dola-seed-2-1-turbo-260628") //Replace with Model ID
         .messages(messagesForReqList)
         .maxTokens(300)
         .build();

    service.createChatCompletion(req)
         .getChoices()
         .forEach(choice -> System.out.println(choice.getMessage().getContent()));
    // shutdown service after all requests are finished
    service.shutdownExecutor();
  }
}
```

## Precision control of image understanding

Controlling the precision of image understanding (referring to the detail level of the image): The **image_pixel_limit** parameter and the **detail** parameter. If both are configured, the effective logic is as follows:

* Prerequisites: The image pixel range is [196, 36000000] px; otherwise, an exception will be thrown.

* Priority: **image_pixel_limit** has higher priority than **detail**. That is, when both **detail** and **image_pixel_limit** are configured, **image_pixel_limit** takes effect.

* Effective when missing: If **min_pixels** / **max_pixels** of **image_pixel_limit** are not set, the corresponding values of **detail** are used. For specific ranges, see [Use the detail parameter (image understanding)](image-understanding.md#885d96dc).

The following sections explain how to control the precision of visual understanding via **detail** and **image_pixel_limit**.

### Use the detail parameter (image understanding)

Control the precision of the model's image understanding via the `detail` parameter. The detail options, token ranges, and image pixel ranges supported by different models are as follows:

**Method 1**:

> **Tip**

> The default value of detail for Seed 2.0 and later models is `high`, with a fixed 1280 tokens per image, consuming fewer tokens without losing quality.

|Detail options |Seed 2.0 and later models<br><br>> The default value of detail is `high` ||
|---|---|---|
| |Token range per image |Image pixel range |
|low |[1, 1280] |[1764, 2257920] |
|high |1280 |2257920 |
|xhigh |[1280, 5120] |[2257920, 9031680] |

* When detail is `low`, the image processing speed increases. It is suitable for scenarios where the image itself has few details, or only the general information of the image needs to be understood by the model, or where speed is a requirement.

* When detail is `high` or `xhigh`, the model can perceive more details of the image, but the image processing speed decreases. It is suitable for scenarios where the image has high pixel values and attention to detail is needed, such as street map analysis.

**Image scaling rule**: If the image pixel range does not fall within the range corresponding to the specified option, ModelArk will scale it proportionally to fit within the range.

**Method 2**:

The detail options and scaling rules supported by `deepseek-v4-1-flash-260910` for image understanding are as follows:

|Detail options |Image pixel range |Chat API |Responses API |Messages API |
|---|---|---|---|---|
|low |262144 |Images are automatically scaled to 512 × 512 while preserving the aspect ratio. |Images are automatically scaled to 512 × 512 while preserving the aspect ratio. |Images are automatically scaled while preserving the aspect ratio, to a size between 544 × 544 and 1300 × 1300. |
|high |[295936, 1690000] |Images are automatically scaled while maintaining their aspect ratio, from 544 × 544 to 1300 × 1300. |Images are automatically scaled while maintaining their aspect ratio, from 544 × 544 to 1300 × 1300. ||
|auto |||||
|xhigh |1690000 |Images are automatically scaled to 1300 × 1300 while preserving the aspect ratio. |Images are automatically scaled to 1300 × 1300 while preserving the aspect ratio. ||

DeepSeek V4.1 Flash series has the following **limits** for image understanding:

|Limitations |Requirements |
|---|---|
|Single image size |50 MB |
|Total request body size |64 MB |
|Number of images in a single request |3600 images |
|Total size of image files passed by URL in a single request |200 MB |

**Code sample**:

* Responses API code samples:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H 'Content-Type: application/json' \
    -d '{
        "model": "dola-seed-2-1-turbo-260628",
        "input": [
            {
                "role": "user",
                "content": [
                    {
                        "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png",
                        "detail": "high"
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

**Python**

```Python
import os
from arkruntime import Ark

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY')
)

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png",
                    "detail": "high"
                },
                {
                    "type": "input_text",
                    "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"
                }
            ]
        }
    ]
)

print(response.output)
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

    inputMessage := responses.InputItem{
    OneOf: responses.NewItemEasyMessageInputItemSum(
        responses.ItemEasyMessage{
            Role: responses.NewOptMessageRole(responses.MessageRoleUser),
            Content: responses.NewContentItemArrayMessageContent(
                []responses.ContentItem{
            responses.ContentItem{OneOf: responses.NewContentItemImageContentItemSum(responses.ContentItemImage{Type: responses.ContentItemImageTypeInputImage, ImageURL: responses.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png"), Detail: responses.NewOptImageDetail(responses.ImageDetailHigh)})},
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
import com.volcengine.ark.runtime.models.responses.ContentItemImage;
import com.volcengine.ark.runtime.models.responses.ContentItemText;
import com.volcengine.ark.runtime.models.responses.ItemEasyMessage;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
import com.volcengine.ark.runtime.models.responses.Response;
import com.volcengine.ark.runtime.models.responses.MessageContent;


public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                    MessageContent.ofList(java.util.Arrays.asList(ContentItemImage.builder().imageUrl("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png").detail(ImageDetail.HIGH).build(), ContentItemText.builder().text("Based on the image, how many planets are listed in the table, and which row contains Earth?").build()))
                        ).build())))
                .build();
        Response resp = arkService.createResponse(request);
        System.out.println(resp);

        arkService.shutdownExecutor();
    }
}
```

* Chat API code samples:

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
                {"type": "image_url","image_url": {"url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png","detail": "high"}},
                    {"type": "text", "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"}
                ]
            }
        ]
      }'
```

**Python**

```Python
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    #The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

completion = client.chat.completions.create(
    #Replace with Model ID
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "image_url","image_url": {"url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png","detail": "high"}},
                {"type": "text", "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"},
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
    // Construct a context background
    ctx := context.Background()
    // Message content
    contentParts := []chat.ChatCompletionContentPart{
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartImageChatCompletionContentPartSum(chat.ChatCompletionContentPartImage{Type: chat.ChatCompletionContentPartImageTypeImageURL, ImageURL: chat.NewChatCompletionContentPartImageImageUrlChatCompletionContentPartImageImageURL(chat.ChatCompletionContentPartImageImageUrl{URL: chat.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png"), Detail: chat.NewOptImageDetail(chat.ImageDetailHigh)})})},
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "Based on the image, how many planets are listed in the table, and which row contains Earth?"})},
    }
    req := &chat.ChatCompletionRequest{
        // Replace with Model ID
       Model: "dola-seed-2-1-turbo-260628",
       Messages: []chat.ChatCompletionRequestMessage{
chat.ChatCompletionRequestMessage{OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(chat.ChatCompletionRequestUserMessage{Role: chat.ChatCompletionRequestUserMessageRoleUser, Content: chat.NewOptNilChatCompletionMessageContent(chat.NewChatCompletionContentPartArrayChatCompletionMessageContent(contentParts))})},
       },
       MaxTokens: chat.NewOptInt32(int32(300)), // Max output token
    }

    resp, err := client.CreateChatCompletion(ctx, req)
    if err!= nil {
       fmt.Printf("standard chat error: %v\n", err)
       return
    }
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
       //The base URL for model invocation  .
       .apiKey(apiKey)
       .build();

  public static void main(String[] args) throws Exception {

    List<ChatCompletionRequestMessage> messagesForReqList = new ArrayList<>();

    List<ChatCompletionContentPart> contentParts = new ArrayList<>();

    contentParts.add(ChatCompletionContentPartImage.builder()
         .type(ChatCompletionContentPartType.IMAGE_URL)
             .imageUrl(ChatCompletionContentPartImageImageUrl2.ofChatCompletionContentPartImageImageUrl(ChatCompletionContentPartImageImageUrl.builder().url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png").detail(ImageDetail.fromValue("high")).build()))

         .build());

    contentParts.add(ChatCompletionContentPartText.builder()
         .type(ChatCompletionContentPartType.TEXT)
         .text("Based on the image, how many planets are listed in the table, and which row contains Earth?")
         .build());

    messagesForReqList.add(ChatCompletionRequestUserMessage.builder()
         .role(ChatCompletionRequestMessageType.USER)
         .content(ChatCompletionMessageContent.ofList(contentParts))
         .build());

    ChatCompletionRequest req = ChatCompletionRequest.builder()
         .model("dola-seed-2-1-turbo-260628") //Replace with Model ID  .
         .messages(messagesForReqList)
         .maxTokens(300)
         .build();

    service.createChatCompletion(req)
         .getChoices()
         .forEach(choice -> System.out.println(choice.getMessage().getContent()));
    // Shutdown service after all requests are finished
    service.shutdownExecutor();
  }
}
```

### **Use the image_pixel_limit object**

Control the pixel range of the image passed to ModelArk. If it is not within this range, it will be scaled proportionally up or down to fit within the range before being passed to the model for understanding. You can finely control the number of image pixels that the model can understand via the **image_pixel_limit** object.

The object is as follows:

> **Tip**

> `max_pixels` is the maximum number of pixels for the image, and `min_pixels` is the minimum number of pixels for the image.

```JSON
...
"image_pixel_limit": {
    "max_pixels": 3014080,
    "min_pixels": 3136
}
...
```

Code samples:

> Java SDK and Go SDK do not support this object.

* Responses API code samples:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H 'Content-Type: application/json' \
    -d '{
        "model": "dola-seed-2-1-turbo-260628",
        "input": [
            {
                "role": "user",
                "content": [
                    {
                        "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png",
                        "image_pixel_limit":  {
                            "max_pixels": 3014080,
                            "min_pixels": 3136
                         }
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

**Python**

```Python
import os
from arkruntime import Ark

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY')
)

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png",
                    "image_pixel_limit": {
                        "max_pixels": 3014080,
                        "min_pixels": 3136,
                    }
                },
                {
                    "type": "input_text",
                    "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"
                }
            ]
        }
    ]
)

print(response.output)
```

* Chat API code samples:

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
                {"type": "image_url","image_url": {"url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png","image_pixel_limit": {"max_pixels": 3014080,"min_pixels": 3136}}},
                    {"type": "text", "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"}
                ]
            }
        ],
        "max_tokens": 300
      }'
```

**Python**

```Python
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    #The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

completion = client.chat.completions.create(
    #Replace with Model ID
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "image_url",
                    "image_url": {
                        "url":  "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png",
                        "image_pixel_limit": {
                            "max_pixels": 3014080,
                            "min_pixels": 3136,
                        },
                    },
                 },
                {"type": "text", "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"},
            ],
        }
    ],
)

print(completion.choices[0])
```

## Mixed input of images and text

Supports passing prompts and images flexibly. You can pass images and text in `system message` or `User message`, and adjust the order arbitrarily. The model will return the processing results in order. Examples are as follows.

> **Tip**

> When images and text are mixed in the input, the order of images and text may affect the model's output. If the result is not as expected, you can adjust the order. When there are multiple images plus a piece of text, it is recommended to place the text after the images.

* Responses API code samples:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H 'Content-Type: application/json' \
    -d '{
        "model": "dola-seed-2-1-turbo-260628",
        "input": [
            {
                "role": "system",
                "content": [
                    {
                        "type": "input_text",
                    "text": "The following person is the target person."
                    },
                    {
                        "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/target.png"
                    },
                    {
                        "type": "input_text",
                    "text": "Please confirm whether the following images contain the target person."
                    }
                ]
            },
            {
                "role": "user",
                "content": [
                    {
                        "type": "input_text",
                    "text": "Does image 1 contain the target person?"
                    },
                    {
                        "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_01.png"
                    },
                    {
                        "type": "input_text",
                    "text": "Does image 2 contain the target person?"
                    },
                    {
                        "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_02.png"
                    }
                ]
            }
        ]
    }'
```

**Python**

```Python
import os
from arkruntime import Ark

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY')
)

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "system",
            "content": [
                {
                    "type": "input_text",
                    "text": "The following person is the target person."
                },
                {
                    "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/target.png"
                },
                {
                    "type": "input_text",
                    "text": "Please confirm whether the following images contain the target person."
                }
            ]
        },
        {
            "role": "user",
            "content": [
                {
                    "type": "input_text",
                    "text": "Does image 1 contain the target person?"
                },
                {
                    "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_01.png"
                },
                {
                    "type": "input_text",
                    "text": "Does image 2 contain the target person?"
                },
                {
                    "type": "input_image",
                    "image_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_02.png"
                }
            ]
        }
    ]
)

print(response.output)
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

    systemMessage := responses.InputItem{
    OneOf: responses.NewItemEasyMessageInputItemSum(
        responses.ItemEasyMessage{
            Role: responses.NewOptMessageRole(responses.MessageRoleSystem),
            Content: responses.NewContentItemArrayMessageContent(
                []responses.ContentItem{
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "The following person is the target person."})},
            responses.ContentItem{OneOf: responses.NewContentItemImageContentItemSum(responses.ContentItemImage{Type: responses.ContentItemImageTypeInputImage, ImageURL: responses.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/target.png")})},
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Please confirm whether the following images contain the target person."})},
                },
            ),
        },
    ),
}

    userMessage := responses.InputItem{
    OneOf: responses.NewItemEasyMessageInputItemSum(
        responses.ItemEasyMessage{
            Role: responses.NewOptMessageRole(responses.MessageRoleUser),
            Content: responses.NewContentItemArrayMessageContent(
                []responses.ContentItem{
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Does image 1 contain the target person?"})},
            responses.ContentItem{OneOf: responses.NewContentItemImageContentItemSum(responses.ContentItemImage{Type: responses.ContentItemImageTypeInputImage, ImageURL: responses.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_01.png")})},
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Does image 2 contain the target person?"})},
            responses.ContentItem{OneOf: responses.NewContentItemImageContentItemSum(responses.ContentItemImage{Type: responses.ContentItemImageTypeInputImage, ImageURL: responses.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_02.png")})},
                },
            ),
        },
    ),
}

    resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{systemMessage, userMessage}),
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
import com.volcengine.ark.runtime.models.responses.ContentItemImage;
import com.volcengine.ark.runtime.models.responses.ContentItemText;
import com.volcengine.ark.runtime.models.responses.ItemEasyMessage;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
import com.volcengine.ark.runtime.models.responses.Response;
import com.volcengine.ark.runtime.models.responses.MessageContent;


public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.SYSTEM).content(
                                    MessageContent.ofList(java.util.Arrays.asList(ContentItemText.builder().text("The following person is the target person.").build(), ContentItemImage.builder().imageUrl("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/target.png").build(), ContentItemText.builder().text("Please confirm whether the following images contain the target person.").build()))
                        ).build(), ItemEasyMessage.builder().role(MessageRole.USER).content(
                                    MessageContent.ofList(java.util.Arrays.asList(ContentItemText.builder().text("Does image 1 contain the target person?").build(), ContentItemImage.builder().imageUrl("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_01.png").build(), ContentItemText.builder().text("Does image 2 contain the target person?").build(), ContentItemImage.builder().imageUrl("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_02.png").build()))
                        ).build()))
                ).build();

        Response resp = arkService.createResponse(request);
        System.out.println(resp);

        arkService.shutdownExecutor();
    }
}
```

* Chat API code samples:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
       -H "Content-Type: application/json" \
       -H "Authorization: Bearer $ARK_API_KEY" \
       -d '{
        "model": "dola-seed-2-1-turbo-260628",
        "messages": [
            {
                "role": "system",
                "content": [
                {"type": "text", "text": "The following person is the target person."},
                    {
                        "type": "image_url",
                        "image_url": {
                        "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/target.png"
                        }
                    },
                {"type": "text", "text": "Please confirm whether the following images contain the target person."}
                ]
            },
            {
                "role": "user",
                "content": [
                {"type": "text", "text": "Does image 1 contain the target person?"},
                    {
                        "type": "image_url",
                        "image_url": {
                        "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_01.png"
                        }
                    },
                {"type": "text", "text": "Does image 2 contain the target person?"},
                    {
                        "type": "image_url",
                        "image_url": {
                        "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_02.png"
                        }
                    }
                ]
            }
        ],
        "max_tokens": 300
      }'
```

**Python**

```Python
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    #The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

completion = client.chat.completions.create(
    #Replace with Model ID
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {
            "role": "system",
            "content": [
                {"type": "text", "text": "The following person is the target person."},
                {
                    "type": "image_url",
                    "image_url": {
                        "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/target.png"
                    },
                },
                {"type": "text", "text": "Please confirm whether the following images contain the target person."}
            ],
        },
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "Does image 1 contain the target person?"},
                {
                    "type": "image_url",
                    "image_url": {
                        "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_01.png"
                    },
                },
                {"type": "text", "text": "Does image 2 contain the target person?"},
                {
                    "type": "image_url",
                    "image_url": {
                        "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_02.png"
                    },
                },
            ],
        },
    ],
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
            //The base URL for model invocation
            arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
        )
        // Create a context, typically used to pass request context information, such as timeouts and cancellations
      ctx := context.Background()

      // Build the system message content
      systemContentParts := []chat.ChatCompletionContentPart{
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "The following person is the target person."})},
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartImageChatCompletionContentPartSum(chat.ChatCompletionContentPartImage{Type: chat.ChatCompletionContentPartImageTypeImageURL, ImageURL: chat.NewChatCompletionContentPartImageImageUrlChatCompletionContentPartImageImageURL(chat.ChatCompletionContentPartImageImageUrl{URL: chat.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/target.png")})})},
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "Please confirm whether the following images contain the target person."})},
    }

      // Build the user message content
      userContentParts := []chat.ChatCompletionContentPart{
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "Does image 1 contain the target person?"})},
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartImageChatCompletionContentPartSum(chat.ChatCompletionContentPartImage{Type: chat.ChatCompletionContentPartImageTypeImageURL, ImageURL: chat.NewChatCompletionContentPartImageImageUrlChatCompletionContentPartImageImageURL(chat.ChatCompletionContentPartImageImageUrl{URL: chat.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_01.png")})})},
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "Does image 2 contain the target person?"})},
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartImageChatCompletionContentPartSum(chat.ChatCompletionContentPartImage{Type: chat.ChatCompletionContentPartImageTypeImageURL, ImageURL: chat.NewChatCompletionContentPartImageImageUrlChatCompletionContentPartImageImageURL(chat.ChatCompletionContentPartImageImageUrl{URL: chat.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_02.png")})})},
    }

      // Build a chat completion request and set the model and message content
      req := &chat.ChatCompletionRequest{
        //Replace with Model ID
        Model: "dola-seed-2-1-turbo-260628",
        Messages: []chat.ChatCompletionRequestMessage{
chat.ChatCompletionRequestMessage{OneOf: chat.NewChatCompletionRequestSystemMessageChatCompletionRequestMessageSum(chat.ChatCompletionRequestSystemMessage{Role: chat.ChatCompletionRequestSystemMessageRoleSystem, Content: chat.NewChatCompletionContentPartArrayChatCompletionMessageContent(systemContentParts)})},
chat.ChatCompletionRequestMessage{OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(chat.ChatCompletionRequestUserMessage{Role: chat.ChatCompletionRequestUserMessageRoleUser, Content: chat.NewOptNilChatCompletionMessageContent(chat.NewChatCompletionContentPartArrayChatCompletionMessageContent(userContentParts))})},
        },
        MaxTokens: chat.NewOptInt32(int32(300)),
      }

        // Send the chat completion request, store the result in resp, and store any possible errors in err
        resp, err := client.CreateChatCompletion(ctx, req)
        if err!= nil {
           fmt.Printf("standard chat error: %v\n", err)
           return
        }
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
       //The base URL for model invocation
       .apiKey(apiKey)
       .build();

  public static void main(String[] args) throws Exception {
    List<ChatCompletionRequestMessage> messagesForReqList = new ArrayList<>();

    // Build the system message content
    List<ChatCompletionContentPart> systemContentParts = new ArrayList<>();
    systemContentParts.add(ChatCompletionContentPartText.builder()
         .type(ChatCompletionContentPartType.TEXT)

         .text("The following person is the target person.")
         .build());
    systemContentParts.add(ChatCompletionContentPartImage.builder()
         .type(ChatCompletionContentPartType.IMAGE_URL)
             .imageUrl(ChatCompletionContentPartImageImageUrl2.ofChatCompletionContentPartImageImageUrl(ChatCompletionContentPartImageImageUrl.builder().url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/target.png").build()))

         .build());
    systemContentParts.add(ChatCompletionContentPartText.builder()
         .type(ChatCompletionContentPartType.TEXT)

         .text("Please confirm whether the following images contain the target person.")
         .build());

    // Create the system message
    messagesForReqList.add(ChatCompletionRequestSystemMessage.builder()
         .role(ChatCompletionRequestMessageType.SYSTEM)
         .content(ChatCompletionMessageContent.ofList(systemContentParts))
         .build());

    // Build the user message content
    List<ChatCompletionContentPart> userContentParts = new ArrayList<>();
    userContentParts.add(ChatCompletionContentPartText.builder()
         .type(ChatCompletionContentPartType.TEXT)

         .text("Does image 1 contain the target person?")
         .build());
    userContentParts.add(ChatCompletionContentPartImage.builder()
         .type(ChatCompletionContentPartType.IMAGE_URL)
             .imageUrl(ChatCompletionContentPartImageImageUrl2.ofChatCompletionContentPartImageImageUrl(ChatCompletionContentPartImageImageUrl.builder().url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_01.png").build()))

         .build());
    userContentParts.add(ChatCompletionContentPartText.builder()
         .type(ChatCompletionContentPartType.TEXT)

         .text("Does image 2 contain the target person?")
         .build());
    userContentParts.add(ChatCompletionContentPartImage.builder()
         .type(ChatCompletionContentPartType.IMAGE_URL)
             .imageUrl(ChatCompletionContentPartImageImageUrl2.ofChatCompletionContentPartImageImageUrl(ChatCompletionContentPartImageImageUrl.builder().url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/scene_02.png").build()))

         .build());

    // Create user message
    messagesForReqList.add(ChatCompletionRequestUserMessage.builder()
         .role(ChatCompletionRequestMessageType.USER)
         .content(ChatCompletionMessageContent.ofList(userContentParts))
         .build());
    ChatCompletionRequest req = ChatCompletionRequest.builder()
         .model("dola-seed-2-1-turbo-260628") //Replace with Model ID
         .messages(messagesForReqList)
         .maxTokens(300)
         .build();

    service.createChatCompletion(req)
         .getChoices()
         .forEach(choice -> System.out.println(choice.getMessage().getContent()));
    // shutdown service after all requests are finished
    service.shutdownExecutor();
  }
}
```

# Instructions

> **Tip**

> Processed images and videos will be deleted from the ModelArk server. ModelArk will not retain user data such as images, videos, and text information you submit for model training.

## About image pixels

1. The pixel ranges for images are as follows. An exception will be thrown if the limit is exceeded.

   * Width \> 14px and height \>14px

   * Width\*height range: [196px, 36000000px]

   * Aspect ratio range: [1/150, 150]

2. Image preprocessing:

   According to the model used and the set detail option, the image is scaled proportionally to the corresponding range (see [Use the detail parameter (image understanding)](image-understanding.md#885d96dc) for details), which can reduce model response latency and token consumption.

## About image token usage

Token usage is calculated based on the width and height pixels of the image. The logic for estimating image token usage for different models is as follows. For the token range of a single image, see [Use the detail parameter (image understanding)](image-understanding.md#885d96dc).

How to estimate for Seed 2.0 and later models:

```Text
min(image_width * image_height ÷ 1764, max_image_tokens)
```

Taking the maximum token count of 1280 for a single image passed to the bytedance\-seed\-2.0 model as an example, the logic for calculating the number of tokens consumed by an image is as follows:

* Image of `1280 px × 960 px`: The tokens consumed to understand this image are `1280×960÷1764=697`, which is less than 1280. According to the formula, the number of tokens consumed is 697.

* Image of `2560 px × 1440 px`: The tokens consumed to understand this image are `2560×1440÷1764=2090`, which is greater than 1280. According to the formula, the number of tokens consumed is 1280.

   In this case, the image will be compressed, meaning some details will be lost. For example, in images with very small text, the model may not be able to recognize the text.

## About image quantity

The number of images passed in a single request is limited by the model's context window. When the input is too long to be held in the model's context window, the information will be truncated.

> For model context windows, see [Model list](model-list.md).

> Examples:

> * When the total pixel value of images is large, the model context window used is 32K tokens, and each image is converted to 1312 tokens: The number of images that can be passed in a single request is `32000 ÷1312 =24`.

> * When the total pixel value of images is small, the model context window used is 32K tokens, and each image is converted to 256 tokens: The number of images that can be passed in a single request is `32000 ÷256 =125`.

> **Tip**

> The quality of the model's response is affected by the amount of information in the input images. Too many images will lead to a decline in the quality of the model's response. Please pass a reasonable number of images in a single request.

## About image size

* The **usage limits** for image files for models other than the DeepSeek V4.1 Flash series are as follows:

   * When passing images via URL, a single image cannot exceed 10 MB.

   * When passing images via Base64 strings, a single image cannot exceed 10 MB, and the request body cannot exceed 64 MB.

   * When passing images via filepath, the image cannot exceed 512 MB.

* For the **usage limits** of image files for the DeepSeek V4.1 Flash series, see [Use the detail parameter (image understanding)](image-understanding.md#885d96dc).

## Supported image formats

The supported image formats are shown in the table below. Note that the file suffix must match the image format, which means that the image file extension (when passed via URL) and the image format declaration (when passed via Base64 encoding) must be consistent with the actual image information.

|**Image format** |**File extension** |**Content Type** |
|---|---|---|
|JPEG |.jpg, .jpeg |`image/jpeg` |
|PNG |.png |`image/png` |
|GIF |.gif |`image/gif` |
|WEBP |.webp |`image/webp` |
|BMP |.bmp |`image/bmp` |
|TIFF |.tiff, .tif |`image/tiff` |
|ICO |.ico |`image/x-icon` |
|DIB |.dib |`image/bmp` |
|ICNS |.icns |`image/x-icns` |
|SGI |.sgi |`image/sgi` |
|JPEG2000 |.j2c, .j2k, .jp2, .jpc, .jpf, .jpx |`image/jp2` |
|HEIC |.heic |`image/heic`<br><br>> Supported by bytedance\-1.5\-vision\-pro and later models |
|HEIF |.heif |`image/heic`<br><br>> Supported by bytedance\-1.5\-vision\-pro and later models |

> **Tip**

* > For configurations when uploading files to TOS, see [Documentation](https://docs.byteplus.com/en/docs/tos/docs-managing-file-metadata).

* > To pass Base64 string, refer to [Pass Base64 string](image-understanding.md#477e51ce).

* > Image formats must be in lowercase.

* > For images in TIFF, SGI, ICNS, and JPEG2000 formats, make sure they match the metadata, such as by correctly setting the file metadata in object storage. Otherwise, parsing will fail. For details, see [InvalidParameter error when using the visual understanding model?](modelark-faq.md#effccb14).

## API parameter description

The following parameters are not currently supported for visual understanding:

* Setting the frequency penalty coefficient is not supported. There is no **frequency_penalty** parameter.

* Setting the presence penalty coefficient is not supported. There is no **presence_penalty** parameter.

* Generating multiple responses for a single request is not supported. There is no **n** parameter.

# FAQs

* [InvalidParameter error when using the visual understanding model?](modelark-faq.md#effccb14)
