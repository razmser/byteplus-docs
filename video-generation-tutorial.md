# Video generation tutorial

The Seedance models have excellent semantic understanding capabilities and can quickly generate high\-quality video clips from multimodal input such as text, images, videos, and audio. This tutorial introduces the general capabilities of video generation models and explains how to generate videos with the [Video Generation API](create-video-generation-task-api.md). For details about the capabilities specific to each model version, see [Dreamina Seedance 2.5 tutorial](seedance-2-5.md) and [Dreamina Seedance 2.0 series tutorial](seedance-2-0.md).

# Demo

Visit the [model card](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0) to view more demos.

# Getting started

> **Tip**

> This section explains in detail how to call the video generation API using different programming languages with code samples.

* > If you have no programming experience, we recommend using the [Model Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/gen_video?model=dreamina-seedance-2-0-260128) in the console. It has a rich template library that allows you to generate the same type of video with one click, so you can start creating quickly without writing any code.

* > If you want to quickly experience API calls, we recommend using the [API Explorer](https://api.byteplus.com/api-explorer/?action=CreateContentsGenerationsTasks&groupName=Chat%20API&serviceCode=ark&version=2024-01-01). It has built\-in preset parameters that allow you to initiate API calls with one click. It also supports flexible parameter adjustment (such as setting video watermarks) to meet diverse testing and usage needs.

* > If you want to start developing but have difficulties setting up the development environment or installing dependencies, see [Get started with Dreamina Seedance 2.5](seedance-2-5.md#2.5_quick_start) or [Get started with Dreamina Seedance 2.0](seedance-2-0.md).

Video generation is an asynchronous process:

1. After successfully calling the `POST /contents/generations/tasks` API, the API will return a task ID.

2. You can poll the `GET /contents/generations/tasks/{id}` API until the task status becomes `succeeded`, or use a webhook to automatically receive status changes of the video generation task.

3. After the task is completed, you can download the final generated MP4 file from the content.**video_url** parameter.

## Step 1: Create a video generation task

Create a video generation task via `POST /contents/generations/tasks`.

**cURL**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dreamina-seedance-2-5-260628",
    "content": [
        {
            "type": "text",
            "text": "A girl holding a fox, the girl opens her eyes and gently looks at the camera, the fox is held gently, the camera slowly pulls back, the wind blows through the hair of the girl, and the sound of the wind can be heard."
        },
        {
            "type": "image_url",
            "image_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png"
            }
        }
    ],
    "generate_audio": true,
    "ratio": "adaptive",
    "duration": 5,
    "watermark": true
}'
```

**Python**

```Python
import os
from arkruntime import Ark

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)

if __name__ == "__main__":
    print("----- create request -----")
    resp = client.content_generation.tasks.create(
        model="dreamina-seedance-2-5-260628", # Replace with Model ID
        content=[
            {
                "text": (
                    "A girl holding a fox, the girl opens her eyes and gently looks at the camera, the fox is held gently, the camera slowly pulls back, the girl's hair is blown by the wind, and the sound of the wind can be heard."
                ),
                "type": "text"
            },
            {
                "image_url": {
                    "url": (
                        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png"
                    )
                },
                "type": "image_url"
            }
        ],
        generate_audio=True,
        ratio="adaptive",
        duration=5,
        watermark=True,
    )

    print(resp)
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.content_generation.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;

public class ContentGenerationTaskExample {
    // Make sure that you have stored the API Key in the environment variable ARK_API_KEY
    // Initialize the Ark client to read your API Key from an environment variable
    static String apiKey = System.getenv("ARK_API_KEY");
    static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
    static Dispatcher dispatcher = new Dispatcher();
    static ArkService service = ArkService.builder()
           .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
           //The base URL for model invocation
           .dispatcher(dispatcher)
           .connectionPool(connectionPool)
           .apiKey(apiKey)
           .build();

