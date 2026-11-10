# Multimodal understanding (Responses API)

Vision models support input of images, videos, and documents to perform vision\-related tasks such as image captioning, visual question answering (VQA), and content moderation.

## Supported models

For visual models of version 250615 and later, the Responses API is supported by default unless otherwise specified. For details, see [Capabilities](model-list.md#76df97c8).

## API reference

[Responses API](create-model-responses-api.md)

## Related tutorials

The Responses API supports multiple file input methods: uploading files via the Files API, input as a Base64 string, and passing in a file URL.

This topic takes the **uploading via Files API** method as an example. For details about other file input methods, as well as image/video/document format requirements, file size limits, video frame extraction policies, etc., see the following tutorials:

* [Image understanding](image-understanding.md)

* [Video understanding](video-understanding.md)

* [Document understanding](document-understanding.md)

## Image understanding

The model can understand information in images and complete vision\-related tasks such as describing objects in the images. For image input methods supported by image understanding and examples, see [Image understanding](image-understanding.md).

It is recommended to prioritize using the Files API to upload local files, especially when image files are large, or images need to be reused across requests.

> This method supports files up to 512 MB. The storage period ranges from 1 to 30 days, with a default value of 7 days.

The image file and prompt used in the example are as follows:

|Image file |Prompt |
|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/planet_comparison.png) |Based on the image, how many planets are listed in the table, and which row is Earth in? |

Code samples:

**Curl**

1. Upload the image file to get the File ID.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/files \
     -H "Authorization: Bearer $ARK_API_KEY" \
     -F "purpose=user_data" \
     -F "file=@/path/to/your/image.png"
   ```

2. Reference the File ID in the Responses API.

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
                       "file_id": "file-20251018****"
                   },
                   {
                       "type": "input_text",
                       "text": "Based on the image, how many planets are listed in the table, and which row is Earth in?"
                   }
               ]
           }
       ]
     }'
   ```

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
#     upload an image file
    print("Upload picture file")
    file = await client.files.create(
# replace with your local picture path
        file=open("/Users/doc/planet_comparison.png", "rb"),
        purpose="user_data"
    )
    print(f"File uploaded: {file.id}")

