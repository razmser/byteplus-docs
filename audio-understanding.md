# Audio understanding

Some large models support audio understanding. You can input audio via local files or audio URLs, and perform semantic understanding and parsing of audio content. This feature is applicable to scenarios such as speech transcription, dialogue semantic extraction, audio moderation, meeting minutes generation, and video audio track analysis.

> **Tip**

> If you're new to ModelArk, see [Quick start](quick-start.md) to get up and running quickly.

# Supported models

See [Capabilities](model-list.md#9619c0ba).

# APIs

* [Responses API](create-model-responses-api.md): Supports audio as input for analysis. Accepts File ID as an input for audio understanding. For instructions, see [Upload via Files API (recommended)](audio-understanding.md#dba3306f).

* [Chat API](chat-api.md): Supports audio as input for analysis. Accepts File ID as an input for audio understanding. For instructions, see [Upload via Files API (recommended)](audio-understanding.md#dba3306f).

# Audio input methods

The supported audio input methods are as follows:

* Upload local files:

   * [Upload via Files API (recommended)](audio-understanding.md#dba3306f): Directly pass local files. The audio file size cannot exceed 512 MB. It is applicable to scenarios where files are reused in multiple requests.

   * [Pass Base64 string](audio-understanding.md#607f74ca): Suitable for audios smaller than 25 MB and shorter than 120 minutes.

* [Pass audio URL](audio-understanding.md#268050a0): Suitable for audios publicly accessible through URLs which are smaller than 25 MB and shorter than 120 minutes.

## Upload local files

### **Upload via Files API (recommended)**

It is recommended that you prioritize using the Files API to upload local files. It not only supports processing files up to 512 MB, but also avoids re\-uploading content when making requests, reducing latency caused by preprocessing. Besides, public network download latency can be reduced because files can be reused across multiple requests. After the upload succeeds and the file status changes to `active`, both the Responses API and Chat API support passing audios through `file_id`.

> * Files uploaded in this way are stored for 7 days by default, and the storage validity period ranges from 1 to 30 days.

> * If you need to obtain analysis in real time or avoid client timeout failures caused by complex tasks, you can use the streaming output. For examples, see [Streaming output](audio-understanding.md#3fe052be).

* Responses API code samples:

**Curl**

1. Upload the audio file to get the File ID.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/files \
   -H "Authorization: Bearer $ARK_API_KEY" \
   -F 'purpose=user_data' \
   -F 'file=@/Users/doc/demo.mp3'
   ```

2. Reference the File ID in the Responses API.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
   -H "Authorization: Bearer $ARK_API_KEY" \
   -H 'Content-Type: application/json' \
   -d '{
       "model": "seed-2-0-lite-260428",
       "input": [
           {
               "role": "user",
               "content": [
                   {
                       "type": "input_audio",
                       "file_id": "file-20260415****"
                   },
                   {
                       "type": "input_text",
                       "text": "Recognize the content in the audio and return the recognition result in textual form."
                   }
               ]
           }
       ]
   }'
   ```

* Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

**Python**

```Python
import os
from pathlib import Path

from arkruntime import Ark

client = Ark(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.environ.get("ARK_API_KEY"),
)
file_path = Path(os.getenv("FILE_PATH", "/Users/doc/demo.mp3"))

uploaded_file = client.files.create(file=file_path, purpose="user_data")
client.files.wait_for_processing(uploaded_file.id)

response = client.responses.create(
    model=os.getenv("MODEL_ID", "seed-2-0-lite-260428"),
    input=[
        {
            "role": "user",
            "content": [
                {"type": "input_audio", "file_id": uploaded_file.id},
                {
                    "type": "input_text",
                    "text": "Recognize the content in the audio and return the recognition result in textual form.",
                },
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
        filePath = "/Users/doc/demo.mp3"
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

    media := responses.ContentItem{OneOf: responses.NewContentItemAudioContentItemSum(responses.ContentItemAudio{Type: responses.ContentItemAudioTypeInputAudio, FileID: responses.NewOptString(uploaded.ID)})}
    text := responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(
        responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Recognize the content in the audio and return the recognition result in textual form."},
    )}
    message := responses.InputItem{OneOf: responses.NewItemEasyMessageInputItemSum(
        responses.ItemEasyMessage{
            Role:    responses.NewOptMessageRole(responses.MessageRoleUser),
            Content: responses.NewContentItemArrayMessageContent([]responses.ContentItem{media, text}),
        },
    )}
    modelID := os.Getenv("MODEL_ID")
    if modelID == "" {
        modelID = "seed-2-0-lite-260428"
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

public class Demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService service = ArkService.builder()
                .apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        System.out.println("===== Upload Audio File Example =====");
        FileObject fileMeta;
        fileMeta = service.uploadFile(FileCreateRequest.builder() // Replace with your local audio file path
                        .purpose(Purpose.USER_DATA)
                        .build(), new File("/Users/doc/demo.mp3"));
        System.out.println("Uploaded file Meta: " + fileMeta);
        System.out.println("status: " + fileMeta.getStatus());

        try {
            while ("processing".equalsIgnoreCase(fileMeta.getStatus().toString())) {
                System.out.println("Waiting for audio to be processed...");
                TimeUnit.SECONDS.sleep(2);
                fileMeta = service.retrieveFile(fileMeta.getId());
            }
        } catch (Exception e) {
            System.err.println("get file status error: " + e.getMessage());
        }
        System.out.println("Processed file Meta: " + fileMeta);

        ResponsesRequest request = ResponsesRequest.builder()
                .model("seed-2-0-lite-260428")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder()
                                .role(MessageRole.USER)
                                .content(MessageContent.ofList(java.util.Arrays.asList(ContentItemAudio.builder().fileId(fileMeta.getId()).build(), ContentItemText.builder()

                                                .text("Recognize the content in the audio and return the recognition result in textual form.")
                                                .build()))
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
    file=open("/Users/doc/demo.mp3", "rb"),
    purpose="user_data"
)

# Wait for the file to finish processing
while file.status == "processing":
    time.sleep(2)
    file = client.files.retrieve(file.id)
print(f"File processed: {file.id}")

response = client.responses.create(
    model="seed-2-0-lite-260428",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_audio",
                    "file_id": file.id,
                },
                {
                    "type": "input_text",
                    "text": "Recognize the content in the audio and return the recognition result in textual form."
                },
            ]
        }
    ]
)

print(response)
```

* Chat API code samples:

**Curl**

1. Upload the audio file to get the file ID.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/files \
   -H "Authorization: Bearer $ARK_API_KEY" \
   -F 'purpose=user_data' \
   -F 'file=@/Users/doc/demo.mp3'
   ```

2. Reference the file ID in the Chat API.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
     -H "Authorization: Bearer $ARK_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
       "model": "seed-2-0-lite-260428",
       "messages": [
         {
           "role": "user",
           "content": [
             {
               "type": "input_audio",
               "input_audio": {
                 "file_id": "file-20260415****"
               }
             },
             {
               "type": "text",
               "text": "Recognize the content in the audio and return the recognition result in textual form."
             }
           ]
         }
       ]
     }'
   ```

* Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

**Python**

```Python
import os
from pathlib import Path

from arkruntime import Ark

client = Ark(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.environ.get("ARK_API_KEY"),
)
file_path = Path(os.getenv("FILE_PATH", "/Users/doc/demo.mp3"))

uploaded_file = client.files.create(
    file=file_path,
    purpose="user_data",
)
client.files.wait_for_processing(uploaded_file.id)

completion = client.chat.completions.create(
    model=os.getenv("MODEL_ID", "seed-2-0-lite-260428"),
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "input_audio", "input_audio": {"file_id": uploaded_file.id}},
                {"type": "text", "text": "Recognize the content in the audio and return the recognition result in textual form."},
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
        filePath = "/Users/doc/demo.mp3"
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

    media := chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartAudioChatCompletionContentPartSum(chat.ChatCompletionContentPartAudio{Type: chat.ChatCompletionContentPartAudioTypeInputAudio, InputAudio: chat.ChatCompletionContentPartAudioInputAudio{FileID: chat.NewOptString(uploaded.ID)}})}
    text := chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(
        chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "Recognize the content in the audio and return the recognition result in textual form."},
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
        modelID = "seed-2-0-lite-260428"
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
        String filePath = System.getenv().getOrDefault("FILE_PATH", "/Users/doc/demo.mp3");
        String modelId = System.getenv().getOrDefault("MODEL_ID", "seed-2-0-lite-260428");

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

        ChatCompletionContentPart media = ChatCompletionContentPartAudio.builder().type(ChatCompletionContentPartType.INPUT_AUDIO).inputAudio(ChatCompletionContentPartAudioInputAudio.builder().fileId(uploaded.getId()).build()).build();
        ChatCompletionContentPart text = ChatCompletionContentPartText.builder()
                .type(ChatCompletionContentPartType.TEXT)
                .text("Recognize the content in the audio and return the recognition result in textual form.")
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

Convert the local file to a Base64 encoded string and submit it to the large model. This method is suitable for audios smaller than 25 MB and shorter than 120 minutes. (Supported by both Responses API and Chat API.)

> **Note**

> When you pass audios via Base64 strings, you need to process the data format according to different API types:

* > Responses API: Concatenate the data in the `data:{mime_type};base64,{base64_data}` format, and pass it to the model through the `audio_url` parameter.`{mime_type}`: The media type of the file, which must be identical with the file's `mime_type`. For details of supported audio formats, see Supported audio formats.`{base64_data}`: The Base64 encoded string of the file.

* > Chat API: Directly fill the Base64 encoded audio data `{base64_data}` into `input_audio.data`, and specify the audio format (such as mp3/wav) separately through the `input_audio.format` parameter.

* Responses API code samples:

**Curl**

```Bash
BASE64_FILE=$(base64 < /Users/doc/demo.mp3) && curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
   -H "Content-Type: application/json"  \
   -H "Authorization: Bearer $ARK_API_KEY"  \
   -d @- <<EOF
   {
    "model": "seed-2-0-lite-260428",
    "input": [
      {
        "role": "user",
        "content": [
          {
            "type": "input_audio",
            "audio_url": "data:audio/mpeg;base64,$BASE64_FILE"
          },
          {
            "type": "input_text",
            "text": "Recognize the content in the audio."
          }
        ]
      }
    ]
  }
EOF
```

* Replace /Users/doc/demo.mp3 with your own audio path.

* Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

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

# Convert local audio file to Base64-encoded string
def encode_file(file_path):
    with open(file_path, "rb") as read_file:
        return base64.b64encode(read_file.read()).decode('utf-8')

base64_file = encode_file("/Users/doc/demo.mp3")

response = client.responses.create(
    model="seed-2-0-lite-260428",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_audio",
                    "audio_url": f"data:audio/mpeg;base64,{base64_file}"
                },
                {
                    "type": "input_text",
                    "text": "Recognize the content in the audio."
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
// Convert local audio file to Base64-encoded strings.
    fileBytes, err := os.ReadFile("/Users/doc/demo.mp3")
    if err != nil {
        fmt.Printf("read audio file error: %v\n", err)
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
            responses.ContentItem{OneOf: responses.NewContentItemAudioContentItemSum(responses.ContentItemAudio{Type: responses.ContentItemAudioTypeInputAudio, AudioURL: responses.NewOptString(fmt.Sprintf("data:audio/mpeg;base64,%s", base64File))})},
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Recognize the content in the audio."})},
                    },
                ),
            },
        ),
    }

    resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model: "seed-2-0-lite-260428",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{inputMessage}),
    })
    if err != nil {
        fmt.Printf("audio recognition response error: %v\n", err)
        return
    }

    fmt.Println(resp)
}
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.responses.*;
import com.volcengine.ark.runtime.service.ArkService;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.Base64;
import java.io.IOException;

public class Sample {
    private static String encodeFile(String filePath) throws IOException {
        byte[] fileBytes = Files.readAllBytes(Paths.get(filePath));
        return Base64.getEncoder().encodeToString(fileBytes);
    }

    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService arkService = ArkService.builder()
                .apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

// Convert local audio file to Base64 data URL
        String base64AudioData = "";
        try {
            base64AudioData = "data:audio/mpeg;base64," + encodeFile("/Users/doc/demo.mp3");
        } catch (IOException e) {
            System.err.println("Encode audio file failed: " + e.getMessage());
            return;
        }

        ResponsesRequest request = ResponsesRequest.builder()
                .model("seed-2-0-lite-260428")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder()
                                .role(MessageRole.USER)
                                .content(MessageContent.ofList(java.util.Arrays.asList(ContentItemAudio.builder().audioUrl(base64AudioData).build(), ContentItemText.builder().text("Recognize the content in the audio.").build()))
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

# Audio filepath
base64_file = encode_file("/Users/doc/demo.mp3")

response = client.responses.create(
    model="seed-2-0-lite-260428",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_audio",
                    "audio_url": f"data:audio/mpeg;base64,{base64_file}"
                },
                {
                    "type": "input_text",
                    "text": "Recognize the content in the audio."
                }
            ],
        }
    ]
)

print(response)
```

* Chat API code samples:

**Curl**

```Bash
BASE64_FILE=$(base64 < /Users/doc/demo.mp3) && curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
   -H "Content-Type: application/json"  \
   -H "Authorization: Bearer $ARK_API_KEY"  \
   -d @- <<EOF
   {
    "model": "seed-2-0-lite-260428",
    "messages": [
      {
        "role": "user",
        "content": [
          {
            "type": "input_audio",
            "input_audio": {
                "data": "$BASE64_FILE",
                "format": "mp3"
                }
          },
          {
            "type": "text",
            "text": "Recognize the content in the audio."
          }
        ]
      }
    ]
  }
EOF
```

* Replace /Users/doc/demo.mp3 with your own audio path.

* Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

**Python**

```Python
import base64
import os
# Install SDK:  python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.getenv('ARK_API_KEY'),
)

# Define a method to convert the audio at the specified path to Base64 string
def encode_audio(audio_path):
    with open(audio_path, "rb") as audio_file:
        return base64.b64encode(audio_file.read()).decode('utf-8')

# Audio to be passed to the large model
audio_path = "/Users/doc/demo.mp3"

# Convert the audio to Base64 string
base64_audio = encode_audio(audio_path)

completion = client.chat.completions.create(
    # Replace with Model ID
    model="seed-2-0-lite-260428",
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_audio",
                    "input_audio": {
                        "data": base64_audio,
                        "format": "mp3"
                    }
                },
                {
                    "type": "text",
                    "text": "Recognize the content in the audio."
                },
            ],
        }
    ],
)

print(completion.choices[0])
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
        .apiKey(apiKey)
        .build();

// Base64 string of the audio file (no prefix, just plain Base64)
    private static String encodeAudio(String audioPath) throws IOException {
        byte[] audioBytes = Files.readAllBytes(java.nio.file.Paths.get(audioPath));
        return Base64.getEncoder().encodeToString(audioBytes);
    }

    public static void main(String[] args) throws Exception {
        List<ChatCompletionRequestMessage> messagesForReqList = new ArrayList<>();

// Your audio path (change to your actual path)
        String audioPath = "/Users/doc/demo.mp3";
// Audio Base64 string
        String base64Data = encodeAudio(audioPath);

// Build multimodal content
        List<ChatCompletionContentPart> contentParts = new ArrayList<>();

// Audio part (input_audio + data + format)
        ChatCompletionContentPartAudioInputAudio inputAudio = new ChatCompletionContentPartAudioInputAudio();
        inputAudio.setData(base64Data);
        inputAudio.setFormat("mp3");

        contentParts.add(ChatCompletionContentPartAudio.builder()
                .type(ChatCompletionContentPartType.INPUT_AUDIO)
                .inputAudio(inputAudio)
                .build());

// Text instruction
        contentParts.add(ChatCompletionContentPartText.builder()
                .type(ChatCompletionContentPartType.TEXT)

                .text("Recognize the content in the audio.")
                .build());

// Construct user message
        messagesForReqList.add(ChatCompletionRequestUserMessage.builder()
                .role(ChatCompletionRequestMessageType.USER)
                .content(ChatCompletionMessageContent.ofList(contentParts))
                .build());

// Request body
        ChatCompletionRequest req = ChatCompletionRequest.builder()
                .model("seed-2-0-lite-260428")
                .messages(messagesForReqList)
                .build();

// Call and print results
        service.createChatCompletion(req)
                .getChoices()
                .forEach(choice -> System.out.println(choice.getMessage().getContent()));

        service.shutdownExecutor();
    }
}
```

## Pass audio URL

If the audio already has a publicly accessible URL, you can directly fill in the public URL of the audio in the request. The audio must be smaller than 25 MB and shorter than 120 minutes. (Supported by both Responses API and Chat API.)

* Responses API code samples:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
-H "Authorization: Bearer $ARK_API_KEY" \
-H 'Content-Type: application/json' \
-d '{
    "model": "seed-2-0-lite-260428",
    "input": [
        {
            "role": "user",
            "content": [
                {
                    "type": "input_audio",
                    "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/resources/demo_audio.mp3"
                },
                {
                    "type": "input_text",
                    "text": "Recognize the content in the audio."
                }
            ]
        }
    ]
}'
```

* Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

**Python**

```Python
import os
from arkruntime import Ark

# Get API Key from environment variable
api_key = os.getenv('ARK_API_KEY')

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# Call responses API with audio URL input
response = client.responses.create(
    model="seed-2-0-lite-260428",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_audio",
                    "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/resources/demo_audio.mp3"
                },
                {
                    "type": "input_text",
                    "text": "Recognize the content in the audio."
                }
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
// Get ARK_API_KEY from environment variable
                os.Getenv("ARK_API_KEY"),
                arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
        )
        ctx := context.Background()

// Construct user input: audio URL + text prompt
        inputMessage := responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role: responses.NewOptMessageRole(responses.MessageRoleUser),
                Content: responses.NewContentItemArrayMessageContent(
                    []responses.ContentItem{
            responses.ContentItem{OneOf: responses.NewContentItemAudioContentItemSum(responses.ContentItemAudio{Type: responses.ContentItemAudioTypeInputAudio, AudioURL: responses.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/files/resources/demo_audio.mp3")})},
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Recognize the content in the audio."})},
                    },
                ),
            },
        ),
    }

// Send request to CreateResponses API
        resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
                Model: "seed-2-0-lite-260428",
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
package com.ark.example;

import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("seed-2-0-lite-260428")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemAudio.builder().audioUrl("https://arkdocs-en.tos-ap-southeast-1.volces.com/files/resources/demo_audio.mp3").build(), ContentItemText.builder().text("Recognize the content in the audio.").build()))
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

# Get API key from environment variables
api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

response = client.responses.create(
    model="seed-2-0-lite-260428",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_audio",
                    "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/resources/demo_audio.mp3"
                },
                {
                    "type": "input_text",
                    "text": "Recognize the content in the audio."
                }
            ]
        }
    ]
)

