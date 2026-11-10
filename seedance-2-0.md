# Dreamina Seedance 2.0 series tutorial

Dreamina Seedance 2.0 series (hereinafter referred to as Seedance 2.0 series) models support multimodal input such as images, videos, audio, and text, with capabilities including video generation, video editing, and video extension. They can restore item details, timbres, effects, styles, camera movements, and more with high accuracy, maintain consistent character features, and give users enterprise\-grade control. This topic introduces the exclusive capabilities of the Seedance 2.0 series models to help you get started quickly.

> **Model activation**

> Before enabling the Dreamina Seedance 2.0 models, make sure you meet one of the following conditions:

* > **Recommended:** BytePlus account balance \> USD 30 ([Top up](https://console.byteplus.com/finance/overview))

* > **Recommended:** Purchase a dedicated AI Savings Plan at the USD 30 tier or above. Purchase entry: [AI Savings Plan](https://console.byteplus.com/common-buy/AI-SavingsPlans%7C%7Cd9urs77og65q382arfog).

* > You have purchased a Dreamina Seedance 2.0 series resource pack with available quota ([Purchase](https://console.byteplus.com/common-buy/ModelArk%7C%7Cd7d6aanpgiftptb9ajcg)).

> For detailed rules, see [Activate, use, and cancel Dreamina Seedance models](seedance-model-activation-usage-and-refund.md).

# Getting started

This getting started tutorial is designed specifically for **users new to API**, to help you set up a Python development environment, create virtual environments, and install the ModelArk SDK with one click. With the provided out\-of\-the\-box Seedance 2.0 series code samples, you only need to replace the input assets to start your video creation.

## **1. Prerequisites**

Before you start, make sure you have completed the following preparations:

1. **Register an account**: Make sure you have a BytePlus account and are [signed in](https://ai.byteplus.com/ark/region:ap-southeast-1/overview).

2. **Get API Key**: Visit the [API keys](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page, click **Create API Key**, then copy and save your API Key. Make sure to keep your API Key safe and do not disclose it to others.

3. [Activate the models](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement): Make sure your BytePlus account balance is greater than USD 30, or purchase a [prepaid resource pack](https://console.byteplus.com/common-buy/ModelArk%7C%7Cd7d6aanpgiftptb9ajcg) in advance. Otherwise, you cannot activate Seedance 2.0 series models.

4. **Download and unzip the file**: Click [here](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/f85a5be8202b45c1bb226669214af8c6~tplv-goo7wpa0wc-image.image) to download the attachment, and unzip it to your local directory (such as the desktop or "Downloads" folder).

## **2. Procedure**

**Windows users**

1. Go to the `scripts/init_dev_env` directory.

2. Double\-click to run `setup_windows.bat`.

3. The script will automatically perform the following operations:

   * Download the uv tool.

   * Automatically download Python 3.12 (if it does not interfere with the Python installed in your system).

   * Create the `.venv` virtual environment.

   * Install the ModelArk SDK.

4. After completion, a `run_demo.bat` file will be generated in the project root directory.

5. Double\-click `run_demo.bat` to run the Python SDK code sample (python/demo_standard.py).

**macOS users**

1. Open the terminal and go to the `scripts/init_dev_env` directory.

2. Run the set up script:

   ```plaintext
   ./setup_mac.sh
   ```

3. The script will automatically configure all environments.

4. After completion, a `run_demo.sh` file will be generated in the project root directory.

5. Run `./run_demo.sh` to run the Python SDK code sample (python/demo_standard.py).

## **3. What the script does**

After running the script, you will see the following process:

1. **Verify the API Key**: The script will automatically detect whether the `ARK_API_KEY` environment variable is configured locally. If not, you will be prompted to enter it manually.

2. **Preview the assets**: The script will automatically pop up a locally generated HTML page in your default browser, displaying the text prompt for this task, the reference image to be replaced, and the reference video.

3. **Create the task and query for status**: The script initiates an asynchronous request to the ModelArk server. Since video generation takes some time, the console will print the task status (such as `running`) every 30 seconds.

4. **Get the results**: After the task is completed successfully, the console will output the URL of the generated video. You can copy the link to your browser to download or play it online.

## **4. Next steps**

After you successfully run this sample, you can try to modify `python/demo_standard.py` to create your own video\-generation task:

1. Modify the text prompt.

   Find the `user_content` variable in the code and change it to any description you want.

2. Replace input assets (images, videos, and audio).

   You can replace `reference_image_url`, `reference_video_url`, and `reference_audio_url` with your own asset links. **Note**: Make sure the URL is publicly accessible. We recommend storing the file in BytePlus TOS and configuring public read access.

3. Continue to explore the following examples.

# Model capabilities

The Seedance 2.0 series models currently include Dreamina Seedance 2.0 (hereinafter referred to as Seedance 2.0), Dreamina Seedance 2.0 fast (hereinafter referred to as Seedance 2.0 fast) and Dreamina Seedance 2.0 mini (hereinafter referred to as Seedance 2.0 mini). The three models support largely the same features, with the primary differences being the trade\-off between generation quality and cost:

* For the highest generation quality, use Seedance 2.0.

* For a balance of cost and generation speed when top\-tier quality is not required, use Seedance 2.0 fast.

* For the best cost performance, use Seedance 2.0 mini.

|Model Name | |[Seedance 2.0](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0) |[Seedance 2.0 fast](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0-fast) |[Seedance 2.0 mini](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0-mini) |
|---|---|---|---|---|
|Model ID | |dreamina\-seedance\-2\-0\-260128 |dreamina\-seedance\-2\-0\-fast\-260128 |dreamina\-seedance\-2\-0\-mini\-260615 |
|[Text to video](video-generation-tutorial.md#4e74bcee) | |✓ |✓ |✓ |
|[Image to video (first frame)](video-generation-tutorial.md#979b2d28) | |✓ |✓ |✓ |
|[Image to video (first and last frames)](video-generation-tutorial.md#0d55ca07) | |✓ |✓ |✓ |
|[Omni reference](seedance-2-0.md#50e1b4ea) [New] |Image reference |✓ |✓ |✓ |
||Video reference |✓ |✓ |✓ |
||Combined reference<br><br><br>* Image + audio<br><br>* Image + video<br><br>* Video + audio<br><br>* Image + video + audio |✓ |✓ |✓ |
|[Edit video](seedance-2-0.md#75a28782) [New] | |✓ |✓ |✓ |
|[Extend video](seedance-2-0.md#46d77653) [New] | |✓ |✓ |✓ |
|[Generate videos with audio](video-generation-tutorial.md#979b2d28)<br><br>> "generate_audio": "true" | |✓ |✓ |✓ |
|[Draft mode](video-generation-tutorial.md#5acd28c8) | |✗ |✗ |✗ |
|[Return the last frame of the generated video](video-generation-tutorial.md#141cf7fa)<br><br>> "return_last_frame": "true" | |✓ |✓ |✓ |
|[Output video specifications](video-generation-tutorial.md#9fe4cce0) |Resolution<br><br>> "resolution": "720p" |* 480p (8\-bit color depth)<br><br>* 720p (8\-bit color depth)<br><br>* 1080p (8\-bit color depth)<br><br>* 4k (10\-bit color depth) |* 480p (8\-bit color depth)<br><br>* 720p (8\-bit color depth) |* 480p (8\-bit color depth)<br><br>* 720p (8\-bit color depth) |
||Aspect ratio<br><br>> "ratio":"16:9" |21:9, 16:9, 4:3, 1:1, 3:4, 9:16 |21:9, 16:9, 4:3, 1:1, 3:4, 9:16 |21:9, 16:9, 4:3, 1:1, 3:4, 9:16 |
||Duration<br><br>> "duration": 5 |4–15 seconds |4–15 seconds |4–15 seconds |
||Video format |mp4 |mp4 |mp4 |
|[Offline inference](video-generation-tutorial.md#a0badaae)<br><br>> "service_tier": "flex" | |✗ |✗ |✗ |
|Online inference rate limits |Max. RPM |**Non\-4K**:<br><br><br>* Enterprise users: 600<br><br>* Individual users: 180<br><br>   **4K**:<br><br>* Enterprise users: 15<br><br>* Individual users: 15 |* Enterprise users: 600<br><br>* Individual users: 180 |* Enterprise users: 600<br><br>* Individual users: 180 |
||Max. concurrency |**Non\-4K**:<br><br><br>* Enterprise users: 10<br><br>* Individual users: 3<br><br>   **4K**:<br><br>* Enterprise users: 1<br><br>* Individual users: 1 |* Enterprise users: 10<br><br>* Individual users: 3 |* Enterprise users: 10<br><br>* Individual users: 3 |
|Offline inference rate limits |Max. TPD |\- |\- |\- |

# Basic usage

## Omni reference

Input text, reference images, videos (with or without audio tracks), and audio to generate a new video. It can inherit core information including character image, visual style, and screen composition from reference images; subject, camera movement, action performance, and overall style from reference videos; and timbre, music melody, and dialogue content from reference audio.

The following are some demos (visit the [model card](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0) to view more samples):

|Input: text |Input: image, video, audio |Output |
|---|---|---|
|Use the first\-person POV framing from [Video 1] throughout, and use [Audio 1] as the background music throughout. First\-person POV fruit tea promotional ad, seedance limited\-edition apple fruit tea; opening frame is [Image 1], your hand picks a dew\-covered Aksu red apple, a light, crisp apple tapping sound; 2–4 seconds: fast cuts, your hand drops apple chunks into a shaker, adds ice and tea base, shakes forcefully, ice clinking and shaking sounds sync with upbeat rhythmic beats, background audio: {Fresh\-cut, shaken fresh}; 4–6 seconds: first\-person close\-up of the finished drink, layered fruit tea is poured into a clear cup, your hand gently squeezes milk foam to spread across the top, a pink brand sticker is applied to the cup, the camera moves closer to show the layered textures of the foam and fruit tea; 6–8 seconds: first\-person hand\-held toast shot, you raise the fruit tea from [Image 2] toward the camera (simulating handing it to the viewer), the cup label is clearly visible, background audio {Take a sip of fresh refreshment}, the final frame freezes on Image 2. All background voice audio uses a female voice. |[video](https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/0ba05cd435f543c5bc65c378d94d094a)<br><br><br>[r2v_tea_audio1.mp3](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/8bbbacecfd7d48dfa7ec6ec74125eb04~tplv-goo7wpa0wc-image.image)<br><br><br>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/37ef4b6af8944a6d9b54ef1c541c1b0e~tplv-goo7wpa0wc-image.image) ![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/91cb11fe32014cd6ad9354e271638d85~tplv-goo7wpa0wc-image.image) |[video](https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/74d43d21b6cf40248c8060bc8181c318)<br> |

**Python**

```Python
import os
import time
# Install SDK:python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    #The base URL for model invocation
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.environ.get("ARK_API_KEY"),
)

if __name__ == "__main__":
    print("----- create request -----")
    create_result = client.content_generation.tasks.create(
        model="dreamina-seedance-2-0-260128", #Replace with Model ID
        content=[
            {
                "type": "text",
                "text": "Use the first-person POV framing from Video 1 throughout, and use Audio 1 as the background music throughout. First-person POV fruit tea promotional ad, seedance limited-edition apple fruit tea; opening frame is Image 1, your hand picks a dew-covered Aksu red apple, a light, crisp apple tapping sound; 2–4 seconds: fast cuts, your hand drops apple chunks into a shaker, adds ice and tea base, shakes forcefully, ice clinking and shaking sounds sync with upbeat rhythmic beats, background audio: {Fresh-cut, shaken fresh}; 4–6 seconds: first-person close-up of the finished drink, layered fruit tea is poured into a clear cup, your hand gently squeezes milk foam to spread across the top, a pink brand sticker is applied to the cup, the camera moves closer to show the layered textures of the foam and fruit tea; 6–8 seconds: first-person hand-held toast shot, you raise the fruit tea from Image 2 toward the camera (simulating handing it to the viewer), the cup label is clearly visible, background audio {Take a sip of fresh refreshment}, the final frame freezes on Image 2. All background voice audio uses a female voice.",
            },
            {
                "type": "image_url",
                "image_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/r2v_tea_pic1.jpg"
                },
                "role": "reference_image",
            },
            {
                "type": "image_url",
                "image_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/r2v_tea_pic2.jpg"
                },
                "role": "reference_image",
            },
            {
                "type": "video_url",
                "video_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_tea_video1.mp4"
                },
                "role": "reference_video",
            },
            {
                "type": "audio_url",
                "audio_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/video-generation/r2v_tea_audio1.mp3"
                },
                "role": "reference_audio",
            },
        ],
        generate_audio=True,
        ratio="16:9",
        duration=11,
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
            print(f"Current status: {status}, Retrying after 30 seconds...")
            time.sleep(30)
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

    // Client initialization
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

        // Model ID
        final String modelId = "dreamina-seedance-2-0-260128";
        // Text prompt
        final String prompt = "Use the first-person POV framing from [Video 1] throughout, and use [Audio 1] as the background music throughout. First-person POV fruit tea promotional ad, seedance limited-edition apple fruit tea; " +
                "opening frame is [Image 1], your hand picks a dew-covered Aksu red apple, a light, crisp apple tapping sound;" +
                "2–4 seconds: fast cuts, your hand drops apple chunks into a shaker, adds ice and tea base, shakes forcefully, ice clinking and shaking sounds sync with upbeat rhythmic beats, background audio: {Fresh-cut, shaken fresh}; " +
                "4–6 seconds: first-person close-up of the finished drink, layered fruit tea is poured into a clear cup, your hand gently squeezes milk foam to spread across the top, a pink brand sticker is applied to the cup, the camera moves closer to show the layered textures of the foam and fruit tea;" +
                "6–8 seconds: first-person hand-held toast shot, you raise the fruit tea from [Image 2] toward the camera (simulating handing it to the viewer), the cup label is clearly visible, background audio {Take a sip of fresh refreshment}, the final frame freezes on Image 2." +
                "All background voice audio uses a female voice.";

        // Example resource URLs
        final String refImage1 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/r2v_tea_pic1.jpg";
        final String refImage2 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/r2v_tea_pic2.jpg";
        final String refVideo = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_tea_video1.mp4";
        final String refAudio = "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/video-generation/r2v_tea_audio1.mp3";

        // Output video parameters
        final boolean generateAudio = true;
        final String videoRatio = "16:9";
        final long videoDuration = 11L;
        final boolean showWatermark = true;

        System.out.println("----- create request -----");
        // Build request content
        List<ContentItem> contents = new ArrayList<>();

        // 1. Text prompt
        contents.add(ContentItem.builder()
                .type(ContentType.TEXT)
                .text(prompt)
                .build());

        // 2. Reference image 1
        contents.add(ContentItem.builder()
                .type(ContentType.IMAGE_URL)
                .imageUrl(ImageURL.builder()
                        .url(refImage1)
                        .build())
                .role("reference_image")
                .build());

        // 3. Reference image 2
        contents.add(ContentItem.builder()
                .type(ContentType.IMAGE_URL)
                .imageUrl(ImageURL.builder()
                        .url(refImage2)
                        .build())
                .role("reference_image")
                .build());

        // 4. Reference video
        contents.add(ContentItem.builder()
                .type(ContentType.VIDEO_URL)
                .videoUrl(VideoURL.builder()
                        .url(refVideo)
                        .build())
                .role("reference_video")
                .build());

        // 5. Reference audio
        contents.add(ContentItem.builder()
                .type(ContentType.AUDIO_URL)
                .audioUrl(AudioURL.builder()
                        .url(refAudio)
                        .build())
                .role("reference_audio")
                .build());

        // Create video generation task
        CreateContentGenerationTaskRequest createRequest = CreateContentGenerationTaskRequest.builder()
                .generateAudio(generateAudio)
                .model(modelId)
                .content(contents)
                .ratio(videoRatio)
                .duration(videoDuration)
                .watermark(showWatermark)
                .build();

        CreateContentGenerationTaskResponse createResult = service.createContentGenerationTask(createRequest);
        System.out.println("Task Created: " + createResult);

        // Get task details and poll status
        String taskId = createResult.getId();
        pollTaskStatus(taskId);
    }

    /**
     * Poll task status
     *@param taskId Task ID
     */

    private static void pollTaskStatus(String taskId) {
        String getRequest = taskId;

        System.out.println("----- polling task status -----");
        try {
            while (true) {
                ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
                String status = getResponse.getStatus().toString();

                if ("succeeded".equalsIgnoreCase(status)) {
                    System.out.println("----- task succeeded -----");
                    System.out.println(getResponse);
                    break;
                } else if ("failed".equalsIgnoreCase(status)) {
                    System.out.println("----- task failed -----");
                    if (getResponse.getError() != null) {
                        System.out.println("Error: " + getResponse.getError().getMessage());
                    }
                    break;
                } else {
                    System.out.printf("Current status: %s, Retrying in 10 seconds...%n", status);
                    TimeUnit.SECONDS.sleep(10);
                }
            }
        } catch (InterruptedException ie) {
            Thread.currentThread().interrupt();
            System.err.println("Polling interrupted");
        } catch (Exception e) {
            System.err.println("Error occurred: " + e.getMessage());
        } finally {
            service.shutdownExecutor();
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
    // Initialize Ark client
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        //The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    // Model ID
    modelID := "dreamina-seedance-2-0-260128"
    // Text prompt
    prompt := "Use the first-person POV framing from [Video 1] throughout, and use [Audio 1] as the background music throughout. First-person POV fruit tea promotional ad, seedance limited-edition apple fruit tea; " +
                "opening frame is [Image 1], your hand picks a dew-covered Aksu red apple, a light, crisp apple tapping sound;" +
                "2–4 seconds: fast cuts, your hand drops apple chunks into a shaker, adds ice and tea base, shakes forcefully, ice clinking and shaking sounds sync with upbeat rhythmic beats, background audio: {Fresh-cut, shaken fresh}; " +
                "4–6 seconds: first-person close-up of the finished drink, layered fruit tea is poured into a clear cup, your hand gently squeezes milk foam to spread across the top, a pink brand sticker is applied to the cup, the camera moves closer to show the layered textures of the foam and fruit tea;" +
                "6–8 seconds: first-person hand-held toast shot, you raise the fruit tea from [Image 2] toward the camera (simulating handing it to the viewer), the cup label is clearly visible, background audio {Take a sip of fresh refreshment}, the final frame freezes on Image 2." +
                "All background voice audio uses a female voice."

    // Example resource URLs
    refImage1 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/r2v_tea_pic1.jpg"
    refImage2 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/r2v_tea_pic2.jpg"
    refVideo := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_tea_video1.mp4"
    refAudio := "https://arkdocs-en.tos-ap-southeast-1.volces.com/files/video-generation/r2v_tea_audio1.mp3"

    // Output video parameters
    generateAudio := true
    videoRatio := "16:9"
    videoDuration := int64(11)
    showWatermark := true

    // 1. Create video generation task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model:         modelID,
        GenerateAudio: model.NewOptBool(generateAudio),
        Ratio:         model.NewOptString(videoRatio),
        Duration:      model.NewOptInt64(videoDuration),
        Watermark:     model.NewOptBool(showWatermark),
        Content: []model.ContentItem{
            {
                Type: model.ContentTypeText,
                Text: model.NewOptString(prompt),
            },
            {
                Type: model.ContentTypeImageURL,
                ImageURL: model.NewOptImageURL(model.ImageURL{
                    URL: refImage1,
                }),
                Role: model.NewOptString("reference_image"),
            },
            {
                Type: model.ContentTypeImageURL,
                ImageURL: model.NewOptImageURL(model.ImageURL{
                    URL: refImage2,
                }),
                Role: model.NewOptString("reference_image"),
            },
            {
                Type: model.ContentTypeVideoURL,
                VideoURL: model.NewOptVideoURL(model.VideoURL{
                    URL: refVideo,
                }),
                Role: model.NewOptString("reference_video"),
            },
            {
                Type: model.ContentTypeAudioURL,
                AudioURL: model.NewOptAudioURL(model.AudioURL{
                    URL: refAudio,
                }),
                Role: model.NewOptString("reference_audio"),
            },
        },
    }

    createResp, err := client.CreateContentGenerationTask(ctx, createReq)
    if err != nil {
        fmt.Printf("create content generation error: %v\n", err)
        return
    }

    taskID := createResp.ID
    fmt.Printf("Task Created with ID: %s\n", taskID)

    // 2. Poll task status
    pollTaskStatus(ctx, client, taskID)
}

// poll task status
func pollTaskStatus(ctx context.Context, client *arkruntime.Client, taskID string) {
    fmt.Println("----- polling task status -----")
    for {
        getReq := taskID
        getResp, err := client.GetContentGenerationTask(ctx, getReq)
        if err != nil {
            fmt.Printf("get content generation task error: %v\n", err)
            return
        }

        status := getResp.Status
        if status == "succeeded" {
            fmt.Println("----- task succeeded -----")
            fmt.Printf("Task ID: %s \n", getResp.ID)
            fmt.Printf("Model: %s \n", getResp.Model)
            fmt.Printf("Video URL: %s \n", getResp.Content.Or(model.TaskContent{}).VideoURL.Or(""))
            fmt.Printf("Completion Tokens: %d \n", getResp.Usage.Or(model.TaskUsage{}).CompletionTokens)
            fmt.Printf("Created At: %d, Updated At: %d\n", getResp.CreatedAt.Or(0), getResp.UpdatedAt.Or(0))
            return
        } else if status == "failed" {
            fmt.Println("----- task failed -----")
            if getResp.Error.IsSet() {
                fmt.Printf("Error Code: %s, Message: %s\n", getResp.Error.Value.Code, getResp.Error.Value.Message)
            }
            return
        } else {
            fmt.Printf("Current status: %s, Retrying in 10 seconds... \n", status)
            time.Sleep(10 * time.Second)
        }
    }
}
```

> **Tip**

* > You can combine the following multimodal content as needed. Note that "text + audio" and "audio\-only" inputs are not supported.

   * > Text

   * > Images: 0–9 images

   * > Videos: 0–3 videos

   * > Audio: 0–3 audio files

* > **Advanced usage**: For omni reference video generation, you can specify reference images as the first/last frame via prompts to indirectly achieve the effect of "first and last frames + omni reference". If you need to strictly ensure that the first and last frames are consistent with the specified images, please **always use image\-to\-video (first and last frame)** feature (configure the `role` parameter to `first_frame` / `last_frame`).

* > See [Omni reference input](video-generation-tutorial.md#63a97f09) for input requirements for each modality.

## Edit video

You can provide the video to be edited, reference images or audio, and use prompts together to complete various video editing tasks, such as replacing the video subject, adding, deleting and modifying objects in the video, redrawing/repairing partial frames, etc.

The following are some demos (visit the [model card](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0) to view more samples):

|Input: text |Input: video & image |Output |
|---|---|---|
|Replace the cat in [Video 1] with the lion from [Image 1]. The lion lies on its side across the girl's legs, gently interacting with her in a warm and tender way. |[video](https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/b172c1b5adf04d6d96f86d19a06bb00a)<br><br><br>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/414aa310038e4e0791412e66b5dc7223~tplv-goo7wpa0wc-image.image) |[video](https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/b0230aa7a0ba4068a03576b6a251f99f)<br> |

**Python**

```Python
import os
import time
# Install SDK:python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    #The base URL for model invocation
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.environ.get("ARK_API_KEY"),
)

if __name__ == "__main__":
    print("----- create request -----")
    create_result = client.content_generation.tasks.create(
        model="dreamina-seedance-2-0-260128", #Replace with Model ID
        content=[
            {
                "type": "text",
                "text": "Replace the cat in [Video 1] with the lion from [Image 1]. The lion lies on its side across the girl’s legs, gently interacting with her in a warm and tender way.",
            },
            {
                "type": "image_url",
                "image_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/r2v_edit_pic1.jpg"
                },
                "role": "reference_image",
            },
            {
                "type": "video_url",
                "video_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_edit_video1.mp4"
                },
                "role": "reference_video",
            },
        ],
        generate_audio=True,
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
            print(f"Current status: {status}, Retrying after 30 seconds...")
            time.sleep(30)
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

    // Client initialization
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

        // Model ID
        final String modelId = "dreamina-seedance-2-0-260128";
        // Text prompt
        final String prompt = "Replace the cat in [Video 1] with the lion from [Image 1]. The lion lies on its side across the girl’s legs, gently interacting with her in a warm and tender way.";

        // Example resource URLs
        final String refImage1 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/r2v_edit_pic1.jpg";
        final String refVideo = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_edit_video1.mp4";

        // Output video parameters
        final boolean generateAudio = true;
        final String videoRatio = "16:9";
        final long videoDuration = 5L;
        final boolean showWatermark = true;

        System.out.println("----- create request -----");
        // Build request content
        List<ContentItem> contents = new ArrayList<>();

        // 1. Text prompt
        contents.add(ContentItem.builder()
                .type(ContentType.TEXT)
                .text(prompt)
                .build());

        // 2. Reference image 1
        contents.add(ContentItem.builder()
                .type(ContentType.IMAGE_URL)
                .imageUrl(ImageURL.builder()
                        .url(refImage1)
                        .build())
                .role("reference_image")
                .build());

        // 3. Reference video
        contents.add(ContentItem.builder()
                .type(ContentType.VIDEO_URL)
                .videoUrl(VideoURL.builder()
                        .url(refVideo)
                        .build())
                .role("reference_video")
                .build());

        // Create video generation task
        CreateContentGenerationTaskRequest createRequest = CreateContentGenerationTaskRequest.builder()
                .generateAudio(generateAudio)
                .model(modelId)
                .content(contents)
                .ratio(videoRatio)
                .duration(videoDuration)
                .watermark(showWatermark)
                .build();

        CreateContentGenerationTaskResponse createResult = service.createContentGenerationTask(createRequest);
        System.out.println("Task Created: " + createResult);

        // Get task details and poll status
        String taskId = createResult.getId();
        pollTaskStatus(taskId);
    }

    /**
     * Poll task status
     *@param taskId Task ID
     */

    private static void pollTaskStatus(String taskId) {
        String getRequest = taskId;

        System.out.println("----- polling task status -----");
        try {
            while (true) {
                ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
                String status = getResponse.getStatus().toString();

                if ("succeeded".equalsIgnoreCase(status)) {
                    System.out.println("----- task succeeded -----");
                    System.out.println(getResponse);
                    break;
                } else if ("failed".equalsIgnoreCase(status)) {
                    System.out.println("----- task failed -----");
                    if (getResponse.getError() != null) {
                        System.out.println("Error: " + getResponse.getError().getMessage());
                    }
                    break;
                } else {
                    System.out.printf("Current status: %s, Retrying in 10 seconds...%n", status);
                    TimeUnit.SECONDS.sleep(10);
                }
            }
        } catch (InterruptedException ie) {
            Thread.currentThread().interrupt();
            System.err.println("Polling interrupted");
        } catch (Exception e) {
            System.err.println("Error occurred: " + e.getMessage());
        } finally {
            service.shutdownExecutor();
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
    // Initialize Ark client
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        //The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    // Model ID
    modelID := "dreamina-seedance-2-0-260128"
    // Text prompt
    prompt := "Replace the cat in [Video 1] with the lion from [Image 1]. The lion lies on its side across the girl’s legs, gently interacting with her in a warm and tender way."

    // Example resource URLs
    refImage1 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/r2v_edit_pic1.jpg"
    refVideo1 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_edit_video1.mp4"

    // Output video parameters
    generateAudio := true
    videoRatio := "16:9"
    videoDuration := int64(5)
    showWatermark := true

    // 1. Create video generation task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model:         modelID,
        GenerateAudio: model.NewOptBool(generateAudio),
        Ratio:         model.NewOptString(videoRatio),
        Duration:      model.NewOptInt64(videoDuration),
        Watermark:     model.NewOptBool(showWatermark),
        Content: []model.ContentItem{
            {
                Type: model.ContentTypeText,
                Text: model.NewOptString(prompt),
            },
            {
                Type: model.ContentTypeImageURL,
                ImageURL: model.NewOptImageURL(model.ImageURL{
                    URL: refImage1,
                }),
                Role: model.NewOptString("reference_image"),
            },
            {
                Type: model.ContentTypeVideoURL,
                VideoURL: model.NewOptVideoURL(model.VideoURL{
                    URL: refVideo1,
                }),
                Role: model.NewOptString("reference_video"),
            },
        },
    }

    createResp, err := client.CreateContentGenerationTask(ctx, createReq)
    if err != nil {
        fmt.Printf("create content generation error: %v\n", err)
        return
    }

    taskID := createResp.ID
    fmt.Printf("Task Created with ID: %s\n", taskID)

    // 2. Poll task status
    pollTaskStatus(ctx, client, taskID)
}

// poll task status
func pollTaskStatus(ctx context.Context, client *arkruntime.Client, taskID string) {
    fmt.Println("----- polling task status -----")
    for {
        getReq := taskID
        getResp, err := client.GetContentGenerationTask(ctx, getReq)
        if err != nil {
            fmt.Printf("get content generation task error: %v\n", err)
            return
        }

        status := getResp.Status
        if status == "succeeded" {
            fmt.Println("----- task succeeded -----")
            fmt.Printf("Task ID: %s \n", getResp.ID)
            fmt.Printf("Model: %s \n", getResp.Model)
            fmt.Printf("Video URL: %s \n", getResp.Content.Or(model.TaskContent{}).VideoURL.Or(""))
            fmt.Printf("Completion Tokens: %d \n", getResp.Usage.Or(model.TaskUsage{}).CompletionTokens)
            fmt.Printf("Created At: %d, Updated At: %d\n", getResp.CreatedAt.Or(0), getResp.UpdatedAt.Or(0))
            return
        } else if status == "failed" {
            fmt.Println("----- task failed -----")
            if getResp.Error.IsSet() {
                fmt.Printf("Error Code: %s, Message: %s\n", getResp.Error.Value.Code, getResp.Error.Value.Message)
            }
            return
        } else {
            fmt.Printf("Current status: %s, Retrying in 10 seconds... \n", status)
            time.Sleep(10 * time.Second)
        }
    }
}
```

## Extend video

Based on the original video, you can extend the video forward or backward, or stitch multiple video clips (up to 3 clips) into a coherent video.

The following are some demos (visit the [model card](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0) to view more samples):

|Input: text |Input: video to be extended |Output |
|---|---|---|
|The arched window in [Video 1] opens, and the camera moves into the interior of the art museum, transitioning into [Video 2]. After that, the camera enters the painting itself, transitioning into [Video 3]. |[video](https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/54519ff7266d4f1caa12b8cc95e2dd1d)<br><br><br>[video](https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/b15d56c80c884faa8526beb6ca540b98)<br><br><br>[video](https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/f5d327311e094361b15dca0a37b14ab4)<br> |[video](https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/849b3f86f609495ca09d559aa14c79ed)<br> |

**Python**

```Python
import os
import time
# Install SDK:python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    #The base URL for model invocation
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.environ.get("ARK_API_KEY"),
)

if __name__ == "__main__":
    print("----- create request -----")
    create_result = client.content_generation.tasks.create(
        model="dreamina-seedance-2-0-260128", #Replace with Model ID
        content=[
            {
                "type": "text",
                "text": "The arched window in [Video 1] opens, and the camera moves into the interior of the art museum, transitioning into [Video 2]. After that, the camera enters the painting itself, transitioning into [Video 3].",

            },
            {
                "type": "video_url",
                "video_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video1.mp4"
                },
                "role": "reference_video",
            },
            {
                "type": "video_url",
                "video_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video2.mp4"
                },
                "role": "reference_video",
            },
            {
                "type": "video_url",
                "video_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video3.mp4"
                },
                "role": "reference_video",
            },
        ],
        generate_audio=True,
        ratio="16:9",
        duration=8,
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
            print(f"Current status: {status}, Retrying after 30 seconds...")
            time.sleep(30)
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

    // Client initialization
    static String apiKey = System.getenv("ARK_API_KEY");
    static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
    static Dispatcher dispatcher = new Dispatcher();
    static ArkService service = ArkService.builder()
           .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3") //The base URL for model invocation
           .dispatcher(dispatcher)
           .connectionPool(connectionPool)
           .apiKey(apiKey)
           .build();

    public static void main(String[] args) {

        // Model ID
        final String modelId = "dreamina-seedance-2-0-260128";
        // Text prompt
        final String prompt = "The arched window in [Video 1] opens, and the camera moves into the interior of the art museum, transitioning into [Video 2]. After that, the camera enters the painting itself, transitioning into [Video 3].";

        // Example resource URLs
        final String refVideo1 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video1.mp4";
        final String refVideo2 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video2.mp4";
        final String refVideo3 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video3.mp4";

        // Output video parameters
        final boolean generateAudio = true;
        final String videoRatio = "16:9";
        final long videoDuration = 8L;
        final boolean showWatermark = true;

        System.out.println("----- create request -----");
        // Build request content
        List<ContentItem> contents = new ArrayList<>();

        // 1. Text prompt
        contents.add(ContentItem.builder()
                .type(ContentType.TEXT)
                .text(prompt)
                .build());

        // 2. Reference video 1
        contents.add(ContentItem.builder()
                .type(ContentType.VIDEO_URL)
                .videoUrl(VideoURL.builder()
                        .url(refVideo1)
                        .build())
                .role("reference_video")
                .build());

        // 3. Reference video 2
        contents.add(ContentItem.builder()
                .type(ContentType.VIDEO_URL)
                .videoUrl(VideoURL.builder()
                        .url(refVideo2)
                        .build())
                .role("reference_video")
                .build());

        // 4. Reference video 3
        contents.add(ContentItem.builder()
                .type(ContentType.VIDEO_URL)
                .videoUrl(VideoURL.builder()
                        .url(refVideo3)
                        .build())
                .role("reference_video")
                .build());

        // Create video generation task
        CreateContentGenerationTaskRequest createRequest = CreateContentGenerationTaskRequest.builder()
                .generateAudio(generateAudio)
                .model(modelId)
                .content(contents)
                .ratio(videoRatio)
                .duration(videoDuration)
                .watermark(showWatermark)
                .build();

        CreateContentGenerationTaskResponse createResult = service.createContentGenerationTask(createRequest);
        System.out.println("Task Created: " + createResult);

        // Get task details and poll status
        String taskId = createResult.getId();
        pollTaskStatus(taskId);
    }

    /**
     * Poll task status
     *@param taskId Task ID
     */

    private static void pollTaskStatus(String taskId) {
        String getRequest = taskId;

        System.out.println("----- polling task status -----");
        try {
            while (true) {
                ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
                String status = getResponse.getStatus().toString();

                if ("succeeded".equalsIgnoreCase(status)) {
                    System.out.println("----- task succeeded -----");
                    System.out.println(getResponse);
                    break;
                } else if ("failed".equalsIgnoreCase(status)) {
                    System.out.println("----- task failed -----");
                    if (getResponse.getError() != null) {
                        System.out.println("Error: " + getResponse.getError().getMessage());
                    }
                    break;
                } else {
                    System.out.printf("Current status: %s, Retrying in 10 seconds...%n", status);
                    TimeUnit.SECONDS.sleep(10);
                }
            }
        } catch (InterruptedException ie) {
            Thread.currentThread().interrupt();
            System.err.println("Polling interrupted");
        } catch (Exception e) {
            System.err.println("Error occurred: " + e.getMessage());
        } finally {
            service.shutdownExecutor();
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
    // Initialize Ark client
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        //The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    // Model ID
    modelID := "dreamina-seedance-2-0-260128"
    // Text prompt
    prompt := "The arched window in [Video 1] opens, and the camera moves into the interior of the art museum, transitioning into [Video 2]. After that, the camera enters the painting itself, transitioning into [Video 3]."

    // Example resource URLs
    refVideo1 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video1.mp4"
    refVideo2 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video2.mp4"
    refVideo3 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video3.mp4"

    // Output video parameters
    generateAudio := true
    videoRatio := "16:9"
    videoDuration := int64(8)
    showWatermark := true

    // 1. Create video generation task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model:         modelID,
        GenerateAudio: model.NewOptBool(generateAudio),
        Ratio:         model.NewOptString(videoRatio),
        Duration:      model.NewOptInt64(videoDuration),
        Watermark:     model.NewOptBool(showWatermark),
        Content: []model.ContentItem{
            {
                Type: model.ContentTypeText,
                Text: model.NewOptString(prompt),
            },
            {
                Type: model.ContentTypeVideoURL,
                VideoURL: model.NewOptVideoURL(model.VideoURL{
                    URL: refVideo1,
                }),
                Role: model.NewOptString("reference_video"),
            },
            {
                Type: model.ContentTypeVideoURL,
                VideoURL: model.NewOptVideoURL(model.VideoURL{
                    URL: refVideo2,
                }),
                Role: model.NewOptString("reference_video"),
            },
            {
                Type: model.ContentTypeVideoURL,
                VideoURL: model.NewOptVideoURL(model.VideoURL{
                    URL: refVideo3,
                }),
                Role: model.NewOptString("reference_video"),
            },
        },
    }

    createResp, err := client.CreateContentGenerationTask(ctx, createReq)
    if err != nil {
        fmt.Printf("create content generation error: %v\n", err)
        return
    }

    taskID := createResp.ID
    fmt.Printf("Task Created with ID: %s\n", taskID)

    // 2. Poll task status
    pollTaskStatus(ctx, client, taskID)
}

// poll task status
func pollTaskStatus(ctx context.Context, client *arkruntime.Client, taskID string) {
    fmt.Println("----- polling task status -----")
    for {
        getReq := taskID
        getResp, err := client.GetContentGenerationTask(ctx, getReq)
        if err != nil {
            fmt.Printf("get content generation task error: %v\n", err)
            return
        }

        status := getResp.Status
        if status == "succeeded" {
            fmt.Println("----- task succeeded -----")
            fmt.Printf("Task ID: %s \n", getResp.ID)
            fmt.Printf("Model: %s \n", getResp.Model)
            fmt.Printf("Video URL: %s \n", getResp.Content.Or(model.TaskContent{}).VideoURL.Or(""))
            fmt.Printf("Completion Tokens: %d \n", getResp.Usage.Or(model.TaskUsage{}).CompletionTokens)
            fmt.Printf("Created At: %d, Updated At: %d\n", getResp.CreatedAt.Or(0), getResp.UpdatedAt.Or(0))
            return
        } else if status == "failed" {
            fmt.Println("----- task failed -----")
            if getResp.Error.IsSet() {
                fmt.Printf("Error Code: %s, Message: %s\n", getResp.Error.Value.Code, getResp.Error.Value.Message)
            }
            return
        } else {
            fmt.Printf("Current status: %s, Retrying in 10 seconds... \n", status)
            time.Sleep(10 * time.Second)
        }
    }
}
```

> **Tip**

* > When you extend a video clip forward or backward, the generated video usually only includes the tail footage of the original video. But you can also flexibly control it via a prompt to make it include the original video content. For example: Extend Video 1 backward, [description of the extended content...], and **then end with Video 1**.

* > When you pass 2 to 3 video clips to fill in the intermediate transition part, the generated video will include both the original video content and the newly generated video content.

## Output 4K videos

> Supported only by Seedance 2.0

Seedance 2.0 supports 4K video output and uses 10\-bit encoding, preserving rich color layers and smooth gradient transitions. It meets the requirements of professional film production and HDR video content.

> **Note**

> 4K videos are output in H.265/HEVC encoding format. Some players or browsers may not support direct playback. For details, see [Playback compatibility for videos with 10\-bit color depth and H.265/HEVC encoding](seedance-2-0.md#4k_player).

|Preview 1 |Preview 2 |
|---|---|
|[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance-4k-makeup-music.mov)<br> |[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance-4k-motorcycle-music.mov)<br> |
|Note: The preview videos are stitched from multiple shots generated by Seedance 2.0, and are not directly generated by the following sample code. | |

**Python**

```Python
import os
import time
# Install SDK:  python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # The base URL for model invocation
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    # Get API Key：https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.environ.get("ARK_API_KEY"),
)

if __name__ == "__main__":
    print("----- create request -----")
    create_result = client.content_generation.tasks.create(
        model="dreamina-seedance-2-0-260128",
        content=[
            {
                "type": "text",
                "text": "Generate a 15-second off-road motorcycle racing commercial-style short film. Use the reference image as the reference for the mid-sequence jump climax. The shot sequence should be: 1) medium tracking shot: the rider approaches the ramp at high speed from a distance along the dirt slope; 2) extreme close-up from a low angle of the rear wheel kicking up sand, with the tire gripping the ground and throwing up a large amount of dirt and gravel; 3) medium-close shot showing the rider controlling the bike, hand force, suspension compression, and mechanical vibration; 4) side hero medium shot of the rider rushing up the slope and jumping into the air, with the image state close to Image 1 and dirt scattering widely in the backlight; 5) stylish airborne close-up details, highlighting the helmet goggles, hand control on the handlebar, suspended tires, or partial side view of the bike body; 6) medium tracking shot of the landing, suspension compressing and rebounding, then continuing to sprint at high speed along the dirt track to finish. Keep the same rider, same bike, and same track throughout. Make the shot sizes and angles clearly distinct, avoid repetition, keep the action continuous, and create a realistic off-road tracking-shot feel with camera shake, speed, flying dirt, and a sunset backlit racing atmosphere.",
            },
            {
                "type": "image_url",
                "image_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_4k.png"
                },
                "role": "reference_image",
            },
        ],
        generate_audio=True,
        resolution="4k",
        ratio="adaptive",
        duration=15,
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
            print(f"Current status: {status}, Retrying after 30 seconds...")
            time.sleep(30)
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

    // Client initialization
    static String apiKey = System.getenv("ARK_API_KEY");
    static ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
    static Dispatcher dispatcher = new Dispatcher();
    static ArkService service = ArkService.builder()
           .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
           // The base URL for model invocation
           .dispatcher(dispatcher)
           .connectionPool(connectionPool)
           .apiKey(apiKey)
           .build();

    public static void main(String[] args) {

        // Model ID
        final String modelId = "dreamina-seedance-2-0-260128";
        // Text prompt
        final String prompt = "Generate a 15-second off-road motorcycle racing commercial-style short film. Use the reference image as the reference for the mid-sequence jump climax. " +
                "The shot sequence should be: 1) medium tracking shot: the rider approaches the ramp at high speed from a distance along the dirt slope; " +
                "2) extreme close-up from a low angle of the rear wheel kicking up sand, with the tire gripping the ground and throwing up a large amount of dirt and gravel; " +
                "3) medium-close shot showing the rider controlling the bike, hand force, suspension compression, and mechanical vibration; " +
                "4) side hero medium shot of the rider rushing up the slope and jumping into the air, with the image state close to Image 1 and dirt scattering widely in the backlight; " +
                "5) stylish airborne close-up details, highlighting the helmet goggles, hand control on the handlebar, suspended tires, or partial side view of the bike body; " +
                "6) medium tracking shot of the landing, suspension compressing and rebounding, then continuing to sprint at high speed along the dirt track to finish. " +
                "Keep the same rider, same bike, and same track throughout. Make the shot sizes and angles clearly distinct, avoid repetition, keep the action continuous, " +
                "and create a realistic off-road tracking-shot feel with camera shake, speed, flying dirt, and a sunset backlit racing atmosphere.";

        // Example resource URLs
        final String refImage = "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_4k.png";

        // Output video parameters
        final boolean generateAudio = true;
        final String videoResolution = "4k";
        final String videoRatio = "adaptive";
        final long videoDuration = 15L;
        final boolean showWatermark = true;

        System.out.println("----- create request -----");
        // Build request content
        List<ContentItem> contents = new ArrayList<>();

        // 1. Text prompt
        contents.add(ContentItem.builder()
                .type(ContentType.TEXT)
                .text(prompt)
                .build());

        // 2. Reference image
        contents.add(ContentItem.builder()
                .type(ContentType.IMAGE_URL)
                .imageUrl(ImageURL.builder()
                        .url(refImage)
                        .build())
                .role("reference_image")
                .build());

        // Create video generation task
        CreateContentGenerationTaskRequest createRequest = CreateContentGenerationTaskRequest.builder()
                .generateAudio(generateAudio)
                .model(modelId)
                .content(contents)
                .resolution(videoResolution)
                .ratio(videoRatio)
                .duration(videoDuration)
                .watermark(showWatermark)
                .build();

        CreateContentGenerationTaskResponse createResult = service.createContentGenerationTask(createRequest);
        System.out.println("Task Created: " + createResult);

        // Get task details and poll status
        String taskId = createResult.getId();
        pollTaskStatus(taskId);
    }

    /**
     * Poll task status
     * @param taskId Task ID
     */

    private static void pollTaskStatus(String taskId) {
        String getRequest = taskId;

        System.out.println("----- polling task status -----");
        try {
            while (true) {
                ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
                String status = getResponse.getStatus().toString();

                if ("succeeded".equalsIgnoreCase(status)) {
                    System.out.println("----- task succeeded -----");
                    System.out.println(getResponse);
                    break;
                } else if ("failed".equalsIgnoreCase(status)) {
                    System.out.println("----- task failed -----");
                    if (getResponse.getError() != null) {
                        System.out.println("Error: " + getResponse.getError().getMessage());
                    }
                    break;
                } else {
                    System.out.printf("Current status: %s, Retrying in 10 seconds...%n", status);
                    TimeUnit.SECONDS.sleep(10);
                }
            }
        } catch (InterruptedException ie) {
            Thread.currentThread().interrupt();
            System.err.println("Polling interrupted");
        } catch (Exception e) {
            System.err.println("Error occurred: " + e.getMessage());
        } finally {
            service.shutdownExecutor();
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
    // Initialize Ark client
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        // The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    // Model ID
    modelID := "dreamina-seedance-2-0-260128"
    // Text prompt
    prompt := "Generate a 15-second off-road motorcycle racing commercial-style short film. Use the reference image as the reference for the mid-sequence jump climax. " +
        "The shot sequence should be: 1) medium tracking shot: the rider approaches the ramp at high speed from a distance along the dirt slope; " +
        "2) extreme close-up from a low angle of the rear wheel kicking up sand, with the tire gripping the ground and throwing up a large amount of dirt and gravel; " +
        "3) medium-close shot showing the rider controlling the bike, hand force, suspension compression, and mechanical vibration; " +
        "4) side hero medium shot of the rider rushing up the slope and jumping into the air, with the image state close to Image 1 and dirt scattering widely in the backlight; " +
        "5) stylish airborne close-up details, highlighting the helmet goggles, hand control on the handlebar, suspended tires, or partial side view of the bike body; " +
        "6) medium tracking shot of the landing, suspension compressing and rebounding, then continuing to sprint at high speed along the dirt track to finish. " +
        "Keep the same rider, same bike, and same track throughout. Make the shot sizes and angles clearly distinct, avoid repetition, keep the action continuous, " +
        "and create a realistic off-road tracking-shot feel with camera shake, speed, flying dirt, and a sunset backlit racing atmosphere."

    // Example resource URLs
    refImage := "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_4k.png"

    // Output video parameters
    generateAudio := true
    videoResolution := "4k"
    videoRatio := "adaptive"
    videoDuration := int64(15)
    showWatermark := true

    // 1. Create video generation task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model:         modelID,
        GenerateAudio: model.NewOptBool(generateAudio),
        Resolution:    model.NewOptString(videoResolution),
        Ratio:         model.NewOptString(videoRatio),
        Duration:      model.NewOptInt64(videoDuration),
        Watermark:     model.NewOptBool(showWatermark),
        Content: []model.ContentItem{
            {
                Type: model.ContentTypeText,
                Text: model.NewOptString(prompt),
            },
            {
                Type: model.ContentTypeImageURL,
                ImageURL: model.NewOptImageURL(model.ImageURL{
                    URL: refImage,
                }),
                Role: model.NewOptString("reference_image"),
            },
        },
    }

    createResp, err := client.CreateContentGenerationTask(ctx, createReq)
    if err != nil {
        fmt.Printf("create content generation error: %v\n", err)
        return
    }

    taskID := createResp.ID
    fmt.Printf("Task Created with ID: %s\n", taskID)

    // 2. Poll task status
    pollTaskStatus(ctx, client, taskID)
}

// poll task status
func pollTaskStatus(ctx context.Context, client *arkruntime.Client, taskID string) {
    fmt.Println("----- polling task status -----")
    for {
        getReq := taskID
        getResp, err := client.GetContentGenerationTask(ctx, getReq)
        if err != nil {
            fmt.Printf("get content generation task error: %v\n", err)
            return
        }

        status := getResp.Status
        if status == "succeeded" {
            fmt.Println("----- task succeeded -----")
            fmt.Printf("Task ID: %s \n", getResp.ID)
            fmt.Printf("Model: %s \n", getResp.Model)
            fmt.Printf("Video URL: %s \n", getResp.Content.Or(model.TaskContent{}).VideoURL.Or(""))
            fmt.Printf("Completion Tokens: %d \n", getResp.Usage.Or(model.TaskUsage{}).CompletionTokens)
            fmt.Printf("Created At: %d, Updated At: %d\n", getResp.CreatedAt.Or(0), getResp.UpdatedAt.Or(0))
            return
        } else if status == "failed" {
            fmt.Println("----- task failed -----")
            if getResp.Error.IsSet() {
                fmt.Printf("Error Code: %s, Message: %s\n", getResp.Error.Value.Code, getResp.Error.Value.Message)
            }
            return
        } else {
            fmt.Printf("Current status: %s, Retrying in 10 seconds... \n", status)
            time.Sleep(10 * time.Second)
        }
    }
}
```

## More capabilities

Seedance 2.0 series models also support common basic capabilities such as text\-to\-video, first\-frame image\-to\-video, first\-and\-last\-frame image\-to\-video, and video output specification configuration. For details, see [Video generation tutorial](video-generation-tutorial.md).

# Create portrait videos

Seedance 2.0 series models do not support direct uploads of reference images or videos containing real human faces. ModelArk provides the following solutions to help you create videos with portrait assets. For detailed instructions, see [Create portrait videos with Dreamina Seedance models](seedance-portrait-asset-guide.md).

|Solution |Overview |
|---|---|
|[Use trusted model outputs as input assets](seedance-portrait-asset-guide.md#trust-model-output) |Original face\-containing outputs generated by some models under your account can be used as input assets of Seedance 2.0 series models without being intercepted by input moderation. |
|[Use preset digital characters](seedance-portrait-asset-guide.md#preset-avatar) |ModelArk has a pre\-built digital character library that provides creatives with free, compliant, and diverse portrait assets. It is suitable for scenarios that require realistic but not specific human faces, and pursue zero compliance risk and fast creation. |
|[Use authorized real-person assets](seedance-portrait-asset-guide.md#authorized-real-person) |Supports video generation using authorized real portrait assets. |

# Prompt engineering techniques

Prompts must reference assets in the format **asset type + number**, where the number is the sorting order of the asset among assets of the same type in the request body. For example, "Image n" refers to the nth reference image with `type="image_url"` in the `content` array (counting starts from 1 in array order). **Note that referencing assets by Asset ID is not supported.**

The following section describes typical prompt formulas for omni reference, video editing, and video extension. For more details, see [Dreamina Seedance 2.0 series prompt guide](seedance-2-0-prompt-guide.md).

> **Tip**

> ModelArk provides the **Seedance 2.0 prompt optimization skill** to help you tune your prompts.

* > How to install: Run the following command in your local project to install `sd2-pe` from the ModelArk documentation Skills catalog:

   <div data-tips="true" data-tips-type="tip">   ```bash
   npx --yes skills@latest add "https://arkdocs-en.tos-ap-southeast-1.volces.com/skills/" \
     --skill sd2-pe \
     --yes
   ```
      </div>
   

   <div data-tips="true" data-tips-type="tip">To install the Skill manually, download the <a href="https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/1a98a5a8685547568ed9ef257ceabe85~tplv-goo7wpa0wc-image.image">SKILL.md</a> file. In OpenClaw, for example, paste the complete file contents into the chat box, enter "Please install this skill", and wait for the installation to finish.   </div>
   

* <div data-tips="true" data-tips-type="tip">How to use: Enter <code>/sd2-pe + your prompt content</code> in the AI dialog box to start debugging prompts.</div>



<span id="b34e43cc"></span>
## **Omni reference**


* Image reference: Reference / extract / combine + "subject / referenced element description" from "Image n" to generate "plot description", keeping the characteristics of "subject / referenced element description" consistent.

* Video reference: Reference "action description / camera movement description / special effect description" from "Video n" to generate "plot description", keeping the action details / camera movement / special effects consistent.

* Audio reference:

   * Voice timbre reference: "Character" says: "lines", voice timbre references "Audio n".

   * Audio content reference: Ideal appearance timing + "Audio n".


<span id="bd0f005e"></span>
## **Edit video**


* Add elements: Clearly describe "element characteristics" + "appearance timing" + "appearance position".

* Delete elements: Specify the elements to delete, and emphasize the elements that remain unchanged in the prompt for better results.

* Modify elements: Simply clearly describe the elements to be replaced.


<span id="d7a8f9f2"></span>
## **Extend video**


* Extend video: Extend "Video n" forward/backward + "description of the video to be extended".

* Track completion: "Video 1" + "transition description" + connect to "Video 2" + "transition description" + connect to "Video 3".


<span id="66cb028f"></span>
# Limitations

See [Limitations](video-generation-tutorial.md#66cb028f).

<span id="d21b3c92"></span>
# FAQs

<span id="4k_player"></span>
## Playback compatibility for videos with 10\-bit color depth and H.265/HEVC encoding

The following compatibility test results cover playback of videos with 10\-bit color depth and H.265/HEVC encoding in browsers and media players on different platforms. Actual performance may vary depending on device configuration.

**Recommended options:** 


* **macOS**: Recommended browsers are Safari and Chrome; recommended media players are VLC, mpv, and QuickTime Player

* **Windows**: Recommended browsers are Edge and Chrome; recommended media players are VLC and mpv


<span id="windows"></span>
### Windows


<Tabs>
<Tab zoneid="HEA6JT3Sx7" title="Browser">
<TabTitle>Browser</TabTitle>


|Browser |Support |
|---|---|
|Chrome |Conditional |
|Edge |Conditional |
|Firefox |Conditional |
|Opera |Conditional |



</Tab>
<Tab zoneid="OgLJ1wlecn" title="Player">
<TabTitle>Player</TabTitle>


|Player |Support |
|---|---|
|VLC |Supported |
|Movies & TV |Conditional |
|PotPlayer |Conditional |
|MPC\-HC / MPC\-BE |Conditional |
|mpv |Supported |
|KMPlayer |Supported |



</Tab>
</Tabs>


> **Conditional support**: Requires relatively strong hardware decoding capability. Playback is known to work on Intel i7 + NVIDIA RTX 4070 + Windows 11 or higher configurations. For other configurations, verify by actual testing.


<span id="macos"></span>
### macOS


<Tabs>
<Tab zoneid="jeNdtXkIOn" title="Browser">
<TabTitle>Browser</TabTitle>


|Browser |Support |
|---|---|
|Safari |Supported |
|Chrome |Conditional |
|Edge |Conditional |
|Firefox |Conditional |
|Opera |Conditional |



</Tab>
<Tab zoneid="wOqKgdHgHj" title="Player">
<TabTitle>Player</TabTitle>


|Player |Support |
|---|---|
|VLC |Supported |
|QuickTime Player |Supported |
|IINA |Supported |
|mpv |Supported |
|Infuse |Supported |
|Kodi |Conditional |



</Tab>
</Tabs>


> **Conditional support**: Requires relatively strong hardware decoding capability. Playback is known to work on Apple M2 and higher devices. For M1 and lower devices, verify by actual testing.


<span id="1df655fb"></span>
## Video frames contain abrupt jumps

**Typical Symptoms**

In **first\-frame image\-to\-video** and **first\-and\-last\-frame image\-to\-video** scenarios, some frames in the generated video exhibit abrupt changes such as image stretching and compression.

**Root Cause**

The resolution width and height of the input image and output video are inconsistent, causing frame\-to\-frame jumps in the video image.

**Solution**


1. Crop the input image: Refer to the table of supported width and height pixel values for the Seedance 2.0 series models (see the **ratio** field in [Create Video Generation Task API](create-video-generation-task-api.md)), and crop the input image to the target width and height pixel values.

2. Set the **ratio** field to `adaptive`.

3. Use the Seedance 2.0 series model to initiate the first\-frame/first\-and\-last\-frame image\-to\-video task again.
