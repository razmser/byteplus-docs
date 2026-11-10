# Video understanding

Vision language models can understand visual information in videos, and complete vision\-related tasks such as describing objects in the video and analyzing action logic. They can be used for automated video content review, intelligent monitoring analysis, etc., which greatly reduces labor costs, and are applicable to fields such as intelligent security, sports event analysis, and media content management. This tutorial helps you complete various tasks with video input through APIs.

# Supported models

See [Capabilities](model-list.md#76df97c8).

# APIs

* [Responses API](create-model-responses-api.md): Supports video as input for analysis. Accepts File ID as an input for video understanding. For instructions, see [Upload via Files API (recommended)](video-understanding.md#35d3ebc5).

* [Chat API](chat-api.md): Supports video as input for analysis. Accepts File ID as an input for video understanding. For instructions, see [Upload via Files API (recommended)](video-understanding.md#35d3ebc5).

# Video input methods

The supported video input methods are as follows:

* Upload local files:

   * [Upload via Files API (recommended)](video-understanding.md#35d3ebc5): Upload local files directly. When files are stored in the default managed storage space of ModelArk, files up to 512 MB can be uploaded. When files are stored in TOS buckets, video files up to 2 GB can be uploaded to meet the needs for large files.

   * [Pass Base64 string](video-understanding.md#22314028): Suitable for documents smaller than 10 MB, and the request body cannot exceed 64 MB.

* [Pass video URL](video-understanding.md#8e3a48ed): Applicable to scenarios where the file already has a publicly accessible URL. The video file size cannot exceed 50 MB.

## Upload local files

### Upload via Files API (recommended)

We recommend using the Files API first to upload local files. In this method, when files are stored in the default storage space managed by ModelArk, file uploads of up to 512 MB are supported. When files are stored in a BytePlus TOS Bucket, video file uploads of up to 2 GB are supported, fully meeting large\-file processing needs. This also avoids repeatedly uploading content during requests, reduces preprocessing latency, and lets files be reused across multiple requests, saving public network download time.

When uploading a video through the Files API, you must pass the ID of a model that supports video understanding in `preprocess_configs.video.model` of the upload request. This configuration specifies the video preprocessing and frame sampling policy; when subsequently calling the Responses API or Chat API, you still need to pass the actual model or endpoint used for inference in `model` of the request.

After the upload succeeds and the file status changes to `active`, both the Responses API and Chat API support passing videos through `file_id`.

> * Files uploaded in this way are stored for 7 days by default, and the storage validity period ranges from 1 to 30 days.

> * If you need to obtain analysis in real time or avoid client timeout failures caused by complex tasks, you can use the streaming output. For examples, see [Streaming output](video-understanding.md#5cfd1f60).

If the TOS parameter is not passed, the file is stored in the default managed storage space of ModelArk by default. Code samples:

* Responses API code samples:

**Curl**

1. Upload the video file to get the File ID.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/files \
   -H "Authorization: Bearer $ARK_API_KEY" \
   -F 'purpose=user_data' \
   -F 'file=@/Users/doc/demo.mp4' \
   -F 'preprocess_configs[video][model]=dola-seed-2-1-turbo-260628' \
   -F 'preprocess_configs[video][fps]=0.3'
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
                       "type": "input_video",
                       "file_id": "file-20251018****"
                   },
                   {
                       "type": "input_text",
                       "text": "Please describe the movement sequence of the characters in the video, and output the start time (start_time), end time (end_time), event (event), and danger status (danger) in JSON format. Please use HH:mm:ss to represent the timestamp."
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
    preprocess_configs={"video": {"model": "dola-seed-2-1-turbo-260628", "fps": 0.3}},
)
client.files.wait_for_processing(uploaded_file.id)

response = client.responses.create(
    model=os.getenv("MODEL_ID", "dola-seed-2-1-turbo-260628"),
    input=[
        {
            "role": "user",
            "content": [
                {"type": "input_video", "file_id": uploaded_file.id},
                {"type": "input_text", "text": "Please describe the movement sequence of the characters in the video, and output the start time (start_time), end time (end_time), event (event), and danger status (danger) in JSON format. Please use HH:mm:ss to represent the timestamp."},
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
                Model: file.NewOptString("dola-seed-2-1-turbo-260628"),
                Fps:   file.NewOptFloat64(0.3),
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

    media := responses.ContentItem{OneOf: responses.NewContentItemVideoContentItemSum(responses.ContentItemVideo{Type: responses.ContentItemVideoTypeInputVideo, VideoURL: "", FileID: responses.NewOptString(uploaded.ID)})}
    text := responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(
        responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Describe the sequence of actions performed by the people in the video. Output the start time (start_time), end time (end_time), event (event), and whether it is dangerous (danger) in JSON format. Use HH:mm:ss for timestamps."},
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
import com.volcengine.ark.runtime.models.file.*;
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
        String filePath = System.getenv().getOrDefault("FILE_PATH", "/Users/doc/demo.mp4");
        String modelId = System.getenv().getOrDefault("MODEL_ID", "dola-seed-2-1-turbo-260628");

        FileObject uploaded = service.uploadFile(
                FileCreateRequest.builder()
                        .purpose(Purpose.USER_DATA)
                        .preprocessConfigs(PreprocessConfigs.builder().video(Video.builder().model("dola-seed-2-1-turbo-260628").fps(0.3).build()).build())
                        .build(),
                new File(filePath)
        );
        uploaded = service.waitForFileProcessing(uploaded.getId());
        if (uploaded.getStatus() != Status.ACTIVE) {
            throw new IllegalStateException("File processing failed: " + uploaded.getStatus());
        }

        ContentItem media = ContentItemVideo.builder().type(ContentItemType.INPUT_VIDEO).fileId(uploaded.getId()).build();
        ContentItem text = ContentItemText.builder()
                .type(ContentItemType.INPUT_TEXT)
                .text("Please describe the movement sequence of the characters in the video, and output the start time (start_time), end time (end_time), event (event), and danger status (danger) in JSON format. Please use HH:mm:ss to represent the timestamp.")
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
    file=open("/Users/doc/demo.mp4", "rb"),
    purpose="user_data",
    extra_body={
        "preprocess_configs": {
            "video": {
                "model": "dola-seed-2-1-turbo-260628",
                "fps": 0.3,
            }
        }
    },
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
                    "type": "input_video",
                    "file_id": file.id,
                },
                {
                    "type": "input_text",
                    "text": "Please describe the movement sequence of the characters in the video, and output the start time (start_time), end time (end_time), event (event), and danger status (danger) in JSON format. Please use HH:mm:ss to represent the timestamp.",
                },
            ]
        }
    ]
)
print(response)
```

* Chat API code samples:

**Curl**

1. Upload the video file to get the File ID.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/files \
   -H "Authorization: Bearer $ARK_API_KEY" \
   -F 'purpose=user_data' \
   -F 'file=@/Users/doc/demo.mp4' \
   -F 'preprocess_configs[video][model]=dola-seed-2-1-turbo-260628' \
   -F 'preprocess_configs[video][fps]=0.3'
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
            "type": "video_url",
            "video_url": {
              "file_id": "file-20251018****"
            }
          },
          {
            "type": "text",
            "text": "Describe the person's sequence of actions in the video and return the result in JSON format, including start_time, end_time, event, and danger. Use HH:mm:ss for timestamps."
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
    preprocess_configs={"video": {"model": "dola-seed-2-1-turbo-260628", "fps": 0.3}},
)
client.files.wait_for_processing(uploaded_file.id)

completion = client.chat.completions.create(
    model=os.getenv("MODEL_ID", "dola-seed-2-1-turbo-260628"),
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "video_url", "video_url": {"file_id": uploaded_file.id}},
                {"type": "text", "text": "Please describe the movement sequence of the characters in the video, and output the start time (start_time), end time (end_time), event (event), and danger status (danger) in JSON format. Please use HH:mm:ss to represent the timestamp."},
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
                Model: file.NewOptString("dola-seed-2-1-turbo-260628"),
                Fps:   file.NewOptFloat64(0.3),
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

    media := chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartVideoChatCompletionContentPartSum(chat.ChatCompletionContentPartVideo{Type: chat.ChatCompletionContentPartVideoTypeVideoURL, VideoURL: chat.ChatCompletionContentPartVideoVideoUrl{FileID: chat.NewOptString(uploaded.ID)}})}
    text := chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(
        chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "Describe the sequence of actions performed by the people in the video. Output the start time (start_time), end time (end_time), event (event), and whether it is dangerous (danger) in JSON format. Use HH:mm:ss for timestamps."},
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
        String filePath = System.getenv().getOrDefault("FILE_PATH", "/Users/doc/demo.mp4");
        String modelId = System.getenv().getOrDefault("MODEL_ID", "dola-seed-2-1-turbo-260628");

        FileObject uploaded = service.uploadFile(
                FileCreateRequest.builder()
                        .purpose(Purpose.USER_DATA)
                        .preprocessConfigs(PreprocessConfigs.builder().video(Video.builder().model("dola-seed-2-1-turbo-260628").fps(0.3).build()).build())
                        .build(),
                new File(filePath)
        );
        uploaded = service.waitForFileProcessing(uploaded.getId());
        if (uploaded.getStatus() != Status.ACTIVE) {
            throw new IllegalStateException("File processing failed: " + uploaded.getStatus());
        }

        ChatCompletionContentPart media = ChatCompletionContentPartVideo.builder().type(ChatCompletionContentPartType.VIDEO_URL).videoUrl(ChatCompletionContentPartVideoVideoUrl.builder().fileId(uploaded.getId()).build()).build();
        ChatCompletionContentPart text = ChatCompletionContentPartText.builder()
                .type(ChatCompletionContentPartType.TEXT)
                .text("Please describe the movement sequence of the characters in the video, and output the start time (start_time), end time (end_time), event (event), and danger status (danger) in JSON format. Please use HH:mm:ss to represent the timestamp.")
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

If the TOS parameter is passed, the file is stored in the BytePlus TOS Bucket specified by the user. Video files up to 2 GB can be uploaded. Code samples:

* Responses API code samples:

**Curl**

```Bash
curl -X POST "https://ark.ap-southeast.bytepluses.com/api/v3/files" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -F "purpose=user_data" \
  -F "url=tos://my-bucket/videos/long-video.mp4" \
  -F "tos[bucket]=my-bucket" \
  -F "tos[prefix]=ark-files/" \
  -F "preprocess_configs[video][model]=dola-seed-2-1-turbo-260628" \
  -F "preprocess_configs[video][max_video_tokens]=200000" \
  -F "preprocess_configs[video][min_frames]=16"
```

**Python SDK**

```Python
import os
from arkruntime import Ark

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY')
)

prompt = "Please describe the movement sequence of the characters in the video, and output the start time (start_time), end time (end_time), event (event), and danger status (danger) in JSON format. Please use HH:mm:ss to represent the timestamp."

file = client.files.create(
    url="tos://my-bucket/videos/long-video.mp4",
    purpose="user_data",
    tos={
        "bucket": "my-bucket",
        "prefix": "ark-files/"
    },
    preprocess_configs={
        "video": {
            "model": "dola-seed-2-1-turbo-260628",
            "max_video_tokens": 200000,
            "min_frames": 16
        }
    }
)
print(file)

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_video",
                    "file_id": file.id
                },
                {
                    "type": "input_text",
                    "text": prompt
                }
            ]
        }
    ]
)
print(response)
```

**Go SDK**

```Go
package main