print(response)
```

* Chat API code samples:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
-H "Content-Type: application/json"  \
-H "Authorization: Bearer $ARK_API_KEY"  \
-d '{
    "model": "seed-2-0-lite-260428",
    "messages": [
        {
            "role": "user",
            "content": [
                {
                    "type": "input_audio",
                    "input_audio": {
                        "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/resources/demo_audio.mp3",
                        "format": "mp3"
                    }
                },
                {
                    "type": "text",
                    "text": "Recognize the content in the audio."
                }
            ]
        }
    ]
}'
```

* Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

**Python**

```Python
import os
# Install SDK:  python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

completion = client.chat.completions.create(
    # Replace with Model ID
    model = "seed-2-0-lite-260428",
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_audio",
                    "input_audio": {
                        "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/resources/demo_audio.mp3",
                        "format": "mp3"
                    }
                },
                {"type": "text", "text": "Recognize the content in the audio."},
            ],
        }
    ],
)

print(completion.choices[0])
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

public class Sample {
    static String apiKey = System.getenv("ARK_API_KEY");
    static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
    static Dispatcher dispatcher = new Dispatcher();
    static ArkService service = ArkService.builder()
        .dispatcher(dispatcher)
        .connectionPool(connectionPool)
        .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
        .apiKey(apiKey)
        .build();

    public static void main(String[] args) throws Exception {

        List<ChatCompletionRequestMessage> messagesForReqList = new ArrayList<>();

        List<ChatCompletionContentPart> contentParts = new ArrayList<>();

        ChatCompletionContentPartAudioInputAudio inputAudio = new ChatCompletionContentPartAudioInputAudio();

        inputAudio.setUrl("https://arkdocs-en.tos-ap-southeast-1.volces.com/files/resources/demo_audio.mp3");
        inputAudio.setFormat("mp3");

        contentParts.add(ChatCompletionContentPartAudio.builder()
                .type(ChatCompletionContentPartType.INPUT_AUDIO)
                .inputAudio(inputAudio)
                .build());

        contentParts.add(ChatCompletionContentPartText.builder()
                .type(ChatCompletionContentPartType.TEXT)

                .text("Recognize the content in the audio.")
                .build());

        messagesForReqList.add(ChatCompletionRequestUserMessage.builder()
                .role(ChatCompletionRequestMessageType.USER)
                .content(ChatCompletionMessageContent.ofList(contentParts))
                .build());

        ChatCompletionRequest req = ChatCompletionRequest.builder()
                .model("seed-2-0-lite-260428")
                .messages(messagesForReqList)
                .build();

        service.createChatCompletion(req)
                .getChoices()
                .forEach(choice -> System.out.println(choice.getMessage().getContent()));

        service.shutdownExecutor();
    }
}
```

