# Document understanding

Some models support processing PDF documents and use visual capabilities to understand the context of the entire document. When a PDF document is passed in, the large model paginates the file into multiple images, then analyzes and interprets the corresponding text, images, and other information, and combines this information to complete document understanding tasks.

# Supported models

See [Capabilities](model-list.md#76df97c8).

# APIs

* [Responses API](create-model-responses-api.md): Supports PDF files as input for analysis. Document understanding through `file_id` is supported. For usage, see [Upload via Files API (recommended)](document-understanding.md#d6d033c5).

* [Chat API](chat-api.md): Supports PDF files as input for analysis. Document understanding through `file_id` is supported. For usage, see [Upload via Files API (recommended)](document-understanding.md#d6d033c5).

# Document input methods

The supported document input methods are as follows:

* Upload local files:

   * [Upload via Files API (recommended)](document-understanding.md#d6d033c5): Pass the local filepath directly. The file size cannot exceed 512 MB.

   * [Pass Base64 string](document-understanding.md#8160343d): Suitable for files smaller than 50 MB, and the request body cannot exceed 64 MB.

* [Pass document URL](document-understanding.md#69145d66): Applicable to scenarios where the file already has a publicly accessible URL. The file size cannot exceed 50 MB.

## Upload local files

### Upload via Files API (recommended)

It is recommended that you prioritize using the Files API to upload local files. It not only supports processing files up to 512 MB, but also avoids re\-uploading content when making requests, reducing latency caused by preprocessing. Besides, public network download latency can be reduced because files can be reused across multiple requests. After the upload succeeds and the file status changes to `active`, both the Responses API and Chat API support passing PDF documents through `file_id`. For how file preprocessing works, see [Appendix: file preprocessing](document-understanding.md#dbf9c161).

> * Files uploaded in this way are stored for 7 days by default, and the storage validity period ranges from 1 to 30 days.

> * If you need to obtain analysis in real time or avoid client timeout failures caused by complex tasks, you can use the streaming output. For examples, see [Streaming output](document-understanding.md#8e18e610).

* Responses API code samples:

**Curl**

1. Upload the PDF file to get the File ID.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/files \
   -H "Authorization: Bearer $ARK_API_KEY" \
   -F 'purpose=user_data' \
   -F 'file=@/Users/doc/demo.pdf'
   ```

2. Reference the File ID in the Responses API.

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
                       "type": "input_file",
                       "file_id": "file-20251018****"
                   },
                   {
                       "type": "input_text",
                   "text": "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."
                   }
               ]
           }
       ]
   }'
   ```

**Python**

```Python
import os
from pathlib import Path

from arkruntime import Ark

client = Ark(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.environ.get("ARK_API_KEY"),
)
file_path = Path(os.getenv("FILE_PATH", "/Users/doc/demo.pdf"))

uploaded_file = client.files.create(file=file_path, purpose="user_data")
client.files.wait_for_processing(uploaded_file.id)

response = client.responses.create(
    model=os.getenv("MODEL_ID", "dola-seed-2-1-turbo-260628"),
    input=[
        {
            "role": "user",
            "content": [
                {"type": "input_file", "file_id": uploaded_file.id},
                {"type": "input_text", "text": "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."},
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
        filePath = "/Users/doc/demo.pdf"
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

    media := responses.ContentItem{OneOf: responses.NewContentItemFileContentItemSum(responses.ContentItemFile{Type: responses.ContentItemFileTypeInputFile, FileID: responses.NewOptString(uploaded.ID)})}
    text := responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(
        responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."},
    )}
    message := responses.InputItem{OneOf: responses.NewItemEasyMessageInputItemSum(
        responses.ItemEasyMessage{
            Role:    responses.NewOptMessageRole(responses.MessageRoleUser),
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
package com.ark.sample;

import com.volcengine.ark.runtime.models.file.*;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
import java.io.File;
import java.util.concurrent.TimeUnit;

public class demo {

    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService service = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        System.out.println("===== Upload File Example=====");
        // upload a file for responses
        FileObject fileMeta;
        fileMeta = service.uploadFile(FileCreateRequest.builder() // replace with your file file path
                        .purpose(Purpose.USER_DATA)
                        .build(), new File("/Users/doc/demo.pdf"));
        System.out.println("Uploaded file Meta: " + fileMeta);
        System.out.println("status:" + fileMeta.getStatus());

        try {
            while ("processing".equalsIgnoreCase(fileMeta.getStatus().toString())) {
                System.out.println("Waiting for file to be processed...");
                TimeUnit.SECONDS.sleep(2);
                fileMeta = service.retrieveFile(fileMeta.getId());
            }
        } catch (Exception e) {
            System.err.println("get file status error: " + e.getMessage());
        }
        System.out.println("Uploaded file Meta: " + fileMeta);

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemFile.builder().fileId(fileMeta.getId()).build(), ContentItemText.builder().text("Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content).").build()))
                        ).build())))
                .build();
        Response resp = service.createResponse(request);
        System.out.println(resp);
        service.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
import os
import time
from openai import OpenAI

api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

file = client.files.create(
    file=open("/Users/doc/demo.pdf", "rb"),
    purpose="user_data"
)
# Wait for the file to finish processing
while (file.status == "processing"):
    time.sleep(2)
    file = client.files.retrieve(file.id)
print(f"File processed: {file}")

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_file",
                    "file_id": file.id,
                },
                {
                    "type": "input_text",
                     "text": "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."
                },
            ]
        }
    ]
)
print(response)
```

Output example:

```Plain Text
{
    "text": [
        {
            "type": "heading",
            "content": "1 Introduction"
        },
        {
            "type": "paragraph",
            "content": "Diffusion models [3–5] learn to reverse a process that incrementally corrupts data with noise, effectively decomposing a complex distribution into a hierarchy of simplified representations. This coarse-to-fine generative approach has proven remarkably successful across a wide range of applications, including image and video synthesis [6] as well as solving complex challenges in natural sciences [7]."
        },
        ...
        {
            "type": "heading",
            "content": "3 Seed Diffusion"
        },
        {
            "type": "paragraph",
            "content": "As the first experimental model in our Seed Diffusion series, Seed Diffusion Preview is specifically focused on code generation, thus adopting the data pipeline (code/code-related data only) and processing methodology of the open-sourced Seed Coder project [20]. The architecture is a standard dense Transformer, and we intentionally omit complex components such as LongCoT reasoning in this initial version to first establish a strong and efficient performance baseline. This section introduces its key components and training strategies."
        }
    ]
}
```

* Chat API code samples:

**Curl**

1. Upload the PDF file to get the File ID.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/files \
   -H "Authorization: Bearer $ARK_API_KEY" \
   -F 'purpose=user_data' \
   -F 'file=@/Users/doc/demo.pdf'
   ```

2. Reference the file ID in the Chat API.

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
    "messages": [
      {
        "role": "user",
        "content": [
          {
            "type": "file",
            "file": {
              "file_id": "file-20251018****"
            }
          },
          {
            "type": "text",
            "text": "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."
          }
        ]
      }
    ]
  }'
```

**Python**

```Python
import os
from pathlib import Path

from arkruntime import Ark

client = Ark(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.environ.get("ARK_API_KEY"),
)
file_path = Path(os.getenv("FILE_PATH", "/Users/doc/demo.pdf"))

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
                {"type": "file", "file": {"file_id": uploaded_file.id}},
                {"type": "text", "text": "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."},
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
        filePath = "/Users/doc/demo.pdf"
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

    media := chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartFileChatCompletionContentPartSum(chat.ChatCompletionContentPartFile{Type: chat.ChatCompletionContentPartFileTypeFile, File: chat.ChatCompletionContentPartFileFile{FileID: chat.NewOptString(uploaded.ID)}})}
    text := chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(
        chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."},
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
        String filePath = System.getenv().getOrDefault("FILE_PATH", "/Users/doc/demo.pdf");
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

        ChatCompletionContentPart media = ChatCompletionContentPartFile.builder().type(ChatCompletionContentPartType.FILE)._file(ChatCompletionContentPartFileFile.builder().fileId(uploaded.getId()).build()).build();
        ChatCompletionContentPart text = ChatCompletionContentPartText.builder()
                .type(ChatCompletionContentPartType.TEXT)
                .text("Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content).")
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

Convert the local file to a Base64 encoded string and submit it to the large model. This method is suitable for documents smaller than 50 MB, and the request body cannot exceed 64 MB.

> **Note**

> Convert the document file to a Base64 encoded string, concatenate it in the format `data:{mime_type};base64,{base64_data}`, and pass it to the model.

* > `{mime_type}`: The media type of the file. It must match the `mime_type` of the file format (`application/pdf`).

* > `{base64_data}`: The Base64 encoded string of the file.

**Curl**

```Bash
BASE64_FILE=$(base64 < demo.pdf) && curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
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
            "type": "input_file",
            "file_data": "data:application/pdf;base64,$BASE64_FILE",
            "filename": "demo.pdf"
          },
          {
            "type": "input_text",
            "text": "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."
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

api_key = os.getenv('ARK_API_KEY')

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# Convert local files to Base64-encoded strings.
def encode_file(file_path):
  with open(file_path, "rb") as read_file:
    return base64.b64encode(read_file.read()).decode('utf-8')
base64_file = encode_file("/Users/doc/demo.pdf")

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_file",
                    "file_data": f"data:application/pdf;base64,{base64_file}",
                    "filename": "demo.pdf"
                },
                {
                    "type": "input_text",
                    "text": "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."
                }
            ]
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
    fileBytes, err := os.ReadFile("/Users/doc/demo.pdf")
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
            responses.ContentItem{OneOf: responses.NewContentItemFileContentItemSum(responses.ContentItemFile{Type: responses.ContentItemFileTypeInputFile, FileData: responses.NewOptString(fmt.Sprintf("data:application/pdf;base64,%s", base64File)), Filename: responses.NewOptString("demo.pdf")})},
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."})},
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
        fmt.Printf("response error: %v", err)
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
            base64Data = "data:application/pdf;base64," + encodeFile("/Users/doc/demo.pdf");
        } catch (IOException e) {
            System.err.println("encode error: " + e.getMessage());
        }

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemFile.builder().fileData(base64Data).filename("demo.pdf").build(), ContentItemText.builder().text("Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content).").build()))
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
base64_file = encode_file("/Users/doc/demo.pdf")

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_file",
                    "file_data": f"data:application/pdf;base64,{base64_file}",
                    "filename": "demo.pdf"
                },
                {
                    "type": "input_text",
                    "text": "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."
                }
            ]
        }
    ]
)