import (
    "context"
    "fmt"
    "os"

    "github.com/volcengine/volcengine-go-sdk/service/arkruntime"
    "github.com/volcengine/volcengine-go-sdk/service/arkruntime/model/file"
    "github.com/volcengine/volcengine-go-sdk/volcengine"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    fileInfo, err := client.UploadFile(ctx, &file.UploadFileRequest{
        Purpose: file.PurposeUserData,
        URL:     volcengine.String("tos://my-bucket/videos/long-video.mp4"),
        Tos: &file.TosStorage{
            Bucket: volcengine.String("my-bucket"),
            Prefix: volcengine.String("ark-files/"),
        },
        PreprocessConfigs: &file.PreprocessConfigs{
            Video: &file.Video{
                Model:          volcengine.String("dola-seed-2-1-turbo-260628"),
                MaxVideoTokens: volcengine.Int64(200000),
                MinFrames:      volcengine.Int64(16),
            },
        },
    })
    if err != nil {
        fmt.Printf("upload file error: %v", err)
        return
    }
    fmt.Printf("file info: %v\n", fileInfo)
}
```

**Java SDK**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.model.files.*;
import com.volcengine.ark.runtime.service.ArkService;

public class demo {

    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ArkService service = ArkService.builder()
                .apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        System.out.println("===== Upload File Example =====");
        FileMeta fileMeta;
        try {
            fileMeta = service.uploadFile(
                    UploadFileRequest.builder()
                            .url("tos://my-bucket/videos/long-video.mp4")
                            .purpose("user_data")
                            .tos(TosStorage.builder()
                                    .bucket("my-bucket")
                                    .prefix("ark-files/")
                                    .build())
                            .preprocessConfigs(PreprocessConfigs.builder()
                                    .video(Video.builder()
                                            .model("dola-seed-2-1-turbo-260628")
                                            .maxVideoTokens(200000L)
                                            .minFrames(16L)
                                            .build())
                                    .build())
                            .build());
            System.out.println("Uploaded file Meta: " + fileMeta);
        } catch (Exception e) {
            e.printStackTrace();
        }

        service.shutdownExecutor();
    }
}
```