# **Use cases**

## Audio input embedded in videos

ModelArk supports parsing and semantic understanding of audio tracks embedded in videos. You can directly use the complete video as input instead of manually extracting the audio, and the model will automatically extract the audio track to complete related tasks, such as speech recognition, content understanding, and emotion and tone analysis. For formats and input parameter specifications, see [Video understanding](video-understanding.md).

When uploading a video with sound tracks through the Files API, you must pass the ID of a model that supports audio understanding in `preprocess_configs.video.model` of the upload request. This parameter specifies the video preprocessing policy; when subsequently calling the Responses API or Chat API, you still need to pass the actual model or endpoint used for inference in `model` of the request. The two `model` parameters are independent of each other and can be configured separately for the upload preprocessing and inference stages.

The following example uses the Files API to upload a video, and then references the File ID through the Responses API.

**Curl**

1. Upload the video file to get the File ID.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/files \
   -H "Authorization: Bearer $ARK_API_KEY" \
   -F 'purpose=user_data' \
   -F 'file=@/Users/doc/demo.mp4' \
   -F 'preprocess_configs[video][model]=seed-2-0-lite-260428'
   ```

2. Reference the File ID in the Responses API.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
   -H "Content-Type: application/json"  \
   -H "Authorization: Bearer $ARK_API_KEY"  \
   -d '{
       "model": "seed-2-0-lite-260428",
       "input": [
           {
               "role": "user",
               "content": [
                   {
                       "type": "input_video",
                       "file_id": "file-20251018****"
                   },
                   {
                       "type": "input_text",
                       "text": "Please identify the audio content in the video, analyze the timbre characteristics, speaker tone, speech rate and emotional tendency in the audio, and output complete and clear text results."
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
file_path = Path(os.getenv("FILE_PATH", "/Users/doc/demo.mp4"))

uploaded_file = client.files.create(
    file=file_path,
    purpose="user_data",
    preprocess_configs={
        "video": {
            "model": "seed-2-0-lite-260428",
        }
    },
)
client.files.wait_for_processing(uploaded_file.id)

response = client.responses.create(
    model="seed-2-0-lite-260428",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_video",
                    "file_id": uploaded_file.id,
                },
                {
                    "type": "input_text",
                    "text": "Please identify the audio content in the video, analyze the timbre characteristics, speaker tone, speech rate and emotional tendency in the audio, and output complete and clear text results."
                }
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
        filePath = "/Users/doc/demo.mp4"
    }
    data, err := os.Open(filePath)
    if err != nil {
        panic(err)
    }
    defer data.Close()

    uploaded, err := client.UploadFile(ctx, &file.FileCreateRequest{
        Purpose: file.PurposeUserData,
        PreprocessConfigs: file.NewOptPreprocessConfigs(file.PreprocessConfigs{
            Video: file.NewOptVideo(file.Video{
                Model: file.NewOptString("seed-2-0-lite-260428"),
            }),
        }),
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

    inputMessage := responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role: responses.NewOptMessageRole(responses.MessageRoleUser),
                Content: responses.NewContentItemArrayMessageContent(
                    []responses.ContentItem{
                        responses.ContentItem{OneOf: responses.NewContentItemVideoContentItemSum(responses.ContentItemVideo{Type: responses.ContentItemVideoTypeInputVideo, VideoURL: "", FileID: responses.NewOptString(uploaded.ID)})},
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Please identify the audio content in the video, analyze the timbre characteristics, speaker tone, speech rate and emotional tendency in the audio, and output complete and clear text results."})},
                    },
                ),
            },
        ),
    }
    resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model: "seed-2-0-lite-260428",
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
package com.ark.example;
import com.volcengine.ark.runtime.models.file.*;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
import java.io.File;

public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();
        String filePath = System.getenv().getOrDefault("FILE_PATH", "/Users/doc/demo.mp4");

        FileObject uploaded = arkService.uploadFile(
                FileCreateRequest.builder()
                        .purpose(Purpose.USER_DATA)
                        .preprocessConfigs(PreprocessConfigs.builder().video(Video.builder().model("seed-2-0-lite-260428").build()).build())
                        .build(),
                new File(filePath)
        );
        uploaded = arkService.waitForFileProcessing(uploaded.getId());
        if (uploaded.getStatus() != Status.ACTIVE) {
            throw new IllegalStateException("File processing failed: " + uploaded.getStatus());
        }

        ResponsesRequest request = ResponsesRequest.builder()
                .model("seed-2-0-lite-260428")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemVideo.builder().fileId(uploaded.getId()).build(), ContentItemText.builder().text("Please identify the audio content in the video, analyze the timbre characteristics, speaker tone, speech rate and emotional tendency in the audio, and output complete and clear text results.").build()))
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
import time

from openai import OpenAI

client = OpenAI(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.environ.get("ARK_API_KEY"),
)
file_path = os.getenv("FILE_PATH", "/Users/doc/demo.mp4")

with open(file_path, "rb") as video_file:
    file = client.files.create(
        file=video_file,
        purpose="user_data",
        extra_body={
            "preprocess_configs": {
                "video": {
                    "model": "seed-2-0-lite-260428",
                }
            }
        },
    )

for _ in range(60):
    if file.status != "processing":
        break
    time.sleep(2)
    file = client.files.retrieve(file.id)

if file.status != "processed" and file.status != "active":
    raise RuntimeError(f"File processing failed or timed out: {file.status}")

response = client.responses.create(
    model="seed-2-0-lite-260428",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_video",
                    "file_id": file.id,
                },
                {
                    "type": "input_text",
                    "text": "Please identify the audio content in the video, analyze the timbre characteristics, speaker tone, speech rate and emotional tendency in the audio, and output complete and clear text results."
                }
            ],
        }
    ]
)
print(response)
```