print(response)
```

## Pass document URL

If the document already has a publicly accessible URL, you can directly enter the document's public URL in the Responses API request. The file cannot exceed 50 MB.

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
                    "type": "input_file",
                    "file_url": "<file URL>"
                },
                {
                    "type": "input_text",
                    "text": "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."
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
                    "type": "input_file",
                    "file_url": "<file URL>"
                },
                {
                    "type": "input_text",
                    "text": "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."
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

    inputMessage := responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role: responses.NewOptMessageRole(responses.MessageRoleUser),
                Content: responses.NewContentItemArrayMessageContent(
                    []responses.ContentItem{
            responses.ContentItem{OneOf: responses.NewContentItemFileContentItemSum(responses.ContentItemFile{Type: responses.ContentItemFileTypeInputFile, FileURL: responses.NewOptString("<file URL>")})},
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."})},
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
        //The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemFile.builder().fileUrl("<file URL>").build(), ContentItemText.builder().text("Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content).").build()))
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
                    "type": "input_file",
                    "file_url": "<file URL>"
                },
                {
                    "type": "input_text",
                    "text": "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."
                },
            ],
        }
    ]
)

print(response)
```

# Streaming output

Streaming output supports dynamic real\-time content presentation. It can not only ease users' waiting anxiety, but also avoid client timeout failures caused by long inference time for complex tasks, ensuring the smooth request process.

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
    # upload pdf file
    print("Upload pdf file")
    file = await client.files.create(
        # replace with your local pdf path
        file=open("/Users/doc/demo.pdf", "rb"),
        purpose="user_data"
    )
    print(f"File uploaded: {file.id}")

    # Wait for the file to finish processing
    await client.files.wait_for_processing(file.id)
    print(f"File processed: {file.id}")

    stream = await client.responses.create(
        model="dola-seed-2-1-turbo-260628",
        input=[
            {"role": "user", "content": [
                {
                    "type": "input_file",
                    "file_id": file.id  # ref pdf file id
                },
                {
                    "type": "input_text",
                    "text": "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."
                }
            ]},
        ],
        caching={
            "type": "enabled",
        },
        store=True,
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

**Go**

```Go
package main

import (
    "context"
    "fmt"
    "io"
    "os"
    "time"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/file"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/responses"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    fmt.Println("----- upload file data -----")
    data, err := os.Open("/Users/doc/demo.pdf")
    if err != nil {
        fmt.Printf("read file error: %v\n", err)
        return
    }
    fileInfo, err := client.UploadFile(ctx, &file.FileCreateRequest{
Purpose: file.PurposeUserData,
    }, data)

    if err != nil {
        fmt.Printf("upload file error: %v", err)
        return
    }

    // Wait for the file to finish processing
    for fileInfo.Status == file.StatusProcessing {
        fmt.Println("Waiting for file to be processed...")
        time.Sleep(2 * time.Second)
        fileInfo, err = client.RetrieveFile(ctx, fileInfo.ID) // update file info
        if err != nil {
            fmt.Printf("get file status error: %v", err)
            return
        }
    }
    fmt.Printf("File processing completed: %s, status: %s\n", fileInfo.ID, fileInfo.Status)
    inputMessage := responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role: responses.NewOptMessageRole(responses.MessageRoleUser),
                Content: responses.NewContentItemArrayMessageContent(
                    []responses.ContentItem{
            responses.ContentItem{OneOf: responses.NewContentItemFileContentItemSum(responses.ContentItemFile{Type: responses.ContentItemFileTypeInputFile, FileID: responses.NewOptString(fileInfo.ID)})},
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content)."})},
                    },
                ),
            },
        ),
    }
    createResponsesReq := &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{inputMessage}),
        Caching: responses.NewOptResponseCaching(responses.ResponseCaching{Type: responses.NewOptCacheMode(responses.CacheModeEnabled)}),
    }

    resp, err := client.CreateResponsesStream(ctx, createResponsesReq)
    if err != nil {
        fmt.Printf("stream error: %v\n", err)
        return
    }
    var responseId string
    for {
        event, err := resp.Recv()
        if err == io.EOF {
            break
        }
        if err != nil {
            fmt.Printf("stream error: %v\n", err)
            return
        }
        handleEvent(event)
        if event.OneOf.Type == responses.ResponseCreatedEventResponseStreamEventSum {
                        responseId = event.OneOf.ResponseCreatedEvent.Response.ID
                        fmt.Printf("Response ID: %s", responseId)
                }
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

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.file.*;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
import java.io.File;
import java.util.concurrent.TimeUnit;

public class demo {

    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService service = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        System.out.println("===== Upload File Example=====");
        // upload a file for responses
        FileObject fileMeta;
        fileMeta = service.uploadFile(FileCreateRequest.builder() // replace with your file file path
                        .purpose(Purpose.USER_DATA)
                        .build(), new File("/Users/doc/demo.pdf"));
        System.out.println("Uploaded file Meta: " + fileMeta);
        System.out.println("status:" + fileMeta.getStatus());

        try {
            while ("processing".equalsIgnoreCase(fileMeta.getStatus().toString())) {
                System.out.println("Waiting for file to be processed...");
                TimeUnit.SECONDS.sleep(2);
                fileMeta = service.retrieveFile(fileMeta.getId());
            }
        } catch (Exception e) {
            System.err.println("get file status error: " + e.getMessage());
        }
        System.out.println("Uploaded file Meta: " + fileMeta);

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .stream(true)
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemFile.builder().fileId(fileMeta.getId()).build(), ContentItemText.builder().text("Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content).").build()))
                        ).build())))
                .build();

        service.streamResponse(request)
                .doOnError(Throwable::printStackTrace)
                .blockingForEach(event -> {
                    if (event instanceof ResponseReasoningSummaryTextDeltaEvent) {
                        System.out.print(((ResponseReasoningSummaryTextDeltaEvent) event).getDelta());
                    }
                    if (event instanceof ResponseOutputItemAddedEvent) {
                        System.out.println("\nOutputItem " + (((ResponseOutputItemAddedEvent) event).getItem().getType()) + " Start: ");
                    }
                    if (event instanceof ResponseTextDeltaEvent) {
                        System.out.print(((ResponseTextDeltaEvent) event).getDelta());
                    }
                    if (event instanceof ResponseTextDoneEvent) {
                        System.out.println("\nOutputText End.");
                    }
                    if (event instanceof ResponseOutputItemDoneEvent) {
                        System.out.println("\nOutputItem " + ((ResponseOutputItemDoneEvent) event).getItem().getType() + " End.");
                    }
                    if (event instanceof ResponseFunctionCallArgumentsDoneEvent) {
                        System.out.println("\nFunctionCall Arguments: " + ((ResponseFunctionCallArgumentsDoneEvent) event).getArguments());
                    }
                    if (event instanceof ResponseCompletedEvent) {
                        System.out.println("\nResponse Completed. Usage = " + ((ResponseCompletedEvent) event).getResponse().getUsage());
                    }
                });


        service.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
import os
import time
from openai import OpenAI

api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

file = client.files.create(
    file=open("/Users/doc/demo.pdf", "rb"),
    purpose="user_data"
)
# Wait for the file to finish processing
while (file.status == "processing"):
    time.sleep(2)
    file = client.files.retrieve(file.id)
print(f"File processed: {file}")

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_file",
                    "file_id": file.id,
                },
                {
                    "type": "input_text",
                    "text": "Provide the document’s text content by paragraph and output it in JSON format, including the paragraph type (type) and text content (content).",
                },
            ]
        }
    ],
    stream=True
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

# Appendix: file preprocessing

PDF files are processed page by page into multiple images. During preprocessing, the split images are not scaled in resolution, so that the original information in the PDF file can be preserved completely and clearly. When used as input, it is automatically scaled according to the `auto` behavior of the model's **input.content.detail** parameter.
