# Vision models

Agent Plan Team provides image and video generation models. You can use its image and video generation capabilities through Skills, ArkCLI, or APIs.

## Access through Skills

After installing image and video generation Skills in an AI tool that supports Skills, you can use natural language to generate images and videos.

1. Install the AI tool and configure a language model for Agent Plan Team.

2. Use the configured AI tool to install image and video generation Skills.

   > **tip**

   > When installing a Skill through an AI tool, you can use the following image generation Skill installation prompt as a reference:

   <div data-tips="true" data-tips-type="tip" data-wrapper-indent="1">   ```PlainText
   Install this Skill in a Skills directory that you can read: `npx skills add https://skills.bytepluses.com/skills/volcengine/agentplan -s volcengine-bp-agentplan-media`. After installation, confirm that the `SKILL.md` file exists.
   ```
      </div>
   

   * **Image and video generation Skill**

      ```Bash
      npx skills add https://skills.bytepluses.com/skills/volcengine/agentplan -s volcengine-bp-agentplan-media
      ```

3. After installation, enter an image or video generation prompt in the AI tool to generate an image or video through the corresponding Skill.

   * **Video generation example**

      ```PlainText
      Use volcengine-bp-agentplan-media to generate a 5-second, 16:9 video: aerial drone footage of sunrise over snow-capped mountains, with the camera slowly moving right.
      ```

   * **Image generation example**

      ```PlainText
      Use volcengine-bp-agentplan-media to generate an image: a cyberpunk cat sits on a Tokyo street, with neon reflections in the scene.
      ```

## Access through ArkCLI

You can also access image and video generation capabilities through ArkCLI, a command\-line tool. Use either **Agent invocation** or **terminal invocation**.

### Installation and configuration

Make sure that you have purchased a [Agent Plan Team](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/subscription). For installation, sign\-in, and configuration, see [ArkCLI: Agent Plan Team guide](agent-plan-enterprise-ark-cli.md).

> **Note**: Select `agent-plan-team` as the billing mode when you sign in to ArkCLI.

You can begin using the service after configuring the Agent tool.

### Examples

**Invoke through an Agent**

State your image or video generation requirements directly in an Agent conversation. We recommend adding  **"do not use postpaid billing"**  to the prompt.

* **Image generation**

   ```text
   Use the dola-seedream-5.0-pro model in Agent Plan Team to generate an image: a cyberpunk cat sits on a Tokyo street with neon reflections. Do not use postpaid billing.
   ```

* **Video generation**

   ```text
   Use the dreamina-seedance-2.5 model in Agent Plan Team to generate a 5-second 16:9 video: aerial drone footage of sunrise over snow-capped mountains, slowly moving right. Wait synchronously for the result. Do not use postpaid billing.
   ```

**Invoke through the terminal**

* **Image generation**

   ```Bash
   arkcli +gen "A cyberpunk cat sits on a Tokyo street with neon reflections." \
     --model dola-seedream-5.0-pro
   ```

* **Video generation**

   ```Bash
   arkcli +gen "Aerial drone footage of sunrise over snow-capped mountains, slowly panning." \
     --model dreamina-seedance-2.5 \
     --modality video --duration 5 --ratio 16:9
   ```

## Access through APIs

If your AI tool does not support Skills, or you do not need to use an AI tool, access image and video generation capabilities through APIs.

### Core configuration

To use Agent Plan Team, call the video and image generation APIs with a dedicated API Key, dedicated Base URL, and supported model. Otherwise, calls might fail or incur additional charges.

* **Dedicated API Key**: [Get a dedicated API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine). Other ModelArk API Keys, such as Coding Plan API Keys, cannot be used with Agent Plan Team.

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/agent-plan-enterprise-api-key.png)

* **Supported models**: See [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

* **Dedicated Base URL**: The API endpoints for Agent Plan Team include `/plan`. Do not mix these endpoints with other API endpoints.

   * Curl: For supported API parameters, see the relevant API documentation.

      |Video generation API |Image generation API |
      |---|---|
      |* [Create a video generation task](create-video-generation-task-api.md): `https://ark.ap-southeast.bytepluses.com/api/plan/v3/contents/generations/tasks`<br><br>* [Retrieve a video generation task](get-video-generation-task-api.md): `https://ark.ap-southeast.bytepluses.com/api/plan/v3/contents/generations/tasks/{id}`<br><br>* [List video generation tasks](list-video-generation-tasks-api.md): `https://ark.ap-southeast.bytepluses.com/api/plan/v3/contents/generations/tasks?page_num={page_num}&page_size={page_size}&filter.status={filter.status}&filter.task_ids={filter.task_ids}&filter.model={filter.model}`<br><br>* [Cancel or delete a video generation task](cancel-or-delete-video-generation-tasks-api.md): `https://ark.ap-southeast.bytepluses.com/api/plan/v3/contents/generations/tasks/{id}` |[Image generation](image-generation-api.md): `https://ark.ap-southeast.bytepluses.com/api/plan/v3/images/generations` |

   * SDK:

      Dedicated Base URL: `https://ark.ap-southeast.bytepluses.com/api/plan/v3`