> **Note**

> Replace the model ID as needed. To process audio information embedded in a video, use a model that supports audio understanding. For details, see [Model list](model-list.md#9619c0ba).

## General audio question answering

It performs open semantic understanding and intelligent question answering based on audio content. The model can fully parse audio information, combine users' text questions, and output structured, analyzable professional answers. According to the interaction mode, it is divided into two sub\-scenarios: single\-turn conversation and multi\-turn conversation.

### Single\-turn conversation

It adopts a lightweight one\-question\-one\-answer interaction mode, which is suitable for lightweight requirements such as audio type identification, environment recognition, content detail query, and basic information interpretation.

Here are some simple code samples:

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "seed-2-0-lite-260428",
    "instructions": "You are an expert in audio understanding, specializing in analyzing audio information to answer questions.",
    "input": [
      {
        "type": "message",
        "role": "user",
        "content": [
          {
              "type": "input_audio",
              "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/advanced-usage/audio_understanding_single_qa.wav"
          },
          {
              "type": "input_text",
              "text": "Which of the following am I wearing: pullover sweater, T-shirt, jacket or camisole?"
          }
        ]
      }
    ]
  }'
```

Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

Response preview

```Plaintext
You are wearing a jacket. In addition to the continuous friction sound of sorting and rubbing fabric materials, there are two typical short and forceful pulling and engaging sounds of a long zipper in this audio. Pullover sweaters, regular T-shirts and camisoles do not have such typical behavioral features that require operating a full-length zipper. These two sounds are exactly the noises produced when you zip up the jacket after adjusting your clothes.
```

### Multi\-turn conversation

Applicable to use cases requiring continuous interaction, such as customer service review, language practice companion, and audio question answering. In this case, users can add new audio input in each round of interaction. The model will perform comprehensive reasoning based on "all historical audio + all historical text responses + new questions in the current round" and output a brand\-new text response, ensuring the coherence of the dialogue context and the accuracy of the response.

> **Tip**

> The Responses API provides native multi\-turn context concatenation capability, so the client does not need to manually splice historical content. Simply pass `previous_response_id = <ID of the last response>` in the request, and the server will automatically use all previous `input` and `assistant` outputs as context to continue reasoning.

Here are some simple code samples:

* First round request: The user uploads the first recording and asks for information about the speakers.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
     -H "Authorization: Bearer $ARK_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
       "model": "seed-2-0-lite-260428",
       "instructions": "You are an audio understanding expert who excels at analyzing audio information to answer questions.",
       "input": [
         {
           "type": "message",
           "role": "user",
           "content": [
             {
                 "type": "input_audio",
                 "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/advanced-usage/audio_demo_video_multispeaker_01.m4a"
             },
             {
                 "type": "input_text",
                 "text": "How many people are speaking in this recording? What are their genders and age groups?"
             }
           ]
         }
       ]
     }'
   ```

Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