#     Wait for the file to finish processing
    await client.files.wait_for_processing(file.id)
    print(f"File processed: {file.id}")

    stream = await client.responses.create(
        model="dola-seed-2-1-turbo-260628",
        input=[
            {"role": "user", "content": [
                {
                    "type": "input_image",
                    "file_id": file.id  # ref pdf file id
                },
                {
                    "type": "input_text",
                    "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?"
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

    fmt.Println("----- upload image data -----")
    data, err := os.Open("/Users/doc/planet_comparison.png")
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
        fmt.Println("Waiting for image to be processed...")
        time.Sleep(2 * time.Second)
        fileInfo, err = client.RetrieveFile(ctx, fileInfo.ID) // update file info
        if err != nil {
            fmt.Printf("get file status error: %v", err)
            return
        }
    }
    fmt.Printf("Image processing completed: %s, status: %s\n", fileInfo.ID, fileInfo.Status)
    inputMessage := responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role: responses.NewOptMessageRole(responses.MessageRoleUser),
                Content: responses.NewContentItemArrayMessageContent(
                    []responses.ContentItem{
            responses.ContentItem{OneOf: responses.NewContentItemImageContentItemSum(responses.ContentItemImage{Type: responses.ContentItemImageTypeInputImage, FileID: responses.NewOptString(fileInfo.ID)})},
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Based on the image, how many planets are listed in the table, and which row contains Earth?"})},
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
// upload an image for responses
        FileObject fileMeta;
        fileMeta = service.uploadFile(FileCreateRequest.builder() // replace with your image file path
                        .purpose(Purpose.USER_DATA)
                        .build(), new File("/Users/doc/planet_comparison.png"));
        System.out.println("Uploaded file Meta: " + fileMeta);
        System.out.println("status:" + fileMeta.getStatus());

        try {
            while ("processing".equalsIgnoreCase(fileMeta.getStatus().toString())) {
                System.out.println("Waiting for image to be processed...");
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
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemImage.builder().fileId(fileMeta.getId()).build(), ContentItemText.builder().text("Based on the image, how many planets are listed in the table, and which row contains Earth?").build()))
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
    file=open("/Users/doc/planet_comparison.png", "rb"),
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
                    "type": "input_image",
                    "file_id": file.id,
                },
                {
                    "type": "input_text",
                    "text": "Based on the image, how many planets are listed in the table, and which row contains Earth?",
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

Output example:

```Plain Text
The table lists 8 planets. Earth is in the 3rd data row; if the header is counted as row 1, Earth is in row 4.
```

## Video understanding

The model can understand the visual information in the video, and complete vision\-related tasks such as describing the objects in it and analyzing action logic. For file input methods supported by video understanding and examples, see [Video understanding](video-understanding.md).

It is recommended to prioritize using the Files API to upload local video files, especially when video files are large, or videos need to be reused across requests.

> This method supports files up to 512 MB. The storage period ranges from 1 to 30 days, with a default value of 7 days.

The video file and prompt used in the example are as follows:

|Video file |Prompt |
|---|---|
|[video](https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/608b664aad1b49ce9fd41b597551236b)<br> |Please describe the movement sequence of the characters in the video, and output the start time (start_time), end time (end_time), event (event), and danger status (danger) in JSON format. Please use HH:mm:ss to represent the timestamp. |

Code sample:

**Curl**

1. Upload the video file to get the File ID.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/files \
     -H "Authorization: Bearer $ARK_API_KEY" \
     -F "purpose=user_data" \
     -F "file=@/Users/doc/demo.mp4" \
     -F "preprocess_configs[video][fps]=0.3"
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

Output example:

```Plain Text
{
    "text": [
        {
            "start_time": "00:00:00",
            "end_time": "00:00:03",
            "event": "Stack the yellow block on top of the red-yellow-green block tower",
            "danger": false
        },
        {
            "start_time": "00:00:05",
            "end_time": "00:00:07",
            "event": "Pick up the blue block, put it in the mouth to bite, then take it out",
            "danger": false
        },
        {
            "start_time": "00:00:08",
            "end_time": "00:00:12",
            "event": "Push the yellow and blue toy trucks across the floor by hand",
            "danger": false
        },
        {
            "start_time": "00:00:12",
            "end_time": "00:00:24",
            "event": "Hold the edge of the wooden drawer cabinet with both hands and try to step on the lower drawer handle with the right foot to climb up",
            "danger": true
        }
    ]
}
```

## Document understanding

The model supports processing PDF documents. During preprocessing, it splits the document into pages and converts them into multiple images, then inputs each image into the model for processing to realize understanding and analysis of the document content. For file input methods supported by document understanding and examples, see [Document understanding](document-understanding.md).

It is recommended to prioritize using the Files API, especially when files are large, or files need to be reused across requests.

> This method supports files up to 512 MB. The storage period ranges from 1 to 30 days, with a default value of 7 days.

The PDF file and prompt used in the example are as follows:

|PDF file |Prompt |
|---|---|
|![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/0335ac1d4cdc4378bd291584131c2cce~tplv-goo7wpa0wc-image.image) |Provide the text content in the document by paragraph in JSON format, including paragraph type (type) and text content (content) information. |

Code samples:

**Curl**

1. Upload the PDF file to get the File ID.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/files \
     -H "Authorization: Bearer $ARK_API_KEY" \
     -F "purpose=user_data" \
     -F "file=@/Users/doc/demo.pdf"
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
                       "text": "Provide the text content in the document by paragraph in JSON format, including paragraph type (type) and text content (content) information."
                   }
               ]
           }
       ]
     }'
   ```

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
                    "text": "Provide the text content in the document by paragraph in JSON format, including paragraph type (type) and text content (content) information."
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
    fmt.Printf("Video processing completed: %s, status: %s\n", fileInfo.ID, fileInfo.Status)
    inputMessage := responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role: responses.NewOptMessageRole(responses.MessageRoleUser),
                Content: responses.NewContentItemArrayMessageContent(
                    []responses.ContentItem{
            responses.ContentItem{OneOf: responses.NewContentItemFileContentItemSum(responses.ContentItemFile{Type: responses.ContentItemFileTypeInputFile, FileID: responses.NewOptString(fileInfo.ID)})},
            responses.ContentItem{OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{Type: responses.ContentItemTextTypeInputText, Text: "Provide the text content in the document by paragraph in JSON format, including paragraph type (type) and text content (content) information."})},
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
// upload a file for responses
        FileObject fileMeta;
        fileMeta = service.uploadFile(FileCreateRequest.builder() // replace with your file path
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
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemFile.builder().fileId(fileMeta.getId()).build(), ContentItemText.builder().text("Provide the text content in the document by paragraph in JSON format, including paragraph type (type) and text content (content) information.").build()))
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
                    "text": "Provide the text content in the document by paragraph in JSON format, including paragraph type (type) and text content (content) information.",
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