### Call the video generation API

See [Video generation tutorial](video-generation-tutorial.md), [Dreamina Seedance 2.5 series tutorial](seedance-2-5.md), and [Dreamina Seedance 2.0 series tutorial](seedance-2-0.md) to complete video generation tasks. You must use the dedicated API Key, dedicated Base URL, and supported models for Agent Plan Team. Otherwise, calls might fail or incur additional charges. For details, see [Core configuration](agent-plan-enterprise-visual-models.md#8d00fec2).

The following examples create video generation tasks with Agent Plan Team:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/plan/v3/contents/generations/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $AGENT_API_KEY" \
  -d '{
    "model": "dreamina-seedance-2.5",
    "content": [
        {
            "type": "text",
            "text": "A girl holding a fox, the girl opens her eyes, looks gently at the camera, the fox hugs affectionately, the camera slowly pulls out, the girl\u0027s hair is blown by the wind, and the sound of the wind can be heard"
        },
        {
            "type": "image_url",
            "image_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png"
            }
        }
    ],
    "generate_audio": true,
    "ratio": "adaptive"
}'
```

**Python**

```Python
import os
from arkruntime import Ark

client = Ark(
    # Dedicated Base URL for Agent Plan Enterprise
    base_url="https://ark.ap-southeast.bytepluses.com/api/plan/v3",
    # Dedicated API key for Agent Plan Enterprise: https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenModelVisible=false&advancedActiveKey=agentEnterprise
    api_key=os.environ.get("AGENT_API_KEY")
)