First round response:

```Plain Text
{
  "id": "resp_0217772771288531389853d7d0951ef6849890aa76b17cc5b65ac",
  "object": "response",
  "model": "seed-2-0-lite-260428",
  "output": [
    {
      "type": "message",
      "role": "assistant",
      "content": [
        {
          "type": "output_text",
          "text": "There are 2 people speaking in this recording:\n1.  The first speaker is a young adult female, who is the customer asking about wifi access.\n2.  The second speaker is a young adult male, who is the on-site staff responding to the customer's questions."
        }
      ]
    }
  ],
  "usage": {
    "input_tokens": 150,
    "output_tokens": 732,
    "total_tokens": 882
  }
}
```

Note down the `id` of the response body (in this case it is `resp_0217772771288531389853d7d0951ef6849890aa76b17cc5b65ac`), which will be used as the `previous_response_id` for the next round.

* Second round request: Append the second recording and ask about mood changes (use `previous_response_id` to connect the content of the previous round)

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
     -H "Authorization: Bearer $ARK_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
       "model": "seed-2-0-lite-260428",
       "previous_response_id": "resp_0217772771288531389853d7d0951ef6849890aa76b17cc5b65ac",
       "input": [
         {
           "type": "message",
           "role": "user",
           "content": [
             {
                 "type": "input_audio",
                 "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/advanced-usage/audio_demo_video_multispeaker_02.m4a"
             },
             {
                 "type": "input_text",
                 "text": "Are these the same people as before? How are their mood changed compared to the previous recording?"
             }
           ]
         }
       ]
     }'
   ```

Second round response:

```Plain Text
{
  "id": "resp_021777277730499f0fcebbb8d813ae358cfc99ccc356325bebca4",
  "object": "response",
  "model": "seed-2-0-lite-260428",
  "output": [
    {
      "type": "message",
      "role": "assistant",
      "content": [
        {
          "type": "output_text",
          "text": "Yes, these are the exact same two speakers from the earlier recording: the female customer, and the male restaurant service staff. Their moods have clear shifts compared to the previous casual, low-stakes wifi query exchange:\n1.  **The female customer** was calm, polite, relaxed and completely amiable in the first recording, only asking a routine, low concern question about wifi access. In this interaction, she goes through a clear mood arc: she starts shocked and confused when handed the wildly overinflated wrong bill, becomes sarcastically frustrated at the silly billing error, then ends up relieved and lightheartedly amused after the mistake is corrected.\n2.  **The male staff** was laid-back, matter-of-fact and perfectly calm answering a routine question in the first recording. In this interaction, he is first confused when the customer objects to the bill, becomes flustered, apologetic and embarrassed once he realizes he gave her the bill for a completely different large table, then returns to a slightly sheepish, friendly tone after he fixes the error."
        }
      ]
    }
  ],
  "previous_response_id": "resp_0217772771288531389853d7d0951ef6849890aa76b17cc5b65ac",
  "usage": {
    "input_tokens": 285,
    "output_tokens": 538,
    "total_tokens": 823
  }
}
```

## Audio analysis (Caption)

The models can perform comprehensive structured description and analysis of audio, covering aspects such as basic audio attributes, core content, speaker information, ambient sound events, and background music. It is widely used in business scenarios such as intelligent cataloging of media assets and pre\-tagging for content compliance review.

Here are some simple code samples:

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "seed-2-0-lite-260428",
    "instructions": "# Role and Objectives\n[Role Positioning] You are a senior audio description expert with sensitive hearing, rigorous logic, proficient literary literacy and fine auditory perception capabilities, skilled in writing descriptions based on audio content.\n[Task Description] I will provide an audio clip. Your task is to listen to the audio completely and conduct in-depth and comprehensive analysis. You need to accurately identify all sound elements in the audio (human voice, sound effects, music) and analyze their acoustic characteristics and narrative functions. Subsequently, generate a well-structured, detailed and vivid audio analysis report in compliance with content requirements and output specifications.",
    "input": [
      {
        "type": "message",
        "role": "user",
        "content": [
          {
              "type": "input_audio",
              "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/advanced-usage/audio_demo_video_caption.m4a"
          },
          {
              "type": "input_text",
              "text": "Please provide an overall description of this audio and output the result in Markdown format. # Content Requirements\n### Audio Overview\nSummarize the physical attributes of the audio (such as duration, tone and volume, clarity), core content composition and overall listening experience;\n### Content Analysis (If Applicable)\nGeneralize the development of dialogue or monologue, and summarize the title and abstract\n### Speaker Information (If Applicable)\nAnalyze the voice characteristics of speakers in the audio\n### Sound Event Information\nAnalyze the acoustic features of non-verbal parts in the audio\n### Music Information\nAnalyze the acoustic features of music segments in the audio"
          }
        ]
      }
    ]
  }'
```

Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

Response preview