* Chat API code samples:

**Curl**

1. Upload the video file to get the File ID.

   ```Bash
   curl -X POST "https://ark.ap-southeast.bytepluses.com/api/v3/files" \
   -H "Authorization: Bearer $ARK_API_KEY" \
   -F "purpose=user_data" \
   -F "url=tos://<your_bucket>/videos/long-video.mp4" \
   -F "tos[bucket]=<your_bucket>" \
   -F "tos[prefix]=ark-files/" \
   -F "preprocess_configs[video][model]=dola-seed-2-1-turbo-260628" \
   -F "preprocess_configs[video][max_video_tokens]=200000" \
   -F "preprocess_configs[video][min_frames]=16"
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
            "type": "video_url",
            "video_url": {
              "file_id": "file-20251018****"
            }
          },
          {
            "type": "text",
            "text": "Describe the person's sequence of actions in the video and return the result in JSON format, including start_time, end_time, event, and danger. Use HH:mm:ss for timestamps."
          }
        ]
      }
    ]
  }'
```

**Python SDK**

```Python
import os

from arkruntime import Ark

client = Ark(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.getenv("ARK_API_KEY"),
)

prompt = "Describe the person's sequence of actions in the video and return the result in JSON format, including start_time, end_time, event, and danger. Use HH:mm:ss for timestamps."

file = client.files.create(
    url="tos://my-bucket/videos/long-video.mp4",
    purpose="user_data",
    tos={
        "bucket": "my-bucket",
        "prefix": "ark-files/",
    },
    preprocess_configs={
        "video": {
            "model": "dola-seed-2-1-turbo-260628",
            "max_video_tokens": 200000,
            "min_frames": 16,
        }
    },
)
print(file)

completion = client.chat.completions.create(
    model="dola-seed-2-1-turbo-260628",
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "video_url",
                    "video_url": {
                        "file_id": file.id,
                    },
                },
                {
                    "type": "text",
                    "text": prompt,
                },
            ],
        }
    ],
)
print(completion.choices[0].message.content)
```

### Pass Base64 string

Convert the local file to a Base64 encoded string and submit it to the large model. This method is suitable for videos smaller than 50 MB, and the request body cannot exceed 64 MB. (Supported by both Responses API and Chat API.)

> **Note**

> Convert the video file to a Base64 encoded string, concatenate it in the format `data:{mime_type};base64,{base64_data}`, and pass it to the model.

* > `{mime_type}`: The media type of the file, which must be identical with the file's mime_type. For details about supported video formats, see [Supported video formats](video-understanding.md#1d125314).

* > `{base64_data}`: The Base64 encoded string of the file.

* Responses API code samples:

**Curl**