if __name__ == "__main__":
    print("----- create request -----")
    resp = client.content_generation.tasks.create(
        model="dreamina-seedance-2.5",
        content=[
            {
                "text": (
                    "A girl holding a fox, the girl opens her eyes, looks gently at the camera, the fox hugs affectionately, the camera slowly pulls out, the girl's hair is blown by the wind"
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
    // Dedicated API key for Agent Plan Enterprise: https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenModelVisible=false&advancedActiveKey=agentEnterprise
    static String apiKey = System.getenv("AGENT_API_KEY");
    static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
    static Dispatcher dispatcher = new Dispatcher();
    static ArkService service = ArkService.builder()
           .baseUrl("https://ark.ap-southeast.bytepluses.com/api/plan/v3") // Dedicated Base URL for Agent Plan Enterprise
           .dispatcher(dispatcher)
           .connectionPool(connectionPool)
           .apiKey(apiKey)
           .build();

    public static void main(String[] args) {
        String model = "dreamina-seedance-2.5";
        Boolean generateAudio = true;
        String ratio = "adaptive";
        Long duration = 5L;
        Boolean watermark = true;
        System.out.println("----- create request -----");
        List<ContentItem> contents = new ArrayList<>();

        // Combination of text prompt and parameters
        contents.add(ContentItem.builder()
                .type(ContentType.TEXT)
                .text("A girl holding a fox, the girl opens her eyes, looks gently at the camera, the fox hugs affectionately, the camera slowly pulls out, the girl's hair is blown by the wind, and the sound of the wind can be heard")
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
    client := arkruntime.NewClientWithApiKey(
        // Dedicated API key for Agent Plan Enterprise: https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenModelVisible=false&advancedActiveKey=agentEnterprise
        os.Getenv("AGENT_API_KEY"),
        // Dedicated Base URL for Agent Plan Enterprise
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/plan/v3"),
    )
    ctx := context.Background()
    modelEp := "dreamina-seedance-2.5"

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
                Text: model.NewOptString("A girl holding a fox, the girl opens her eyes, looks gently at the camera, the fox hugs affectionately, the camera slowly pulls out, the girl's hair is blown by the wind, and the sound of the wind can be heard"),
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

### Call the image generation API

See the [Seedream 5.0 Pro tutorial](seedream-5-0-pro.md) and [Seedream 4.0-5.0 tutorial](image-generation-tutorial.md) to complete image generation tasks. You must use the dedicated API Key, dedicated Base URL, and supported models for Agent Plan Team. Otherwise, calls might fail or incur additional charges. For details, see [Core configuration](agent-plan-enterprise-visual-models.md#8d00fec2).

The following example implements text\-to\-image generation with Agent Plan Team:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/plan/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $AGENT_API_KEY" \
  -d '{
    "model": "dola-seedream-5.0-pro",
    "prompt": "Vibrant close-up editorial portrait, model with piercing gaze, wearing a sculptural hat, rich color blocking, sharp focus on eyes, shallow depth of field, Vogue magazine cover aesthetic, shot on medium format, dramatic studio lighting.",
    "size": "2K",
    "output_format":"png",
    "watermark": true
}'
```

**Python**

```Python
import os
# Install SDK:  python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # Dedicated Base URL for Agent Plan Enterprise
    base_url="https://ark.ap-southeast.bytepluses.com/api/plan/v3",
    # Dedicated API key for Agent Plan Enterprise: https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenModelVisible=false&advancedActiveKey=agentEnterprise
    api_key=os.getenv('AGENT_API_KEY'),
)

imagesResponse = client.images.generate(
    model="dola-seedream-5.0-pro",
    prompt="Vibrant close-up editorial portrait, model with piercing gaze, wearing a sculptural hat, rich color blocking, sharp focus on eyes, shallow depth of field, Vogue magazine cover aesthetic, shot on medium format, dramatic studio lighting.",
    size="2K",
    output_format="png",
    response_format="url",
    watermark=True
)

print(imagesResponse.data[0].url)
```

**Java**

```Java
package com.ark.sample;
import com.volcengine.ark.runtime.models.images.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

import java.util.Arrays;
import java.util.List;
import java.util.concurrent.TimeUnit;

public class ImageGenerationsExample {
    public static void main(String[] args) {
        // Dedicated API key for Agent Plan Enterprise: https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenModelVisible=false&advancedActiveKey=agentEnterprise
        String apiKey = System.getenv("AGENT_API_KEY");
        ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
        Dispatcher dispatcher = new Dispatcher();
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/plan/v3") // Dedicated Base URL for Agent Plan Enterprise
                .dispatcher(dispatcher)
                .connectionPool(connectionPool)
                .apiKey(apiKey)
                .build();

        CreateImageGenerationRequest generateRequest = CreateImageGenerationRequest.builder()
                .model("dola-seedream-5.0-pro")
                .prompt("Vibrant close-up editorial portrait, model with piercing gaze, wearing a sculptural hat, rich color blocking, sharp focus on eyes, shallow depth of field, Vogue magazine cover aesthetic, shot on medium format, dramatic studio lighting.")
                .size("2K")
                .sequentialImageGeneration(SequentialImageGenerationMode.DISABLED)
                .outputFormat(OutputFormat.PNG)
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
        // Dedicated API key for Agent Plan Enterprise: https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenModelVisible=false&advancedActiveKey=agentEnterprise
        os.Getenv("AGENT_API_KEY"),
        // Dedicated Base URL for Agent Plan Enterprise
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/plan/v3"),
    )
    ctx := context.Background()
    outputFormat := model.OutputFormatPNG


    generateReq := &model.CreateImageGenerationRequest{
       Model:          "dola-seedream-5.0-pro",
       Prompt: model.NewOptString("Vibrant close-up editorial portrait, model with piercing gaze, wearing a sculptural hat, rich color blocking, sharp focus on eyes, shallow depth of field, Vogue magazine cover aesthetic, shot on medium format, dramatic studio lighting."),
       Size:           model.NewOptString("2K"),
       OutputFormat: model.NewOptOutputFormat(outputFormat),
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
    # Dedicated Base URL for Agent Plan Enterprise
    base_url="https://ark.ap-southeast.bytepluses.com/api/plan/v3",
    # Dedicated API key for Agent Plan Enterprise: https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenModelVisible=false&advancedActiveKey=agentEnterprise
    api_key=os.getenv('AGENT_API_KEY'),
)

imagesResponse = client.images.generate(
    model="dola-seedream-5.0-pro",
    prompt="Vibrant close-up editorial portrait, model with piercing gaze, wearing a sculptural hat, rich color blocking, sharp focus on eyes, shallow depth of field, Vogue magazine cover aesthetic, shot on medium format, dramatic studio lighting.",
    size="2K",
    output_format="png",
    response_format="url",
    extra_body={
        "watermark": True,
    },
)

print(imagesResponse.data[0].url)
```

## Supported models and capabilities

> **warning**

> Agent Plan Team plans support different models. For details, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

### Video generation models

|Model | |dreamina\-seedance\-2.5 |dreamina\-seedance\-2.5\-flash |
|---|---|---|---|
|Text\-to\-video | |✓ |✓ |
|Image\-to\-video, first frame | |✓ |✓ |
|Image\-to\-video, first and last frames | |✓ |✓ |
|Multimodal reference |Image reference |✓ |✓ |
||Video reference |✓ |✓ |
||Audio reference |✓ |✓ |
||Combined reference<br><br><br>* Image + audio<br><br>* Image + video<br><br>* Video + audio<br><br>* Image + video + audio |✓ |✓ |
|Video editing | |✓ |✓ |
|Video extension | |✓ |✓ |
|Video generation with audio | |✓ |✓ |
|Maximum reference assets | |50 (30 images + 10 videos + 10 audio files) |20 (10 images + 5 videos + 5 audio files) |
|Draft mode | |✓ |✗ |
|Return the last\-frame image of the generated video | |✓ |✓ |
|Output video specifications |Output resolution |* 480p (8\-bit depth)<br><br>* 720p (8\-bit depth)<br><br>* 1080p (10\-bit depth)<br><br>* 4K (10\-bit depth) |* 480p (8\-bit depth)<br><br>* 720p (8\-bit depth) |
||Output aspect ratio |21:9, 16:9, 4:3,<br><br>1:1, 3:4, 9:16, adaptive |21:9, 16:9, 4:3,<br><br>1:1, 3:4, 9:16, adaptive |
||Output duration |4~30 seconds; \-1 (the model automatically selects the optimal duration within the valid range) |4~30 seconds; \-1 (the model automatically selects the optimal duration within the valid range) |
||Output video format |mp4, mov |mp4, mov |
|Deduction coefficient | |* Output video resolution: 480p or 720p<br><br>   * Input includes video: 64<br><br>   * Input does not include video: 107<br><br>* Output video resolution: 1080p<br><br>   * Input includes video: 70<br><br>   * Input does not include video: 117 |* Output video resolution: 480p or 720p<br><br>   * Input includes video: 43<br><br>   * Input does not include video: 70 |

### Image generation models

|Model ||dola\-seedream\-5.5 |dola\-seedream\-5.0\-pro |dola\-seedream\-5.0\-flash |
|---|---|---|---|---|
|Text\-to\-image ||✓ |✓ |✓ |
|Single\-image image generation ||✓ |✓ |✓ |
|Multiple\-reference image generation ||✓, up to 10 reference images |✓, up to 10 reference images |✓, up to 10 reference images |
|Layer decomposition ||✓, 1 base image + up to 99 layers |✓, 1 base image + up to 16 layers |✓, 1 base image + up to 16 layers |
|Native alpha channel |Text\-to\-image |✓ |— |— |
||Image\-to\-image |✓, up to 10 reference images; input images do not need an alpha channel |✓, only 1 PNG reference image with an alpha channel |✓, only 1 PNG reference image with an alpha channel |
|Maximum output image count<br><br>> Layer decomposition ||✓, specify 2–100 images, including the base image and layers |— |— |
|Rich text OCR ||✓ |— |— |
|Model parameters |Resolution<br><br>> Image generation<br><br><br> |* Method 1: Specify a resolution tier: `1K`, `1.5K`, `2K`<br><br>* Method 2: Specify width and height in pixels: [`1280x720`, `2048x2048x1.1025`] |* Method 1: Specify a resolution tier: `1K`, `1.5K`, `2K`<br><br>* Method 2: Specify width and height in pixels: [`1280x720`, `2048x2048x1.1025`] |* Method 1: Specify a resolution tier: `1K`, `1.5K`, `2K`<br><br>* Method 2: Specify width and height in pixels: [`1280x720`, `2048x2048x1.1025`] |
||Resolution<br><br>> Layer decomposition<br><br><br> |1K, 1.5K, 2K, auto (default) |1K, 1.5K, 2K, auto (default) |1K, 1.5K, 2K, auto (default) |
||Image file format |png (default), jpeg |png, jpeg (default) |png, jpeg (default) |
||Background type<br><br>> Image generation<br><br><br> |auto (default), transparent, opaque |transparent, opaque (default) |transparent, opaque (default) |
||Prompt optimization mode |Standard mode (default) |Standard mode (default), fast mode |Standard mode (default) |
|Deduction coefficient |Input image |8 |* First image: free<br><br>* From the second image: 3 |\- |
||Output image |* ≤ 2.61M pixels: 20<br><br>* 2.61M < pixels ≤ 4.63M: 35 |* Single\-image generation:<br><br>   * ≤ 2.61M pixels: 45<br><br>   * \> 2.61M pixels: 90<br><br>* Layer decomposition:<br><br>   * ≤ 2.61M pixels: 22.5<br><br>   * \> 2.61M pixels: 45 |20 |