```Plaintext
### Audio Overview\nThis is a 34-second clear, noise-free English news broadcast segment, with stable volume, high speech clarity and no distracting ambient interference. The full clip consists only of a news presenter delivering a story lead, with a formal, polished journalistic tone, creating an authoritative, easy-to-follow broadcast listening experience. The recording cuts off abruptly at the very end of the presenter's line.\n\n### Content Analysis\nThis segment is the opening lead of a feature news story focused on American international students in China:\n1.  It opens by establishing that China is emerging as an increasingly popular international education destination, with a growing number of American students choosing to study Chinese and experience life in China firsthand.\n2.  It frames the value of this experience as extending far beyond language learning, as an opportunity to understand Chinese culture and build cross-border connections.\n3.  It teases upcoming interview content from American students at Beijing Language and Culture University, who will share how studying in China changed their perspectives and enriched their lives, before introducing field reporter Wang Zihang.\n\n### Speaker Information\nThe only speaker is a professional female English news anchor, likely working for an international Chinese broadcast outlet:\n- She has a clear, warm mid-range vocal tone, precise enunciation, and standard neutral international English pronunciation.\n- She delivers lines at a steady, measured pace consistent with formal news broadcasting, with an objective, composed delivery free of strong emotional inflection, maintaining a consistent professional journalistic register across the segment.\n\n### Sound Event Information\nThere are no non-verbal sound effects, ambient noises, or interruptions present in this audio clip. The recording contains exclusively clear human speech.\n\n### Music Information\nNo musical segments, background soundtracks or broadcast musical stings appear in this audio recording.
```

## Automatic Speech Recognition (ASR)

It performs structured descriptive parsing on input audio, and transcribes it in real time into plain text or structured text with timestamps. It is the core capability for all types of speech understanding tasks. Based on business output requirements, this capability covers refined application scenarios including general text transcription, timestamp marking, multi\-speaker differentiated transcription, and speaker\-separated diarization.

> **Tip**

* > **19 languages are supported for speech recognition**: zh (Chinese), en (English), yue (Cantonese), ar (Arabic), nl (Dutch), vi (Vietnamese), fr (French), de (German), id (Indonesian), it (Italian), ja (Japanese), ko (Korean), ms (Malay), pt (Portuguese), ru (Russian), es (Spanish), th (Thai), tr (Turkish), fil (Filipino).

* > **Chinese dialect adaptation**: Supports recognition of Jianghuai Mandarin, Jilu Mandarin, Lanyin Mandarin, Zhongyuan Mandarin, Sichuan dialect, Cantonese, Minnan dialect, Shanghainese, Hakka, and Jin dialect.

### Standard ASR

It only outputs original transcribed text, without any extra formatting or redundant information, and the result is concise and clean. It is applicable to business scenarios with high requirements for text purity, such as meeting minutes and voice notes.

Here are some simple code samples:

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "seed-2-0-lite-260428",
    "instructions": "You are a highly advanced AI specialized in Automatic Speech Recognition (ASR). Your sole function is to transcribe the audio provided by the user.\nYou must adhere to the following rules STRICTLY:\n1. Your output must contain ONLY the transcribed text from the audio.\n2. Do not include any introductory phrases, explanations, apologies, or any other conversational text. For example, never start your response with \"Here is the transcription:\" or \"The transcribed text is:\".\n3. Do not use any formatting, such as markdown, bolding, or italics.\n4. If the audio is unclear, inaudible, or contains no speech, you must output an empty string.",
    "input": [
      {
        "type": "message",
        "role": "user",
        "content": [
          {
              "type": "input_audio",
              "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/advanced-usage/audio_demo_video_caption.m4a"
          },
          {
              "type": "input_text",
              "text": "The content of this audio is as follows:"
          }
        ]
      }
    ]
  }'
```

Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

Response preview

```Plaintext
as china emerges as an increasingly popular destination for international education a growing number of american students are choosing to study chinese and experience life in the country firsthand for many the journey is far more than learning a language it's about understanding a culture and building connections across borders at beijing language and culture university students from the united states shared how studying in china has changed their perspectives and enriched their lives wang zihang reports
```

### Output timestamps

Besides audio transcription, timestamps are also attached to each character (or each sentence). Depending on business scenarios, it can be further divided into two modes: the model independently completes audio transcription and adds timestamps synchronously, or completes precise timestamp alignment based on existing transcribed text.

#### Audio transcription timestamps

While outputting the transcribed text, the model simultaneously returns detailed start and end time information for each character, measured in seconds. It can meet business requirements such as audio retrieval, video subtitle timing, and precise audio clip positioning, effectively improving the efficiency of content retrieval and editing processing.

Here are some simple code samples:

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "seed-2-0-lite-260428",
    "instructions": "You are a multilingual speech recognition expert capable of understanding and capturing temporal relationships during speech recognition. You must output results in strict accordance with the template provided by the user and avoid irrelevant content.",
    "input": [
      {
        "type": "message",
        "role": "user",
        "content": [
          {
              "type": "input_audio",
              "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/advanced-usage/audio_demo_video_caption.m4a"
          },
          {
              "type": "input_text",
              "text": "Please transcribe this audio file. Provide the accurate start time and end time for each recognized character.\nYou shall arrange the results in a one-character-per-line format, with each line separated by '';''. Each line consists of three parts: start time, end time and transcribed character, which are separated by ''-''. Note that the unit of start time and end time is second, accurate to two decimal places.\nPlease refer to the template below:\n{start time}-{end time}-{transcribed character};{start time}-{end time}-{transcribed character};...{start time}-{end time}-{transcribed character};\nYou shall only output results in accordance with the template, and do not output any other irrelevant information and content."
          }
        ]
      }
    ]
  }'
```

Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

Response preview

```Plaintext
0.00-0.28-as;0.28-0.69-china;0.69-1.58-emerges;1.62-1.92-as;1.92-2.07-an;2.07-2.82-increasingly;2.82-3.33-popular;3.33-4.02-destination;4.02-4.35-for;4.35-5.10-international;5.10-5.80-education;6.12-6.27-a;6.27-6.84-growing;6.84-7.23-number;7.23-7.41-of;7.41-7.92-american;7.92-8.40-students;8.40-8.49-are;8.49-9.06-choosing;9.06-9.18-to;9.18-9.63-study;9.63-10.32-chinese;10.35-10.50-and;10.50-11.13-experience;11.13-11.64-life;11.85-12.00-in;12.00-12.06-the;12.06-12.42-country;12.42-13.18-firsthand;13.50-13.68-for;13.68-14.05-many;14.22-14.37-the;14.37-14.88-journey;14.88-15.03-is;15.03-15.39-far;15.39-15.63-more;15.63-15.81-than;15.81-16.35-learning;16.35-16.50-a;16.50-17.17-language;17.37-17.50-it;17.50-17.56-s;17.56-17.97-about;17.97-18.78-understanding;18.78-18.87-a;18.87-19.63-culture;20.04-20.46-and;20.49-21.08-building;21.11-21.87-connections;21.87-22.38-across;22.38-23.25-borders;23.61-23.79-at;23.79-24.18-beijing;24.18-24.55-language;24.55-24.66-and;24.66-25.14-culture;25.14-25.68-university;25.74-26.25-students;26.25-26.48-from;26.48-26.58-the;26.58-26.94-united;26.94-27.33-states;27.33-27.69-shared;27.69-28.08-how;28.08-28.62-studying;28.62-28.80-in;28.80-29.37-china;29.40-29.64-has;29.64-30.27-changed;30.27-30.45-their;30.45-30.99-perspectives;31.17-31.38-and;31.38-31.83-enriched;31.83-32.10-their;32.10-32.73-lives;33.00-33.18-wang;33.18-33.48-zihang;33.48-34.08-reports;
```