```Bash
BASE64_FILE=$(base64 < demo.mp4) && curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
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
            "type": "input_video",
            "video_url": "data:video/mp4;base64,$BASE64_FILE",
            "fps": 1
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
base64_file = encode_file("/Users/doc/demo.mp4")

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_video",
                    "video_url": f"data:video/mp4;base64,{base64_file}",
                    "fps":1
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
    "encoding/base64"
    "fmt"
    "os"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/responses"
)

func main() {
    // Convert local files to Base64-encoded strings.
    fileBytes, err := os.ReadFile("/Users/doc/demo.mp4")
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
            responses.ContentItem{OneOf: responses.NewContentItemVideoContentItemSum(responses.ContentItemVideo{Type: responses.ContentItemVideoTypeInputVideo, VideoURL: fmt.Sprintf("data:video/mp4;base64,%s", base64File), Fps: responses.NewOptFloat64(1)})},
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
            base64Data = "data:video/mp4;base64," + encodeFile("/Users/demo.mp4");
        } catch (IOException e) {
            System.err.println("Encoding failed: " + e.getMessage());
        }
        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemVideo.builder().videoUrl(base64Data).fps(1D).build()))
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
base64_file = encode_file("/Users/doc/demo.mp4")

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_video",
                    "video_url": f"data:video/mp4;base64,{base64_file}",
                    "fps":1
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
BASE64_VIDEO=$(base64 < demo.mp4) && curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
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
            "type": "video_url",
            "video_url": {
              "url": "data:video/mp4;base64,$BASE64_VIDEO"
            }
          },
          {
            "type": "text",
            "text": "What is in the video?"
          }
        ]
      }
    ],
    "max_tokens": 300
  }
EOF
```

* Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

**Python**

```Python
import base64
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # The base URL for model invocation .
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

# Define a method to convert the video at the specified path to Base64 string
def encode_video(video_path):
  with open(video_path, "rb") as video_file:
    return base64.b64encode(video_file.read()).decode('utf-8')

# Video to be passed to the large model
video_path = "demo.mp4"

# Convert the video to Base64 string
base64_video = encode_video(video_path)

completion = client.chat.completions.create(
  # Replace with Model ID .
  model = "dola-seed-2-1-turbo-260628",
  messages=[
    {
      "role": "user",
      "content": [
        {
          "type": "video_url",
          "video_url": {
            "url":  f"data:video/<VIDEO_FORMAT>;base64,{base64_video}"
          },
        },
        {
          "type": "text",
          "text": "What's in the video?",
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
// Read local video file
    videoBytes, err := os.ReadFile("demo.mp4") // Replace with the actual video path
    if err != nil {
        fmt.Printf("Failed to read video: %v\n", err)
        return
    }
    base64Video := base64.StdEncoding.EncodeToString(videoBytes)

    client := arkruntime.NewClientWithApiKey(
// Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        os.Getenv("ARK_API_KEY"),
// The base URL for model invocation .
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
        )
    ctx := context.Background()
    req := &chat.ChatCompletionRequest{
// Replace with Model ID
        Model: "dola-seed-2-1-turbo-260628",
        Messages: []chat.ChatCompletionRequestMessage{
chat.ChatCompletionRequestMessage{OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(chat.ChatCompletionRequestUserMessage{Role: chat.ChatCompletionRequestUserMessageRoleUser, Content: chat.NewOptNilChatCompletionMessageContent(chat.NewChatCompletionContentPartArrayChatCompletionMessageContent(
                []chat.ChatCompletionContentPart{
                    chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartVideoChatCompletionContentPartSum(chat.ChatCompletionContentPartVideo{Type: chat.ChatCompletionContentPartVideoTypeVideoURL, VideoURL: chat.ChatCompletionContentPartVideoVideoUrl{URL: chat.NewOptString(fmt.Sprintf("data:video/mp4;base64,%s", base64Video))}})},
                    chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "What's in the video?"})},
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
        //The base URL for model invocation
        .apiKey(apiKey) //Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        .build();

    // Base64 encoding method
    private static String encodeVideo(String videoPath) throws IOException {
        byte[] videoBytes = Files.readAllBytes(java.nio.file.Paths.get(videoPath));
        return Base64.getEncoder().encodeToString(videoBytes);
    }

    public static void main(String[] args) throws Exception {

        List<ChatCompletionRequestMessage> messagesForReqList = new ArrayList<>();

        // Local video path (replace with the actual path)
        String videoPath = "demo.mp4";

        // Generate Base64 data URL
        String base64Data = "data:video/mp4;base64," + encodeVideo(videoPath);

        // Build message content (fix the way the content part is constructed)
        List<ChatCompletionContentPart> contentParts = new ArrayList<>();

        // Use builder pattern for the image part
        contentParts.add(ChatCompletionContentPartVideo.builder()
                .type(ChatCompletionContentPartType.VIDEO_URL)
                .videoUrl(ChatCompletionContentPartVideoVideoUrl.builder().url(base64Data).fps(2.0).build())
                .build());

        // Use builder pattern for the text part
        contentParts.add(ChatCompletionContentPartText.builder()
                .type(ChatCompletionContentPartType.TEXT)
                .text("What's in the video?")
                .build());

        // Create message
        messagesForReqList.add(ChatCompletionRequestUserMessage.builder()
                .role(ChatCompletionRequestMessageType.USER)
                .content(ChatCompletionMessageContent.ofList(contentParts))
                .build());

        ChatCompletionRequest req = ChatCompletionRequest.builder()
                .model("dola-seed-2-1-turbo-260628") //Replace with Model ID .
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

## Pass video URL

If the video already has a publicly accessible URL, you can directly fill in the public URL of the video in the request. A single video cannot exceed 50 MB. (Supported by both Responses API and Chat API.)

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
                    "type": "input_video",
                    "video_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/ark_vlm_video_input.mp4",
                    "fps":1
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

# Get your API key from the environment variable. For configuration instructions, see https://ai.byteplus.com/ark/region:ap-southeast-1/docs/quick-start.
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
                    "type": "input_video",
                    "video_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/ark_vlm_video_input.mp4",
                    "fps":1
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
            responses.ContentItem{OneOf: responses.NewContentItemVideoContentItemSum(responses.ContentItemVideo{Type: responses.ContentItemVideoTypeInputVideo, VideoURL: "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/ark_vlm_video_input.mp4", Fps: responses.NewOptFloat64(1)})},
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
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemVideo.builder().videoUrl("https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/ark_vlm_video_input.mp4").fps(1D).build()))
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

# Get your API key from the environment variable. For configuration instructions, see https://ai.byteplus.com/ark/region:ap-southeast-1/docs/quick-start.
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
                    "type": "input_video",
                    "video_url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/ark_vlm_video_input.mp4",
                    "fps":1
                }
            ],
        }
    ]
)

print(response)
```

