# Dreamina Seedance 2.5 tutorial

Dreamina Seedance 2.5 (hereinafter referred to as Seedance 2.5) is a next\-generation video creation model with major improvements in long\-form storytelling, omni reference support, and editing. It can generate up to 30 seconds of video in one request, extend videos over multiple rounds, accept up to 50 omni reference assets per request, and perform precise timestamp\-based editing. Improved visual quality produces results with a more realistic, cinematic look.

> **Model availability**

> Dreamina Seedance 2.5 flash is now available. For details, see the [Model details](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?name=dreamina-seedance-2-5-flash) page. Starting at 14:00 (UTC+8) on November 15, 2026, the model will be available for online experience and API calls.

This tutorial introduces the key capabilities and usage of Seedance 2.5 to help you call the [Create video generation task API](create-video-generation-task-api.md).

> **tip**

> Before enabling Dreamina Seedance 2.5, make sure you meet one of the following conditions:

* > **Recommended:** BytePlus account balance \> USD 30 ([Top up](https://console.byteplus.com/finance/overview)).

* > **Recommended:** Purchase a dedicated AI Savings Plan at the USD 30 tier or above. Purchase entry: [AI Savings Plan](https://console.byteplus.com/common-buy/AI-SavingsPlans%7C%7Cd9urs77og65q382arfog).

* > You have purchased a Dreamina Seedance 2.5 series resource pack with available quota ([Purchase a resource pack](https://console.byteplus.com/common-buy/ModelArk%7C%7Cd7d6aanpgiftptb9ajcg)).

> For detailed rules, see [Activate, use, and cancel Dreamina Seedance 2.5 and 2.0 series models](seedance-model-activation-usage-and-refund.md).

## Read before use

> **danger**

> Seedance 2.5 has special constraints for **video editing, first\-frame/first\-last\-frame video generation, and video extension tasks**. Violating these constraints triggers an [error](seedance-2-5.md#2.5_error_handling).

> **Before calling the model, read the following carefully and make sure the task type, prompt intent, and parameter configuration are consistent.**

### Task types and constraints

Seedance 2.5 determines the task type based on the input assets and prompt intent. Task types include text\-to\-video, first\-frame/first\-last\-frame video generation, and **omni reference\-to\-video tasks**. Omni reference\-to\-video tasks are further divided into three subtasks based on prompt intent: reference\-to\-video, video editing, and video extension.

|Task type ||Trigger condition |Special constraints |
|---|---|---|---|
|Text\-to\-video ||Only a text prompt is provided. |No special constraints on `ratio` or `duration`. |
|First\-frame/first\-last\-frame video generation ||`content.role` is set to `first_frame` or `last_frame`. |`ratio` must be `adaptive`.<br><br>> The model automatically keeps the output video aspect ratio consistent with the first\-frame image specified by `first_frame`. |
|Omni reference video generation |Reference\-to\-video |`content` contains at least one reference asset whose `role` is `reference_image`, `reference_video`, or `reference_audio`. |No special constraints on `ratio` or `duration`. |
||Video editing |`content` contains at least one reference video whose `role` is `reference_video`, and the prompt expresses an editing intent. |* `ratio` must be `adaptive`, and `duration` must be `-1`.<br><br>> The model selects the video to edit based on the prompt intent and automatically keeps the output video aspect ratio and duration consistent with the selected video.<br><br><br>\* The reference video must be 4–30 seconds long. |
||Video extension |`content` contains at least one reference video whose `role` is `reference_video`, and the prompt expresses an extension intent. |`ratio` must be `adaptive`.<br><br>> The model selects the video to extend based on the prompt intent and automatically keeps the output video aspect ratio consistent with the selected video. |

> **Note**

> To reduce asynchronous errors caused by special constraints, omni reference\-to\-video tasks can use `omni_reference_task_type` to explicitly guide the task type and move validation earlier. For details, see the configuration methods below.

### Configuration methods

#### Omni reference\-to\-video without specifying a subtask

For an omni reference\-to\-video task, if you do not explicitly distinguish the specific subtask type, use the following recommended parameter configuration:

1. Set `omni_reference_task_type` to `auto`.

2. Set `content.role` to `reference_image`, `reference_video`, or `reference_audio`.

3. We recommend setting `ratio` to `adaptive` and `duration` to `-1`. If you provide reference videos, we recommend that each video be 4–30 seconds long.

> **Note**

> When `omni_reference_task_type` is set to `auto`, the model may classify the task as reference\-to\-video, video editing, or video extension based on the prompt intent. Video editing and video extension have special parameter constraints. **Using the recommended configuration satisfies these constraints and reduces**[**asynchronous errors**](seedance-2-5.md#2.5_error_handling)**after task creation.**

#### Video editing

> Edit the visuals or audio of an original video, such as replacing the video subject, adding or removing objects in the video, or redrawing or repairing part of the frame.

For a clear video editing task, use the following **configuration method**:

1. Set `omni_reference_task_type` to `edit`.

2. Include at least one reference video whose `role` is `reference_video` in `content`.

3. `ratio` must be `adaptive`; `duration` must be `-1`; the reference video must be 4–30 seconds long.

4. The prompt must include at least one keyword or phrase such as edit the video, add, delete/remove, or modify/replace/change.

> Prompt examples: Add some little animals to @Video 1; replace the character in @Video 1 with the character in @Image 1; remove the background music from @Video 1.

#### Video extension

> Extend an original video forward or backward.

For a clear video extension task, use the following **configuration method**:

1. Set `omni_reference_task_type` to `extend`.

2. Include at least one reference video whose `role` is `reference_video` in `content`.

3. `ratio` must be `adaptive`.

4. The prompt must include at least one keyword or phrase such as extend forward/backward, continue, or continue the story.

> Prompt examples: Extend @Video 1 backward, and have the character in @Image 1 descend from the sky; continue the 5 seconds before @Video 1, with the woman in @Video 2 entering the frame and speaking.

#### First\-frame/first\-last\-frame video generation

> Input one image as the first frame, or two images as the first and last frames, to generate a video.

1. `content` must contain an image whose `role` is `first_frame`. First\-last\-frame video generation must also contain an image whose `role` is `last_frame`.

2. `ratio` must be `adaptive`.

### Error handling

Seedance 2.5 validates parameters when a task is submitted and again after the task starts.

#### Synchronous validation (when submitting a task)

* For omni reference\-to\-video tasks, when `omni_reference_task_type` is explicitly set to `edit` or `extend`, the API validates the special parameter constraints earlier. If the request does not meet the requirements, the API immediately returns an error.

> **Note**

> When actually processing the task, the model still further determines the task type based on the prompt. If the actual task type identified by the model does not match the specified value, the task still triggers an asynchronous error ([error code:](error-codes.md)[`InvalidParameter.TaskTypeMismatch`](error-codes.md)). Follow the prompting guidance for each task type to reduce the chance of errors.

#### Asynchronous validation (after a task starts)

* For omni reference\-to\-video tasks, when `omni_reference_task_type` is omitted or set to `auto`, the model first determines the actual task type from the input assets and prompt, and then validates the special parameter constraints for that task type. If the request does not meet the requirements, the task returns an error asynchronously.

* For first\-frame/first\-last\-frame video generation, if the parameter configuration does not meet the special constraints for the task, the task returns an error asynchronously.

> **Note**

> Asynchronous errors caused by incompatibility between parameter configuration and task type return the error code [`InvalidParameter.TaskTypeConstraint`](error-codes.md).

## Getting started

> **tip**

* > If you have no programming experience, use the [ModelArk Console](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/gen_video?model=dreamina-seedance-2-5-260628), which provides templates for creating videos without writing code.

* > If you want to quickly try API calls, we recommend using [API Explorer](https://api.byteplus.com/api-explorer/?action=CreateContentsGenerationsTasks&groupName=Chat%20API&serviceCode=ark&version=2024-01-01), which has built\-in preset parameter templates and can initiate API calls with one click. It also supports flexible parameter adjustments (such as setting video watermarks) to meet diverse testing and usage scenarios.

* > If you need to start developing but are struggling with setting up the environment, installing dependencies, and other issues, we recommend reading this section.

This tutorial is designed for **users new to APIs**. It helps you set up a Python development environment, create a virtual environment, install the ModelArk SDK, and run Seedance 2.5 sample code. Replace the input assets to start generating videos.

**1. Preparation**

Before you begin, make sure you have completed the following preparations:

1. **Register an account**: Make sure you have a BytePlus account and are [signed in](https://ai.byteplus.com/ark/region:ap-southeast-1/overview).

2. **Get an API key**: Go to the [API Key management page](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey), click **Create API Key**, and copy and save your API key. Be sure to keep your API key safe and do not disclose it to others.

3. [Activate the model](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=model&projectName=default&tab=ComputerVision): Make sure you have [purchased a resource pack](https://console.byteplus.com/common-buy/ModelArk%7C%7Cd7d6aanpgiftptb9ajcg). Otherwise, you cannot activate Seedance 2.5.

4. **Download and extract the files**: Click [here](https://arkdocs-en.tos-ap-southeast-1.volces.com/files/video-generation/modelark_seedance2.5_quickstart_package_arkruntime-20260909.zip) to download the attachment. Extract it to your local directory, such as the Desktop or Downloads folder.

**2. Procedure**

**Windows users**

1. Enter the `scripts/init_dev_env` directory.

2. Double\-click to run `setup_windows.bat`.

3. The script will automatically perform the following operations:

* Download the uv tool.

* Automatically download Python 3.12 (if it does not interfere with your system Python).

* Create the virtual environment `.venv`.

* Install the ModelArk SDK.

4. After completion, a `run_demo.bat` will be generated in the project root directory.

5. Double\-click `run_demo.bat` to run the Python SDK sample code (python/demo_standard.py).

**macOS users**

1. Open a terminal and enter the `scripts/init_dev_env` directory.

2. Run the build script:

```BASH
./setup_mac.sh
```

3. The script will automatically configure the entire environment.

4. After completion, a `run_demo.sh` will be generated in the project root directory.

5. Run `./run_demo.sh` to run the Python SDK sample code (python/demo_standard.py).

**3. Usage instructions**

After running the script, you will see the following process:

1. **API Key verification**: The script will automatically check whether the `ARK_API_KEY` environment variable is configured locally. If not, it will prompt you to enter it manually.

2. **Asset preview**: The script will automatically open a locally generated HTML page in your default browser, visually displaying the text prompt for this task, the reference image to be replaced, and the original reference video.

3. **Task creation and polling**: The script sends an asynchronous request to the ModelArk server. Because video generation takes some time, the console will print the task status every 30 seconds (such as `running`, etc.).

4. **Get the result**: After the task succeeds, the console will output the final generated video URL. You can copy the link to a browser to download or play it online.

**4. Next steps**

After successfully running this example, modify `python/demo_standard.py` to create your own video generation task:

1. Modify the text prompt

Find the `user_content` variable in the code and change it to the image description you want.

2. Replace input assets (image, video, audio)

You can replace `reference_image_url`, `reference_video_url`, and `reference_audio_url` with your own asset links.

**Note**: Make sure the URL is publicly accessible. We recommend storing it in BytePlus TOS and configuring it for public read. For details, see [Data subscription](https://docs.byteplus.com/en/docs/tos/Data_subscription).

3. Continue to explore the following examples.

## Latest capabilities

**Card**

[Double the generated video duration](seedance-2-5.md#2.5_30s_video)

* The maximum generated video duration is extended from 15 seconds to 30 seconds.

**Card**

[More reference assets](seedance-2-5.md#2.5_50_reference)

* The maximum number of reference images increases from 9 to 30.

* The maximum number of reference audio or video assets increases from 3 to 10.

* The maximum total duration of reference audio or video assets increases from 15 seconds to 30 seconds.

**Card**

[Smarter aspect ratio and duration control](seedance-2-5.md#2.5_smart_ratio_duration)

* For video editing, the output video retains the input video's aspect ratio and duration.

* For video extension and first\-frame/first\-last\-frame video generation, the output video retains the aspect ratio of the input video or image.

**Card**

[New mov output format](seedance-2-5.md#2.5_output_format)

* The new mov output format uses H.264 video encoding, yuv444p chroma sampling, and PCM audio encoding to improve color fidelity and audiovisual consistency for video editing and extension.

**Card**

[Native multilingual support](seedance-2-5.md#2.5_multi_language)

* Supports 11 languages: Chinese, English, Spanish, Indonesian, Malay, Thai, Arabic, Portuguese, Vietnamese, Japanese, and Korean.

## Capabilities overview

|Model name | |[Seedance 2.5](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-5) |[Seedance 2.0](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0) |[Seedance 2.0 fast](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0-fast) |[Seedance 2.0 mini](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0-mini) |
|---|---|---|---|---|---|
|Model ID | |`dreamina-seedance-2-5-260628` |`dreamina-seedance-2-0-260128` |`dreamina-seedance-2-0-fast-260128` |`dreamina-seedance-2-0-mini-260615` |
|[Text-to-video](video-generation-tutorial.md#4e74bcee) | |✓ |✓ |✓ |✓ |
|[First-frame-to-video](video-generation-tutorial.md#979b2d28) | |✓ |✓ |✓ |✓ |
|[First-last-frame-to-video](video-generation-tutorial.md#0d55ca07) | |✓ |✓ |✓ |✓ |
|[Omni reference](seedance-2-5.md#2.5_50_reference) |Image reference |✓ |✓ |✓ |✓ |
||Video reference |✓ |✓ |✓ |✓ |
||Audio reference |✓ |✗ (requires image/video) |✗ (requires image/video) |✗ (requires image/video) |
||Combined reference<br><br><br>* Image + audio<br><br>* Image + video<br><br>* Video + audio<br><br>* Image + video + audio |✓ |✓ |✓ |✓ |
|[Edit video](seedance-2-5.md#2.5_edit) | |✓ |✓ |✓ |✓ |
|[Extend video](seedance-2-5.md#2.5_extend) | |✓ |✓ |✓ |✓ |
|[Generate video with audio](seedance-2-5.md#2.5_multi_language) | |✓ |✓ |✓ |✓ |
|Maximum number of reference assets | |50 (30 images + 10 videos + 10 audios) |15 (9 images + 3 videos + 3 audios) |15 (9 images + 3 videos + 3 audios) |15 (9 images + 3 videos + 3 audios) |
|[Draft mode](seedance-2-5.md#2.5_draft_mode) | |✓ |✗ |✗ |✗ |
|[Return last frame](video-generation-tutorial.md#141cf7fa) | |✓ |✓ |✓ |✓ |
|[Output video specifications](seedance-2-5.md#2.5_video_output_specs) |[Output resolution](seedance-2-5.md#2.5_resolution) |* 480p (8\-bit color depth)<br><br>* 720p (8\-bit color depth)<br><br>* 1080p (10\-bit color depth) |* 480p (8\-bit color depth)<br><br>* 720p (8\-bit color depth)<br><br>* 1080p (8\-bit color depth)<br><br>* 4k (10\-bit color depth) |* 480p (8\-bit color depth)<br><br>* 720p (8\-bit color depth) |* 480p (8\-bit color depth)<br><br>* 720p (8\-bit color depth) |
||[Output aspect ratio](seedance-2-5.md#2.5_ratio) |21:9, 16:9, 4:3,<br><br>1:1, 3:4, 9:16, adaptive |21:9, 16:9, 4:3,<br><br>1:1, 3:4, 9:16, adaptive |21:9, 16:9, 4:3,<br><br>1:1, 3:4, 9:16, adaptive |21:9, 16:9, 4:3,<br><br>1:1, 3:4, 9:16, adaptive |
||[Output duration](seedance-2-5.md#2.5_duration) |4–30 seconds; \-1 (the model automatically selects the optimal duration within the valid duration range) |4–15 seconds; \-1 (the model automatically selects the optimal duration within the valid duration range) |4–15 seconds; \-1 (the model automatically selects the optimal duration within the valid duration range) |4–15 seconds; \-1 (the model automatically selects the optimal duration within the valid duration range) |
||[Output video format](seedance-2-5.md#2.5_output_format) |mp4, mov |mp4 |mp4 |mp4 |
|[Offline inference](video-generation-tutorial.md#a0badaae) | |✗ |✗ |✗ |✗ |

## Key capabilities

### Generate a coherent 30\-second video

The upper limit for Seedance 2.5 video generation duration has been extended from 15 seconds in the Seedance 2.0 series to 30 seconds, supporting one\-time generation of coherent videos up to 30 seconds long and presenting a complete story without multi\-segment stitching.

|Input: text + 8 images |Output |
|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference1.png)![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference2.png)![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference3.png)![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference4.png)![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference5.png)![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference6.png)![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference7.png)![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference8.png)<br><br>> Prompt: One continuous shot follows a person in a black coat moving from left to right through six connected rooms with different color palettes and atmospheres... (See the full prompt in the code example below.) |[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_30s_output1.mp4)<br> |

**cURL**

```Bash
curl -X POST https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dreamina-seedance-2-5-260628",
    "content": [
        {
            "type": "text",
            "text": "One continuous shot. The camera steadily follows a person in a black coat (refer to Image 1) moving from left to right through six connected rooms, each with a different color palette and atmosphere. Every room has the same structure: white walls, light herringbone wood flooring, French double floor-to-ceiling windows, and white sheer curtains, based on Image 2, but the view outside and the interior atmosphere are completely different. The protagonist walks at a constant pace throughout and passes through every open doorway in the walls. 0-5 seconds: The first room has an American comic-book fight theme. The protagonist enters and fights the character in Image 3; the character is defeated. 5-10 seconds: The second room has a warm, felt-crafted style. Outside the windows is a sunflower field from Image 4. Warm orange soft light fills the room, where a painter is painting sunflowers from Image 5. After entering, the protagonist also transforms into a felt-crafted style. 10-15 seconds: The third room has a sad theme. The entire scene uses a black-and-white comic stop-motion style. Rain falls outside, the interior lighting is cold gray and subdued, and a person sits alone on the floor in the center of the empty room, head lowered and knees hugged, with a phone beside them displaying an unanswered call. After entering, the protagonist turns off the light and immediately turns it back on. The room changes to color, and flowers instantly grow throughout it. 15-20 seconds: The fourth room has a joyful theme and is completely submerged in the sea, based on Image 6. The protagonist swims into the room, surrounded by beautiful coral reefs and schools of fish. 20-25 seconds: The fifth room has a surprise theme. Outside is a night sky filled with fireworks, based on Image 7. Colorful flashing light reflects throughout the room, and the protagonist is swept into the cheering atmosphere. 25-30 seconds: Finally, the protagonist reaches an empty room, stands in the center, and snaps their fingers with a finger-snap sound effect. The screen cuts to black, with the word seedance centered, based on Image 8. The overall look is cinematic and styled like a premium fashion commercial. Lighting is determined entirely by the view outside each window, creating strong emotional contrasts. No text in the image."
        },
        {
            "type": "image_url",
            "role": "reference_image",
            "image_url": {
                "url": "asset://asset-20260831132854-f5l7h"
            }
        },
        {
            "type": "image_url",
            "role": "reference_image",
            "image_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference2.png"
            }
        },
        {
            "type": "image_url",
            "role": "reference_image",
            "image_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference3.png"
            }
        },
        {
            "type": "image_url",
            "role": "reference_image",
            "image_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference4.png"
            }
        },
        {
            "type": "image_url",
            "role": "reference_image",
            "image_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference5.png"
            }
        },
        {
            "type": "image_url",
            "role": "reference_image",
            "image_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference6.png"
            }
        },
        {
            "type": "image_url",
            "role": "reference_image",
            "image_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference7.png"
            }
        },
        {
            "type": "image_url",
            "role": "reference_image",
            "image_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference8.png"
            }
        }
    ],
    "generate_audio": true,
    "ratio": "16:9",
    "duration": 30,
    "watermark": true
}'
```

**Python**

```Python
import os
import time
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # The base URL for model invocation
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.environ.get("ARK_API_KEY"),
)

if __name__ == "__main__":
    prompt = "One continuous shot. The camera steadily follows a person in a black coat (refer to Image 1) moving from left to right through six connected rooms, each with a different color palette and atmosphere. Every room has the same structure: white walls, light herringbone wood flooring, French double floor-to-ceiling windows, and white sheer curtains, based on Image 2, but the view outside and the interior atmosphere are completely different. The protagonist walks at a constant pace throughout and passes through every open doorway in the walls. 0-5 seconds: The first room has an American comic-book fight theme. The protagonist enters and fights the character in Image 3; the character is defeated. 5-10 seconds: The second room has a warm, felt-crafted style. Outside the windows is a sunflower field from Image 4. Warm orange soft light fills the room, where a painter is painting sunflowers from Image 5. After entering, the protagonist also transforms into a felt-crafted style. 10-15 seconds: The third room has a sad theme. The entire scene uses a black-and-white comic stop-motion style. Rain falls outside, the interior lighting is cold gray and subdued, and a person sits alone on the floor in the center of the empty room, head lowered and knees hugged, with a phone beside them displaying an unanswered call. After entering, the protagonist turns off the light and immediately turns it back on. The room changes to color, and flowers instantly grow throughout it. 15-20 seconds: The fourth room has a joyful theme and is completely submerged in the sea, based on Image 6. The protagonist swims into the room, surrounded by beautiful coral reefs and schools of fish. 20-25 seconds: The fifth room has a surprise theme. Outside is a night sky filled with fireworks, based on Image 7. Colorful flashing light reflects throughout the room, and the protagonist is swept into the cheering atmosphere. 25-30 seconds: Finally, the protagonist reaches an empty room, stands in the center, and snaps their fingers with a finger-snap sound effect. The screen cuts to black, with the word seedance centered, based on Image 8. The overall look is cinematic and styled like a premium fashion commercial. Lighting is determined entirely by the view outside each window, creating strong emotional contrasts. No text in the image."
    reference_images = [
        "asset://asset-20260831132854-f5l7h",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference2.png",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference3.png",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference4.png",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference5.png",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference6.png",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference7.png",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference8.png",
    ]
    content = [{"type": "text", "text": prompt}]
    content.extend(
        {
            "type": "image_url",
            "role": "reference_image",
            "image_url": {"url": image_url},
        }
        for image_url in reference_images
    )

    print("----- create request -----")
    create_result = client.content_generation.tasks.create(
        model="dreamina-seedance-2-5-260628",  # Replace with Model ID
        content=content,
        generate_audio=True,
        ratio="16:9",
        duration=30,
        watermark=True,
    )
    print(create_result)

    # Polling query section
    print("----- polling task status -----")
    task_id = create_result.id
    deadline = time.monotonic() + 30 * 60
    while time.monotonic() < deadline:
        get_result = client.content_generation.tasks.get(task_id=task_id)
        status = get_result.status
        if status == "succeeded":
            print("----- task succeeded -----")
            print(get_result)
            break
        elif status == "failed":
            raise RuntimeError(f"Video generation task failed: {get_result.error}")
        else:
            print(f"Current status: {status}, Retrying after 30 seconds...")
            time.sleep(30)
    else:
        raise TimeoutError("Video generation task did not finish within 30 minutes")
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
           .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3") // The base URL for model invocation
           .dispatcher(dispatcher)
           .connectionPool(connectionPool)
           .apiKey(apiKey)
           .build();

    public static void main(String[] args) {

        // Model ID
        final String modelId = "dreamina-seedance-2-5-260628";
        // Text prompt
        final String prompt = "One continuous shot. The camera steadily follows a person in a black coat (refer to Image 1) moving from left to right through six connected rooms, each with a different color palette and atmosphere. Every room has the same structure: white walls, light herringbone wood flooring, French double floor-to-ceiling windows, and white sheer curtains, based on Image 2, but the view outside and the interior atmosphere are completely different. The protagonist walks at a constant pace throughout and passes through every open doorway in the walls. 0-5 seconds: The first room has an American comic-book fight theme. The protagonist enters and fights the character in Image 3; the character is defeated. 5-10 seconds: The second room has a warm, felt-crafted style. Outside the windows is a sunflower field from Image 4. Warm orange soft light fills the room, where a painter is painting sunflowers from Image 5. After entering, the protagonist also transforms into a felt-crafted style. 10-15 seconds: The third room has a sad theme. The entire scene uses a black-and-white comic stop-motion style. Rain falls outside, the interior lighting is cold gray and subdued, and a person sits alone on the floor in the center of the empty room, head lowered and knees hugged, with a phone beside them displaying an unanswered call. After entering, the protagonist turns off the light and immediately turns it back on. The room changes to color, and flowers instantly grow throughout it. 15-20 seconds: The fourth room has a joyful theme and is completely submerged in the sea, based on Image 6. The protagonist swims into the room, surrounded by beautiful coral reefs and schools of fish. 20-25 seconds: The fifth room has a surprise theme. Outside is a night sky filled with fireworks, based on Image 7. Colorful flashing light reflects throughout the room, and the protagonist is swept into the cheering atmosphere. 25-30 seconds: Finally, the protagonist reaches an empty room, stands in the center, and snaps their fingers with a finger-snap sound effect. The screen cuts to black, with the word seedance centered, based on Image 8. The overall look is cinematic and styled like a premium fashion commercial. Lighting is determined entirely by the view outside each window, creating strong emotional contrasts. No text in the image.";

        // Example resource URLs
        final String[] referenceImages = {
                "asset://asset-20260831132854-f5l7h",
                "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference2.png",
                "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference3.png",
                "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference4.png",
                "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference5.png",
                "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference6.png",
                "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference7.png",
                "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference8.png"
        };

        // Output video parameters
        final boolean generateAudio = true;
        final String videoRatio = "16:9";
        final long videoDuration = 30L;

        System.out.println("----- create request -----");
        // Build request content
        List<ContentItem> contents = new ArrayList<>();

        // 1. Text prompt
        contents.add(ContentItem.builder()
                .type(ContentType.TEXT)
                .text(prompt)
                .build());

        // 2. Reference images
        for (String referenceImage : referenceImages) {
            contents.add(ContentItem.builder()
                    .type(ContentType.IMAGE_URL)
                    .imageUrl(ImageURL.builder()
                            .url(referenceImage)
                            .build())
                    .role("reference_image")
                    .build());
        }

        // Create video generation task
        CreateContentGenerationTaskRequest createRequest = CreateContentGenerationTaskRequest.builder()
                .generateAudio(generateAudio)
                .model(modelId)
                .content(contents)
                .ratio(videoRatio)
                .duration(videoDuration)
                .watermark(true)
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
            long deadlineNanos = System.nanoTime() + TimeUnit.MINUTES.toNanos(30);
            while (System.nanoTime() < deadlineNanos) {
                ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
                String status = getResponse.getStatus().toString();

                if ("succeeded".equalsIgnoreCase(status)) {
                    System.out.println("----- task succeeded -----");
                    System.out.println(getResponse);
                    return;
                } else if ("failed".equalsIgnoreCase(status)) {
                    throw new IllegalStateException(
                            "Video generation task failed: " + getResponse.getError());
                } else {
                    System.out.printf("Current status: %s, Retrying in 10 seconds...%n", status);
                    TimeUnit.SECONDS.sleep(10);
                }
            }
            throw new IllegalStateException(
                    "Video generation task did not finish within 30 minutes");
        } catch (InterruptedException ie) {
            Thread.currentThread().interrupt();
            throw new RuntimeException("Polling interrupted", ie);
        } catch (Exception e) {
            throw new RuntimeException("Error occurred while polling", e);
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
    ctx, cancel := context.WithTimeout(context.Background(), 30*time.Minute)
    defer cancel()

    // Model ID
    modelID := "dreamina-seedance-2-5-260628"
    // Text prompt
    prompt := "One continuous shot. The camera steadily follows a person in a black coat (refer to Image 1) moving from left to right through six connected rooms, each with a different color palette and atmosphere. Every room has the same structure: white walls, light herringbone wood flooring, French double floor-to-ceiling windows, and white sheer curtains, based on Image 2, but the view outside and the interior atmosphere are completely different. The protagonist walks at a constant pace throughout and passes through every open doorway in the walls. 0-5 seconds: The first room has an American comic-book fight theme. The protagonist enters and fights the character in Image 3; the character is defeated. 5-10 seconds: The second room has a warm, felt-crafted style. Outside the windows is a sunflower field from Image 4. Warm orange soft light fills the room, where a painter is painting sunflowers from Image 5. After entering, the protagonist also transforms into a felt-crafted style. 10-15 seconds: The third room has a sad theme. The entire scene uses a black-and-white comic stop-motion style. Rain falls outside, the interior lighting is cold gray and subdued, and a person sits alone on the floor in the center of the empty room, head lowered and knees hugged, with a phone beside them displaying an unanswered call. After entering, the protagonist turns off the light and immediately turns it back on. The room changes to color, and flowers instantly grow throughout it. 15-20 seconds: The fourth room has a joyful theme and is completely submerged in the sea, based on Image 6. The protagonist swims into the room, surrounded by beautiful coral reefs and schools of fish. 20-25 seconds: The fifth room has a surprise theme. Outside is a night sky filled with fireworks, based on Image 7. Colorful flashing light reflects throughout the room, and the protagonist is swept into the cheering atmosphere. 25-30 seconds: Finally, the protagonist reaches an empty room, stands in the center, and snaps their fingers with a finger-snap sound effect. The screen cuts to black, with the word seedance centered, based on Image 8. The overall look is cinematic and styled like a premium fashion commercial. Lighting is determined entirely by the view outside each window, creating strong emotional contrasts. No text in the image."

    // Example resource URLs
    referenceImages := []string{
        "asset://asset-20260831132854-f5l7h",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference2.png",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference3.png",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference4.png",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference5.png",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference6.png",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference7.png",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_30s_reference8.png",
    }

    // Output video parameters
    generateAudio := true
    videoRatio := "16:9"
    videoDuration := int64(30)

    content := []model.ContentItem{
        {
            Type: model.ContentTypeText,
            Text: model.NewOptString(prompt),
        },
    }
    for _, referenceImage := range referenceImages {
        content = append(content, model.ContentItem{
            Type: model.ContentTypeImageURL,
            ImageURL: model.NewOptImageURL(model.ImageURL{
                URL: referenceImage,
            }),
            Role: model.NewOptString("reference_image"),
        })
    }

    // 1. Create video generation task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model:         modelID,
        GenerateAudio: model.NewOptBool(generateAudio),
        Ratio:         model.NewOptString(videoRatio),
        Duration:      model.NewOptInt64(videoDuration),
        Content:       content,
        Watermark:     model.NewOptBool(true),
    }

    createResp, err := client.CreateContentGenerationTask(ctx, createReq)
    if err != nil {
        panic(fmt.Errorf("create content generation task: %w", err))
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
            panic(fmt.Errorf("get content generation task: %w", err))
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
            if getResp.Error.IsSet() {
                panic(fmt.Errorf("video generation task failed: %s: %s", getResp.Error.Value.Code, getResp.Error.Value.Message))
            }
            panic("video generation task failed")
        } else {
            fmt.Printf("Current status: %s, Retrying in 10 seconds... \n", status)
            time.Sleep(10 * time.Second)
        }
    }
}
```

### Up to 50 omni reference assets

Seedance 2.5 increases the upper limit for assets in a single input to 50 (30 images + 10 video clips + 10 audio clips). You can freely combine multimodal assets such as images, videos, and audio to achieve richer creative expression. In addition, Seedance 2.5 newly supports generating videos with pure audio references, without requiring image or video assets.

|Input: Text + 1 Image + 6 Video Clips |||Output |
|---|---|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_reference1.png)<br><br>> Prompt: A bright and colorful commercial style, with fruit\-flavored cookies as the main subject... (see the complete prompt in the code example below for details) |[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference2.mp4)<br><br><br> <br><br>[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference4.mp4)<br><br><br> <br><br>[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference6.mp4)<br> |[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference3.mp4)<br><br><br> <br><br>[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference5.mp4)<br><br><br> <br><br>[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference7.mp4)<br> |[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference_output.mp4)<br> |

**cURL**

```Bash
curl -X POST https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dreamina-seedance-2-5-260628",
    "content": [
        {
            "type": "text",
            "text": "A bright, colorful advertising-film style with fruit-flavored cookies as the main subject, including four flavors: strawberry, apple, grape, and orange. The strawberry flavor refers to @Image1. The cookies and corresponding fruits are arranged in strongly ordered geometric arrays. The overall image is clean, premium, and strongly rhythmic. The opening quickly builds visual focus on the fruit, referring to the composition of @Video1, with the music downbeat cutting in. Then cookies of different flavors are arranged neatly, cutting to close-ups, referring to the motion and camera movement of @Video2. In the climax, one cookie is snapped in half and instantly enters slow motion. The fruit-flavored filling bursts open, crumbs fly, and the juicy texture and granular impact are amplified, referring to the impact of @Video3. A horizontal array creates a rhythmic parabolic motion, referring to the movement in @Video4, highlighting ordered beauty and product variety. It then quickly returns to fast-paced editing. At the end, the English text \"One bite of crispness, a heart full of delight\" appears through rapid word-by-word cuts, paired with strong rhythmic text motion and product freeze frames, referring to @Video5. The final brand moment resolves as the cookies and fruits scatter outward, referring to @Video6. The image is filled with a young, energetic, delicious, and shareable advertising atmosphere."
        },
        {
            "type": "image_url",
            "image_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_reference1.png"
            },
            "role": "reference_image"
        },
        {
            "type": "video_url",
            "video_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference2.mp4"
            },
            "role": "reference_video"
        },
        {
            "type": "video_url",
            "video_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference3.mp4"
            },
            "role": "reference_video"
        },
        {
            "type": "video_url",
            "video_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference4.mp4"
            },
            "role": "reference_video"
        },
        {
            "type": "video_url",
            "video_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference5.mp4"
            },
            "role": "reference_video"
        },
        {
            "type": "video_url",
            "video_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference6.mp4"
            },
            "role": "reference_video"
        },
        {
            "type": "video_url",
            "video_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference7.mp4"
            },
            "role": "reference_video"
        }
    ],
    "generate_audio": true,
    "ratio": "16:9",
    "duration": 15,
    "omni_reference_task_type": "reference",
    "output_format": "mov",
    "watermark": true
}'
```

**Python**

```Python
import os
import time
# Install SDK: python -m pip install --upgrade arkruntime
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
        model="dreamina-seedance-2-5-260628", # Replace with Model ID
        content=[
            {
                "type": "text",
                "text": "A bright, colorful advertising-film style with fruit-flavored cookies as the main subject, including four flavors: strawberry, apple, grape, and orange. The strawberry flavor refers to @Image1. The cookies and corresponding fruits are arranged in strongly ordered geometric arrays. The overall image is clean, premium, and strongly rhythmic. The opening quickly builds visual focus on the fruit, referring to the composition of @Video1, with the music downbeat cutting in. Then cookies of different flavors are arranged neatly, cutting to close-ups, referring to the motion and camera movement of @Video2. In the climax, one cookie is snapped in half and instantly enters slow motion. The fruit-flavored filling bursts open, crumbs fly, and the juicy texture and granular impact are amplified, referring to the impact of @Video3. A horizontal array creates a rhythmic parabolic motion, referring to the movement in @Video4, highlighting ordered beauty and product variety. It then quickly returns to fast-paced editing. At the end, the English text \"One bite of crispness, a heart full of delight\" appears through rapid word-by-word cuts, paired with strong rhythmic text motion and product freeze frames, referring to @Video5. The final brand moment resolves as the cookies and fruits scatter outward, referring to @Video6. The image is filled with a young, energetic, delicious, and shareable advertising atmosphere."
            },
            {
                "type": "image_url",
                "image_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_reference1.png"
                },
                "role": "reference_image",
            },
            {
                "type": "video_url",
                "video_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference2.mp4"
                },
                "role": "reference_video",
            },
            {
                "type": "video_url",
                "video_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference3.mp4"
                },
                "role": "reference_video",
            },
            {
                "type": "video_url",
                "video_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference4.mp4"
                },
                "role": "reference_video",
            },
            {
                "type": "video_url",
                "video_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference5.mp4"
                },
                "role": "reference_video",
            },
            {
                "type": "video_url",
                "video_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference6.mp4"
                },
                "role": "reference_video",
            },
            {
                "type": "video_url",
                "video_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference7.mp4"
                },
                "role": "reference_video",
            },
        ],
        generate_audio=True,
        ratio="16:9",
        duration=15,
        extra_body={
            "omni_reference_task_type": "reference",
            "output_format": "mov",
        },
        watermark=True,
    )
    print(create_result)


    # Polling query section
    print("----- polling task status -----")
    task_id = create_result.id
    deadline = time.monotonic() + 30 * 60
    while time.monotonic() < deadline:
        get_result = client.content_generation.tasks.get(task_id=task_id)
        status = get_result.status
        if status == "succeeded":
            print("----- task succeeded -----")
            print(get_result)
            break
        elif status == "failed":
            raise RuntimeError(f"Video generation task failed: {get_result.error}")
        else:
            print(f"Current status: {status}, Retrying after 10 seconds...")
            time.sleep(10)
    else:
        raise TimeoutError("Video generation task did not finish within 30 minutes")
```

**Java**

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

    interface ContentGenerationApi {
        @POST("contents/generations/tasks")
        Call<CreateContentGenerationTaskResponse> create(@Body Map<String, Object> request);
    }

    // Client initialization
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
    static ContentGenerationApi contentGenerationApi =
            retrofit.create(ContentGenerationApi.class);

    public static void main(String[] args) throws IOException {

        // Model ID
        final String modelId = "dreamina-seedance-2-5-260628";
        // Text prompt
        final String prompt = "A bright, colorful advertising-film style with fruit-flavored cookies as the main subject, including four flavors: strawberry, apple, grape, and orange. The strawberry flavor refers to @Image1. The cookies and corresponding fruits are arranged in strongly ordered geometric arrays. The overall image is clean, premium, and strongly rhythmic. The opening quickly builds visual focus on the fruit, referring to the composition of @Video1, with the music downbeat cutting in. Then cookies of different flavors are arranged neatly, cutting to close-ups, referring to the motion and camera movement of @Video2. In the climax, one cookie is snapped in half and instantly enters slow motion. The fruit-flavored filling bursts open, crumbs fly, and the juicy texture and granular impact are amplified, referring to the impact of @Video3. A horizontal array creates a rhythmic parabolic motion, referring to the movement in @Video4, highlighting ordered beauty and product variety. It then quickly returns to fast-paced editing. At the end, the English text \"One bite of crispness, a heart full of delight\" appears through rapid word-by-word cuts, paired with strong rhythmic text motion and product freeze frames, referring to @Video5. The final brand moment resolves as the cookies and fruits scatter outward, referring to @Video6. The image is filled with a young, energetic, delicious, and shareable advertising atmosphere.";

        // Example resource URLs
        final String refImage1 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_reference1.png";
        final String refVideo1 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference2.mp4";
        final String refVideo2 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference3.mp4";
        final String refVideo3 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference4.mp4";
        final String refVideo4 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference5.mp4";
        final String refVideo5 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference6.mp4";
        final String refVideo6 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference7.mp4";

        // Output video parameters
        final boolean generateAudio = true;
        final String videoRatio = "16:9";
        final long videoDuration = 15L;
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

        // 3. Reference video 1
        contents.add(ContentItem.builder()
                .type(ContentType.VIDEO_URL)
                .videoUrl(VideoURL.builder()
                        .url(refVideo1)
                        .build())
                .role("reference_video")
                .build());

        // 4. Reference video 2
        contents.add(ContentItem.builder()
                .type(ContentType.VIDEO_URL)
                .videoUrl(VideoURL.builder()
                        .url(refVideo2)
                        .build())
                .role("reference_video")
                .build());

        // 5. Reference video 3
        contents.add(ContentItem.builder()
                .type(ContentType.VIDEO_URL)
                .videoUrl(VideoURL.builder()
                        .url(refVideo3)
                        .build())
                .role("reference_video")
                .build());

        // 6. Reference video 4
        contents.add(ContentItem.builder()
                .type(ContentType.VIDEO_URL)
                .videoUrl(VideoURL.builder()
                        .url(refVideo4)
                        .build())
                .role("reference_video")
                .build());

        // 7. Reference video 5
        contents.add(ContentItem.builder()
                .type(ContentType.VIDEO_URL)
                .videoUrl(VideoURL.builder()
                        .url(refVideo5)
                        .build())
                .role("reference_video")
                .build());

        // 8. Reference video 6
        contents.add(ContentItem.builder()
                .type(ContentType.VIDEO_URL)
                .videoUrl(VideoURL.builder()
                        .url(refVideo6)
                        .build())
                .role("reference_video")
                .build());

        // Create video generation task
        Map<String, Object> createRequest = new LinkedHashMap<>();
        createRequest.put("model", modelId);
        createRequest.put("content", contents);
        createRequest.put("generate_audio", generateAudio);
        createRequest.put("ratio", videoRatio);
        createRequest.put("duration", videoDuration);
        createRequest.put("omni_reference_task_type", "reference");
        createRequest.put("output_format", "mov");
        createRequest.put("watermark", true);

        retrofit2.Response<CreateContentGenerationTaskResponse> response =
                contentGenerationApi.create(createRequest).execute();
        if (!response.isSuccessful() || response.body() == null) {
            throw new IOException("Unexpected code " + response.code());
        }
        CreateContentGenerationTaskResponse createResult = response.body();
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
            long deadlineNanos = System.nanoTime() + TimeUnit.MINUTES.toNanos(30);
            while (System.nanoTime() < deadlineNanos) {
                ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
                String status = getResponse.getStatus().toString();

                if ("succeeded".equalsIgnoreCase(status)) {
                    System.out.println("----- task succeeded -----");
                    System.out.println(getResponse);
                    return;
                } else if ("failed".equalsIgnoreCase(status)) {
                    throw new IllegalStateException(
                            "Video generation task failed: " + getResponse.getError());
                } else {
                    System.out.printf("Current status: %s, Retrying in 10 seconds...%n", status);
                    TimeUnit.SECONDS.sleep(10);
                }
            }
            throw new IllegalStateException(
                    "Video generation task did not finish within 30 minutes");
        } catch (InterruptedException ie) {
            Thread.currentThread().interrupt();
            throw new RuntimeException("Polling interrupted", ie);
        } catch (Exception e) {
            throw new RuntimeException("Error occurred while polling", e);
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
    ctx, cancel := context.WithTimeout(context.Background(), 30*time.Minute)
    defer cancel()

    // Model ID
    modelID := "dreamina-seedance-2-5-260628"
    // Text prompt
    prompt := "A bright, colorful advertising-film style with fruit-flavored cookies as the main subject, including four flavors: strawberry, apple, grape, and orange. The strawberry flavor refers to @Image1. The cookies and corresponding fruits are arranged in strongly ordered geometric arrays. The overall image is clean, premium, and strongly rhythmic. The opening quickly builds visual focus on the fruit, referring to the composition of @Video1, with the music downbeat cutting in. Then cookies of different flavors are arranged neatly, cutting to close-ups, referring to the motion and camera movement of @Video2. In the climax, one cookie is snapped in half and instantly enters slow motion. The fruit-flavored filling bursts open, crumbs fly, and the juicy texture and granular impact are amplified, referring to the impact of @Video3. A horizontal array creates a rhythmic parabolic motion, referring to the movement in @Video4, highlighting ordered beauty and product variety. It then quickly returns to fast-paced editing. At the end, the English text \"One bite of crispness, a heart full of delight\" appears through rapid word-by-word cuts, paired with strong rhythmic text motion and product freeze frames, referring to @Video5. The final brand moment resolves as the cookies and fruits scatter outward, referring to @Video6. The image is filled with a young, energetic, delicious, and shareable advertising atmosphere."

    // Example resource URLs
    refImage1 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_reference1.png"
    refVideo1 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference2.mp4"
    refVideo2 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference3.mp4"
    refVideo3 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference4.mp4"
    refVideo4 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference5.mp4"
    refVideo5 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference6.mp4"
    refVideo6 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_reference7.mp4"

    // Output video parameters
    generateAudio := true
    videoRatio := "16:9"
    videoDuration := int64(15)

    // 1. Create video generation task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model:         modelID,
        GenerateAudio: model.NewOptBool(generateAudio),
        Ratio:         model.NewOptString(videoRatio),
        Duration:      model.NewOptInt64(videoDuration),
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
            {
                Type: model.ContentTypeVideoURL,
                VideoURL: model.NewOptVideoURL(model.VideoURL{
                    URL: refVideo4,
                }),
                Role: model.NewOptString("reference_video"),
            },
            {
                Type: model.ContentTypeVideoURL,
                VideoURL: model.NewOptVideoURL(model.VideoURL{
                    URL: refVideo5,
                }),
                Role: model.NewOptString("reference_video"),
            },
            {
                Type: model.ContentTypeVideoURL,
                VideoURL: model.NewOptVideoURL(model.VideoURL{
                    URL: refVideo6,
                }),
                Role: model.NewOptString("reference_video"),
            },
        },
        Watermark:     model.NewOptBool(true),
    }

    createResp, err := client.CreateContentGenerationTask(
        ctx,
        createReq,
        arkruntime.WithExtraBody(map[string]interface{}{
            "omni_reference_task_type": "reference",
            "output_format":            "mov",
        }),
    )
    if err != nil {
        panic(fmt.Errorf("create content generation task: %w", err))
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
            panic(fmt.Errorf("get content generation task: %w", err))
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
            if getResp.Error.IsSet() {
                panic(fmt.Errorf("video generation task failed: %s: %s", getResp.Error.Value.Code, getResp.Error.Value.Message))
            }
            panic("video generation task failed")
        } else {
            fmt.Printf("Current status: %s, Retrying in 10 seconds... \n", status)
            time.Sleep(10 * time.Second)
        }
    }
}
```

### Control duration and aspect ratio more intelligently

Seedance 2.5 can intelligently control the output aspect ratio and duration when `ratio` is set to `adaptive` and `duration` is set to `-1`. For **video editing, video extension, and first\-frame/first\-last\-frame video generation**, these parameters have special behavior, as shown in the following examples.

#### Example: Edit a video

In video editing tasks, Seedance 2.5 selects the video to edit based on the prompt intent, then automatically keeps the output aspect ratio and duration consistent with the selected video (`ratio` defaults to and only supports `adaptive`, and `duration` defaults to and only supports `-1`; neither value can be set separately).

> **Note**

> Due to input frame processing, the output may be slightly shorter than the input video, with a difference of no more than 0.4 seconds.

|Input: Text + Video (aspect ratio 5:4, duration 16 seconds) |Output (aspect ratio 5:4, duration 16 seconds) |
|---|---|
|[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_edit_input.mov)<br><br><br>> Prompt: Video edit: remove everyone in @Video 1 except the protagonist. |[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_edit_output.mov)<br><br><br>> **The output retains the input video's 5:4 aspect ratio and is approximately 16 seconds long.**  |

**cURL**

```Bash
curl -X POST https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dreamina-seedance-2-5-260628",
    "content": [
        {
            "type": "text",
            "text": "Video edit: remove everyone in @Video1 except the protagonist."
        },
        {
            "type": "video_url",
            "video_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_edit_input.mov"
            },
            "role": "reference_video"
        }
    ],
    "generate_audio": true,
    "ratio": "adaptive",
    "duration": -1,
    "output_format": "mov",
    "watermark": true
}'
```

**Python**

```Python
import os
import time
# Install SDK: python -m pip install --upgrade arkruntime
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
        model="dreamina-seedance-2-5-260628", # Replace with Model ID
        content=[
            {
                "type": "text",
                "text": "Video edit: remove everyone in @Video1 except the protagonist."
            },
            {
                "type": "video_url",
                "video_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_edit_input.mov"
                },
                "role": "reference_video",
            },
        ],
        generate_audio=True,
        ratio="adaptive",
        duration=-1,
        extra_body={
            "omni_reference_task_type": "edit",
            "output_format": "mov",
        },
        watermark=True,
    )
    print(create_result)


    # Polling query section
    print("----- polling task status -----")
    task_id = create_result.id
    deadline = time.monotonic() + 30 * 60
    while time.monotonic() < deadline:
        get_result = client.content_generation.tasks.get(task_id=task_id)
        status = get_result.status
        if status == "succeeded":
            print("----- task succeeded -----")
            print(get_result)
            break
        elif status == "failed":
            raise RuntimeError(f"Video generation task failed: {get_result.error}")
        else:
            print(f"Current status: {status}, Retrying after 10 seconds...")
            time.sleep(10)
    else:
        raise TimeoutError("Video generation task did not finish within 30 minutes")
```

**Java**

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

    interface ContentGenerationApi {
        @POST("contents/generations/tasks")
        Call<CreateContentGenerationTaskResponse> create(@Body Map<String, Object> request);
    }

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
    static Retrofit retrofit = ArkService.defaultRetrofit(
            ArkService.defaultApiKeyClient(apiKey, Duration.ofSeconds(180)),
            ArkService.defaultObjectMapper(),
            "https://ark.ap-southeast.bytepluses.com/api/v3/",
            Runnable::run);
    static ContentGenerationApi contentGenerationApi =
            retrofit.create(ContentGenerationApi.class);

    public static void main(String[] args) throws IOException {

        // Model ID
        final String modelId = "dreamina-seedance-2-5-260628";
        // Text prompt
        final String prompt = "Video edit: remove everyone in @Video1 except the protagonist.";

        // Example resource URLs
        final String refVideo = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_edit_input.mov";

        // Output video parameters
        final boolean generateAudio = true;
        final String videoRatio = "adaptive";
        final long videoDuration = -1L;
        System.out.println("----- create request -----");
        // Build request content
        List<ContentItem> contents = new ArrayList<>();

        // 1. Text prompt
        contents.add(ContentItem.builder()
                .type(ContentType.TEXT)
                .text(prompt)
                .build());

        // 2. Reference video
        contents.add(ContentItem.builder()
                .type(ContentType.VIDEO_URL)
                .videoUrl(VideoURL.builder()
                        .url(refVideo)
                        .build())
                .role("reference_video")
                .build());

        // Create video generation task
        Map<String, Object> createRequest = new LinkedHashMap<>();
        createRequest.put("model", modelId);
        createRequest.put("content", contents);
        createRequest.put("generate_audio", generateAudio);
        createRequest.put("ratio", videoRatio);
        createRequest.put("duration", videoDuration);
        createRequest.put("omni_reference_task_type", "edit");
        createRequest.put("output_format", "mov");
        createRequest.put("watermark", true);

        retrofit2.Response<CreateContentGenerationTaskResponse> response =
                contentGenerationApi.create(createRequest).execute();
        if (!response.isSuccessful() || response.body() == null) {
            throw new IOException("Unexpected code " + response.code());
        }
        CreateContentGenerationTaskResponse createResult = response.body();
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
            long deadlineNanos = System.nanoTime() + TimeUnit.MINUTES.toNanos(30);
            while (System.nanoTime() < deadlineNanos) {
                ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
                String status = getResponse.getStatus().toString();

                if ("succeeded".equalsIgnoreCase(status)) {
                    System.out.println("----- task succeeded -----");
                    System.out.println(getResponse);
                    return;
                } else if ("failed".equalsIgnoreCase(status)) {
                    throw new IllegalStateException(
                            "Video generation task failed: " + getResponse.getError());
                } else {
                    System.out.printf("Current status: %s, Retrying in 10 seconds...%n", status);
                    TimeUnit.SECONDS.sleep(10);
                }
            }
            throw new IllegalStateException(
                    "Video generation task did not finish within 30 minutes");
        } catch (InterruptedException ie) {
            Thread.currentThread().interrupt();
            throw new RuntimeException("Polling interrupted", ie);
        } catch (Exception e) {
            throw new RuntimeException("Error occurred while polling", e);
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
    ctx, cancel := context.WithTimeout(context.Background(), 30*time.Minute)
    defer cancel()

    // Model ID
    modelID := "dreamina-seedance-2-5-260628"
    // Text prompt
    prompt := "Video edit: remove everyone in @Video1 except the protagonist."

    // Example resource URLs
    refVideo := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/seedance2.5_edit_input.mov"

    // Output video parameters
    generateAudio := true
    videoRatio := "adaptive"
    videoDuration := int64(-1)

    // 1. Create video generation task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model:         modelID,
        GenerateAudio: model.NewOptBool(generateAudio),
        Ratio:         model.NewOptString(videoRatio),
        Duration:      model.NewOptInt64(videoDuration),
        Content: []model.ContentItem{
            {
                Type: model.ContentTypeText,
                Text: model.NewOptString(prompt),
            },
            {
                Type: model.ContentTypeVideoURL,
                VideoURL: model.NewOptVideoURL(model.VideoURL{
                    URL: refVideo,
                }),
                Role: model.NewOptString("reference_video"),
            },
        },
        Watermark:     model.NewOptBool(true),
    }

    createResp, err := client.CreateContentGenerationTask(
        ctx,
        createReq,
        arkruntime.WithExtraBody(map[string]interface{}{
            "omni_reference_task_type": "edit",
            "output_format":            "mov",
        }),
    )
    if err != nil {
        panic(fmt.Errorf("create content generation task: %w", err))
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
            panic(fmt.Errorf("get content generation task: %w", err))
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
            if getResp.Error.IsSet() {
                panic(fmt.Errorf("video generation task failed: %s: %s", getResp.Error.Value.Code, getResp.Error.Value.Message))
            }
            panic("video generation task failed")
        } else {
            fmt.Printf("Current status: %s, Retrying in 10 seconds... \n", status)
            time.Sleep(10 * time.Second)
        }
    }
}
```

#### Example: Extend a video

In video extension tasks, Seedance 2.5 selects the video to extend based on the prompt intent, then automatically keeps the output aspect ratio consistent with the selected video (`ratio` defaults to and only supports `adaptive`). You can specify the output duration or let the model determine it automatically (`duration` supports `[4, 30]` or `-1`).

|Input: text + video (video 1 aspect ratio 7:5, video 2 and video 3 aspect ratio 16:9) ||Output (aspect ratio 7:5) |
|---|---|---|
|[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video1_75.mov)<br><br><br>> Prompt: Extend @Video 1. After the window opens, move into the art gallery interior shown in @Video 2. Finally, move the camera into the painting shown in @Video 3. |[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video2.mp4)<br><br><br>[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video3.mp4)<br> |[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_output_75.mov)<br><br><br>> **The output video retains the 7:5 aspect ratio of the video being extended.**  |

**cURL**

```Bash
curl -X POST https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dreamina-seedance-2-5-260628",
    "content": [
        {
            "type": "text",
            "text": "Extend @Video 1. After the window opens, move into the art gallery interior shown in @Video 2. Finally, move the camera into the painting shown in @Video 3."
        },
        {
            "type": "video_url",
            "video_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video1_75.mov"
            },
            "role": "reference_video"
        },
        {
            "type": "video_url",
            "video_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video2.mp4"
            },
            "role": "reference_video"
        },
        {
            "type": "video_url",
            "video_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video3.mp4"
            },
            "role": "reference_video"
        }
    ],
    "generate_audio": true,
    "ratio": "adaptive",
    "duration": 11,
    "output_format": "mov",
    "watermark": true
}'
```

**Python**

```Python
import os
import time
# Install SDK: python -m pip install --upgrade arkruntime
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
        model="dreamina-seedance-2-5-260628", # Replace with Model ID
        content=[
            {
                "type": "text",
                "text": "Extend @Video 1. After the window opens, move into the art gallery interior shown in @Video 2. Finally, move the camera into the painting shown in @Video 3."
            },
            {
                "type": "video_url",
                "video_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video1_75.mov"
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
        ratio="adaptive",
        duration=11,
        extra_body={
            "omni_reference_task_type": "extend",
            "output_format": "mov",
        },
        watermark=True,
    )
    print(create_result)


    # Polling query section
    print("----- polling task status -----")
    task_id = create_result.id
    deadline = time.monotonic() + 30 * 60
    while time.monotonic() < deadline:
        get_result = client.content_generation.tasks.get(task_id=task_id)
        status = get_result.status
        if status == "succeeded":
            print("----- task succeeded -----")
            print(get_result)
            break
        elif status == "failed":
            raise RuntimeError(f"Video generation task failed: {get_result.error}")
        else:
            print(f"Current status: {status}, Retrying after 10 seconds...")
            time.sleep(10)
    else:
        raise TimeoutError("Video generation task did not finish within 30 minutes")
```

**Java**

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

    interface ContentGenerationApi {
        @POST("contents/generations/tasks")
        Call<CreateContentGenerationTaskResponse> create(@Body Map<String, Object> request);
    }

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
    static Retrofit retrofit = ArkService.defaultRetrofit(
            ArkService.defaultApiKeyClient(apiKey, Duration.ofSeconds(180)),
            ArkService.defaultObjectMapper(),
            "https://ark.ap-southeast.bytepluses.com/api/v3/",
            Runnable::run);
    static ContentGenerationApi contentGenerationApi =
            retrofit.create(ContentGenerationApi.class);

    public static void main(String[] args) throws IOException {

        // Model ID
        final String modelId = "dreamina-seedance-2-5-260628";
        // Text prompt
        final String prompt = "Extend @Video 1. After the window opens, move into the art gallery interior shown in @Video 2. Finally, move the camera into the painting shown in @Video 3.";

        // Example resource URLs
        final String refVideo1 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video1_75.mov";
        final String refVideo2 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video2.mp4";
        final String refVideo3 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video3.mp4";

        // Output video parameters
        final boolean generateAudio = true;
        final String videoRatio = "adaptive";
        final long videoDuration = 11L;
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
        Map<String, Object> createRequest = new LinkedHashMap<>();
        createRequest.put("model", modelId);
        createRequest.put("content", contents);
        createRequest.put("generate_audio", generateAudio);
        createRequest.put("ratio", videoRatio);
        createRequest.put("duration", videoDuration);
        createRequest.put("omni_reference_task_type", "extend");
        createRequest.put("output_format", "mov");
        createRequest.put("watermark", true);

        retrofit2.Response<CreateContentGenerationTaskResponse> response =
                contentGenerationApi.create(createRequest).execute();
        if (!response.isSuccessful() || response.body() == null) {
            throw new IOException("Unexpected code " + response.code());
        }
        CreateContentGenerationTaskResponse createResult = response.body();
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
            long deadlineNanos = System.nanoTime() + TimeUnit.MINUTES.toNanos(30);
            while (System.nanoTime() < deadlineNanos) {
                ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
                String status = getResponse.getStatus().toString();

                if ("succeeded".equalsIgnoreCase(status)) {
                    System.out.println("----- task succeeded -----");
                    System.out.println(getResponse);
                    return;
                } else if ("failed".equalsIgnoreCase(status)) {
                    throw new IllegalStateException(
                            "Video generation task failed: " + getResponse.getError());
                } else {
                    System.out.printf("Current status: %s, Retrying in 10 seconds...%n", status);
                    TimeUnit.SECONDS.sleep(10);
                }
            }
            throw new IllegalStateException(
                    "Video generation task did not finish within 30 minutes");
        } catch (InterruptedException ie) {
            Thread.currentThread().interrupt();
            throw new RuntimeException("Polling interrupted", ie);
        } catch (Exception e) {
            throw new RuntimeException("Error occurred while polling", e);
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
    ctx, cancel := context.WithTimeout(context.Background(), 30*time.Minute)
    defer cancel()

    // Model ID
    modelID := "dreamina-seedance-2-5-260628"
    // Text prompt
    prompt := "Extend @Video 1. After the window opens, move into the art gallery interior shown in @Video 2. Finally, move the camera into the painting shown in @Video 3."

    // Example resource URLs
    refVideo1 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video1_75.mov"
    refVideo2 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video2.mp4"
    refVideo3 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/video-generation/r2v_extend_video3.mp4"

    // Output video parameters
    generateAudio := true
    videoRatio := "adaptive"
    videoDuration := int64(11)

    // 1. Create video generation task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model:         modelID,
        GenerateAudio: model.NewOptBool(generateAudio),
        Ratio:         model.NewOptString(videoRatio),
        Duration:      model.NewOptInt64(videoDuration),
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
        Watermark:     model.NewOptBool(true),
    }

    createResp, err := client.CreateContentGenerationTask(
        ctx,
        createReq,
        arkruntime.WithExtraBody(map[string]interface{}{
            "omni_reference_task_type": "extend",
            "output_format":            "mov",
        }),
    )
    if err != nil {
        panic(fmt.Errorf("create content generation task: %w", err))
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
            panic(fmt.Errorf("get content generation task: %w", err))
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
            if getResp.Error.IsSet() {
                panic(fmt.Errorf("video generation task failed: %s: %s", getResp.Error.Value.Code, getResp.Error.Value.Message))
            }
            panic("video generation task failed")
        } else {
            fmt.Printf("Current status: %s, Retrying in 10 seconds... \n", status)
            time.Sleep(10 * time.Second)
        }
    }
}
```

#### Example: Generate a video from the first or first and last frames

In first\-frame/first\-last\-frame video generation tasks, Seedance 2.5 automatically keeps the output aspect ratio consistent with the first\-frame image specified by `first_frame` (`ratio` defaults to and only supports `adaptive`). You can specify the output duration or let the model determine it automatically (`duration` supports `[4, 30]` or `-1`).

|Input: text + first frame (aspect ratio 1:1) + last frame ||Output (aspect ratio 1:1) |
|---|---|---|
|![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/649cb2057eae48d6a6eec872d912c75c~tplv-goo7wpa0wc-image.image)<br><br>> Prompt: the girl in the image says "cheese" to the camera, with a 360\-degree orbiting camera shot |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/e39fd8e500a34bbdad50d06659c4ea6b~tplv-goo7wpa0wc-image.image) |[video](https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/3aa8c84b8a29408ab29e95992d61c559)<br><br><br>> **The output video aspect ratio automatically remains consistent with the 1:1 of the first\-frame image** |

**cURL**

```Bash
curl -X POST https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dreamina-seedance-2-5-260628",
    "content": [
        {
            "type": "text",
            "text": "A strawberry sandwich cookie slowly completes one full rotation while the camera moves smoothly around it and keeps the product centered."
        },
        {
            "type": "image_url",
            "image_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_reference1.png"
            },
            "role": "first_frame"
        },
        {
            "type": "image_url",
            "image_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_reference1.png"
            },
            "role": "last_frame"
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
                "text": "A strawberry sandwich cookie slowly completes one full rotation while the camera moves smoothly around it and keeps the product centered."
            },
            {
                "type": "image_url",
                "image_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_reference1.png"
                },
                "role": "first_frame",
            },
            {
                "type": "image_url",
                "image_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_reference1.png"
                },
                "role": "last_frame",
            },
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
    deadline = time.monotonic() + 30 * 60
    while time.monotonic() < deadline:
        get_result = client.content_generation.tasks.get(task_id=task_id)
        status = get_result.status
        if status == "succeeded":
            print("----- task succeeded -----")
            print(get_result)
            break
        if status == "failed":
            raise RuntimeError(f"Video generation task failed: {get_result.error}")

        print(f"Current status: {status}, Retrying after 10 seconds...")
        time.sleep(10)
    else:
        raise TimeoutError("Video generation task did not finish within 30 minutes")
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
        final String modelId = "dreamina-seedance-2-5-260628";
        // Text prompt
        final String prompt = "A strawberry sandwich cookie slowly completes one full rotation while the camera moves smoothly around it and keeps the product centered.";

        // Example resource URLs
        final String firstFrameUrl = "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_reference1.png";
        final String lastFrameUrl = "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_reference1.png";

        // Output video parameters
        final boolean generateAudio = true;
        final String videoRatio = "adaptive";
        final long videoDuration = 5L;

        System.out.println("----- create request -----");
        // Build request content
        List<ContentItem> contents = new ArrayList<>();

        // 1. Text prompt
        contents.add(ContentItem.builder()
                .type(ContentType.TEXT)
                .text(prompt)
                .build());

        // 2. First frame image
        contents.add(ContentItem.builder()
                .type(ContentType.IMAGE_URL)
                .imageUrl(ImageURL.builder()
                        .url(firstFrameUrl)
                        .build())
                .role("first_frame")
                .build());

        // 3. Last frame image
        contents.add(ContentItem.builder()
                .type(ContentType.IMAGE_URL)
                .imageUrl(ImageURL.builder()
                        .url(lastFrameUrl)
                        .build())
                .role("last_frame")
                .build());

        // Create video generation task
        CreateContentGenerationTaskRequest createRequest = CreateContentGenerationTaskRequest.builder()
                .generateAudio(generateAudio)
                .model(modelId)
                .content(contents)
                .ratio(videoRatio)
                .duration(videoDuration)
                .watermark(true)
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
            long deadlineNanos = System.nanoTime() + TimeUnit.MINUTES.toNanos(30);
            while (System.nanoTime() < deadlineNanos) {
                ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
                String status = getResponse.getStatus().toString();

                if ("succeeded".equalsIgnoreCase(status)) {
                    System.out.println("----- task succeeded -----");
                    System.out.println(getResponse);
                    return;
                } else if ("failed".equalsIgnoreCase(status)) {
                    throw new IllegalStateException(
                            "Video generation task failed: " + getResponse.getError());
                } else {
                    System.out.printf("Current status: %s, Retrying in 10 seconds...%n", status);
                    TimeUnit.SECONDS.sleep(10);
                }
            }
            throw new IllegalStateException(
                    "Video generation task did not finish within 30 minutes");
        } catch (InterruptedException ie) {
            Thread.currentThread().interrupt();
            throw new RuntimeException("Polling interrupted", ie);
        } catch (Exception e) {
            throw new RuntimeException("Error occurred while polling", e);
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
    ctx, cancel := context.WithTimeout(context.Background(), 30*time.Minute)
    defer cancel()

    // Model ID
    modelID := "dreamina-seedance-2-5-260628"
    // Text prompt
    prompt := "A strawberry sandwich cookie slowly completes one full rotation while the camera moves smoothly around it and keeps the product centered."

    // Example resource URLs
    firstFrameURL := "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_reference1.png"
    lastFrameURL := "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_reference1.png"

    // Output video parameters
    generateAudio := true
    videoRatio := "adaptive"
    videoDuration := int64(5)

    // 1. Create video generation task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model:         modelID,
        GenerateAudio: model.NewOptBool(generateAudio),
        Ratio:         model.NewOptString(videoRatio),
        Duration:      model.NewOptInt64(videoDuration),
        Content: []model.ContentItem{
            {
                Type: model.ContentTypeText,
                Text: model.NewOptString(prompt),
            },
            {
                Type: model.ContentTypeImageURL,
                ImageURL: model.NewOptImageURL(model.ImageURL{
                    URL: firstFrameURL,
                }),
                Role: model.NewOptString("first_frame"),
            },
            {
                Type: model.ContentTypeImageURL,
                ImageURL: model.NewOptImageURL(model.ImageURL{
                    URL: lastFrameURL,
                }),
                Role: model.NewOptString("last_frame"),
            },
        },
        Watermark:     model.NewOptBool(true),
    }

    createResp, err := client.CreateContentGenerationTask(ctx, createReq)
    if err != nil {
        panic(fmt.Errorf("create content generation task: %w", err))
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
            panic(fmt.Errorf("get content generation task: %w", err))
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
            if getResp.Error.IsSet() {
                panic(fmt.Errorf("video generation task failed: %s: %s", getResp.Error.Value.Code, getResp.Error.Value.Message))
            }
            panic("video generation task failed")
        } else {
            fmt.Printf("Current status: %s, Retrying in 10 seconds... \n", status)
            time.Sleep(10 * time.Second)
        }
    }
}
```

### Generate multilingual videos natively

Seedance 2.5 natively supports prompt input and videos with audio generation in multiple languages, including Chinese, English, Spanish, Indonesian, Malay, Thai, Arabic, Portuguese, Vietnamese, Japanese, and Korean.

|Input: text + image |Output |
|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_multi_language_input.png)<br><br>> Prompt: cinematic hip\-hop / rap music video, lyrics covering 8 languages... (see the full prompt in the code example below for details) |[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/resources/seedacne2.5_muti_language_output.mp4)<br> |

**cURL**

```Bash
curl -X POST https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dreamina-seedance-2-5-260628",
    "content": [
        {
            "type": "text",
            "text": "A cinematic hip-hop / rap music video with photorealistic texture, a premium tone, and a seaside setting. Build the image from @Image1: a band performs on a golden beach by the shore where waves break - a lead vocalist grips a microphone and sings passionately, with the mic stand planted in wet sand; one guitarist stands on the left side of the frame, another guitarist on the right side, and a drummer sits behind a drum kit in the back, playing. A vast coastline spreads behind them, rolling waves surge in layers, and a huge warm golden-hour sunset slants across the beach, sparkling on the water. Sea mist and salty humidity float in the air. The lead vocalist in a red tracksuit raps rhythmically toward the camera - lip shapes and jaw movement precisely match every word, and the head nods hard to the beat, driving the entire flow. The musicians sway and groove with the rhythm while waves crash behind them. This is a bright, energetic rap track - fast delivery, confident attitude, and a powerful beat. Use HARD CUTS on the beat, with dual contrast on every cut (both shot size and camera type change at the same time). Lyrics (the lead vocalist sings the following \"hello\" in each language in order, with precise lip sync): English: \"Hello\" Chinese: \"你好\" Japanese: \"こんにちは\" Korean: \"안녕하세요\" Portuguese: \"Olá\" Thai: \"สวัสดี\" Spanish: \"Hola\" Arabic: \"مرحبا\" Shot 1 [0:00-0:03] - Low-angle extreme wide establishing shot. A Steadicam slowly pushes in through golden sunset light and sea mist, with waves surging behind the band. Lyric line 1 (English \"Hello\"). Hard cut. Shot 2 [0:03-0:05] - Close-up of the red-tracksuit lead vocalist rapping into the camera, entering with a handheld whip pan, with the sparkling ocean blurred in the background. Lyric line 2 (Chinese \"你好\"). Hard cut. Shot 3 [0:05-0:08] - Macro insert shot, locked-off camera, a guitarist'\''s fingers rapidly picking the strings as grains of sand and salty mist sweep past the foreground. Lyric line 3 (Japanese \"こんにちは\"). Hard cut. Shot 4 [0:08-0:10] - 3/4 side medium shot of one musician, slow prowling orbit, with instrument hardware and wet highlights reflecting the low slanting sunset over the sea. Lyric line 4 (Korean \"안녕하세요\"). Hard cut. Shot 5 [0:10-0:13] - A musician by the shore; a fast lateral dolly move sweeps past them, they turn toward the camera, and a wave breaks behind them. Lyric line 5 (Portuguese \"Olá\"). Hard cut. Shot 6 [0:13-0:15] - The drummer by the water, handheld fast tilt-up, sea wind and spray blowing through their hair as they groove and strike the drums on the beat. Lyric line 6 (Thai \"สวัสดี\"). Hard cut. Shot 7 [0:15-0:18] - Tight aggressive push-in on the red-tracksuit lead vocalist at the height of the flow, aggressive handheld, the dusk ocean behind them silhouetting the band. Lyric line 7 (Spanish \"Hola\"). Hard cut. Shot 8 [0:18-0:20] - Heroic extreme wide shot of the full band, aggressive handheld push-in. The vocalist and musicians step toward the camera on the beat, waves break, and golden sunset light blooms behind the whole band. Lyric line 8 (Arabic \"مرحبا\"). White balance 4000K, teal-and-amber color grade, 35mm, shallow depth of field, film grain, diffused sea mist, golden-hour glow. Solid, premium, high-end texture. Rhythmic rap performance, precise lip sync, head nodding to the beat. No subtitles, no text overlays, no dissolve transitions, no duplicated people, only hard cuts. Total duration 20 seconds."
        },
        {
            "type": "image_url",
            "image_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_multi_language_input.png"
            },
            "role": "reference_image"
        }
    ],
    "generate_audio": true,
    "ratio": "16:9",
    "duration": 20,
    "watermark": true
}'
```

**Python**

```Python
import os
import time
# Install SDK: python -m pip install --upgrade arkruntime
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
        model="dreamina-seedance-2-5-260628", # Replace with Model ID
        content=[
            {
                "type": "text",
                "text": "A cinematic hip-hop / rap music video with photorealistic texture, a premium tone, and a seaside setting. Build the image from @Image1: a band performs on a golden beach by the shore where waves break - a lead vocalist grips a microphone and sings passionately, with the mic stand planted in wet sand; one guitarist stands on the left side of the frame, another guitarist on the right side, and a drummer sits behind a drum kit in the back, playing. A vast coastline spreads behind them, rolling waves surge in layers, and a huge warm golden-hour sunset slants across the beach, sparkling on the water. Sea mist and salty humidity float in the air. The lead vocalist in a red tracksuit raps rhythmically toward the camera - lip shapes and jaw movement precisely match every word, and the head nods hard to the beat, driving the entire flow. The musicians sway and groove with the rhythm while waves crash behind them. This is a bright, energetic rap track - fast delivery, confident attitude, and a powerful beat. Use HARD CUTS on the beat, with dual contrast on every cut (both shot size and camera type change at the same time). Lyrics (the lead vocalist sings the following \"hello\" in each language in order, with precise lip sync): English: \"Hello\" Chinese: \"你好\" Japanese: \"こんにちは\" Korean: \"안녕하세요\" Portuguese: \"Olá\" Thai: \"สวัสดี\" Spanish: \"Hola\" Arabic: \"مرحبا\" Shot 1 [0:00-0:03] - Low-angle extreme wide establishing shot. A Steadicam slowly pushes in through golden sunset light and sea mist, with waves surging behind the band. Lyric line 1 (English \"Hello\"). Hard cut. Shot 2 [0:03-0:05] - Close-up of the red-tracksuit lead vocalist rapping into the camera, entering with a handheld whip pan, with the sparkling ocean blurred in the background. Lyric line 2 (Chinese \"你好\"). Hard cut. Shot 3 [0:05-0:08] - Macro insert shot, locked-off camera, a guitarist's fingers rapidly picking the strings as grains of sand and salty mist sweep past the foreground. Lyric line 3 (Japanese \"こんにちは\"). Hard cut. Shot 4 [0:08-0:10] - 3/4 side medium shot of one musician, slow prowling orbit, with instrument hardware and wet highlights reflecting the low slanting sunset over the sea. Lyric line 4 (Korean \"안녕하세요\"). Hard cut. Shot 5 [0:10-0:13] - A musician by the shore; a fast lateral dolly move sweeps past them, they turn toward the camera, and a wave breaks behind them. Lyric line 5 (Portuguese \"Olá\"). Hard cut. Shot 6 [0:13-0:15] - The drummer by the water, handheld fast tilt-up, sea wind and spray blowing through their hair as they groove and strike the drums on the beat. Lyric line 6 (Thai \"สวัสดี\"). Hard cut. Shot 7 [0:15-0:18] - Tight aggressive push-in on the red-tracksuit lead vocalist at the height of the flow, aggressive handheld, the dusk ocean behind them silhouetting the band. Lyric line 7 (Spanish \"Hola\"). Hard cut. Shot 8 [0:18-0:20] - Heroic extreme wide shot of the full band, aggressive handheld push-in. The vocalist and musicians step toward the camera on the beat, waves break, and golden sunset light blooms behind the whole band. Lyric line 8 (Arabic \"مرحبا\"). White balance 4000K, teal-and-amber color grade, 35mm, shallow depth of field, film grain, diffused sea mist, golden-hour glow. Solid, premium, high-end texture. Rhythmic rap performance, precise lip sync, head nodding to the beat. No subtitles, no text overlays, no dissolve transitions, no duplicated people, only hard cuts. Total duration 20 seconds."
            },
            {
                "type": "image_url",
                "image_url": {
                    "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_multi_language_input.png"
                },
                "role": "reference_image",
            },
        ],
        generate_audio=True,
        ratio="16:9",
        duration=20,
        watermark=True,
    )
    print(create_result)


    # Polling query section
    print("----- polling task status -----")
    task_id = create_result.id
    deadline = time.monotonic() + 30 * 60
    while time.monotonic() < deadline:
        get_result = client.content_generation.tasks.get(task_id=task_id)
        status = get_result.status
        if status == "succeeded":
            print("----- task succeeded -----")
            print(get_result)
            break
        elif status == "failed":
            raise RuntimeError(f"Video generation task failed: {get_result.error}")
        else:
            print(f"Current status: {status}, Retrying after 30 seconds...")
            time.sleep(30)
    else:
        raise TimeoutError("Video generation task did not finish within 30 minutes")
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
        final String modelId = "dreamina-seedance-2-5-260628";
        // Text prompt
        final String prompt = "A cinematic hip-hop / rap music video with photorealistic texture, a premium tone, and a seaside setting. Build the image from @Image1: a band performs on a golden beach by the shore where waves break - a lead vocalist grips a microphone and sings passionately, with the mic stand planted in wet sand; one guitarist stands on the left side of the frame, another guitarist on the right side, and a drummer sits behind a drum kit in the back, playing. A vast coastline spreads behind them, rolling waves surge in layers, and a huge warm golden-hour sunset slants across the beach, sparkling on the water. Sea mist and salty humidity float in the air. The lead vocalist in a red tracksuit raps rhythmically toward the camera - lip shapes and jaw movement precisely match every word, and the head nods hard to the beat, driving the entire flow. The musicians sway and groove with the rhythm while waves crash behind them. This is a bright, energetic rap track - fast delivery, confident attitude, and a powerful beat. Use HARD CUTS on the beat, with dual contrast on every cut (both shot size and camera type change at the same time). Lyrics (the lead vocalist sings the following \"hello\" in each language in order, with precise lip sync): English: \"Hello\" Chinese: \"你好\" Japanese: \"こんにちは\" Korean: \"안녕하세요\" Portuguese: \"Olá\" Thai: \"สวัสดี\" Spanish: \"Hola\" Arabic: \"مرحبا\" Shot 1 [0:00-0:03] - Low-angle extreme wide establishing shot. A Steadicam slowly pushes in through golden sunset light and sea mist, with waves surging behind the band. Lyric line 1 (English \"Hello\"). Hard cut. Shot 2 [0:03-0:05] - Close-up of the red-tracksuit lead vocalist rapping into the camera, entering with a handheld whip pan, with the sparkling ocean blurred in the background. Lyric line 2 (Chinese \"你好\"). Hard cut. Shot 3 [0:05-0:08] - Macro insert shot, locked-off camera, a guitarist's fingers rapidly picking the strings as grains of sand and salty mist sweep past the foreground. Lyric line 3 (Japanese \"こんにちは\"). Hard cut. Shot 4 [0:08-0:10] - 3/4 side medium shot of one musician, slow prowling orbit, with instrument hardware and wet highlights reflecting the low slanting sunset over the sea. Lyric line 4 (Korean \"안녕하세요\"). Hard cut. Shot 5 [0:10-0:13] - A musician by the shore; a fast lateral dolly move sweeps past them, they turn toward the camera, and a wave breaks behind them. Lyric line 5 (Portuguese \"Olá\"). Hard cut. Shot 6 [0:13-0:15] - The drummer by the water, handheld fast tilt-up, sea wind and spray blowing through their hair as they groove and strike the drums on the beat. Lyric line 6 (Thai \"สวัสดี\"). Hard cut. Shot 7 [0:15-0:18] - Tight aggressive push-in on the red-tracksuit lead vocalist at the height of the flow, aggressive handheld, the dusk ocean behind them silhouetting the band. Lyric line 7 (Spanish \"Hola\"). Hard cut. Shot 8 [0:18-0:20] - Heroic extreme wide shot of the full band, aggressive handheld push-in. The vocalist and musicians step toward the camera on the beat, waves break, and golden sunset light blooms behind the whole band. Lyric line 8 (Arabic \"مرحبا\"). White balance 4000K, teal-and-amber color grade, 35mm, shallow depth of field, film grain, diffused sea mist, golden-hour glow. Solid, premium, high-end texture. Rhythmic rap performance, precise lip sync, head nodding to the beat. No subtitles, no text overlays, no dissolve transitions, no duplicated people, only hard cuts. Total duration 20 seconds.";

        // Example resource URLs
        final String refImage1 = "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_multi_language_input.png";

        // Output video parameters
        final boolean generateAudio = true;
        final String videoRatio = "16:9";
        final long videoDuration = 20L;

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
                        .url(refImage1)
                        .build())
                .role("reference_image")
                .build());

        // Create video generation task
        CreateContentGenerationTaskRequest createRequest = CreateContentGenerationTaskRequest.builder()
                .generateAudio(generateAudio)
                .model(modelId)
                .content(contents)
                .ratio(videoRatio)
                .duration(videoDuration)
                .watermark(true)
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
            long deadlineNanos = System.nanoTime() + TimeUnit.MINUTES.toNanos(30);
            while (System.nanoTime() < deadlineNanos) {
                ContentGenerationTask getResponse = service.getContentGenerationTask(getRequest);
                String status = getResponse.getStatus().toString();

                if ("succeeded".equalsIgnoreCase(status)) {
                    System.out.println("----- task succeeded -----");
                    System.out.println(getResponse);
                    return;
                } else if ("failed".equalsIgnoreCase(status)) {
                    throw new IllegalStateException(
                            "Video generation task failed: " + getResponse.getError());
                } else {
                    System.out.printf("Current status: %s, Retrying in 10 seconds...%n", status);
                    TimeUnit.SECONDS.sleep(10);
                }
            }
            throw new IllegalStateException(
                    "Video generation task did not finish within 30 minutes");
        } catch (InterruptedException ie) {
            Thread.currentThread().interrupt();
            throw new RuntimeException("Polling interrupted", ie);
        } catch (Exception e) {
            throw new RuntimeException("Error occurred while polling", e);
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
    ctx, cancel := context.WithTimeout(context.Background(), 30*time.Minute)
    defer cancel()

    // Model ID
    modelID := "dreamina-seedance-2-5-260628"
    // Text prompt
    prompt := "A cinematic hip-hop / rap music video with photorealistic texture, a premium tone, and a seaside setting. Build the image from @Image1: a band performs on a golden beach by the shore where waves break - a lead vocalist grips a microphone and sings passionately, with the mic stand planted in wet sand; one guitarist stands on the left side of the frame, another guitarist on the right side, and a drummer sits behind a drum kit in the back, playing. A vast coastline spreads behind them, rolling waves surge in layers, and a huge warm golden-hour sunset slants across the beach, sparkling on the water. Sea mist and salty humidity float in the air. The lead vocalist in a red tracksuit raps rhythmically toward the camera - lip shapes and jaw movement precisely match every word, and the head nods hard to the beat, driving the entire flow. The musicians sway and groove with the rhythm while waves crash behind them. This is a bright, energetic rap track - fast delivery, confident attitude, and a powerful beat. Use HARD CUTS on the beat, with dual contrast on every cut (both shot size and camera type change at the same time). Lyrics (the lead vocalist sings the following \"hello\" in each language in order, with precise lip sync): English: \"Hello\" Chinese: \"你好\" Japanese: \"こんにちは\" Korean: \"안녕하세요\" Portuguese: \"Olá\" Thai: \"สวัสดี\" Spanish: \"Hola\" Arabic: \"مرحبا\" Shot 1 [0:00-0:03] - Low-angle extreme wide establishing shot. A Steadicam slowly pushes in through golden sunset light and sea mist, with waves surging behind the band. Lyric line 1 (English \"Hello\"). Hard cut. Shot 2 [0:03-0:05] - Close-up of the red-tracksuit lead vocalist rapping into the camera, entering with a handheld whip pan, with the sparkling ocean blurred in the background. Lyric line 2 (Chinese \"你好\"). Hard cut. Shot 3 [0:05-0:08] - Macro insert shot, locked-off camera, a guitarist's fingers rapidly picking the strings as grains of sand and salty mist sweep past the foreground. Lyric line 3 (Japanese \"こんにちは\"). Hard cut. Shot 4 [0:08-0:10] - 3/4 side medium shot of one musician, slow prowling orbit, with instrument hardware and wet highlights reflecting the low slanting sunset over the sea. Lyric line 4 (Korean \"안녕하세요\"). Hard cut. Shot 5 [0:10-0:13] - A musician by the shore; a fast lateral dolly move sweeps past them, they turn toward the camera, and a wave breaks behind them. Lyric line 5 (Portuguese \"Olá\"). Hard cut. Shot 6 [0:13-0:15] - The drummer by the water, handheld fast tilt-up, sea wind and spray blowing through their hair as they groove and strike the drums on the beat. Lyric line 6 (Thai \"สวัสดี\"). Hard cut. Shot 7 [0:15-0:18] - Tight aggressive push-in on the red-tracksuit lead vocalist at the height of the flow, aggressive handheld, the dusk ocean behind them silhouetting the band. Lyric line 7 (Spanish \"Hola\"). Hard cut. Shot 8 [0:18-0:20] - Heroic extreme wide shot of the full band, aggressive handheld push-in. The vocalist and musicians step toward the camera on the beat, waves break, and golden sunset light blooms behind the whole band. Lyric line 8 (Arabic \"مرحبا\"). White balance 4000K, teal-and-amber color grade, 35mm, shallow depth of field, film grain, diffused sea mist, golden-hour glow. Solid, premium, high-end texture. Rhythmic rap performance, precise lip sync, head nodding to the beat. No subtitles, no text overlays, no dissolve transitions, no duplicated people, only hard cuts. Total duration 20 seconds."

    // Example resource URLs
    refImage1 := "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/seedance2.5_multi_language_input.png"

    // Output video parameters
    generateAudio := true
    videoRatio := "16:9"
    videoDuration := int64(20)

    // 1. Create video generation task
    fmt.Println("----- create request -----")
    createReq := &model.CreateContentGenerationTaskRequest{
        Model:         modelID,
        GenerateAudio: model.NewOptBool(generateAudio),
        Ratio:         model.NewOptString(videoRatio),
        Duration:      model.NewOptInt64(videoDuration),
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
        },
        Watermark:     model.NewOptBool(true),
    }

    createResp, err := client.CreateContentGenerationTask(ctx, createReq)
    if err != nil {
        panic(fmt.Errorf("create content generation task: %w", err))
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
            panic(fmt.Errorf("get content generation task: %w", err))
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
            if getResp.Error.IsSet() {
                panic(fmt.Errorf("video generation task failed: %s: %s", getResp.Error.Value.Code, getResp.Error.Value.Message))
            }
            panic("video generation task failed")
        } else {
            fmt.Printf("Current status: %s, Retrying in 10 seconds... \n", status)
            time.Sleep(10 * time.Second)
        }
    }
}
```

## More capabilities

Seedance 2.5 also supports the following basic capabilities. For detailed usage and code examples, see the corresponding links:

* [Text-to-video](video-generation-tutorial.md#4e74bcee): Generate a video from a text prompt.

* [Draft mode](seedance-2-5.md#2.5_draft_mode): Generate a 480p Draft video to validate the result before generating a high\-quality final video.

## Draft mode

**Draft mode is a two\-step video generation method that lets you quickly validate the result before generating a high\-quality final video.**  After you enable this feature, you can quickly generate a low\-resolution preview video to validate key elements such as the scene structure, shot scheduling, subject motion, and prompt intent. After confirming that the result meets expectations, use the Draft video task ID to generate a high\-quality final video.

> **Draft video task ID validity period**

* > A Draft video task ID has a validity period, measured from its `created_at` timestamp. The ID automatically becomes invalid when this period expires.

* > Draft video task IDs created at or after 10:00 on October 28, 2026 (UTC+8) are valid for 14 days. IDs created before this time remain valid for seven days. After a Draft video task ID expires, it cannot be used to generate a final video.

|Input |Draft video |Final video |
|---|---|---|
|![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/ebb5217645b04cfc94209a6f7d36a523~tplv-goo7wpa0wc-image.image)<br><br>> Prompt: A girl holds a fox. She opens her eyes and looks gently at the camera while the fox hugs her. The camera slowly pulls back, the wind blows through the girl's hair, and the sound of the wind can be heard. |[video](https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/7c190b3a0ed34b29bc1192acbce2f4d2)<br><br><br>> Generate a Draft video to preview and confirm the result. |[video](https://p9-arcosite.byteimg.com/obj/tos-cn-i-goo7wpa0wc/a82cd582a5d54f34a8adec10f2815081)<br><br><br>> Reuse key parameters from the Draft video, including the model, prompt, input assets, seed, audio settings, aspect ratio, and duration, to generate a consistent final video. |

### Step 1: Generate a Draft video

When calling `POST /contents/generations/tasks` to create a video generation task, set `draft=true` to enable Draft mode.

> **Note**

* > Only 480p Draft videos can be generated. Setting another resolution causes an error.

* > The token consumption and token rate for a 480p Draft video are the same as those for a normal 480p video.

* > A Draft video task ID has a validity period, measured from its `created_at` timestamp. The ID automatically becomes invalid when this period expires. Draft video task IDs created at or after 10:00 on October 28, 2026 (UTC+8) are valid for 14 days. IDs created before this time remain valid for seven days. After a Draft video task ID expires, it cannot be used to generate a final video.

**cURL**

1. Create a Draft video generation task.

> After the request succeeds, the system returns a task ID. This is the Draft video task ID used to generate the final video.

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dreamina-seedance-2-5-260628",
    "content": [
      {
        "type": "text",
        "text": "A girl holds a fox. She opens her eyes and looks gently at the camera as the camera slowly pulls back."
      },
      {
        "type": "image_url",
        "image_url": {
          "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/video-generation/i2v_foxrgirl.png"
        }
      }
    ],
    "duration": 5,
    "ratio": "adaptive",
    "resolution": "480p",
    "output_format": "mov",
    "service_tier": "default",
    "draft": true,
    "watermark": true
  }'
```

2. Use the Draft video task ID to retrieve the generation status and result.

> After the task status changes to `succeeded`, download the generated Draft video from `content.video_url`. After confirming that the Draft video meets expectations, proceed to generate the final video.

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks/cgt-2026****-draft \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

**Python**

Create a Draft video generation task and poll for its status and result. When the task status changes to `succeeded`, confirm that the Draft video in `content.video_url` meets expectations, then use the task ID to generate a final video.

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

Create a Draft video generation task and poll for its status and result. When the task status changes to `succeeded`, confirm that the Draft video in `content.video_url` meets expectations, then use the task ID to generate a final video.

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

Create a Draft video generation task and poll for its status and result. When the task status changes to `succeeded`, confirm that the Draft video in `content.video_url` meets expectations, then use the task ID to generate a final video.

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

### Step 2: Generate the final video

After confirming that the Draft video meets expectations, use the Draft video task ID returned in Step 1 to call `POST /contents/generations/tasks` again and generate the final video.

> **Note**

* > When generating a final video from a Draft video, Dreamina Seedance 2.5 currently supports only 1080p output. Setting another resolution causes an error.

* > A Draft video task ID has a validity period, measured from its `created_at` timestamp. The ID automatically becomes invalid when this period expires. Draft video task IDs created at or after 10:00 on October 28, 2026 (UTC+8) are valid for 14 days. IDs created before this time remain valid for seven days. After a Draft video task ID expires, it cannot be used to generate a final video.

* > Step 1 and Step 2 tasks are billed independently:

   * > Step 1: Generate a Draft video. This step is billed as a 480p video.

   * > Step 2: Generate the final video from the Draft video. This step is billed based on the target resolution:

      * > The token rate is **determined by whether Step 1 includes input video**.

      * > When calculating token consumption, **the input video duration is calculated based on the input video in Step 1. The Draft video is not counted**.

When generating a final video from a Draft video, some parameters must retain fixed values, some are automatically reused by the model, and others can be specified again. Configure the parameters according to the following rules to avoid request errors.

|Type |Parameter |Rule |
|---|---|---|
|Fixed\-value parameters |* Model ID (`model`)<br><br>* Draft mode (`draft`)<br><br>* Resolution (`resolution`) |* `model` must match the model used to create the Draft task.<br><br>* Omit `draft` or set it to `false`.<br><br>* `resolution` is required and only supports `1080p`. |
|Automatically reused parameters |* Prompt (`content.text`)<br><br>* Reference image, video, and audio (`content.image_url`, `content.video_url`, `content.audio_url`)<br><br>* Duration (`duration`)<br><br>* Aspect ratio (`ratio`)<br><br>* Seed (`seed`)<br><br>* Generate audio (`generate_audio`)<br><br>* Task type (`omni_reference_task_type`) |* The model automatically reuses the values used to create the Draft task.<br><br>* **Do not provide these parameters again. An error occurs even if their values match those used in the Draft task.**  |
|Parameters that can be specified again |* Return last frame (`return_last_frame`)<br><br>* Output format (`output_format`)<br><br>* Watermark (`watermark`)<br><br>* Service tier (`service_tier`)<br><br>* Task expiration threshold (`execution_expires_after`)<br><br>* Execution priority (`priority`)<br><br>* Callback URL (`callback_url`)<br><br>* User identifier (`safety_identifier`) |* If omitted, the model default is used instead of the Draft video's actual value.<br><br>* Values must comply with the restrictions for Dreamina Seedance 2.5. For details, see [Create a video generation task API](create-video-generation-task-api.md). |

**cURL**

1. Create a video generation task based on `content.draft_task.id`.

   ```Bash
   curl https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer $ARK_API_KEY" \
     -d '{
       "model": "dreamina-seedance-2-5-260628",
       "content": [
         {
           "type": "draft_task",
           "draft_task": {
             "id": "<DRAFT_TASK_ID>"
           }
         }
       ],
       "resolution": "1080p",
       "output_format": "mov",
       "watermark": true
     }'
   ```

2. Use the video task ID to retrieve the generation status and result.

> After the task status changes to `succeeded`, download the generated video from `content.video_url`.

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks/cgt-2026****-final \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

**Python**

Replace `<DRAFT_TASK_ID>` with the Draft video task ID whose status is `succeeded` in Step 1. Create a final video generation task and poll for the result. When the task status changes to `succeeded`, download the final video from `content.video_url`.

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

Replace `<DRAFT_TASK_ID>` with the Draft video task ID whose status is `succeeded` in Step 1. Create a final video generation task and poll for the result. When the task status changes to `succeeded`, download the final video from `content.video_url`.

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

Replace `<DRAFT_TASK_ID>` with the Draft video task ID whose status is `succeeded` in Step 1. Create a final video generation task and poll for the result. When the task status changes to `succeeded`, download the final video from `content.video_url`.

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

## Customize video output specifications

Control the specifications of the output video through API parameters, including resolution, aspect ratio, duration, output format, whether to include a watermark, and more.

* **resolution**: Specifies the resolution of the output video.

* **ratio**: Specifies the aspect ratio of the output video.

* **duration**: Specifies the duration of the output video.

* **output_format**: Specifies the format of the output video.

* **watermark**: Specifies whether to add a watermark to the output video.

### Resolution

Specify the resolution of the output video through the **resolution** parameter.

* Default value: `720p`

* Optional values:

   * `480p` (8\-bit color depth)

   * `720p` (8\-bit color depth)

   * `1080p` (10\-bit color depth)

> **Note**

> **Dreamina Seedance 2.5 1080p output uses 10\-bit color depth and H.265/HEVC encoding.**

* > Compared with standard 8\-bit color depth, 10\-bit color depth preserves richer color gradations and smoother tonal transitions, making it suitable for professional video production and HDR content.

* > H.265/HEVC encoding may not be compatible with some playback environments. If playback fails, upgrade your system, use another device, or try a [player](seedance-2-0.md#4k_player) such as VLC, mpv, or QuickTime Player.

```Json
{
    "resolution": "720p"
}
```

### Aspect ratio

Specify the aspect ratio of the output video through the **ratio** parameter.

* Default value: `adaptive`. The model automatically adapts the video aspect ratio based on the input prompt and reference assets.

* Optional values: `16:9`, `4:3`, `1:1`, `3:4`, `9:16`, `21:9`, `adaptive`

> **Rules for adaptive**

* > **Video editing / video extension**: The model selects the video to edit or extend based on the prompt intent, then automatically keeps the output aspect ratio consistent with the selected video.

* > **First\-frame / first\-last\-frame video generation**: Keep the output aspect ratio consistent with the first\-frame image specified by `first_frame`.

* > **Text\-to\-video/reference\-to\-video**: Select the most suitable aspect ratio from the optional aspect ratios (`16:9`, `4:3`, `1:1`, `3:4`, `9:16`, `21:9`) based on the prompt.

> **Note**

> For Seedance 2.5, in video editing, video extension, and first\-frame / first\-last\-frame video generation tasks, only `ratio` can be configured as `adaptive`; a specific aspect ratio cannot be specified.

```Json
{
    "ratio": "16:9"
}
```

Width and height pixel values corresponding to different aspect ratios:

|Resolution |Aspect Ratio |Pixel Dimensions (Width × Height) |
|---|---|---|
|480p |16:9 |854×480 |
||4:3 |752×560 |
||1:1 |640×640 |
||3:4 |560×752 |
||9:16 |480×854 |
||21:9 |992×432 |
|720p |16:9 |1280×720 |
||4:3 |1112×834 |
||1:1 |960×960 |
||3:4 |834×1112 |
||9:16 |720×1280 |
||21:9 |1470×630 |
|1080p |16:9 |1920×1080 |
||4:3 |1664×1248 |
||1:1 |1440×1440 |
||3:4 |1248×1664 |
||9:16 |1080×1920 |
||21:9 |2206×946 |

### Video duration

Specify the duration of the generated video (unit: seconds) through the **duration** parameter.

* Default value: `-1`. The model automatically adapts the video duration based on the input prompt and reference assets.

* Value range: `[4, 30]` or `-1`

> **Rules for duration = \-1**

* > Video editing tasks: The model selects the video to edit based on the prompt intent, then automatically keeps the output duration approximately the same as the selected video.

   * > The output video may be a non\-integer number of seconds

   * > The output video may be slightly shorter than the selected video, with a difference of no more than 0.4 seconds

* > Other video generation tasks: The model autonomously selects an appropriate video length (integer seconds) within the `[4, 30]` range.

> **Note**

> For Seedance 2.5 video editing tasks, `duration` only supports `-1`; you cannot specify a duration. The input video to be edited must be `[4, 30]` seconds long. Otherwise, an error is returned.

```Json
{
   "duration": 10
}
```

### Output format

Control the format of the output video through the **output_format** parameter.

* `mp4` (default): A general\-purpose format with the best compatibility. It uses standard color precision and can be played on webpages, mobile devices, media players, and distribution platforms.

* `mov`: A high color precision format for professional scenarios. It better maintains consistency of image color and brightness, and is suitable for professional post\-production processing with high requirements for color reproduction, such as color grading, keying, and compositing. It is recommended to use the mov format as input and output in video editing and video extension scenarios.

> **mov playback compatibility**

> The mov format uses professional encoding (H.264 video encoding + yuv444p chroma sampling + PCM audio encoding), and some players may not be compatible. The following are common players that support playing the mov format:

> |Player |macOS |Windows |
> |---|---|---|
> |IINA |✓ |✕ |
> |VLC |✓ |✓ |
> |mpv |✓ |✓ |
> |ffplay |✓ |✓ |

```Json
{
    "output_format": "mov"
}
```

### Add a watermark to the video

Through the **watermark** parameter, control whether to add a watermark to the generated video.

* `true`: Add a watermark indicating this is an AI\-generated result to the lower\-right corner of the video.

* false (default): Do not add a watermark.

```Json
{
    "watermark": true
}
```

## Create portrait videos with ease

Seedance 2.5 supports the same convenient creation features as the Seedance 2.0 series. For the detailed tutorial, see [Create portrait videos with Dreamina Seedance models](seedance-portrait-asset-guide.md):

|Solution |Introduction |
|---|---|
|[Use trusted model outputs as input assets](seedance-portrait-asset-guide.md#trust-model-output) |Original outputs containing human faces generated by some models under this account can be reused as input assets for secondary creation with Seedance 2.5 without triggering input moderation blocking. |
|[Use preset digital characters](seedance-portrait-asset-guide.md#preset-avatar) |The platform has a preset digital characters library, providing creators with free, compliant, rich, and diverse portrait assets. It is suitable for scenarios that require real\-person\-style faces but do not need to specify a particular person, and that pursue zero compliance risk and fast creation. |
|[Use authorized real-person assets](seedance-portrait-asset-guide.md#authorized-real-person) |Supports using authorized real\-person portrait assets for video generation. |

## Prompt tips

### Prompt skill

The platform provides the **Seedance 2.5 prompt optimization skill**, making it convenient for you to tune prompts.

* **Install in a local project with NPX**:

   ```bash
   npx --yes skills@latest add \
     "https://arkdocs-en.tos-ap-southeast-1.volces.com/skills/" \
     --skill sd25-pe \
     --yes
   ```

* **Usage**: In an AI chat, enter `/sd25-pe + your prompt` to optimize the prompt.

### Prompt rules

* **Follow the basic formula**: Organize the prompt in the order of "subject + action/event + scene and environment + visual style + camera movement/shot cuts + sound"; unnecessary parts may be omitted.

* **Clarify asset responsibilities**: Use `@Image 1`, `@Video 1`, and `@Audio 1` to refer to reference assets. Specify what each asset provides, such as appearance, action, or timbre, and what should not be referenced.

* **Use special characters to distinguish sounds**: Use `()` for music, `<>` for sound effects, `{}` for dialogue, and `【】` for subtitles. For non\-Chinese dialogue, it is recommended to specify the language before the dialogue.

For general prompt\-writing methods and templates, see the [Dreamina Seedance 2.5 prompt guide](seedance-2-5-prompt-guide.md).

## Usage limits

### Multimodal input

> **warning**

> Seedance 2.5 does not support directly uploading reference images or videos containing real human faces.

> To make it easier for creators to use portraits, the platform has launched a series of solutions. For details, see [Create portrait videos with Dreamina Seedance models](seedance-portrait-asset-guide.md).

**Image requirements**

* Input methods: image URL, image Base64 string, asset ID.

* Image formats: jpeg, png, webp, bmp, tiff, gif, heic, heif.

* Single image dimensions:

   * Aspect ratio (width/height): [0.4, 2.5]

   * Width and height length (px): [300, 6000]

* Size: A single image must be smaller than 30 MB. The request body size must not exceed 64 MB. Do not use Base64 strings for large files.

* Number of images:

   * Image\-to\-video \- first frame: 1 image

   * Image\-to\-video \- first and last frames: 2 images

   * Omni reference\-to\-video: 1–30 images

**Video requirements**

* Input methods: video URL, asset ID.

* Video formats: mp4, mov. For supported encoding formats, see the table below.

* Resolution: 480p, 720p, 1080p, 4k

* Duration:

   * Non\-video\-editing tasks: Each video must be [2, 30] s long.

   * [Video editing tasks](seedance-2-5.md#2.5_task_type_intro): Each video must be [4, 30] s long.

   * You can provide up to 10 reference videos with a total duration of no more than 30 s.

* Single video dimensions:

   * Aspect ratio (width/height): [0.4, 2.5]

   * Width and height length (px): [300, 6000]

   * Total number of pixels: [614×664=407696, 3326×2494=8295044], meaning the product of the width and height meets the interval requirement of [407696, 8295044].

* Size: A single video must not exceed 200 MB.

* Frame rate (FPS): [24, 60]

|**Container Format** |**Common File Extension** |**MIME** |**Supported Encoding** |
|---|---|---|---|
|MP4 |.mp4 |video/mp4 |Video: H.264/AVC, H.265/HEVC<br><br>Audio: AAC, MP3 |
|QuickTime |.mov |video/quicktime |Video: H.264/AVC, H.265/HEVC<br><br>Audio: AAC, MP3, PCM |

**Audio Requirements**

* Input methods: audio URL, audio Base64 string, asset ID.

* Audio formats: wav, mp3

* Duration: The duration of a single audio clip is [2, 30] s. Up to 10 reference audio clips can be input, and the total duration of all audio clips must not exceed 30 s.

* Size: A single audio clip must not exceed 15 MB, and the request body size must not exceed 64 MB. Do not use Base64 strings for large files.

### Retention period

* Video task IDs have a validity period, measured from the `created_at` timestamp. An ID automatically becomes invalid when this period expires.

   * **Draft video task IDs:**  IDs created at or after 10:00 on October 28, 2026 (UTC+8) are valid for 14 days. IDs created before this time remain valid for seven days. After a Draft video task ID expires, it cannot be used to generate a final video.

   * **Other video task IDs:**  Valid for seven days.

* Video URL: Retained for 24 hours (inaccessible after timeout). The maximum number of downloads is 100. Please download or transfer it in a timely manner.

### Rate limits

Under the same primary account, requests to the same model, regardless of model version, are subject to the following limits. Requests that exceed the applicable limit return a `429: 'Too Many Requests'` error.

#### Model\-level rate limits

* **RPM (requests per minute)** : The maximum number of video generation tasks that can be created per minute. If this limit is exceeded, the task creation request fails due to rate limiting.

* **Maximum concurrent tasks**: The maximum number of tasks that can be processed at the same time. When this limit is reached, newly created tasks enter a queue for processing.

|Rate Limit Dimension |Enterprise User |Individual User |
|---|---|---|
|Maximum RPM |600 |180 |
|Maximum Concurrency |10 |3 |

#### Non\-inference API request rate limits

* **QPS (queries per second)** : The maximum number of requests allowed per second. If this limit is exceeded, the request fails due to rate limiting.

|API |Account\-level QPS limit |
|---|---|
|Retrieve a video generation task |20 |
|List video generation tasks |1 |
|Cancel or delete a video generation task |20 |