#### Subtitle alignment timestamps

Suitable for scenarios where complete audio text is already available, such as manual proofreading of subtitles. The model only completes timeline alignment between text and audio, without modifying the original text. Suitable for subtitle production, Karaoke timestamping and similar scenarios.

Here are some simple code samples:

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "seed-2-0-lite-260428",
    "instructions": "You are capable of aligning audio content with transcribed texts and deeply understanding the temporal relationships contained in speeches. You are required to output recognition results in strict accordance with user requirements and designated templates, and refrain from providing any irrelevant content.",
    "input": [
      {
        "type": "message",
        "role": "user",
        "content": [
          {
              "type": "input_audio",
              "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/advanced-usage/audio_demo_video_caption.m4a"
          },
          {
              "type": "input_text",
              "text": "Transcribe the audio. The corresponding transcription result of the audio is: 'I found my holy-grail face cream it has a cloud-like creamy texture that absorbs instantly perfect for post all nighter rescue deep hydration and moisturization my skin glows naturally even without makeup'. You need to match each character in the audio with its start time and end time based on the transcription result of the audio. You are required not to modify the transcription result, and only output the corresponding time information according to the audio transcription result.\nYou need to arrange the results in a one-word-per-line format, with each line separated by '';''. Each line consists of three parts, namely the start time, end time and transcribed character, which are separated by ''-''. Note that the unit of start time and end time is second, accurate to two decimal places.\nPlease refer to the template below:\n{start time}-{end time}-{transcribed character};...;{start time}-{end time}-{transcribed character};\nNote that you shall only output content in accordance with the template, and do not output any other irrelevant information and content."
          }
        ]
      }
    ]
  }'
```

Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

Response preview

```Plaintext
0.00-0.27-as;0.27-0.69-china;0.69-1.59-emerges;1.62-1.89-as;1.89-2.02-an;2.02-2.85-increasingly;2.85-3.33-popular;3.33-4.02-destination;4.02-4.35-for;4.35-5.10-international;5.10-5.78-education;6.12-6.24-a;6.24-6.72-growing;6.72-7.08-number;7.08-7.23-of;7.23-7.74-american;7.74-8.25-students;8.25-8.32-are;8.32-8.85-choosing;8.85-8.97-to;8.97-9.33-study;9.33-10.08-chinese;10.08-10.29-and;10.29-10.98-experience;10.98-11.41-life;11.67-11.84-in;11.84-11.91-the;11.91-12.24-country;12.24-13.09-firsthand;13.35-13.53-for;13.53-13.90-many;14.07-14.22-the;14.22-14.73-journey;14.73-14.88-is;14.88-15.24-far;15.24-15.51-more;15.51-15.81-than;15.81-16.20-learning;16.20-16.35-a;16.35-16.99-language;17.22-17.33-it's;17.33-17.64-about;17.64-18.30-understanding;18.30-18.45-a;18.45-19.15-culture;19.38-19.83-and;19.86-20.43-building;20.46-21.21-connections;21.21-21.78-across;21.78-22.63-borders;22.95-23.16-at;23.16-23.52-beijing;23.52-23.91-language;23.91-24.03-and;24.03-24.36-culture;24.36-24.88-university;24.93-25.41-students;25.41-25.65-from;25.65-25.77-the;25.77-26.13-united;26.13-26.46-states;26.46-26.91-shared;26.91-27.27-how;27.27-27.87-studying;27.87-27.99-in;27.99-28.41-china;28.41-28.65-has;28.65-29.22-changed;29.22-29.40-their;29.40-30.18-perspectives;30.25-30.41-and;30.41-30.84-enriched;30.84-31.05-their;31.05-31.53-lives;31.89-32.10-wang;32.10-32.40-zihang;32.40-32.97-reports;
```

## Multispeaker ASR

Transcription of multi\-person conversations is supported. The model can distinguish different speakers, and mark each segment with a unique speaker ID (such as `spk0`, `spk1`, etc.). Suitable for scenarios such as interviews, daily conversations, customer service recordings, etc., to facilitate content organization and classification.

Here are some simple code samples:

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "seed-2-0-lite-260428",
    "instructions": "The following is an audio recording with multiple speakers. You need to recognize the spoken content and label the corresponding speaker for each sentence. Use [spk0] for the first person who appears in the conversation, [spk1] for the second person, and so on.",
    "input": [
      {
        "type": "message",
        "role": "user",
        "content": [
          {
              "type": "input_audio",
              "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/advanced-usage/audio_demo_video_multispeaker_01.m4a"
          },
          {
              "type": "input_text",
              "text": "Please output the speaker IDs and the corresponding speech content in order:"
          }
        ]
      }
    ]
  }'
```

Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

Response preview

```Plaintext
[spk0] Excuse me, do you have wifi here?
[spk1] Yes we do.[spk0] Is it free to use?
[spk1] Yes, it's free for customers.
[spk0] Could you please tell me the wifi password?
[spk1] The password is written on the counter.
[spk0] Thank you so much.
[spk1] You're welcome, enjoy your time here.
```

## Speaker diarization and ASR

Based on the multi\-speaker speech recognition capability, it further matches each segment of speech with accurate start and end times. It uniformly outputs the structured format of "speaker ID + time range + transcribed text": `[spkN][start-end] speech content`.

Here are some simple code samples:

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "seed-2-0-lite-260428",
    "instructions": "You are a top-tier audio analysis expert, capable of accurately identifying each speaker and marking precise timestamps for their speech.",
    "input": [
      {
        "type": "message",
        "role": "user",
        "content": [
          {
              "type": "input_audio",
              "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/advanced-usage/audio_demo_video_multispeaker_01.m4a"
          },
          {
              "type": "input_text",
              "text": "I have a recording. Please help me organize it. Mainly, please find the speaking times for different people and tell me who spoke when.\nYou need to arrange the results in the format of speaker and timestamp. The timestamp includes the start time and end time. Note that the start time and end time are in seconds and can be accurate to two decimal places. Speakers can be labeled as spk0, spk1, spk2, and so on in order of appearance.\nYou can see the template below:\n[Speaker][Start time-End time]Speech content[Speaker][Start time-End time]Speech content...\nNote that you can only output the results according to the template. Do not output any other irrelevant information or content."
          }
        ]
      }
    ]
  }'