* The Chat API code samples are as follows: (see [Precision control of video understanding](https://ai.byteplus.com/ark/region:ap\-southeast\-1/docs/video\-understanding#bf4d9224){target="_self"})

**Python SDK**

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
# Replace with Model ID .
    model = "dola-seed-2-1-turbo-260628",
    messages = [
        {
            "role": "user",
            "content": [
                {
                    "type": "video_url",
                    "video_url": {
# Replace the link with your actual video link
                        "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/video_understanding.mp4",
                        "fps": 1
                    }
                },
            ],
        }
    ],
)

print(completion.choices[0].message.content)
```

# Use cases

## Precision control of video understanding

You can use the **fps** parameter to control the frequency of sampling images from the video. The default value is 1, which means one frame is sampled from the video per second and input to the model for visual understanding. You can also use the **fps** parameter to control the model's sensitivity to image changes in the video.

* When the video frames change drastically or you need to focus on picture changes, such as counting the number of character movements in the video, you can turn up the **fps** setting (maximum `5`) to avoid misjudgment caused by low frame sampling frequency.

* When the video frames do not change frequently or you do not need to focus on picture changes, such as counting the number of people in the picture, you can turn down the **fps** (minimum `0.2`), which can improve processing speed and save token usage.

Code samples:

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
                {"type": "video_url","video_url": {"url":  "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/video_understanding.mp4", "fps": 2}},
                {"type": "text", "text": "What is in the video?"}
            ]
        }
    ],
    "max_tokens": 300
  }'
```

* Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

**Python**

```Python
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
# The base URL for model invocation .
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

completion = client.chat.completions.create(
# Replace with Model ID .
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {
# The role of the message is user
            "role": "user",
            "content": [
                {
                    "type": "video_url",
                    "video_url": {
# Replace the link with your actual video link
                        "url":  "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/video_understanding.mp4",
                        "fps": 2, # Capture 2 frames per second for video understanding
                    }
                },
# Message of text type, asking what is in the video
                {"type": "text", "text": "What's in the video?"},
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
        //Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        os.Getenv("ARK_API_KEY"),
        // The base URL for model invocation .
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    // Create a context, which is usually used to pass request context information such as timeout, cancellation, etc.
    ctx := context.Background()
    // Construct message content
    contentParts := []chat.ChatCompletionContentPart{
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartVideoChatCompletionContentPartSum(chat.ChatCompletionContentPartVideo{Type: chat.ChatCompletionContentPartVideoTypeVideoURL, VideoURL: chat.ChatCompletionContentPartVideoVideoUrl{URL: chat.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/video_understanding.mp4"), Fps: chat.NewOptFloat64(2)}})},
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "What's in the video?"})},
    }
    // Construct a chat completion request, and set the model and message content for the request
    req := &chat.ChatCompletionRequest{
        // Replace with Model ID
       Model: "dola-seed-2-1-turbo-260628",
       Messages: []chat.ChatCompletionRequestMessage{
chat.ChatCompletionRequestMessage{OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(chat.ChatCompletionRequestUserMessage{Role: chat.ChatCompletionRequestUserMessageRoleUser, Content: chat.NewOptNilChatCompletionMessageContent(chat.NewChatCompletionContentPartArrayChatCompletionMessageContent(contentParts))})},
       },
       MaxTokens: chat.NewOptInt32(int32(300)), // Set the maximum number of tokens the model outputs
    }

    // Send the chat completion request, store the result in resp, and store possible errors in err
    resp, err := client.CreateChatCompletion(ctx, req)
    if err!= nil {
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

public class VideoSample {
  static String apiKey = System.getenv("ARK_API_KEY");
  static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
  static Dispatcher dispatcher = new Dispatcher();
  static ArkService service = ArkService.builder()
      .dispatcher(dispatcher)
      .connectionPool(connectionPool)
      .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
      // The base URL for model invocation .
      .apiKey(apiKey) //Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
      .build();

  public static void main(String[] args) throws Exception {

    List<ChatCompletionRequestMessage> messagesForReqList = new ArrayList<>();

// Construct message content
    List<ChatCompletionContentPart> contentParts = new ArrayList<>();

    contentParts.add(ChatCompletionContentPartVideo.builder()
        .type(ChatCompletionContentPartType.VIDEO_URL)
        .videoUrl(ChatCompletionContentPartVideoVideoUrl.builder().url("https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/video_understanding.mp4").fps(2.0).build())

        .build());

    contentParts.add(ChatCompletionContentPartText.builder()
        .type(ChatCompletionContentPartType.TEXT)
        .text("What's in the video?")
        .build());

// Create message
    messagesForReqList.add(ChatCompletionRequestUserMessage.builder()
        .role(ChatCompletionRequestMessageType.USER)
        .content(ChatCompletionMessageContent.ofList(contentParts))
        .build());

    ChatCompletionRequest req = ChatCompletionRequest.builder()
        .model("dola-seed-2-1-turbo-260628") //Replace with Model ID .
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

## Video temporal perception

Video understanding can interpret the relationship between video time and images, such as answering time\-related questions like when an event occurs, at which timestamps a specific event happens, etc. For the working principle, see [How video understanding works](video-understanding.md#b5f696d3).

Here are some simple code samples:

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
                {"type": "video_url","video_url": {"url":  "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/video_understanding.mp4", "fps": 5}},
                {"type": "text", "text": "At what timestamp does the referee appear?"}
            ]
        }
    ],
    "max_tokens": 300
  }'
```

* Replace the model ID as needed. To query the model ID, see [Model list](model-list.md).

**Python**

```Python
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
# The base URL for model invocation .
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

completion = client.chat.completions.create(
# Replace with Model ID .
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {
# The role of the message is user
            "role": "user",
            "content": [
                {
                    "type": "video_url",
                    "video_url": {
# Replace the link with your actual video link
                        "url":  "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/video_understanding.mp4",
                        "fps": 5, # Capture 5 frames per second for video understanding
                    }
                },
# Message of text type, asking what is in the video
                {"type": "text", "text": "At what timestamp does the referee appear?"},
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
// Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        os.Getenv("ARK_API_KEY"),
// The base URL for model invocation .
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
// Create a context, which is usually used to pass request context information such as timeout, cancellation, etc.
    ctx := context.Background()
// Construct message content
    contentParts := []chat.ChatCompletionContentPart{
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartVideoChatCompletionContentPartSum(chat.ChatCompletionContentPartVideo{Type: chat.ChatCompletionContentPartVideoTypeVideoURL, VideoURL: chat.ChatCompletionContentPartVideoVideoUrl{URL: chat.NewOptString("https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/video_understanding.mp4"), Fps: chat.NewOptFloat64(5)}})},
        chat.ChatCompletionContentPart{OneOf: chat.NewChatCompletionContentPartTextChatCompletionContentPartSum(chat.ChatCompletionContentPartText{Type: chat.ChatCompletionContentPartTextTypeText, Text: "At what timestamp does the referee appear?"})},
    }
// Construct a chat completion request, and set the model and message content for the request
    req := &chat.ChatCompletionRequest{
// Replace with Model ID
       Model: "dola-seed-2-1-turbo-260628",
       Messages: []chat.ChatCompletionRequestMessage{
chat.ChatCompletionRequestMessage{OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(chat.ChatCompletionRequestUserMessage{Role: chat.ChatCompletionRequestUserMessageRoleUser, Content: chat.NewOptNilChatCompletionMessageContent(chat.NewChatCompletionContentPartArrayChatCompletionMessageContent(contentParts))})},
       },
       MaxTokens: chat.NewOptInt32(int32(300)), // Set the maximum number of tokens the model outputs
    }

// Send the chat completion request, store the result in resp, and store possible errors in err
    resp, err := client.CreateChatCompletion(ctx, req)
    if err!= nil {
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

public class VideoSample {
  static String apiKey = System.getenv("ARK_API_KEY");
  static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
  static Dispatcher dispatcher = new Dispatcher();
  static ArkService service = ArkService.builder()
      .dispatcher(dispatcher)
      .connectionPool(connectionPool)
      .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
      // The base URL for model invocation .
      .apiKey(apiKey) //Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
      .build();

  public static void main(String[] args) throws Exception {

    List<ChatCompletionRequestMessage> messagesForReqList = new ArrayList<>();

// Construct message content
    List<ChatCompletionContentPart> contentParts = new ArrayList<>();

    contentParts.add(ChatCompletionContentPartVideo.builder()
        .type(ChatCompletionContentPartType.VIDEO_URL)
        .videoUrl(ChatCompletionContentPartVideoVideoUrl.builder().url("https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/video_understanding.mp4").fps(5.0).build())
        .build());

    contentParts.add(ChatCompletionContentPartText.builder()
        .type(ChatCompletionContentPartType.TEXT)
        .text("At what timestamp does the referee appear?")
        .build());

// Create message
    messagesForReqList.add(ChatCompletionRequestUserMessage.builder()
        .role(ChatCompletionRequestMessageType.USER)
        .content(ChatCompletionMessageContent.ofList(contentParts))
        .build());

    ChatCompletionRequest req = ChatCompletionRequest.builder()
        .model("dola-seed-2-1-turbo-260628") //Replace with Model ID .
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

Response preview

```Plaintext
According to the video description, the referee appears around **3.7 seconds**. At that moment, the two boxers in the frame (the one on the left wears a black T-shirt and red shorts, the one on the right wears a white T-shirt and black shorts) were in a confrontation. Then the referee (wearing a black suit and white gloves) stood between the two, as if preparing to start the match or pause the current round, while the audience continued cheering in the background.
```

## Streaming output

Streaming output supports dynamic real\-time content presentation. It can not only ease users' waiting anxiety, but also avoid client timeout failures caused by long inference time for complex tasks, ensuring the smooth request process.

**Python SDK**

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
    # upload video file
    print("Upload video file")
    file = await client.files.create(
        # replace with your local video path
        file=open("/Users/doc/demo.mp4", "rb"),
        purpose="user_data",
        preprocess_configs={
            "video": {
                "model": "dola-seed-2-1-turbo-260628",
                "fps": 0.3,  # define the sampling fps of the video, default is 1.0
            }
        }
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
                    "type": "input_video",
                    "file_id": file.id  # ref video file id
                },
                {
                    "type": "input_text",
                    "text": "Please describe the movement sequence of the characters in the video, and output the start time (start_time), end time (end_time), event (event), and danger status (danger) in JSON format. Please use HH:mm:ss to represent the timestamp."

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

**Go SDK**

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
        // Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    fmt.Println("----- upload video data -----")
    data, err := os.Open("/Users/doc/demo.mp4")
    if err != nil {
        fmt.Printf("read file error: %v\n", err)
        return
    }
    fileInfo, err := client.UploadFile(ctx, &file.FileCreateRequest{
        Purpose: file.PurposeUserData,
        PreprocessConfigs: file.NewOptPreprocessConfigs(file.PreprocessConfigs{
            Video: file.NewOptVideo(file.Video{
                Model: file.NewOptString("dola-seed-2-1-turbo-260628"),
                Fps:   file.NewOptFloat64(0.3),
            }),
        }),
    }, data)

    if err != nil {
        fmt.Printf("upload file error: %v", err)
        return
    }

    // Wait for the file to finish processing
    for fileInfo.Status == file.StatusProcessing {
        fmt.Println("Waiting for video to be processed...")
        time.Sleep(2 * time.Second)
        fileInfo, err = client.RetrieveFile(ctx, fileInfo.ID) // update file info
        if err != nil {
            fmt.Printf("get file status error: %v", err)
            return
        }
    }
    fmt.Printf("Video processing completed: %s, status: %s\n", fileInfo.ID, fileInfo.Status)
    inputMessage := responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role: responses.NewOptMessageRole(responses.MessageRoleUser),
                Content: responses.NewContentItemArrayMessageContent(
                    []responses.ContentItem{
                        responses.ContentItem{OneOf: responses.NewContentItemVideoContentItemSum(responses.ContentItemVideo{Type: responses.ContentItemVideoTypeInputVideo, VideoURL: "", FileID: responses.NewOptString(fileInfo.ID)})},
                        responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Describe the sequence of actions performed by the people in the video. Output the start time (start_time), end time (end_time), event (event), and whether it is dangerous (danger) in JSON format. Use HH:mm:ss for timestamps."})},
                    },
                ),
            },
        ),
    }
    createResponsesReq := &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
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

**Java SDK**

```Java
package com.ark.example;

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
        // upload a video for responses
        FileObject fileMeta;
        fileMeta = service.uploadFile(FileCreateRequest.builder() // replace with your video file path
                        .purpose(Purpose.USER_DATA)
                        .preprocessConfigs(PreprocessConfigs.builder().video(Video.builder().model("dola-seed-2-1-turbo-260628").fps(0.3).build()).build())
                        .build(), new File("/Users/doc/demo.mp4"));
        System.out.println("Uploaded file Meta: " + fileMeta);
        System.out.println("status:" + fileMeta.getStatus());

        try {
            while ("processing".equalsIgnoreCase(fileMeta.getStatus().toString())) {
                System.out.println("Waiting for video to be processed...");
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
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemVideo.builder().fileId(fileMeta.getId()).build(), ContentItemText.builder().text("Please describe the movement sequence of the characters in the video, and output the start time (start_time), end time (end_time), event (event), and danger status (danger) in JSON format. Please use HH:mm:ss to represent the timestamp.").build()))
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
    file=open("/Users/doc/demo.mp4", "rb"),
    purpose="user_data",
    extra_body={
        "preprocess_configs": {
            "video": {
                "model": "dola-seed-2-1-turbo-260628",
                "fps": 0.3,
            }
        }
    },
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
                    "type": "input_video",
                    "file_id": file.id,
                },
                {
                    "type": "input_text",
                    "text": "Please describe the movement sequence of the characters in the video, and output the start time (start_time), end time (end_time), event (event), and danger status (danger) in JSON format. Please use HH:mm:ss to represent the timestamp.",
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

# Instructions

> **Tip**

> Processed images and videos will be deleted from the ModelArk server. ModelArk will not retain user data such as images, videos, and text information you submit for model training.

## Temporal information

Video keyframes are obtained through FPS\-based frame sampling, then temporal information is marked by concatenating `timestamp + image`. The model achieves complete understanding of the video based on the timing marks and image content in the request, including content changes, movement logic, temporal correlation, etc.

For how it works, see [How video understanding works](video-understanding.md#b5f696d3).

## Supported video formats

|**Video format** |**File extension** |**Content Type** |
|---|---|---|
|MP4 |.mp4 |`video/mp4` |
|AVI |.avi |`video/x-msvideo` |
|MOV |.mov |`video/quicktime` |

> There are many variants of video file formats, so we cannot guarantee that all files can be recognized. Please verify that the files can be recognized as expected through testing.

> **Tip**

* > For common issues and solutions, see [Are TS video files supported?](modelark-faq.md#85251eec).

* > For configurations when uploading videos to TOS, see [Documentation](https://docs.byteplus.com/en/docs/tos/docs-managing-file-metadata).

* > To pass Base64 string, refer to [Pass Base64 string](video-understanding.md#22314028).

* > Video formats must be in lowercase.

## About video size

* When passing videos via URL, a single video cannot exceed 50 MB.

* When passing videos via Base64 strings, a single video cannot exceed 50 MB, and the request body cannot exceed 64 MB.

* When files are uploaded via Files API and stored in the default managed storage space of ModelArk, files up to 512 MB can be uploaded. When files are stored in the TOS buckets, video files up to 2 GB can be uploaded to meet the needs for large files.

## **Audio understanding support**

Some models support understanding audio information in video files. For details, see [Audio input embedded in videos](audio-understanding.md#0a9900d2).

## **Frame sampling strategy**

About token usage: The maximum token usage for a single video is 80k. The maximum token amount for a single request video is also limited by the model's maximum context window and maximum input length (when deep thinking is enabled). If the limit is exceeded, you need to adjust the number or duration of input videos.

Basic concepts:

* Frame image: A video frame at a certain moment, which specifically refers to the frame image input to the model in this article

* Number of frame images: Video duration in seconds \* **fps**

ModelArk compresses frame images based on the number of frame images to balance the video understanding accuracy and token usage.

Different models have different frame sampling strategies, as detailed below:

|Frame sampling strategy |Seed 2.0 and later models |
|---|---|
|Tokens per frame |* `max_frame_tokens`: The maximum number of tokens per frame, used to control the upper limit of single\-frame quality.<br><br>* `min_frame_tokens`: The minimum number of tokens per frame used as the lower limit of single\-frame compression. It supports continuous dynamic adjustment of tokens within the `[64, 384]` range. The default value is 384. |
|Max pixels per frame |Maximum tokens per frame \* 42 \* 42<br><br>[110000, 670000] |
|Number of sampled frames |* Frame sampling count range: [16 frames, 1280 frames].<br><br><br>```Bash```<br>```# Maximum number of sampled frames```<br>```80×1024 tokens ÷ 64 tokens per frame = 1280 frames```<br><br><br><br>* `min_frames`: The minimum frame sampling count. The default value is 16. If the total number of sampled frames calculated based on `fps` is less than 16, processing is performed with 16 frames first. |
|Maximum tokens of the video |`max_video_tokens`: The upper limit on the total number of tokens for all sampled frames in a single video. The default value is 81920 (80 × 1024). |
|Frame sampling scheme |Note<br><br><br>* It is recommended to evaluate the output quality and adjust the fps parameter configuration or video duration as needed.<br><br>* If the video binary data does not contain frame count encoding information, frames will be evenly sampled according to fps. If the maximum token amount of video in a single request exceeds the model's maximum input length, an error will be reported. If it does not exceed the limit, no error will be reported, but the tokens of the video input may exceed 80K. |
| |* If the fps is too high or the video duration is too long, and the number of processed frame images exceeds 1280 frames, 1280 frames will be evenly sampled at a time interval of `video duration / 1280` with 64 tokens per frame image.<br><br>* If the fps is too low or the video duration is too short, and the number of processed frame images is less than 16, the sampling scheme is as follows:<br><br>   * If the total number of video frames \>= 16, evenly sample 16 frames.<br><br>   * If the total number of video frames < 16, sample all frames of the video. |

# How video understanding works

The core method of video processing is "structured concatenation of frames and timestamps", with the following specific rules:

* For each frame image sampled from the video, insert timestamp text before it.

   * Seed 2.0 and later models: The timestamp format is `<timestamp> second`. For example: `4.0 second` and `4.5 second`.

   * Models before Seed 2.0: The timestamp format is `<timestamp> second`. For example: `[4.0 second]` and `[4.5 second]`.

* After concatenation, an ordered sequence of "timestamp + image" is formed, and the model understands the temporal logic and content changes of the video through this sequence.

## Examples of frame sampling

| |FPS 1 |FPS 0.5 |FPS 2 |
|---|---|---|---|
|Timestamp |0.0 second |0.0 second |0.0 second |
|Video frame |`<IMAGE>` |`<IMAGE>` |`<IMAGE>` |
|Timestamp |1.0 second |2.0 second |0.5 second |
|Video frame |`<IMAGE>` |`<IMAGE>` |`<IMAGE>` |
|Timestamp |2.0 second |4.0 second |1.0 second |
|Video frame |`<IMAGE>` |`<IMAGE>` |`<IMAGE>` |
|Timestamp |3.0 second | |1.5 second |
|Video frame |`<IMAGE>` | |`<IMAGE>` |
|Timestamp |4.0 second | |2.0 second |
|Video frame |`<IMAGE>` | |`<IMAGE>` |
|Timestamp |5.0 second | |2.5 second |
|Video frame |`<IMAGE>` | |`<IMAGE>` |
|Timestamp | | |3.0 second |
|Video frame | | |`<IMAGE>` |
|Timestamp | | |3.5 second |
|Video frame | | |`<IMAGE>` |
|Timestamp | | |4.0 second |
|Video frame | | |`<IMAGE>` |
|Timestamp | | |4.5 second |
|Video frame | | |`<IMAGE>` |
|Timestamp | | |5.0 second |
|Video frame | | |`<IMAGE>` |
| |*Total 6 frames* |*Total 3 frames* |*Total 11 frames* |

## Equivalent multi\-image request

A video understanding request is equivalent to the multi\-image understanding request shown in the example below.

```JSON
{
    "model": "dola-seed-2-1-turbo-260628",
    "messages": [
        {
            "role": "user",
            "content": [
                {
                    "type":"text",
                    "text":"Do you think this is scary?"
                },
                {
                    "type":"text",
                    "text":"0.0 second"
                },
                {
                    "type":"image_url",
                    "image_url":{
                        "url":"<image_url_01>"}
                },
                {
                    "type":"text",
                    "text":"1.0 second"
                },
                {
                    "type":"image_url",
                    "image_url":{
                        "url":"<image_url_02>"}
                },
                {
                    "type":"text",
                    "text":"2.0 second"
                },
                {
                    "type":"image_url",
                    "image_url":{
                        "url":"<image_url_03>"}
                },
                {
                    "type":"text",
                    "text":"3.0 second"
                },
                {
                    "type":"image_url",
                    "image_url":{
                        "url":"<image_url_04>"}
                },
                {
                    "type":"text",
                    "text":"4.0 second"
                },
                {
                    "type":"image_url",
                    "image_url":{
                        "url":"<image_url_05>"}
                },
                {
                    "type":"text",
                    "text":"5.0 second"
                },
                {
                    "type":"image_url",
                    "image_url":{
                        "url":"<image_url_06>"}
                }
            ]
        }
    ]
}
```