    public static void main(String[] args) {
        String model = "dreamina-seedance-2-5-260628"; // Replace with Model ID

        Boolean generateAudio = true;
        String ratio = "adaptive";
        Long duration = 5L;
        Boolean watermark = true;
        System.out.println("----- create request -----");
        List<ContentItem> contents = new ArrayList<>();

        // Combination of text prompt and parameters
        contents.add(ContentItem.builder()
                .type(ContentType.TEXT)

                .text("A girl holding a fox, the girl opens her eyes and gently looks at the camera, the fox is held gently, the camera slowly pulls back, the girl's hair is blown by the wind, and the sound of the wind can be heard.")
                .build());
        // The URL of the first frame image
        contents.add(ContentItem.builder()
                .type(ContentType.IMAGE_URL)
                .imageUrl(ImageURL.builder()
                        .url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png")

                        .build())
                .build());

        // Create a video generation task
        CreateContentGenerationTaskRequest createRequest = CreateContentGenerationTaskRequest.builder()
                .model(model)
                .content(contents)
                .generateAudio(generateAudio)
                .ratio(ratio)
                .duration(duration)
                .watermark(watermark)
                .build();

        CreateContentGenerationTaskResponse createResult = service.createContentGenerationTask(createRequest);
        System.out.println(createResult);

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
    model "github.com/volcengine/ark-runtime-go/arkruntime/model/contentgeneration"
)

func main() {
    // Make sure that you have stored the API Key in the environment variable ARK_API_KEY
    // Initialize the Ark client to read your API Key from an environment variable
    client := arkruntime.NewClientWithApiKey(
        // Get your API Key from the environment variable. This is the default mode and you can modify it as required
        os.Getenv("ARK_API_KEY"),
        //The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    //Replace with Model ID
    modelEp := "dreamina-seedance-2-5-260628"

    // Generate a task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model: modelEp,
        GenerateAudio: model.NewOptBool(true),
        Ratio:         model.NewOptString("adaptive"),
        Duration:      model.NewOptInt64(5),
        Watermark:     model.NewOptBool(true),
        Content: []model.ContentItem{
            {
                // Combination of text prompt and parameters
                Type: model.ContentTypeText,

                Text: model.NewOptString("A girl holding a fox, the girl opens her eyes and gently looks at the camera, the fox is held gently, the camera slowly pulls back, the girl's hair is blown by the wind, and the sound of the wind can be heard."),
            },
            {
                // The URL of the first frame image
                Type: model.ContentTypeImageURL,
                ImageURL: model.NewOptImageURL(model.ImageURL{
                    URL: "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png",

                }),
            },
        },
    }
    createResp, err := client.CreateContentGenerationTask(ctx, createReq)
    if err != nil {
        fmt.Printf("create content generation error: %v", err)
        return
    }
    taskID := createResp.ID
    fmt.Printf("Task Created with ID: %s", taskID)
}
```

After the request is successful, the system will return a task ID.

```JSON
{
  "id": "cgt-2025******-****"
}
```

## Step 2: Query video generation task

Use the ID returned from the video generation task to query the detailed task status and result. This API returns the current task status (such as `queued`, `running`, and `succeeded`) and information related to the generated video, such as the video download link, resolution, and duration.

> **Tip**

> The video generation process may take a long time depending on the model, API load, and video output specifications. To manage this process efficiently, you can request status updates by polling the API (see the SDK examples in the [Basic usage](video-generation-tutorial.md#1bf58128) and [Advanced usage](video-generation-tutorial.md#2aa4e615) sections for details), or receive notifications via [Use Webhook notifications](video-generation-tutorial.md#caf01f12).

**cURL**

```Bash
# Replace <TASK_ID> with the ID acquired from "Create Video Generation Task".

curl https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks/<TASK_ID> \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

**Python**

```Python
import os
from arkruntime import Ark

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)

if __name__ == "__main__":
    resp = client.content_generation.tasks.get(
        task_id="<TASK_ID>",
    )
    print(resp)
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.content_generation.*;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.volcengine.ark.runtime.service.ArkService;
import java.util.concurrent.TimeUnit;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;


public class Sample {

    static String apiKey = System.getenv("ARK_API_KEY");

    static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
    static Dispatcher dispatcher = new Dispatcher();
    static ArkService service =
            ArkService.builder()
                    .dispatcher(dispatcher)
                    .connectionPool(connectionPool)
                    .apiKey(apiKey)
                    .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                    .build();

    public static void main(String[] args) throws JsonProcessingException {
        String taskId = "<TASK_ID>";

        String req = taskId;


        service.getContentGenerationTask(req).toString();
        System.out.println(service.getContentGenerationTask(req));

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
)


func main() {
        client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
        ctx := context.Background()

        req := "<TASK_ID>"
        resp, err := client.GetContentGenerationTask(ctx, req)
        if err != nil {
                fmt.Printf("get content generation task error: %v\n", err)
                return
        }
        fmt.Printf("%+v\n", resp)
}
```

After the task status changes to succeeded, you can download the final generated video file from the content.**video_url** parameter.

```JSON
{
    "id": "cgt-2025****",
    "model": "dreamina-seedance-2-5-260628",
    "status": "succeeded",
    "content": {
        "video_url": "https://ark-content-generation-ap-southeast-1.tos-ap-southeast-1.volces.com/****" 
},
    "usage": {
        "completion_tokens": 246840,
        "total_tokens": 246840
},
    "created_at": 1765510475,
    "updated_at": 1765510559,
    "seed": 58944,
    "resolution": "1080p",
    "ratio": "16:9",
    "duration": 5,
    "framespersecond": 24,
    "service_tier": "default",
    "execution_expires_after": 172800
}
```

# Model capabilities

This table lists the capabilities supported by all Seedance models to help you compare and select a model. For details about the capabilities specific to each model version, see [Dreamina Seedance 2.5 tutorial](seedance-2-5.md) and [Dreamina Seedance 2.0 series tutorial](seedance-2-0.md).

|Model name | |[Dreamina Seedance 2.5](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-5&projectName=default) |[Dreamina Seedance 2.0](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0&projectName=default) |[Dreamina Seedance 2.0 Fast](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0-fast&projectName=default) |[Dreamina Seedance 2.0 Mini](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0-mini&projectName=default) |[Seedance 1.5 Pro](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedance-1-5-pro&projectName=default) |[Seedance 1.0 Pro](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedance-1-0-pro&projectName=default) |[Seedance 1.0 Pro Fast](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedance-1-0-pro-fast&projectName=default) |
|---|---|---|---|---|---|---|---|---|
|Model ID | |`dreamina-seedance-2-5-260628` |`dreamina-seedance-2-0-260128` |`dreamina-seedance-2-0-fast-260128` |`dreamina-seedance-2-0-mini-260615` |`seedance-1-5-pro-251215` |`seedance-1-0-pro-250528` |`seedance-1-0-pro-fast-251015` |
|[Text to video](video-generation-tutorial.md#4e74bcee) | |✓ |✓ |✓ |✓ |✓ |✓ |✓ |
|[Image to video - first frame](video-generation-tutorial.md#979b2d28) | |✓ |✓ |✓ |✓ |✓ |✓ |✓ |
|[Image to video - first and last frames](video-generation-tutorial.md#0d55ca07) | |✓ |✓ |✓ |✓ |✓ |✓ |✗ |
|[Omni reference](seedance-2-0.md#50e1b4ea) |Image reference |✓ |✓ |✓ |✓ |✗ |✗ |✗ |
||Video reference |✓ |✓ |✓ |✓ |✗ |✗ |✗ |
||Audio reference |✓ |✗ (must be used with an image or video) |✗ (must be used with an image or video) |✗ (must be used with an image or video) |✗ |✗ |✗ |
||Combined reference<br><br><br>* Image + audio<br><br>* Image + video<br><br>* Video + audio<br><br>* Image + video + audio |✓ |✓ |✓ |✓ |✗ |✗ |✗ |
|[Edit video](seedance-2-0.md#75a28782) | |✓ |✓ |✓ |✓ |✗ |✗ |✗ |
|[Extend video](seedance-2-0.md#46d77653) | |✓ |✓ |✓ |✓ |✗ |✗ |✗ |
|[Generate videos with audio](video-generation-tutorial.md#979b2d28) | |✓ |✓ |✓ |✓ |✓ |✗ |✗ |
|[Draft mode](video-generation-tutorial.md#5acd28c8) | |✓ |✗ |✗ |✗ |✓ |✗ |✗ |
|[Return the last frame of the generated video](video-generation-tutorial.md#141cf7fa) | |✓ |✓ |✓ |✓ |✓ |✓ |✓ |
|[Output video specifications](video-generation-tutorial.md#9fe4cce0) |Resolution |* 480p (8\-bit color depth)<br><br>* 720p (8\-bit color depth)<br><br>* 1080p (10\-bit color depth) |* 480p (8\-bit color depth)<br><br>* 720p (8\-bit color depth)<br><br>* 1080p (8\-bit color depth)<br><br>* 4k (10\-bit color depth) |* 480p (8\-bit color depth)<br><br>* 720p (8\-bit color depth) |* 480p (8\-bit color depth)<br><br>* 720p (8\-bit color depth) |480p<br><br>720p<br><br>1080p |480p<br><br>720p<br><br>1080p |480p<br><br>720p<br><br>1080p |
| |Aspect ratio |21:9<br><br>16:9<br><br>4:3<br><br>1:1<br><br>3:4<br><br>9:16 |21:9<br><br>16:9<br><br>4:3<br><br>1:1<br><br>3:4<br><br>9:16 |21:9<br><br>16:9<br><br>4:3<br><br>1:1<br><br>3:4<br><br>9:16 |21:9<br><br>16:9<br><br>4:3<br><br>1:1<br><br>3:4<br><br>9:16 |21:9<br><br>16:9<br><br>4:3<br><br>1:1<br><br>3:4<br><br>9:16 |21:9<br><br>16:9<br><br>4:3<br><br>1:1<br><br>3:4<br><br>9:16 |21:9<br><br>16:9<br><br>4:3<br><br>1:1<br><br>3:4<br><br>9:16 |
| |Frame rate |24 fps |24 fps |24 fps |24 fps |24 fps |24 fps |24 fps |
| |Duration |4\-30 seconds |4\-15 seconds |4\-15 seconds |4\-15 seconds |4\-12 seconds |2\-12 seconds |2\-12 seconds |
| |Output format |MP4 |MP4 |MP4 |MP4 |MP4 |MP4 |MP4 |
|[Offline inference](video-generation-tutorial.md#a0badaae) | |✗ |✗ |✗ |✗ |✓ |✓ |✓ |

# Basic usage

## Text to video

Generates videos based on the prompts entered by users. The output is highly random, and can be used as a source of inspiration.

|Prompt |Output |
|---|---|
|Photorealistic style: Under a clear blue sky, a vast expanse of white daisy fields stretches out. The camera gradually zooms in and finally fixates on a close\-up of a single daisy, with several glistening dewdrops resting on its petals. |[video](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/b847f3e831c244b39f7b4d53d904988f~tplv-goo7wpa0wc-image.image)<br> |

**Python**

```Python
import os
import time
# Install SDK:python -m pip install --upgrade arkruntime
from arkruntime import Ark

# Make sure that you have stored the API Key in the environment variable ARK_API_KEY
# Initialize the Ark client to read your API Key from an environment variable
client = Ark(
    # This is the default path. You can configure it based on the service location
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.environ.get("ARK_API_KEY"),
)

if __name__ == "__main__":
    print("----- create request -----")
    create_result = client.content_generation.tasks.create(
        model="dreamina-seedance-2-5-260628", #Replace with Model ID 
        content=[
            {
                # Combination of text prompt and parameters
                "type": "text",
                "text": "Photorealistic style: Under a clear blue sky, a vast expanse of white daisy fields stretches out. The camera gradually zooms in and finally fixates on a close-up of a single daisy, with several glistening dewdrops resting on its petals."
            }
        ],
        ratio="16:9",
        duration=5,
        watermark=True,
    )
    print(create_result)

    # Polling query section
    print("----- polling task status -----")
    task_id = create_result.id
    while True:
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
            print(f"Current status: {status}, Retrying after 10 seconds...")
            time.sleep(10)
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.content_generation.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;

public class ContentGenerationTaskExample {
    // Make sure that you have stored the API Key in the environment variable ARK_API_KEY
    // Initialize the Ark client to read your API Key from an environment variable
    static String apiKey = System.getenv("ARK_API_KEY");
    static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
    static Dispatcher dispatcher = new Dispatcher();
    static ArkService service = ArkService.builder()
           .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
           //The base URL for model invocation
           .dispatcher(dispatcher)
           .connectionPool(connectionPool)
           .apiKey(apiKey)
           .build();

    public static void main(String[] args) {
        String model = "dreamina-seedance-2-5-260628"; //Replace with Model ID

        String ratio = "16:9";
        Long duration = 5L;
        Boolean watermark = true;
        System.out.println("----- create request -----");
        List<ContentItem> contents = new ArrayList<>();

        // Combination of text prompt and parameters
        contents.add(ContentItem.builder()
                .type(ContentType.TEXT)

                .text("Photorealistic style: Under a clear blue sky, a vast expanse of white daisy fields stretches out. The camera gradually zooms in and finally fixates on a close-up of a single daisy, with several glistening dewdrops resting on its petals.")
                .build());

        // Create a video generation task
        CreateContentGenerationTaskRequest createRequest = CreateContentGenerationTaskRequest.builder()
                .model(model)
                .content(contents)
                .ratio(ratio)
                .duration(duration)
                .watermark(watermark)
                .build();

        CreateContentGenerationTaskResponse createResult = service.createContentGenerationTask(createRequest);
        System.out.println(createResult);

        // Get the details of the task
        String taskId = createResult.getId();
        String getRequest = taskId;

        // Polling query section
        System.out.println("----- polling task status -----");
        while (true) {
            try {
                ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
                String status = getResponse.getStatus().toString();
                if ("succeeded".equalsIgnoreCase(status)) {
                    System.out.println("----- task succeeded -----");
                    System.out.println(getResponse);
                    break;
                } else if ("failed".equalsIgnoreCase(status)) {
                    System.out.println("----- task failed -----");
                    System.out.println("Error: " + getResponse.getStatus());
                    break;
                } else {
                    System.out.printf("Current status: %s, Retrying in 10 seconds...", status);
                    TimeUnit.SECONDS.sleep(10);
                }
            } catch (InterruptedException ie) {
                Thread.currentThread().interrupt();
                System.err.println("Polling interrupted");
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
    // Make sure that you have stored the API Key in the environment variable ARK_API_KEY
    // Initialize the Ark client to read your API Key from an environment variable
    client := arkruntime.NewClientWithApiKey(
        // Get your API Key from the environment variable. This is the default mode and you can modify it as required
        os.Getenv("ARK_API_KEY"),
        //The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    //Replace with Model ID
    modelEp := "dreamina-seedance-2-5-260628"


    // Generate a task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model: modelEp,
        Ratio:         model.NewOptString("16:9"),
        Duration:      model.NewOptInt64(5),
        Watermark:     model.NewOptBool(true),
        Content: []model.ContentItem{
            {
                // Combination of text prompt and parameters
                Type: model.ContentTypeText,

                Text: model.NewOptString("Photorealistic style: Under a clear blue sky, a vast expanse of white daisy fields stretches out. The camera gradually zooms in and finally fixates on a close-up of a single daisy, with several glistening dewdrops resting on its petals."),
            },
        },
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
            fmt.Printf("Current status: %s, Retrying in 10 seconds... \n", status)
            time.Sleep(10 * time.Second)
        }
    }
}
```

## Image to video – based on the first frame (`with audio`)

By specifying the first frame image of the video, the model can generate video content that is related to and visually coherent with the image.

|Prompt |First frame |Output |
|---|---|---|
|A girl holding a fox, the girl opens her eyes and gently looks at the camera, the fox is held gently, the camera slowly pulls back, the girl's hair is blown by the wind, and the sound of the wind can be heard. |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/a28ec84ff9fc4287a0d98191020a3218~tplv-goo7wpa0wc-image.image) |[video](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/f1f7b95a38ee4ee094c724233e4da4f8~tplv-goo7wpa0wc-image.image)<br> |

**Python**

```Python
import os
import time
# Install SDK:python -m pip install --upgrade arkruntime
from arkruntime import Ark

# Make sure that you have stored the API Key in the environment variable ARK_API_KEY
# Initialize the Ark client to read your API Key from an environment variable
client = Ark(
    # This is the default path. You can configure it based on the service location
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.environ.get("ARK_API_KEY"),
)

if __name__ == "__main__":
    print("----- create request -----")
    create_result = client.content_generation.tasks.create(
        model="dreamina-seedance-2-5-260628", # Replace with Model ID
        content=[
            {
                # Combination of text prompt and parameters
                "type": "text",
                "text": "A girl holding a fox, the girl opens her eyes, looks gently at the camera, the fox hugs affectionately, the camera slowly pulls out, the girl’s hair is blown by the wind, and the sound of the wind can be heard"             
            },
            {
                # The URL of the first frame image
                "type": "image_url",
                "image_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png"
                }
            }
        ],
        generate_audio=True,
        ratio="adaptive",
        duration=5,
        watermark=True,
    )
    print(create_result)

    # Polling query section
    print("----- polling task status -----")
    task_id = create_result.id
    while True:
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
            print(f"Current status: {status}, Retrying after 10 seconds...")
            time.sleep(10)
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.content_generation.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;

public class ContentGenerationTaskExample {
    // Make sure that you have stored the API Key in the environment variable ARK_API_KEY
    // Initialize the Ark client to read your API Key from an environment variable
    static String apiKey = System.getenv("ARK_API_KEY");
    static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
    static Dispatcher dispatcher = new Dispatcher();
    static ArkService service = ArkService.builder()
           .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
           //The base URL for model invocation
           .dispatcher(dispatcher)
           .connectionPool(connectionPool)
           .apiKey(apiKey)
           .build();

    public static void main(String[] args) {
        String model = "dreamina-seedance-2-5-260628"; // Replace with Model ID
        Boolean generateAudio = true;
        String ratio = "adaptive";
        Long duration = 5L;
        Boolean watermark = true;
        System.out.println("----- create request -----");
        List<ContentItem> contents = new ArrayList<>();

        // Combination of text prompt and parameters
        contents.add(ContentItem.builder()
                .type(ContentType.TEXT)

                .text("A girl holding a fox, the girl opens her eyes, looks gently at the camera, the fox hugs affectionately, the camera slowly pulls out, the girl’s hair is blown by the wind, and the sound of the wind can be heard")
                .build());
        // The URL of the first frame image
        contents.add(ContentItem.builder()
                .type(ContentType.IMAGE_URL)
                .imageUrl(ImageURL.builder()
                        .url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png")

                        .build())
                .build());

        // Create a video generation task
        CreateContentGenerationTaskRequest createRequest = CreateContentGenerationTaskRequest.builder()
                .model(model)
                .content(contents)
                .generateAudio(generateAudio)
                .ratio(ratio)
                .duration(duration)
                .watermark(watermark)
                .build();

        CreateContentGenerationTaskResponse createResult = service.createContentGenerationTask(createRequest);
        System.out.println(createResult);

        // Get the details of the task
        String taskId = createResult.getId();
        String getRequest = taskId;

        // Polling query section
        System.out.println("----- polling task status -----");
        while (true) {
            try {
                ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
                String status = getResponse.getStatus().toString();
                if ("succeeded".equalsIgnoreCase(status)) {
                    System.out.println("----- task succeeded -----");
                    System.out.println(getResponse);
                    break;
                } else if ("failed".equalsIgnoreCase(status)) {
                    System.out.println("----- task failed -----");
                    System.out.println("Error: " + getResponse.getStatus());
                    break;
                } else {
                    System.out.printf("Current status: %s, Retrying in 10 seconds...", status);
                    TimeUnit.SECONDS.sleep(10);
                }
            } catch (InterruptedException ie) {
                Thread.currentThread().interrupt();
                System.err.println("Polling interrupted");
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
    // Make sure that you have stored the API Key in the environment variable ARK_API_KEY
    // Initialize the Ark client to read your API Key from an environment variable
    client := arkruntime.NewClientWithApiKey(
        // Get your API Key from the environment variable. This is the default mode and you can modify it as required
        os.Getenv("ARK_API_KEY"),
        //The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    //Replace with Model ID
    modelEp := "dreamina-seedance-2-5-260628"

    // Generate a task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model: modelEp,
        GenerateAudio: model.NewOptBool(true),
        Ratio:         model.NewOptString("adaptive"),
        Duration:      model.NewOptInt64(5),
        Watermark:     model.NewOptBool(true),
        Content: []model.ContentItem{
            {
                // Combination of text prompt and parameters
                Type: model.ContentTypeText,

                Text: model.NewOptString("A girl holding a fox, the girl opens her eyes, looks gently at the camera, the fox hugs affectionately, the camera slowly pulls out, the girl’s hair is blown by the wind, and the sound of the wind can be heard"),
            },
            {
                // The URL of the first frame image
                Type: model.ContentTypeImageURL,
                ImageURL: model.NewOptImageURL(model.ImageURL{
                    URL: "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png",

                }),
            },
        },
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
            fmt.Printf("Current status: %s, Retrying in 10 seconds... \n", status)
            time.Sleep(10 * time.Second)
        }
    }
}
```

## Image to video – based on the first and last frames (`with audio`)

By specifying the starting and ending images of the video, the model can generate a video that smoothly connects the first and last frames, achieving natural and coherent transition effects between scenes.

|Prompt |First frame |Last frame |Output |
|---|---|---|---|
|The girl in the frame says "Cheese" to the camera, with a 360\-degree orbiting camera shot |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/649cb2057eae48d6a6eec872d912c75c~tplv-goo7wpa0wc-image.image) |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/e39fd8e500a34bbdad50d06659c4ea6b~tplv-goo7wpa0wc-image.image) |[video](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/3aa8c84b8a29408ab29e95992d61c559~tplv-goo7wpa0wc-image.image)<br> |

**Python**

```Python
import os
import time
# Install SDK:python -m pip install --upgrade arkruntime
from arkruntime import Ark

# Make sure that you have stored the API Key in the environment variable ARK_API_KEY
# Initialize the Ark client to read your API Key from an environment variable
client = Ark(
    # This is the default path. You can configure it based on the service location
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.environ.get("ARK_API_KEY"),
)


if __name__ == "__main__":
    print("----- create request -----")
    create_result = client.content_generation.tasks.create(
        model="dreamina-seedance-2-5-260628", # Replace with Model ID
        content=[
            {
                # Combination of text prompt and parameters
                "type": "text",
                "text": "The girl in the frame says “Cheese” to the camera, with a 360-degree orbiting camera shot"
            },
            {
                # The URL of the first frame image
                "type": "image_url",
                "image_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seepro_first_frame.jpeg"
                },
                "role": "first_frame"
            },
            {
                # The URL of the last frame image
                "type": "image_url",
                "image_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seepro_last_frame.jpeg"
                },
                "role": "last_frame"
            }
        ],
        generate_audio=True,
        ratio="adaptive",
        duration=5,
        watermark=True,
    )
    print(create_result)

    # Polling query section
    print("----- polling task status -----")
    task_id = create_result.id
    while True:
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
            print(f"Current status: {status}, Retrying after 10 seconds...")
            time.sleep(10)
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.content_generation.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;

public class ContentGenerationTaskExample {
    // Make sure that you have stored the API Key in the environment variable ARK_API_KEY
    // Initialize the Ark client to read your API Key from an environment variable
    static String apiKey = System.getenv("ARK_API_KEY");
    static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
    static Dispatcher dispatcher = new Dispatcher();
    static ArkService service = ArkService.builder()
           .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
           //The base URL for model invocation
           .dispatcher(dispatcher)
           .connectionPool(connectionPool)
           .apiKey(apiKey)
           .build();

    public static void main(String[] args) {
        String model = "dreamina-seedance-2-5-260628"; // Replace with Model ID

        Boolean generateAudio = true;
        String ratio = "adaptive";
        Long duration = 5L;
        Boolean watermark = true;
        System.out.println("----- create request -----");
        List<ContentItem> contents = new ArrayList<>();

        // Combination of text prompt and parameters
        contents.add(ContentItem.builder()
                .type(ContentType.TEXT)

                .text("The girl in the frame says “Cheese” to the camera, with a 360-degree orbiting camera shot")
                .build());
         // The URL of the first frame image
        contents.add(ContentItem.builder()
                .type(ContentType.IMAGE_URL)
                .imageUrl(ImageURL.builder()
                        .url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seepro_first_frame.jpeg")

                        .build())
                .role("first_frame")
                .build());

        // The URL of the last frame image
        contents.add(ContentItem.builder()
                .type(ContentType.IMAGE_URL)
                .imageUrl(ImageURL.builder()
                        .url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seepro_last_frame.jpeg")

                        .build())
                .role("last_frame")
                .build());

        // Create a video generation task
        CreateContentGenerationTaskRequest createRequest = CreateContentGenerationTaskRequest.builder()
                .model(model)
                .content(contents)
                .generateAudio(generateAudio)
                .ratio(ratio)
                .duration(duration)
                .watermark(watermark)
                .build();

        CreateContentGenerationTaskResponse createResult = service.createContentGenerationTask(createRequest);
        System.out.println(createResult);

        // Get the details of the task
        String taskId = createResult.getId();
        String getRequest = taskId;

        // Polling query section
        System.out.println("----- polling task status -----");
        while (true) {
            try {
                ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
                String status = getResponse.getStatus().toString();
                if ("succeeded".equalsIgnoreCase(status)) {
                    System.out.println("----- task succeeded -----");
                    System.out.println(getResponse);
                    break;
                } else if ("failed".equalsIgnoreCase(status)) {
                    System.out.println("----- task failed -----");
                    System.out.println("Error: " + getResponse.getStatus());
                    break;
                } else {
                    System.out.printf("Current status: %s, Retrying in 10 seconds...", status);
                    TimeUnit.SECONDS.sleep(10);
                }
            } catch (InterruptedException ie) {
                Thread.currentThread().interrupt();
                System.err.println("Polling interrupted");
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
    // Make sure that you have stored the API Key in the environment variable ARK_API_KEY
    // Initialize the Ark client to read your API Key from an environment variable
    client := arkruntime.NewClientWithApiKey(
        // Get your API Key from the environment variable. This is the default mode and you can modify it as required
        os.Getenv("ARK_API_KEY"),
        //The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    //Replace with Model ID
    modelEp := "dreamina-seedance-2-5-260628"


    // Generate a task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model: modelEp,
        GenerateAudio: model.NewOptBool(true),
        Ratio:         model.NewOptString("adaptive"),
        Duration:      model.NewOptInt64(5),
        Watermark:     model.NewOptBool(true),
        Content: []model.ContentItem{
            {
                // Combination of text prompt and parameters
                Type: model.ContentTypeText,

                Text: model.NewOptString("The girl in the frame says “Cheese” to the camera, with a 360-degree orbiting camera shot"),
            },
            {
                // The URL of the first frame image
                Type: model.ContentTypeImageURL,
                ImageURL: model.NewOptImageURL(model.ImageURL{
                    URL: "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seepro_first_frame.jpeg",

                }),
                Role: model.NewOptString("first_frame"),
            },
            {
                // The URL of the last frame image
                Type: model.ContentTypeImageURL,
                ImageURL: model.NewOptImageURL(model.ImageURL{
                    URL: "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seepro_last_frame.jpeg",

                }),
                Role: model.NewOptString("last_frame"),
            },
        },
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
            fmt.Printf("Current status: %s, Retrying in 10 seconds... \n", status)
            time.Sleep(10 * time.Second)
        }
    }
}
```

## Manage video tasks

### Query video generation task list

This API supports passing filter parameters to query the list of video generation tasks that meet the specified conditions.

**cURL**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks?page_size=2&filter.status=succeeded& \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

**Python**

```Python
import os
from arkruntime import Ark

client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)

if __name__ == "__main__":
    resp = client.content_generation.tasks.list(
        page_size=3,
        status="succeeded",
    )
    print(resp)
```

**Java**

```Java
package com.byteplus.ark.runtime;

import com.volcengine.ark.runtime.models.content_generation.*;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.volcengine.ark.runtime.service.ArkService;
import java.util.concurrent.TimeUnit;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;


public class Sample {

    static String apiKey = System.getenv("ARK_API_KEY");

    static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
    static Dispatcher dispatcher = new Dispatcher();
    static ArkService service =
            ArkService.builder()
                    .dispatcher(dispatcher)
                    .connectionPool(connectionPool)
                    .apiKey(apiKey)
                    .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                    .build();

    public static void main(String[] args) throws JsonProcessingException {

        ListContentGenerationTasksRequest req =
                ListContentGenerationTasksRequest.builder().pageSize(3).filterStatus(TaskStatus.SUCCEEDED).build();

        service.listContentGenerationTasks(req).toString();
        System.out.println(service.listContentGenerationTasks(req));

        // shutdown service after all requests is finished
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
    model "github.com/volcengine/ark-runtime-go/arkruntime/model/contentgeneration"
)

func main() {
        client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
        ctx := context.Background()

        req := &arkruntime.ListContentGenerationTasksRequest{
                PageSize: int32Pointer(3),
                Status: taskStatusPointer(model.TaskStatus("succeeded")),
        }

        resp, err := client.ListContentGenerationTasks(ctx, req)
        if err != nil {
                fmt.Printf("failed to list content generation tasks: %v\n", err)
                return
        }
        fmt.Printf("%+v\n", resp)
}

func int32Pointer(value int32) *int32 { return &value }
func taskStatusPointer(value model.TaskStatus) *model.TaskStatus { return &value }
```

### Delete or cancel video generation tasks

Cancel queued video generation tasks, or delete video generation task records.

**cURL**

```Bash
# Replace <TASK_ID> with the ID acquired from "Create Video Generation Task".

curl -X DELETE https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks/<TASK_ID> \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

**Python**

```Python
import os
from arkruntime import Ark

client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)

if __name__ == "__main__":
    try:
        client.content_generation.tasks.delete(
            task_id="<TASK_ID>",
        )
    except Exception as e:
        print(f"failed to delete task: {e}")
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.content_generation.*;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.volcengine.ark.runtime.service.ArkService;
import java.util.concurrent.TimeUnit;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

public class Sample {

    static String apiKey = System.getenv("ARK_API_KEY");

    static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
    static Dispatcher dispatcher = new Dispatcher();
    static ArkService service =
            ArkService.builder()
                    .dispatcher(dispatcher)
                    .connectionPool(connectionPool)
                    .apiKey(apiKey)
                    .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                    .build();

    public static void main(String[] args) throws JsonProcessingException {

        String req =
                "<TASK_ID>";

        service.deleteContentGenerationTask(req);

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
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    req := "<TASK_ID>"
    err := client.DeleteContentGenerationTask(ctx, req)
    if err != nil {
        fmt.Printf("delete content generation task error: %v\n", err)
        return
    }
}
```

## Set video output specifications

Use API parameters to control output video specifications, including resolution, aspect ratio, duration, and whether to include a watermark.

**Conventional method (recommended)** : Pass parameters directly in the request body.

> This method uses strict validation. If a parameter is invalid, the model returns an error.

```JSON
{
    "content": [
        {
            "type": "text",
            "text": "<Your prompt>"
}
],
    "resolution": "720p",
    "ratio": "16:9",
    "duration": 5,
    "seed": 11,
    "camera_fixed": false,
    "watermark": true
}
```

**Legacy method**: Append `--[parameters]` after the text prompt.

> This method uses loose validation. If a parameter is invalid, it is ignored or an error is returned.

```JSON
{
    "content": [
        {
            "type": "text",
            "text": "<Your prompt> --rs 720p --rt 16:9 --dur 5 --seed 11 --cf false --wm true"
}
]
}
```

### Resolution and aspect ratio

Use the following parameters to control the output video resolution and aspect ratio. Together, they determine the final video dimensions.

* **resolution**: Specifies the output video resolution. Supported values: 480p, 720p, 1080p, and 4k.

* **ratio**: Specifies the output video aspect ratio. Supported values: 16:9, 4:3, 1:1, 3:4, 9:16, 21:9, and adaptive.

> **tip**

> For Dreamina Seedance 2.5 video editing, video extension, first\-frame image\-to\-video, and first\-and\-last\-frame image\-to\-video tasks, `ratio` only supports `adaptive`, which automatically preserves the aspect ratio of the input video. You cannot specify another aspect ratio. For details, see [Configuration methods](seedance-2-5.md#2.5_param_constraints).

```Json
{
    "resolution": "720p",
    "ratio": "16:9"
}
```

The pixel dimensions of output videos for each model are listed below:

|Resolution |Aspect ratio |Dreamina Seedance 2.5 |Dreamina Seedance 2.0 series |Seedance 1.5 Pro |Seedance 1.0 series |
|---|---|---|---|---|---|
|480p |16:9 |854×480 |864×496 |864×496 |864×480 |
||4:3 |752×560 |752×560 |752×560 |736×544 |
||1:1 |640×640 |640×640 |640×640 |640×640 |
||3:4 |560×752 |560×752 |560×752 |544×736 |
||9:16 |480×854 |496×864 |496×864 |480×864 |
||21:9 |992×432 |992×432 |992×432 |960×416 |
|720p |16:9 |1280×720 |1280×720 |1280×720 |1248×704 |
||4:3 |1112×834 |1112×834 |1112×834 |1120×832 |
||1:1 |960×960 |960×960 |960×960 |960×960 |
||3:4 |834×1112 |834×1112 |834×1112 |832×1120 |
||9:16 |720×1280 |720×1280 |720×1280 |704×1248 |
||21:9 |1470×630 |1470×630 |1470×630 |1504×640 |
|1080p<br><br>> Dreamina Seedance 2.0 Fast and Dreamina Seedance 2.0 Mini do not support 1080p |16:9 |1920×1080 |1920×1080 |1920×1080 |1920×1088 |
||4:3 |1664×1248 |1664×1248 |1664×1248 |1664×1248 |
||1:1 |1440×1440 |1440×1440 |1440×1440 |1440×1440 |
||3:4 |1248×1664 |1248×1664 |1248×1664 |1248×1664 |
||9:16 |1080×1920 |1080×1920 |1080×1920 |1088×1920 |
||21:9 |2206×946 |2206×946 |2206×946 |2176×928 |
|4k<br><br>> Only Dreamina Seedance 2.0 supports 4K |16:9 |*  |3840×2160 |*  |*  |
||4:3 |\- |3326×2494 |\- |\- |
||1:1 |\- |2880×2880 |\- |\- |
||3:4 |\- |2494×3326 |\- |\- |
||9:16 |\- |2160×3840 |\- |\- |
||21:9 |\- |4398×1886 |\- |\- |

### Video duration

Use the `duration` parameter to control the generated video length, in whole seconds:

* **Seedance 1.0 series:**  `[2, 12]`

* **Seedance 1.5 Pro:**  `[4, 12]` or `-1`

* **Seedance 2.0 series:**  `[4, 15]` or `-1`

* **Dreamina Seedance 2.5:**  `[4, 30]` or `-1`

> A value of `-1` enables intelligent duration selection, allowing the model to choose an appropriate video length within the supported range.

> **tip**

> For Dreamina Seedance 2.5 [video editing tasks](seedance-2-5.md#2.5_task_type_intro), `duration` only supports `-1`, which automatically keeps the output duration approximately the same as the input video. You cannot specify a duration in seconds. For details, see [Configuration methods](seedance-2-5.md#2.5_param_constraints).

```json
{
  "duration": 5
}
```

Seedance 1.0 models also support the `frames` parameter, which lets you specify the number of generated frames and create videos with fractional\-second durations.

* **Calculation**: `frames = duration × frame rate (24)`

* **Valid values:**  `frames` supports integer values in the range `[29, 289]` that follow the format `25 + 4n`, where `n` is a positive integer.

* **Note:**  Specify either `duration` or `frames`. If both are provided, `frames` takes precedence over `duration`.

```json
{
  "frames": 29
}
```

### Add a watermark to the video

Use the `watermark` parameter to control whether an AI\-generated watermark is added to the output video.

* `true`: Adds an AI\-generated watermark in the lower\-right corner of the video.

* `false`: Does not add a watermark.

```json
{
  "watermark": true
}
```

## Prompt engineering techniques

* **Prompt = subject + motion, background + motion, camera + motion ...**

* Describe what you want in concise and accurate natural language.

* If you have relatively clear expectations, it is recommended to first use an image generation model to generate images that meet your expectations, then generate video clips based on those images.

* The output of text\-to\-video generation is highly random, and can be used as a source of inspiration.

* When using image\-to\-video generation, please try to upload high\-definition, high\-quality images. The quality of the uploaded image has a significant impact on the image\-to\-video result.

* When the generated video does not meet expectations, it is recommended to modify the prompt, replace abstract descriptions with concrete ones, remove unimportant parts, and place important content at the front.

* For more prompt usage tips, see [Seedance-1.5-pro prompt guide](seedance-1-5-pro.md) and [Seedance-1.0-pro&pro-fast prompt guide](seedance-1-0-pro-pro-fast.md).

# Advanced usage

## Offline inference

> Dreamina Seedance 2.5 and Dreamina Seedance 2.0 series models are not supported.

For scenarios with low inference latency sensitivity (e.g., hour\-level response), it is recommended to set **service_tier** to `flex` to switch to offline inference mode with one click. The cost is only 50% of that of online inference, which significantly reduces costs.

Note that you should set an appropriate timeout period according to your business needs. Tasks will be automatically terminated after the timeout period expires.

**Python**

```Python
import os
import time
# Install SDK:python -m pip install --upgrade arkruntime
from arkruntime import Ark

# Make sure that you have stored the API Key in the environment variable ARK_API_KEY
# Initialize the Ark client to read your API Key from an environment variable
client = Ark(
    # This is the default path. You can configure it based on the service location
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.environ.get("ARK_API_KEY"),
)

if __name__ == "__main__":
    print("----- create request -----")
    create_result = client.content_generation.tasks.create(
        model="seedance-1-5-pro-251215", #Replace with Model ID
        content=[
            {
                # Combination of text prompt and parameters
                "type": "text",
                "text": "A girl holding a fox, the girl opens her eyes, looks gently at the camera, the fox hugs affectionately, the camera slowly pulls out, the girl’s hair is blown by the wind"             
            },
            {
                # The URL of the first frame image
                "type": "image_url",
                "image_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png"
                }
            }
        ],
        ratio="adaptive",
        duration=5,
        watermark=True,
        service_tier="flex",
        execution_expires_after=172800,
    )
    print(create_result)

    # Polling query section
    print("----- polling task status -----")
    task_id = create_result.id
    while True:
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
            print(f"Current status: {status}, Retrying after 60 seconds...")
            time.sleep(60)
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.content_generation.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;

public class ContentGenerationTaskExample {
    // Make sure that you have stored the API Key in the environment variable ARK_API_KEY
    // Initialize the Ark client to read your API Key from an environment variable
    static String apiKey = System.getenv("ARK_API_KEY");
    static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
    static Dispatcher dispatcher = new Dispatcher();
    static ArkService service = ArkService.builder()
           .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
           //The base URL for model invocation
           .dispatcher(dispatcher)
           .connectionPool(connectionPool)
           .apiKey(apiKey)
           .build();

    public static void main(String[] args) {
        String model = "seedance-1-5-pro-251215"; //Replace with Model ID
        String ratio = "adaptive";
        Long duration = 5L;
        Boolean watermark = true;
        String serviceTier = "flex";
        Long executionExpiresAfter = 172800L;
        System.out.println("----- create request -----");
        List<ContentItem> contents = new ArrayList<>();

        // Combination of text prompt and parameters
        contents.add(ContentItem.builder()
                .type(ContentType.TEXT)

                .text("A girl holding a fox, the girl opens her eyes, looks gently at the camera, the fox hugs affectionately, the camera slowly pulls out, the girl's hair is blown by the wind")
                .build());
        // The URL of the first frame image
        contents.add(ContentItem.builder()
                .type(ContentType.IMAGE_URL)
                .imageUrl(ImageURL.builder()
                        .url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png")
                        .build())
                .build());

        // Create a video generation task
        CreateContentGenerationTaskRequest createRequest = CreateContentGenerationTaskRequest.builder()
                .model(model)
                .content(contents)
                .ratio(ratio)
                .duration(duration)
                .watermark(watermark)
                .serviceTier(serviceTier)
                .executionExpiresAfter(executionExpiresAfter)
                .build();

        CreateContentGenerationTaskResponse createResult = service.createContentGenerationTask(createRequest);
        System.out.println(createResult);

        // Get the details of the task
        String taskId = createResult.getId();
        String getRequest = taskId;

        // Polling query section
        System.out.println("----- polling task status -----");
        while (true) {
            try {
                ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
                String status = getResponse.getStatus().toString();
                if ("succeeded".equalsIgnoreCase(status)) {
                    System.out.println("----- task succeeded -----");
                    System.out.println(getResponse);
                    break;
                } else if ("failed".equalsIgnoreCase(status)) {
                    System.out.println("----- task failed -----");
                    System.out.println("Error: " + getResponse.getStatus());
                    break;
                } else {
                    System.out.printf("Current status: %s, Retrying in 60 seconds...", status);
                    TimeUnit.SECONDS.sleep(60);
                }
            } catch (InterruptedException ie) {
                Thread.currentThread().interrupt();
                System.err.println("Polling interrupted");
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
    // Make sure that you have stored the API Key in the environment variable ARK_API_KEY
    // Initialize the Ark client to read your API Key from an environment variable
    client := arkruntime.NewClientWithApiKey(
        // Get your API Key from the environment variable. This is the default mode and you can modify it as required
        os.Getenv("ARK_API_KEY"),
        //The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    //Replace with Model ID
    modelEp := "seedance-1-5-pro-251215"

    // Generate a task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model: modelEp,
        Ratio:                 model.NewOptString("adaptive"),
        Duration:              model.NewOptInt64(5),
        Watermark:             model.NewOptBool(true),
        ServiceTier:           model.NewOptString("flex"),
        ExecutionExpiresAfter: model.NewOptInt64(172800),
        Content: []model.ContentItem{
            {
                // Combination of text prompt and parameters
                Type: model.ContentTypeText,

                Text: model.NewOptString("A girl holding a fox, the girl opens her eyes, looks gently at the camera, the fox hugs affectionately, the camera slowly pulls out, the girl's hair is blown by the wind"),
            },
            {
                // The URL of the first frame image
                Type: model.ContentTypeImageURL,
                ImageURL: model.NewOptImageURL(model.ImageURL{
                    URL: "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png",
                }),
            },
        },
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
            fmt.Printf("Current status: %s, Retrying in 60 seconds... \n", status)
            time.Sleep(60 * time.Second)
        }
    }
}
```

## Draft mode

Draft mode is a two\-step video generation method that lets you preview the result before generating a high\-quality final video. After you enable this feature, you can generate a low\-resolution preview video to validate key elements such as the scene structure, shot scheduling, subject motion, and prompt intent. After confirming that the result meets expectations, use the Draft video task ID to generate a high\-quality final video.

Video task IDs have a validity period, measured from the `created_at` timestamp. An ID automatically becomes invalid when this period expires.

* **Draft video task IDs:**  IDs created at or after 10:00 on October 28, 2026 (UTC+8) are valid for 14 days. IDs created before this time remain valid for seven days. After a Draft video task ID expires, it cannot be used to generate a final video.

* **Other video task IDs:**  Valid for seven days.

> **Note**

> Usage rules and billing methods vary by model. Refer to the instructions for the model you use.

|Model |Instructions |
|---|---|
|Dreamina Seedance 2.5 |See [Dreamina Seedance 2.5 Draft mode](seedance-2-5.md#2.5_draft_mode). |
|Seedance 1.5 pro |See the instructions that follow. |

**Seedance 1.5 pro Draft mode uses two steps:**

**Step 1: Generate a Draft video**

1. Set `"draft": true` and call the `POST /contents/generations/tasks` API to create a Draft video generation task.

2. Call the `GET /contents/generations/tasks/{id}` API to query the generation status and result, download the Draft video, and confirm whether it meets expectations.

> **Tip**

* > Only 480p resolution is supported. Using another resolution causes an error. The return\-last\-frame and offline inference features are not supported.

* > The token rate for Draft videos remains the same, but fewer tokens are consumed. `Draft video token consumption = Normal video token consumption × Conversion factor`. For Seedance 1.5 pro, the conversion factor for videos with audio is 0.6, so generating a Draft video with audio costs 60% of generating a normal video with audio.

**cURL**

1. Create a Draft video generation task.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer $ARK_API_KEY" \
     -d '{
       "model": "seedance-1-5-pro-251215",
       "content": [
           {
               "type": "text",
               "text": "A girl holding a fox, the girl opens her eyes, looks gently at the camera, the fox hugs affectionately, the camera slowly pulls out, the girl’s hair is blown by the wind"
           },
           {
               "type": "image_url",
               "image_url": {
                   "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png"
               }
           }
       ],
       "seed": 20,
       "duration": 6,
       "draft": true,
       "watermark": true
   }'
   ```

After the request succeeds, the system returns a task ID. This ID is the Draft video task ID used to generate the final video.

2. Use the Draft video task ID to query the generation status and result.

   ```Bash
   # Replace cgt-2026****-pzjqb with the ID acquired from the previous step.
   
   curl https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks/cgt-2026****-pzjqb \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer $ARK_API_KEY"
   ```

After the task status changes to `succeeded`, download the generated Draft video from `content.video_url`. If the result does not meet expectations, adjust the parameters and create another Draft video generation task. After confirming that the result meets expectations, generate the final video as described in the next step.

**Python**

1. Create a Draft video task and poll the task status.

2. After the task status changes to `succeeded`, download the generated Draft video from `content.video_url`. If the result does not meet expectations, adjust the parameters and create another Draft video generation task. After confirming that the result meets expectations, generate the final video as described in the next step.

   ```Python
   import os
   import time
   # Install SDK: python -m pip install --upgrade arkruntime
   from arkruntime import Ark
   
   client = Ark(
       # The base URL for model invocation
       base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
       # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
       api_key=os.environ.get("ARK_API_KEY"),
   )
   
   if __name__ == "__main__":
       print("----- create request -----")
       create_result = client.content_generation.tasks.create(
           model="dreamina-seedance-2-5-260628",  # Replace with Model ID
           content=[
               {
                   "type": "text",
                   "text": "A girl holds a fox. She opens her eyes and looks gently at the camera as the camera slowly pulls back."
               },
               {
                   # The URL of the first frame image
                   "type": "image_url",
                   "image_url": {
                       "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png"
                   }
               }
           ],
           duration=5,
           ratio="adaptive",
           resolution="480p",
           service_tier="default",
           draft=True,
           watermark=True,
           extra_body={
               "output_format": "mov",
           },
       )
       print(create_result)
   
       # Polling query section
       print("----- polling task status -----")
       task_id = create_result.id
       deadline = time.monotonic() + 30 * 60
       for attempt in range(180):
           if time.monotonic() >= deadline:
               raise TimeoutError(f"Polling timed out for task {task_id}")
           get_result = client.content_generation.tasks.get(task_id=task_id)
           status = get_result.status
           if status == "succeeded":
               print("----- task succeeded -----")
               print(get_result)
               break
           elif status in {"failed", "cancelled", "expired"}:
               raise RuntimeError(f"Task {task_id} {status}: {get_result.error}")
           else:
               print(f"Current status: {status}, Retrying after 10 seconds...")
               time.sleep(min(10, max(0, deadline - time.monotonic())))
       else:
           raise TimeoutError(f"Polling limit reached for task {task_id}")
   ```

**Java**

1. Create a Draft video task and poll the task status.

2. After the task status changes to `succeeded`, download the generated Draft video from `content.video_url`. If the result does not meet expectations, adjust the parameters and create another Draft video generation task. After confirming that the result meets expectations, generate the final video as described in the next step.

   ```Java
   package com.ark.sample;
   
   import com.volcengine.ark.runtime.models.content_generation.*;
   import com.volcengine.ark.runtime.service.ArkService;
   import okhttp3.ConnectionPool;
   import okhttp3.Dispatcher;
   import retrofit2.Call;
   import retrofit2.Retrofit;
   import retrofit2.http.Body;
   import retrofit2.http.POST;
   
   import java.io.IOException;
   import java.time.Duration;
   import java.util.ArrayList;
   import java.util.LinkedHashMap;
   import java.util.List;
   import java.util.Map;
   import java.util.concurrent.TimeUnit;
   
   public class ContentGenerationTaskExample {
       // Use the SDK's Retrofit client for fields not yet modeled by the SDK.
       interface ContentGenerationApi {
           @POST("contents/generations/tasks")
           Call<CreateContentGenerationTaskResponse> create(@Body Map<String, Object> request);
       }
   
       // Client initialization.
       static String apiKey = System.getenv("ARK_API_KEY");
       static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
       static Dispatcher dispatcher = new Dispatcher();
       static ArkService service = ArkService.builder()
               .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3") // The base URL for model invocation
               .dispatcher(dispatcher)
               .connectionPool(connectionPool)
               .apiKey(apiKey)
               .build();
       static Retrofit retrofit = ArkService.defaultRetrofit(
               ArkService.defaultApiKeyClient(apiKey, Duration.ofSeconds(180)),
               ArkService.defaultObjectMapper(),
               "https://ark.ap-southeast.bytepluses.com/api/v3/",
               Runnable::run);
       static ContentGenerationApi contentGenerationApi = retrofit.create(ContentGenerationApi.class);
   
       public static void main(String[] args) throws IOException {
           try {
               pollTaskStatus(createTask());
           } finally {
               service.shutdownExecutor();
           }
       }
   
       private static String createTask() throws IOException {
           String modelId = "dreamina-seedance-2-5-260628"; // Replace with Model ID
           System.out.println("----- create request -----");
           List<ContentItem> contents = new ArrayList<>();
   
           // Text prompt.
           contents.add(ContentItem.builder()
                   .type(ContentType.TEXT)
                   .text("A girl holds a fox. She opens her eyes and looks gently at the camera as the camera slowly pulls back.")
   
                   .build());
           // The URL of the first frame image
           contents.add(ContentItem.builder()
                   .type(ContentType.IMAGE_URL)
                   .imageUrl(ImageURL.builder()
                           .url("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png")
   
                           .build())
                   .build());
   
           // Create a video generation task
           Map<String, Object> createRequest = new LinkedHashMap<>();
           createRequest.put("model", modelId);
           createRequest.put("content", contents);
           createRequest.put("duration", 5);
           createRequest.put("ratio", "adaptive");
           createRequest.put("resolution", "480p");
           createRequest.put("output_format", "mov");
           createRequest.put("service_tier", "default");
           createRequest.put("draft", true);
           createRequest.put("watermark", true);
   
           retrofit2.Response<CreateContentGenerationTaskResponse> response =
                   contentGenerationApi.create(createRequest).execute();
           if (!response.isSuccessful() || response.body() == null) {
               String errorBody = response.errorBody() == null ? "" : response.errorBody().string();
               throw new IOException("Create task failed with HTTP " + response.code() + ": " + errorBody);
           }
           CreateContentGenerationTaskResponse createResult = response.body();
           System.out.println(createResult);
           return createResult.getId();
       }
   
       private static void pollTaskStatus(String taskId) {
           System.out.println("----- polling task status -----");
           long deadlineNanos = System.nanoTime() + TimeUnit.MINUTES.toNanos(30);
           for (int attempt = 0; attempt < 180 && System.nanoTime() < deadlineNanos; attempt++) {
               try {
                   ContentGenerationTask getResponse = service.getContentGenerationTask(taskId);
                   String status = getResponse.getStatus().toString();
                   if ("succeeded".equalsIgnoreCase(status)) {
                       System.out.println("----- task succeeded -----");
                       System.out.println(getResponse);
                       return;
                   } else if ("failed".equalsIgnoreCase(status)
                           || "cancelled".equalsIgnoreCase(status)
                           || "expired".equalsIgnoreCase(status)) {
                       throw new IllegalStateException(
                               "Task " + taskId + " " + status + ": " + getResponse.getError());
                   } else {
                       System.out.printf("Current status: %s, Retrying in 10 seconds...%n", status);
                       TimeUnit.SECONDS.sleep(10);
                   }
               } catch (InterruptedException ie) {
                   Thread.currentThread().interrupt();
                   throw new IllegalStateException("Polling interrupted", ie);
               }
           }
           throw new IllegalStateException("Polling timed out or reached its limit for task " + taskId);
       }
   }
   ```

**Go**

1. Create a Draft video task and poll the task status.

2. After the task status changes to `succeeded`, download the generated Draft video from `content.video_url`. If the result does not meet expectations, adjust the parameters and create another Draft video generation task. After confirming that the result meets expectations, generate the final video as described in the next step.

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
       // Initialize Ark client.
       client := arkruntime.NewClientWithApiKey(
           os.Getenv("ARK_API_KEY"),
           // The base URL for model invocation
           arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
       )
       ctx, cancel := context.WithTimeout(context.Background(), 30*time.Minute)
       defer cancel()
   
       modelID := "dreamina-seedance-2-5-260628"
       prompt := "A girl holds a fox. She opens her eyes and looks gently at the camera as the camera slowly pulls back."
       firstFrameImageURL := "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png"
   
       fmt.Println("----- create request -----")
       createReq := &model.CreateContentGenerationTaskRequest{
           Model:       modelID,
           Duration:    model.NewOptInt64(5),
           Ratio:       model.NewOptString("adaptive"),
           Resolution:  model.NewOptString("480p"),
           ServiceTier: model.NewOptString("default"),
           Draft:       model.NewOptBool(true),
           Content: []model.ContentItem{
               {
                   Type: model.ContentTypeText,
                   Text: model.NewOptString(prompt),
               },
               {
                   Type: model.ContentTypeImageURL,
                   ImageURL: model.NewOptImageURL(model.ImageURL{
                       URL: firstFrameImageURL,
                   }),
               },
           },
           Watermark: model.NewOptBool(true),
       }
       createResp, err := client.CreateContentGenerationTask(
           ctx,
           createReq,
           arkruntime.WithExtraBody(map[string]interface{}{
               "output_format": "mov",
           }),
       )
       if err != nil {
           panic(fmt.Errorf("create content generation task: %w", err))
       }
       taskID := createResp.ID
       fmt.Printf("Task Created with ID: %s\n", taskID)
       pollTaskStatus(ctx, client, taskID)
   }
   
   func pollTaskStatus(ctx context.Context, client *arkruntime.Client, taskID string) {
       fmt.Println("----- polling task status -----")
       for attempt := 0; attempt < 180; attempt++ {
           getResp, err := client.GetContentGenerationTask(ctx, taskID)
           if err != nil {
               panic(fmt.Errorf("get content generation task: %w", err))
           }
   
           status := getResp.Status
           if status == "succeeded" {
               fmt.Println("----- task succeeded -----")
               fmt.Printf("Task ID: %s\n", getResp.ID)
               fmt.Printf("Model: %s\n", getResp.Model)
               fmt.Printf("Video URL: %s\n", getResp.Content.Or(model.TaskContent{}).VideoURL.Or(""))
               fmt.Printf("Completion Tokens: %d\n", getResp.Usage.Or(model.TaskUsage{}).CompletionTokens)
               fmt.Printf("Created At: %d, Updated At: %d\n", getResp.CreatedAt.Or(0), getResp.UpdatedAt.Or(0))
               return
           } else if status == "failed" || status == "cancelled" || status == "expired" {
               if getResp.Error.IsSet() {
                   panic(fmt.Errorf("task %s %s: %s: %s", taskID, status, getResp.Error.Value.Code, getResp.Error.Value.Message))
               }
               panic(fmt.Errorf("task %s %s", taskID, status))
           } else {
               fmt.Printf("Current status: %s, Retrying in 10 seconds...\n", status)
               select {
               case <-ctx.Done():
                   panic(fmt.Errorf("poll task %s: %w", taskID, ctx.Err()))
               case <-time.After(10 * time.Second):
               }
           }
       }
       panic(fmt.Errorf("polling limit reached for task %s", taskID))
   }
   ```

**Step 2: Generate the final video based on the Draft video**

If the Draft video meets your expectations, call the `POST /contents/generations/tasks` API again based on the Draft video task ID returned in Step 1 to generate the final video.

> **Tip**

* > ModelArk automatically reuses the user input used for the Draft video (`model`, `content.text`, `content.image_url`, `generate_audio`, `seed`, `ratio`, `duration`, and `camera_fixed`) to generate the final video.

* > Other parameters can be specified manually. If they are omitted, the model's default values are used. For example, you can specify the resolution of the final video, whether to include a watermark, whether to use offline inference, and whether to return the last frame.

* > Generating the final video from a Draft video is a normal inference process and is billed based on normal video token consumption.

* > A Draft video task ID has a validity period, measured from its `created_at` timestamp. The ID automatically becomes invalid when this period expires. Draft video task IDs created at or after 10:00 on October 28, 2026 (UTC+8) are valid for 14 days. IDs created before this time remain valid for seven days. After a Draft video task ID expires, it cannot be used to generate the final video.

**cURL**

1. Create a video generation task based on `content.draft_task.id`.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer $ARK_API_KEY" \
     -d '{
       "model": "seedance-1-5-pro-251215",
       "content": [
           {
               "type": "draft_task",
               "draft_task": {"id": "cgt-2026****-pzjqb"}
           }
       ],
         "watermark": true,
         "resolution": "720p",
         "return_last_frame": true,
         "service_tier": "default"
     }'
   ```

After the request succeeds, the system returns a task ID.

2. Use the video task ID to query the generation status and result.

   ```Bash
   # Replace cgt-2026****-bn6zj with the ID acquired from the previous step.
   
   curl https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks/cgt-2026****-bn6zj \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer $ARK_API_KEY"
   ```

After the task status changes to `succeeded`, download the generated video from `content.video_url`.

**Python**

1. Create a video generation task based on `content.draft_task.id`, which is returned in Step 1, and poll the task status.

2. After the task status changes to `succeeded`, download the generated video from `content.video_url`.

   ```Python
   import os
   import time
   # Install SDK: python -m pip install --upgrade arkruntime
   from arkruntime import Ark
   
   client = Ark(
       # The base URL for model invocation
       base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
       # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
       api_key=os.environ.get("ARK_API_KEY"),
   )
   
   if __name__ == "__main__":
       print("----- create request -----")
       create_result = client.content_generation.tasks.create(
           model="dreamina-seedance-2-5-260628",  # Replace with Model ID
           content=[
               {
                   "type": "draft_task",
                   "draft_task": {
                       # Replace with the succeeded Draft task ID from Step 1.
                       "id": "<DRAFT_TASK_ID>"
                   }
               }
           ],
           watermark=True,
           resolution="1080p",
           extra_body={
               "output_format": "mov",
           },
       )
       print(create_result)
   
       # Polling query section
       print("----- polling task status -----")
       task_id = create_result.id
       deadline = time.monotonic() + 30 * 60
       for attempt in range(180):
           if time.monotonic() >= deadline:
               raise TimeoutError(f"Polling timed out for task {task_id}")
           get_result = client.content_generation.tasks.get(task_id=task_id)
           status = get_result.status
           if status == "succeeded":
               print("----- task succeeded -----")
               print(get_result)
               break
           elif status in {"failed", "cancelled", "expired"}:
               raise RuntimeError(f"Task {task_id} {status}: {get_result.error}")
           else:
               print(f"Current status: {status}, Retrying after 10 seconds...")
               time.sleep(min(10, max(0, deadline - time.monotonic())))
       else:
           raise TimeoutError(f"Polling limit reached for task {task_id}")
   ```

**Java**

1. Create a video generation task based on `content.draft_task.id`, which is returned in Step 1, and poll the task status.

2. After the task status changes to `succeeded`, download the generated video from `content.video_url`.

   ```Java
   package com.ark.sample;
   
   import com.volcengine.ark.runtime.models.content_generation.*;
   import com.volcengine.ark.runtime.service.ArkService;
   import okhttp3.ConnectionPool;
   import okhttp3.Dispatcher;
   import retrofit2.Call;
   import retrofit2.Retrofit;
   import retrofit2.http.Body;
   import retrofit2.http.POST;
   
   import java.io.IOException;
   import java.time.Duration;
   import java.util.ArrayList;
   import java.util.LinkedHashMap;
   import java.util.List;
   import java.util.Map;
   import java.util.concurrent.TimeUnit;
   
   public class ContentGenerationTaskExample {
       // Use the SDK's Retrofit client for fields not yet modeled by the SDK.
       interface ContentGenerationApi {
           @POST("contents/generations/tasks")
           Call<CreateContentGenerationTaskResponse> create(@Body Map<String, Object> request);
       }
   
       // Client initialization.
       static String apiKey = System.getenv("ARK_API_KEY");
       static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
       static Dispatcher dispatcher = new Dispatcher();
       static ArkService service = ArkService.builder()
               .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3") // The base URL for model invocation
               .dispatcher(dispatcher)
               .connectionPool(connectionPool)
               .apiKey(apiKey)
               .build();
       static Retrofit retrofit = ArkService.defaultRetrofit(
               ArkService.defaultApiKeyClient(apiKey, Duration.ofSeconds(180)),
               ArkService.defaultObjectMapper(),
               "https://ark.ap-southeast.bytepluses.com/api/v3/",
               Runnable::run);
       static ContentGenerationApi contentGenerationApi = retrofit.create(ContentGenerationApi.class);
   
       public static void main(String[] args) throws IOException {
           try {
               pollTaskStatus(createTask());
           } finally {
               service.shutdownExecutor();
           }
       }
   
       private static String createTask() throws IOException {
           String modelId = "dreamina-seedance-2-5-260628"; // Replace with Model ID
           System.out.println("----- create request -----");
           List<ContentItem> contents = new ArrayList<>();
   
           // Replace with the succeeded Draft task ID from Step 1.
           contents.add(ContentItem.builder()
                   .type(ContentType.DRAFT_TASK)
                   .draftTask(DraftTask.builder()
                           .id("<DRAFT_TASK_ID>")
                           .build())
                   .build());
   
           // Create a video generation task
           Map<String, Object> createRequest = new LinkedHashMap<>();
           createRequest.put("model", modelId);
           createRequest.put("content", contents);
           createRequest.put("resolution", "1080p");
           createRequest.put("output_format", "mov");
           createRequest.put("watermark", true);
   
           retrofit2.Response<CreateContentGenerationTaskResponse> response =
                   contentGenerationApi.create(createRequest).execute();
           if (!response.isSuccessful() || response.body() == null) {
               String errorBody = response.errorBody() == null ? "" : response.errorBody().string();
               throw new IOException("Create task failed with HTTP " + response.code() + ": " + errorBody);
           }
           CreateContentGenerationTaskResponse createResult = response.body();
           System.out.println(createResult);
           return createResult.getId();
       }
   
       private static void pollTaskStatus(String taskId) {
           System.out.println("----- polling task status -----");
           long deadlineNanos = System.nanoTime() + TimeUnit.MINUTES.toNanos(30);
           for (int attempt = 0; attempt < 180 && System.nanoTime() < deadlineNanos; attempt++) {
               try {
                   ContentGenerationTask getResponse = service.getContentGenerationTask(taskId);
                   String status = getResponse.getStatus().toString();
                   if ("succeeded".equalsIgnoreCase(status)) {
                       System.out.println("----- task succeeded -----");
                       System.out.println(getResponse);
                       return;
                   } else if ("failed".equalsIgnoreCase(status)
                           || "cancelled".equalsIgnoreCase(status)
                           || "expired".equalsIgnoreCase(status)) {
                       throw new IllegalStateException(
                               "Task " + taskId + " " + status + ": " + getResponse.getError());
                   } else {
                       System.out.printf("Current status: %s, Retrying in 10 seconds...%n", status);
                       TimeUnit.SECONDS.sleep(10);
                   }
               } catch (InterruptedException ie) {
                   Thread.currentThread().interrupt();
                   throw new IllegalStateException("Polling interrupted", ie);
               }
           }
           throw new IllegalStateException("Polling timed out or reached its limit for task " + taskId);
       }
   }
   ```

**Go**

1. Create a video generation task based on `content.draft_task.id`, which is returned in Step 1, and poll the task status.

2. After the task status changes to `succeeded`, download the generated video from `content.video_url`.

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
       // Initialize Ark client.
       client := arkruntime.NewClientWithApiKey(
           os.Getenv("ARK_API_KEY"),
           // The base URL for model invocation
           arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
       )
       ctx, cancel := context.WithTimeout(context.Background(), 30*time.Minute)
       defer cancel()
       modelID := "dreamina-seedance-2-5-260628"
   
       fmt.Println("----- create request -----")
       createReq := &model.CreateContentGenerationTaskRequest{
           Model:      modelID,
           Watermark:  model.NewOptBool(true),
           Resolution: model.NewOptString("1080p"),
           Content: []model.ContentItem{
               {
                   Type: model.ContentTypeDraftTask,
                   // Replace with the succeeded Draft task ID from Step 1.
                   DraftTask: model.NewOptDraftTask(model.DraftTask{ID: "<DRAFT_TASK_ID>"}),
               },
           },
       }
   
       createResp, err := client.CreateContentGenerationTask(
           ctx,
           createReq,
           arkruntime.WithExtraBody(map[string]interface{}{
               "output_format": "mov",
           }),
       )
       if err != nil {
           panic(fmt.Errorf("create content generation task: %w", err))
       }
       taskID := createResp.ID
       fmt.Printf("Task Created with ID: %s\n", taskID)
       pollTaskStatus(ctx, client, taskID)
   }
   
   func pollTaskStatus(ctx context.Context, client *arkruntime.Client, taskID string) {
       fmt.Println("----- polling task status -----")
       for attempt := 0; attempt < 180; attempt++ {
           getResp, err := client.GetContentGenerationTask(ctx, taskID)
           if err != nil {
               panic(fmt.Errorf("get content generation task: %w", err))
           }
   
           status := getResp.Status
           if status == "succeeded" {
               fmt.Println("----- task succeeded -----")
               fmt.Printf("Task ID: %s\n", getResp.ID)
               fmt.Printf("Model: %s\n", getResp.Model)
               fmt.Printf("Video URL: %s\n", getResp.Content.Or(model.TaskContent{}).VideoURL.Or(""))
               fmt.Printf("Completion Tokens: %d\n", getResp.Usage.Or(model.TaskUsage{}).CompletionTokens)
               fmt.Printf("Created At: %d, Updated At: %d\n", getResp.CreatedAt.Or(0), getResp.UpdatedAt.Or(0))
               return
           } else if status == "failed" || status == "cancelled" || status == "expired" {
               if getResp.Error.IsSet() {
                   panic(fmt.Errorf("task %s %s: %s: %s", taskID, status, getResp.Error.Value.Code, getResp.Error.Value.Message))
               }
               panic(fmt.Errorf("task %s %s", taskID, status))
           } else {
               fmt.Printf("Current status: %s, Retrying in 10 seconds...\n", status)
               select {
               case <-ctx.Done():
                   panic(fmt.Errorf("poll task %s: %w", taskID, ctx.Err()))
               case <-time.After(10 * time.Second):
               }
           }
       }
       panic(fmt.Errorf("polling limit reached for task %s", taskID))
   }
   ```

## Generate multiple consecutive videos

Use the last frame of the previously generated video as the first frame of the next video task, and generate multiple consecutive videos in a loop.

Afterward, you can use tools such as FFmpeg by yourself to stitch the generated multiple short videos into a complete long video.

|Output 1 |Output 2 |Output 3 |
|---|---|---|
|[video](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/c984894e448f43ca8a593babe411a078~tplv-goo7wpa0wc-image.image)<br><br><br>> A girl holding a fox, the girl opens her eyes, looks gently at the camera, the fox hugs affectionately, the camera slowly pulls out, the girl's hair is blown by the wind |[video](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/ccb8cebc70bd42738ba8d4bb894b69e6~tplv-goo7wpa0wc-image.image)<br><br><br>> A girl and a fox running on the grass, sunny weather, the girl's smile is brilliant, the fox jumps happily |[video](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/b78ed8dd418a4c97ac94253cb0c00728~tplv-goo7wpa0wc-image.image)<br><br><br>> A girl and a fox resting under a tree, the girl gently strokes the fox's fur, the fox lies meekly on the girl's lap |

```Python
import os
import time
# Install SDK:python -m pip install --upgrade arkruntime
from arkruntime import Ark

# Make sure that you have stored the API Key in the environment variable ARK_API_KEY
# Initialize the Ark client to read your API Key from an environment variable
client = Ark(
    # This is the default path. You can configure it based on the service location
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.environ.get("ARK_API_KEY"),
)

def generate_video_with_last_frame(prompt, initial_image_url=None):
    """
    Generate video and return video URL and last frame URL
    Parameters:
    prompt: Text prompt for video generation
    initial_image_url: Initial image URL (optional)
    Returns:
    video_url: Generated video URL
    last_frame_url: URL of the last frame of the video
    """
    print(f"----- Generating video: {prompt} -----")

    # Build content list
    content = [{
        "text": prompt,
        "type": "text"
    }]

    # If initial image is provided, add to content
    if initial_image_url:
        content.append({
            "image_url": {
                "url": initial_image_url
            },
            "type": "image_url"
        })

    # Create video generation task
    create_result = client.content_generation.tasks.create(
        model="dreamina-seedance-2-5-260628", #Replace with Model ID
        content=content,
        return_last_frame=True,
        ratio="adaptive",
        duration=5,
        generate_audio=False,
        watermark=True,
    )

    # Poll to check task status
    task_id = create_result.id
    while True:
        get_result = client.content_generation.tasks.get(task_id=task_id)
        status = get_result.status

        if get_result.status == "succeeded":
            print("Video generation succeeded")
            try:
                if hasattr(get_result, 'content') and hasattr(get_result.content, 'video_url') and hasattr(get_result.content, 'last_frame_url'):
                    return get_result.content.video_url, get_result.content.last_frame_url
                print("Failed to obtain video URL or last frame URL")
                return None, None
            except Exception as e:
                print(f"Error occurred while obtaining video URL and last frame URL: {e}")
                return None, None
        elif status == "failed":
            print(f"----- Video generation failed -----")
            print(f"Error: {get_result.error}")
            return None, None
        else:
            print(f"Current status: {status}, retrying in 10 seconds...")
            time.sleep(10)



if __name__ == "__main__":
    # Define 3 video prompts
    prompts = [
        "A girl holding a fox, the girl opens her eyes, looks gently at the camera, the fox hugs affectionately, the camera slowly pulls out, the girl's hair is blown by the wind",
        "A girl and a fox running on the grass, sunny weather, the girl's smile is brilliant, the fox jumps happily",
        "A girl and a fox resting under a tree, the girl gently strokes the fox's fur, the fox lies meekly on the girl's lap"
    ]

    # Store generated video URLs
    video_urls = []

    # Initial image URL
    initial_image_url = "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png"

    # Generate 3 short videos
    for i, prompt in enumerate(prompts):
        print(f"Generating video {i+1}")
        video_url, last_frame_url = generate_video_with_last_frame(prompt, initial_image_url)

        if video_url and last_frame_url:
            video_urls.append(video_url)
            print(f"Video {i+1} URL: {video_url}")
            # Use the last frame of the current video as the first frame of the next video
            initial_image_url = last_frame_url
        else:
            print(f"Video {i+1} generation failed, exiting program")
            exit(1)

    print("All videos generated successfully!")
    print("Generated video URL list:")
    for i, url in enumerate(video_urls):
        print(f"Video {i+1}: {url}")
```

## Use webhook notifications

You can specify a callback notification address via the **callback_url** parameter. When the status of a video generation task changes, ModelArk will send a POST request to this address, so you can get the latest status of the task in time. The request content structure is consistent with the response body of the [Retrieve a video generation task](get-video-generation-task-api.md) API.

```Bash
{
  "id": "cgt-2025****",
  "model": "dreamina-seedance-2-5-260628",
  "status": "running", # Possible status values: queued, running, succeeded, failed, expired
  "created_at": 1765434920,
  "updated_at": 1765434920,
  "service_tier": "default",
  "execution_expires_after": 172800
}
```

You need to build a publicly accessible web server on your own to receive Webhook notifications. See the following simple web server code sample for your reference.

```Python
# Building a Simple Web Server with Python Flask for Webhook Notification Processing

from flask import Flask, request, jsonify
import sqlite3
import logging
from datetime import datetime
import os

# === Basic Configuration ===
app = Flask(__name__)
# Configure logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[logging.FileHandler('webhook.log'), logging.StreamHandler()]
)
# Database path
DB_PATH = 'video_tasks.db'

# === Database Initialization ===
def init_db():
    """Automatically create task table on first run, aligning fields with callback parameters"""
    conn = sqlite3.connect(DB_PATH)
    cursor = conn.cursor()
    # Create table: task_id as primary key for idempotent updates
    cursor.execute('''
    CREATE TABLE IF NOT EXISTS video_generation_tasks (
        task_id TEXT PRIMARY KEY,
        model TEXT NOT NULL,
        status TEXT NOT NULL,
        created_at INTEGER NOT NULL,
        updated_at INTEGER NOT NULL,
        service_tier TEXT NOT NULL,
        execution_expires_after INTEGER NOT NULL,
        last_callback_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
    )
    ''')
    conn.commit()
    conn.close()
    logging.info("Database initialized, table created/exists")

# === Core Webhook Interface ===
@app.route('/webhook/callback', methods=['POST'])
def video_task_callback():
    """Core interface for receiving Ark callback"""
    try:
        # 1. Parse callback request body (JSON format)
        callback_data = request.get_json()
        if not callback_data:
            logging.error("Callback request body empty or non-JSON format")
            return jsonify({"code": 400, "msg": "Invalid JSON data"}), 400

        # 2. Validate required fields
        required_fields = ['id', 'model', 'status', 'created_at', 'updated_at', 'service_tier', 'execution_expires_after']
        for field in required_fields:
            if field not in callback_data:
                logging.error(f"Callback data missing required field: {field}, data: {callback_data}")
                return jsonify({"code": 400, "msg": f"Missing field: {field}"}), 400

        # 3. Extract key information and log
        task_id = callback_data['id']
        status = callback_data['status']
        model = callback_data['model']
        logging.info(f"Received task callback | Task ID: {task_id} | Status: {status} | Model: {model}")
        print(f"[{datetime.now()}] Task {task_id} status updated to: {status}")  # Console output

        # 4. Database operation
        conn = sqlite3.connect(DB_PATH)
        cursor = conn.cursor()
        cursor.execute('''
        INSERT OR REPLACE INTO video_generation_tasks (
            task_id, model, status, created_at, updated_at, service_tier, execution_expires_after
        ) VALUES (?, ?, ?, ?, ?, ?, ?)
        ''', (
            task_id,
            model,
            status,
            callback_data['created_at'],
            callback_data['updated_at'],
            callback_data['service_tier'],
            callback_data['execution_expires_after']
        ))
        conn.commit()
        conn.close()
        logging.info(f"Task {task_id} database update successful")

        # 5. Return 200 response
        return jsonify({"code": 200, "msg": "Callback received successfully", "task_id": task_id}), 200

    except Exception as e:
        # Catch all exceptions to avoid returning 5xx
        logging.error(f"Callback processing failed: {str(e)}", exc_info=True)
        return jsonify({"code": 200, "msg": "Callback received successfully (internal processing exception)"}), 200

# === Helper Interface (Optional, for querying task status) ===
@app.route('/tasks/<task_id>', methods=['GET'])
def get_task_status(task_id):
    """Query latest status of specified task"""
    conn = sqlite3.connect(DB_PATH)
    cursor = conn.cursor()
    cursor.execute('SELECT * FROM video_generation_tasks WHERE task_id = ?', (task_id,))
    task = cursor.fetchone()
    conn.close()
    if not task:
        return jsonify({"code": 404, "msg": "Task not found"}), 404
    # Map field names for response
    fields = ['task_id', 'model', 'status', 'created_at', 'updated_at', 'service_tier', 'execution_expires_after', 'last_callback_at']
    task_dict = dict(zip(fields, task))
    return jsonify({"code": 200, "data": task_dict}), 200

# === Service Startup ===
if __name__ == '__main__':
    # Initialize database
    init_db()
    # Start Flask service (bind to 0.0.0.0 for public access, port customizable)
    # Test environment: debug=True; Production environment should disable debug and use gunicorn
    app.run(host='0.0.0.0', port=8080, debug=False)
```

# Limitations

## Omni reference input

> **Note**

> Dreamina Seedance 2.5 and Dreamina Seedance 2.0 series models do not support direct uploads of reference images or videos containing real human faces. ModelArk provides several solutions to help you use portrait assets. For details, see [Create portrait videos with Dreamina Seedance models](seedance-portrait-asset-guide.md).

**Image requirements**

* Input methods: Image URL, Base64 string of image, or asset ID.

* Image formats: .jpeg, .png, .webp, .bmp, .tiff, .gif. In addition, Seedance 1.5 Pro and Seedance 2.0 series also support .heic and .heif.

* Single image dimensions:

   * Aspect ratio (width/height): (0.4, 2.5)

   * Width and height (px): (300, 6000)

* Size: Single image is less than 30 MB. Request body size must not exceed 64 MB. Do not use Base64 encoding for large files.

* Number of images:

   * Image\-to\-video (first frame): 1 image

   * Image\-to\-video (first and last frames): 2 images

   * Dreamina Seedance 2.5 omni reference\-to\-video: 1–30 images

   * Dreamina Seedance 2.0 series omni reference\-to\-video: 1–9 images

**Video requirements**

* Input methods: Video URL or asset ID.

* Video formats: .mp4, .mov. See the table below for supported encoding formats.

* Resolution: 480p, 720p, 1080p, 4k

* Duration:

   * Dreamina Seedance 2.5:

      * Non\-video\-editing tasks: Each video must be 2–30 seconds long.

      * [Video editing tasks](seedance-2-5.md#2.5_task_type_intro): Each video must be 4–30 seconds long.

      * You can provide up to 10 reference videos with a total duration of no more than 30 seconds.

   * Dreamina Seedance 2.0 series: Each video must be 2–15 seconds long. You can provide up to 3 reference videos with a total duration of no more than 15 seconds.

* Single video dimensions:

   * Aspect ratio (width/height): [0.4, 2.5]

   * Width and height (px): [300, 6000]

   * Total pixel count: [614×664=407696, 3326×2494=8295044], that is, the product of width and height must fall within the range [407696, 8295044].

* Size: Each video must not exceed 200 MB.

* Frame rate (FPS): [24, 60]

|**Container format** |**Common file extensions** |**MIME** |**Supported encoding** |
|---|---|---|---|
|MP4 |.mp4 |video/mp4 |video: H.264/AVC, H.265/HEVC<br><br>audio: AAC, MP3 |
|QuickTime |.mov |video/quicktime |video: H.264/AVC, H.265/HEVC<br><br>audio: AAC, MP3 |

**Audio requirements**

* Input methods: Audio URL, Base64 string of audio, or asset ID.

* Audio formats: .wav, .mp3.

* Duration:

   * Dreamina Seedance 2.5: Each audio clip must be 2–30 seconds long. You can provide up to 10 reference audio clips with a total duration of no more than 30 seconds.

   * Dreamina Seedance 2.0 series: Each audio clip must be 2–15 seconds long. You can provide up to 3 reference audio clips with a total duration of no more than 15 seconds.

* Size: Each audio file must not exceed 15 MB, and the request body size must not exceed 64 MB. Do not use Base64 encoding for large files.

## Retention period

* Video task IDs have a validity period, measured from the `created_at` timestamp. An ID automatically becomes invalid when this period expires.

   * **Draft video task IDs:**  IDs created at or after 10:00 on October 28, 2026 (UTC+8) are valid for 14 days. IDs created before this time remain valid for seven days.

   * **Other video task IDs:**  Valid for seven days.

* Video URLs are retained for 24 hours and can be downloaded up to 100 times. Download or transfer generated videos before the URL expires.

## Rate limits

Under the same primary account, requests to the same model, regardless of model version, are subject to the following limits. Requests that exceed the applicable limit return a `429: 'Too Many Requests'` error.

### Model\-level rate limits

**Online inference**

> RPM and maximum concurrent task limits vary by model. For details, see [Video generation](model-list.md#7571da3f).

* **RPM (requests per minute)** : The maximum number of video generation tasks that can be created per minute. If this limit is exceeded, the task creation request fails due to rate limiting.

* **Maximum concurrent tasks**: The maximum number of tasks that can be processed at the same time. When this limit is reached, newly created tasks enter a queue for processing.

**Offline inference**

> TPD limits vary by model. For details, see [Video generation](model-list.md#7571da3f).

* **TPD (tokens per day)** : The maximum number of tokens that can be processed per day.

### Non\-inference API request rate limits

* **QPS (queries per second)** : The maximum number of requests allowed per second. If this limit is exceeded, the request fails due to rate limiting.

|API |Account\-level QPS limit |
|---|---|
|Retrieve a video generation task |20 |
|List video generation tasks |1 |
|Cancel or delete a video generation task |20 |

## Image cropping rules

**For image\-to\-video tasks using Seedance series models, you can set the aspect ratio of the generated video.**  When the selected video aspect ratio is inconsistent with the aspect ratio of your uploaded image, ModelArk will crop your image, and the cropping will be centered. The detailed rules are as follows:

> **Tip**

> For better video quality, it is recommended that the specified video aspect ratio (ratio) is as close as possible to the aspect ratio of the actual uploaded image.

1. Input parameters:

   * The width of the original image is recorded as `W` (unit: pixels), and the height is recorded as `H` (unit: pixels).

   * The target ratio is recorded as `A:B` (for example, 21:9), which means the ratio of the width to height after cropping should be `A/B` (e.g. 21/9 ≈ 2.333).

2. Compare aspect ratios:

   * Calculate the aspect ratio of the original image `Ratio_original = W/H`.

   * Calculate the target ratio value `Ratio_target = A/B` (for example, the Ratio_target for 21:9 is 21/9 ≈ 2.333).

   * Determine the cropping benchmark based on the comparison result:

      * If `Ratio_original < Ratio_target` (that is, the original image is "too tall" or portrait\-oriented), crop based on the width.

      * If `Ratio_original > Ratio_target` (that is, the original image is "too wide" or landscape\-oriented), crop based on the height.

      * If they are equal, no cropping is required, and the full image is used directly.

3. Cropping size calculation (quantitative formula):

   * Based on width (applicable to portrait images):

      * Cropped width `Crop_W = W` (uses the entire original width).

      * Cropped height `Crop_H = (B/A) × W` (calculate the height proportionally according to the target ratio).

      * Starting coordinates of the cropping area (centered positioning):

         * X coordinate (horizontal): always 0 (since the full width is used, starting from the left edge).

         * Y coordinate (vertical): `(H - Crop_H)/2` (ensures vertical centering, starting from the top edge).

   * Based on height (applicable to landscape images):

      * Cropped height `Crop_H = H` (uses the entire original height).

      * Cropped width `Crop_W = (A/B) × H` (calculate the width proportionally according to the target ratio).

      * Starting coordinates of the cropping area (centered positioning):

         * X coordinate (horizontal): `(W − Crop_W)/2` (ensures horizontal centering, starting from the left edge).

         * Y coordinate (vertical): always 0 (since the full height is used, starting from the top edge).

4. Cropping result:

   * The final cropped image size is `Crop_W × Crop_H`, with a strict aspect ratio of `A:B`, and it is completely inside the original image with no black borders.

   * The cropping area is always based on the center of the original image, so the content is centered.