```

Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

Response preview

```Plaintext
[spk0][0.00-2.53] Excuse me, do you have wifi here?
[spk1][2.83-3.74] Yes we do.
[spk0][4.08-5.49] Is it free to use?
[spk1][5.63-7.62] Yes it's free for customers.
[spk0][7.88-10.45] Could you please tell me the wifi password?
[spk1][10.71-12.72] The password is written on the counter.
[spk0][12.98-14.46] Thank you so much.
[spk1][14.68-16.82] You're welcome enjoy your time here.
```

## Speech translation (AST)

It performs translation of spoken content in audio and outputs text in the target language. It supports two\-way translation among multiple languages, and can meet business requirements such as simultaneous interpretation for international conferences, localization of multilingual video subtitles, and review and proofreading of audio content for overseas markets.

> **Tip**

* > **Supported languages and language pairs**: AST covers a total of **15 languages**, including: zh (Chinese), en (English), yue (Cantonese), ar (Arabic), vi (Vietnamese), fr (French), de (German), id (Indonesian), it (Italian), ja (Japanese), ko (Korean), pt (Portuguese), ru (Russian), es (Spanish), th (Thai). It only supports two\-way translation between Chinese/English and the other 14 languages. Mutual translation between other pairs of languages is not supported for now.

* > **Capability combination**: Combined with the automatic speech recognition (ASR) capability, it can provide high\-precision audio transcription services for **19 languages**, including: zh (Chinese), en (English), yue (Cantonese), ar (Arabic), nl (Dutch), vi (Vietnamese), fr (French), de (German), id (Indonesian), it (Italian), ja (Japanese), ko (Korean), ms (Malay), pt (Portuguese), ru (Russian), es (Spanish), th (Thai), tr (Turkish), fil (Filipino). If the required translation language pair is beyond the support scope of AST, but the source language or target language is in the ASR supported language list, you can use the combined solution of **ASR + text translation**:

   * > **Speech transcription**: Upload the original audio, call the ASR capability to accurately transcribe the source language audio into the source text.

   * > **Text translation**: Use the text output by ASR as input, call the text translation capability to generate the translation in the target language.

Here are some simple code samples:

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "seed-2-0-lite-260428",
    "instructions": "Your task is to accurately translate the spoken content in the audio and return it in text form.",
    "input": [
      {
        "type": "message",
        "role": "user",
        "content": [
          {
              "type": "input_text",
              "text": "Translate this sentence into German. Only the translation result shall be included in the final output, with no redundant content provided."
          },
          {
              "type": "input_audio",
              "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/advanced-usage/audio_demo_video_caption.m4a"
          }
        ]
      }
    ]
  }'
```

Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

Response preview

```Plaintext
Da China zu einem zunehmend beliebten Ziel für internationale Bildung wird, entscheidet sich eine wachsende Zahl amerikanischer Studierender dafür, Chinesisch zu lernen und das Leben im Land aus erster Hand zu erleben. Für viele ist diese Reise weit mehr als nur das Erlernen einer Sprache. Es geht darum, eine Kultur zu verstehen und grenzübergreifende Verbindungen aufzubauen. An der Pekinger Universität für Sprache und Kultur berichteten Studierende aus den Vereinigten Staaten, wie das Studium in China ihre Perspektiven verändert und ihr Leben bereichert hat. Wang Zexhang berichtet.
```

## Streaming output

Streaming output supports dynamic real\-time content presentation. It can not only ease users' waiting anxiety, but also avoid client timeout failures caused by long inference time for complex tasks, ensuring the smooth request process.

Code samples:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
-H "Authorization: Bearer $ARK_API_KEY" \
-H 'Content-Type: application/json' \
-d '{
    "model": "seed-2-0-lite-260428",
    "stream": true,
    "input": [
        {
            "role": "user",
            "content": [
                {
                    "type": "input_audio",
                    "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/resources/demo_audio.mp3"
                },
                {
                    "type": "input_text",
                    "text": "Recognize the content in the audio."
                }
            ]
        }
    ]
}'
```

Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

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
    # Directly use remote audio URL (no need to upload file)
    print("Use remote audio URL for transcription")

    # Streaming Responses API request (matches your curl)
    stream = await client.responses.create(
        model="seed-2-0-lite-260428",
        input=[
            {"role": "user", "content": [
                {
                    "type": "input_audio",
                    "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/resources/demo_audio.mp3"
                },
                {
                    "type": "input_text",
                    "text": "Recognize the content in the audio."
                }
            ]},
        ],
        caching={
            "type": "enabled",
        },
        store=True,
        stream=True
    )

    # Handle streaming events (same format as your video script)
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

        fmt.Println("----- Use remote audio URL -----")

        inputMessage := responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role: responses.NewOptMessageRole(responses.MessageRoleUser),
                Content: responses.NewContentItemArrayMessageContent(
                    []responses.ContentItem{
            responses.ContentItem{OneOf: responses.NewContentItemAudioContentItemSum(responses.ContentItemAudio{Type: responses.ContentItemAudioTypeInputAudio, AudioURL: responses.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/files/resources/demo_audio.mp3")})},
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Recognize the content in the audio."})},
                    },
                ),
            },
        ),
    }

        createResponsesReq := &responses.ResponsesRequest{
                Model: "seed-2-0-lite-260428",
                Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{inputMessage}),
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
                        fmt.Printf("\nResponse ID: %s\n", responseId)
                }
        }

        fmt.Println("\n----- Audio recognition completed -----")
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
package com.ark.example;

import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService service = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        System.out.println("===== Use Remote Audio URL Example =====");

        ResponsesRequest request = ResponsesRequest.builder()
                .model("seed-2-0-lite-260428")
                .stream(true)
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemAudio.builder()

                                                .audioUrl("https://arkdocs-en.tos-ap-southeast-1.volces.com/files/resources/demo_audio.mp3")
                                                .build(), ContentItemText.builder()

                                                .text("Recognize the content in the audio.")
                                                .build()))
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
from openai import OpenAI

api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# Directly use remote audio URL (no need to upload file)
print("Use remote audio URL for transcription")

response = client.responses.create(
    model="seed-2-0-lite-260428",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_audio",
                    "audio_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/resources/demo_audio.mp3"
                },
                {
                    "type": "input_text",
                    "text": "Recognize the content in the audio."
                },
            ]
        }
    ],
    stream=True
)

# Handle streaming events (same format as your video script)
for event in response:
    if event.type == "response.reasoning_summary_text.delta":
        print(event.delta, end="")
    if event.type == "response.output_item.added":
        print("\noutPutItem " + event.type + " start:")
    if event.type == "response.output_text.delta":
        print(event.delta, end="")
    if event.type == "response.output_item.done":
        print("\noutPutTextDone.")
    if event.type == "response.completed":
        print("\nResponse Completed. Usage = " + str(event.response.usage))
```

# **Instructions**

## Supported audio formats

The supported audio format MIME types are as follows:

**Pure audios**:

\*.mp3: `audio/mpeg`

\*.wav: `audio/wav`

\*.aac: `audio/aac`

\*.m4a: `audio/x-m4a`

**Embedded audios**:

\*.mp3: `audio/mpeg`

\*.wav: `audio/wav`

\*.aac: `audio/aac`

\*.m4a: `audio/x-m4a`

\*.ac3: `audio/ac3`

\*.alac: `audio/mp4`

## About audio token usage

Each second of audio consumes approximately 6.25 tokens. The actual token usage is subject to the `audio_tokens` value returned by the API.

## Audio file size limits

The file size and duration limits for different audio input methods are as follows:

* Upload via Files API (recommended): A single file must be smaller than 512 MB.

* Pass Base64 string: A single file must be smaller than 25 MB and shorter than 120 minutes.

* Pass audio URL: A single file must be smaller than 25 MB and shorter than 120 minutes.
