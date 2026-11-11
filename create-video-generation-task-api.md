# Create a video generation task

`POST https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks` [Try](https://api.byteplus.com/api-explorer/?action=CreateContentsGenerationsTasks&groupName=Video%20Generation%20API&serviceCode=ark&version=2024-01-01)

This topic describes the input and output parameters of the API for creating video generation tasks. The model generates a video based on the input text, images, audio, videos, or sample task ID. After generation is complete, you can query the task and obtain the generated video.

> **New model available**

> Dreamina Seedance 2.5 is now fully available. You can call the API and try the model online on the BytePlus ModelArk platform. Before calling the model, **please carefully read**[**Must\-read before use**](seedance-2-5.md#2.5_compatibility)**to ensure that you correctly set the task type and configure parameters**.

**Model activation**

Before enabling the Dreamina Seedance 2.0 and Seedance 2.5 models, make sure you meet one of the following conditions:

* **Recommended:**  BytePlus account balance \> USD 30 ([Top up](https://console.byteplus.com/finance/overview))

* **Recommended:**  Purchase a dedicated AI Savings Plan at the USD 30 tier or above. Purchase entry: [AI Savings Plan](https://console.byteplus.com/common-buy/AI-SavingsPlans%7C%7Cd9urs77og65q382arfog).

* You have purchased a Seedance resource pack with available quota ([Purchase](https://console.byteplus.com/common-buy/ModelArk%7C%7Cd7d6aanpgiftptb9ajcg)).

For detailed rules, see [Activate, use, and cancel Dreamina Seedance 2.5 and 2.0 series models](seedance-model-activation-usage-and-refund.md).

**Model capabilities**

* **Dreamina Seedance 2.5new** (video with audio / silent video)

   * **Omni reference\-to\-video**: Input reference images (0–30), reference videos (0–10), reference audio clips (0–10), and an optional text prompt to generate one target video. Audio\-only input is supported. The model supports generating new videos, editing videos, extending videos, and coherently generating videos up to 30 seconds long.

   * **Image\-to\-video (first and last frames)** : Input a first\-frame image, a last\-frame image, and an optional text prompt to generate one target video.

   * **Image\-to\-video (first frame)** : Input a first\-frame image and an optional text prompt to generate one target video.

   * **Text\-to\-video**: Input a text prompt to generate one target video.

* **Dreamina Seedance 2.0 series** (video with audio / silent video)

   * **Omni reference\-to\-video**: Input reference images (0–9), reference videos (0–3), reference audio clips (0–3), and an optional text prompt to generate one target video. Audio\-only input is not supported; include at least one reference image or video. The models support generating new videos, editing videos, and extending videos.

   * **Image\-to\-video (first and last frames)** : Input a first\-frame image, a last\-frame image, and an optional text prompt to generate one target video.

   * **Image\-to\-video (first frame)** : Input a first\-frame image and an optional text prompt to generate one target video.

   * **Text\-to\-video**: Input a text prompt to generate one target video.

* **Seedance 1.5 pro** (video with audio / silent video)

   * Supports image\-to\-video (first and last frames), image\-to\-video (first frame), and text\-to\-video.

* **Seedance 1.0 pro**

   * Supports image\-to\-video (first and last frames), image\-to\-video (first frame), and text\-to\-video.

* **Seedance 1.0 pro fast**

   * Supports image\-to\-video (first frame) and text\-to\-video.

**Parameter input methods**

> **tip**

* > For `resolution`, `ratio`, `duration`, `frames`, `seed`, `camera_fixed`, and `watermark`, all models support both passing parameters directly in the request body and appending `--[parameters]` after the text prompt.

* > Supported parameters and values vary by model. For details, see [Set video output specifications](video-generation-tutorial.md#9fe4cce0). When a parameter or value is invalid or unsupported:

   * > **Conventional method (recommended)**: Pass parameters directly in the request body. This method uses strict validation and returns an error for invalid parameters.

   * > **Legacy method**: Append `--[parameters]` after the text prompt. Invalid parameters are ignored or cause an error.

**Conventional method (recommended): Pass parameters directly in the request body**

```json
{
    "model": "seedance-1-5-pro-251215",
    "content": [
        {
            "type": "text",
            "text": "The kitten is yawning at the camera."
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

**Weak\-validation method: Append **  **`--[parameters]`**  ** after the text prompt**

```json
{
    "model": "seedance-1-5-pro-251215",
    "content": [
        {
            "type": "text",
            "text": "The kitten is yawning at the camera. --rs 720p --rt 16:9 --dur 5 --seed 11 --cf false --wm true"
        }
    ]
}
```

**Try**

[去调试](https://api.byteplus.com/api-explorer/?action=CreateContentsGenerationsTasks&groupName=Video%20Generation%20API&serviceCode=ark&version=2024-01-01)

**Quick start**

[Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/gen_video) | [Model list](model-list.md) | [Model billing](model-pricing.md#8f25f772) | [API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D)

[API tutorial](video-generation-tutorial.md) | [API reference](create-video-generation-task-api.md) | [FAQs](modelark-faq.md) | [Model activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&tab=ComputerVision)

**Authentication**

This API supports only API Key authentication. Obtain a long\-term API Key on the [API keys](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D) page.

## Request parameters

### Request body

**model** `string` `Required`  |  Model ID

ID of the model you need to call. You can [activate a model service](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&tab=ComputerVision) and [query the model ID](model-list.md).

You can also use an endpoint ID to call a model, querying its rate limits, billing method (prepaid or postpaid), and status, and using its advanced capabilities such as monitoring and security. For more information, refer to [Obtaining an endpoint ID](online-inference-standard.md).

**content** `object[]` `Required`  |  Input content list

The references provided to the model for video generation, supporting text, image, audio, video, and sample task ID.

The following combinations are supported:

* Text

* Text (optional) + image

* Text (optional) + video

* Text (optional) + audio (Dreamina Seedance 2.5 supports audio\-only input)

* Text (optional) + image + audio

* Text (optional) + image + video

* Text (optional) + video + audio

* Text (optional) + image + video + audio

* Sample task ID: A sample video successfully generated using a Seedance model. The model can generate a high\-quality final video based on the sample.

> **Note**

> Dreamina Seedance 2.5 and Dreamina Seedance 2.0 series models do not support directly uploading reference images or videos that contain real human faces. ModelArk provides the following solutions to help you create videos with portrait assets. For details, see [Create portrait videos with Dreamina Seedance models](seedance-portrait-asset-guide.md).

* > Supports using original outputs containing human faces from certain models as input assets

* > Supports using preset digital characters as input assets

* > Supports using authorized real\-person assets as input assets

**Text** `object`

The input text information for the model to generate a video.

**type** `string` `Required`  |  Content type

`content.type`

The type of the input content. In this case, set the value to `text`.

**text** `string` `Required`  |  Text prompt

`content.text`

Text prompt input to the model, describing the expected generated video.

> **Tip**

* > **Supported prompt languages**: All models support English prompts.

   * > **Dreamina Seedance 2.5** additionally supports Spanish, Indonesian, Portuguese, Japanese, Malay, Thai, Arabic, Vietnamese, and Korean.

   * > **Dreamina Seedance 2.0 series** additionally supports Spanish, Indonesian, Portuguese, and Japanese.

* > Recommended prompt length: no more than 500 Chinese characters or 1,000 English words. Lengthy text will lead to scattered information, and the model may ignore details and only focus on key points, resulting in missing elements in the generated video.

* > See [Dreamina Seedance 2.0 series prompt guide](seedance-2-0-prompt-guide.md) for more tips on using prompts.

**Image** `object`

The input image information for the model to generate a video.

**type** `string` `Required`  |  Content type

`content.type`

The type of the input content. In this case, set the value to `image_url`. Supports image URL or image Base64 encoding.

**image_url** `object` `Required`  |  Image object

`content.image_url`

The input image object for the model.

**url** `string` `Required`  |  Image source

`content.image_url.url`

Accepts image URL, Base64\-encoded image, or asset ID.

* URL: Enter the publicly accessible URL of the image.

* Base64 encoding: Convert the local file to a Base64\-encoded string, then submit to the model. Follow the format: `data:image/<image format>;base64,<Base64 encoding>`. Note that `<image format>` must be lowercase, for example `data:image/png;base64,{base64_image}`.

* Asset ID: The URI of the digital character used for video generation. It follows the format `asset://<ASSET_ID>` and can be obtained from the [Digital characters library](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/gen_video?model=dreamina-seedance-2-0-260128).

> **Requirements for uploading a single image**

* > Format: `jpeg`, `png`, `webp`, `bmp`, `tiff`, or `gif`. Seedance 1.5 pro and later models also support `heic` and `heif`.

* > Aspect ratio (width/height): `[0.4, 2.5]`

* > Width and height (px): `[300, 6000]`

* > Size:

   * > Single image must be less than 30 MB

   * > Request body size does not exceed 64 MB.

   * > Do not use Base64 encoding for large files.

* > Number of images:

   * > Image\-to\-video (first frame): 1

   * > Image\-to\-video (first and last frames): 2

   * > Dreamina Seedance 2.5 omni reference\-to\-video: 1–30 images

   * > Seedance 2.0 series omni reference\-to\-video: 1–9 images

**role** `string` `Required under certain conditions`  |  Role/purpose

`content.role`

The location or purpose of the image.

Image\-to\-video (first frame)

* **Supported models:**  All models

* **Values**: You need to pass in one **image_url** object, with the role field set to `first_frame` or leave **role** blank.

Image\-to\-video (first and last frames)

* **Supported models:**  Dreamina Seedance 2.5, Dreamina Seedance 2.0 series, Seedance 1.5 pro, and Seedance 1.0 pro.

* **Values**: Two **image_url** objects must be provided, and the role field is required.

   * Role of the first frame: `first_frame`

   * Role of the last frame: `last_frame`

> **Tip**

> The first\-frame and last\-frame images can be identical.

> If the aspect ratios of the first and last frame images are inconsistent, the first frame image takes precedence, and the last frame image will be automatically cropped to fit.

Image\-to\-video (reference images)

* **Supported models:**  Dreamina Seedance 2.5 (1–30 images) and Dreamina Seedance 2.0 series (1–9 images)

* **Values**: Required. The **role** field for each reference image must be set to `reference_image`

> **Note**

* > **Image\-to\-video (first frame)**, **image\-to\-video (first and last frames)**, and **omni reference\-to\-video** (including reference images, videos, and audio) are mutually exclusive scenarios and **cannot be mixed**.

* > For omni reference\-based video generation, you can specify reference images as the first and/or last frame in the prompt to indirectly achieve a "first/last frame + omni references" effect. If you need to strictly ensure the first and last frames exactly match the specified images, use **image to video \- first and last frames** instead (set `role` to `first_frame` or `last_frame`).

**Video** `object`

Reference video provided to the model. Dreamina Seedance 2.5 and Dreamina Seedance 2.0 series models support video input.

ModelArk trusts face\-containing videos generated by these models. You can use the **original face\-containing videos generated by these models under your account within the past 30 days** as input assets. For details, see [Trust model outputs as input assets](seedance-portrait-asset-guide.md#trust-model-output).

**type** `string` `Required`  |  Content type

`content.type`

Type of the input; in this case, set to `video_url`.

**video_url** `object` `Required`  |  Video object

`content.video_url`

The video object provided to the model.

**url** `string` `Required`  |  Video source

`content.video_url.url`

Video URL or asset ID.

* Video URL: Enter the public URL of the video.

* Asset ID: The URI of the digital character used for video generation. It follows the format `asset://<ASSET_ID>` and can be obtained from the [digital characters library](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/gen_video?model=dreamina-seedance-2-0-260128).

> **Video input requirements**

* > Video format: `mp4`, `mov`. Supported encoding formats are listed in the table below.

* > Resolution: `480p`, `720p`, `1080p`, `4k`

* > Duration:

   * > **Dreamina Seedance 2.5:**

      * > Non\-video\-editing tasks: Each video must be 2–30 seconds long.

      * > [Video editing tasks](seedance-2-5.md#2.5_task_type_intro): Each video must be 4–30 seconds long.

      * > You can submit up to 10 reference videos with a total duration of no more than 30 seconds.

   * > **Dreamina Seedance 2.0 series:** Each video must be 2–15 seconds long. You can submit up to 3 reference videos with a total duration of no more than 15 seconds.

* > Dimensions:

   * > Aspect ratio (width/height): `[0.4, 2.5]`

   * > Width and height (px): `[300, 6000]`

   * > Total pixels: `[614×664=407696, 3326×2494=8295044]`, that is, the product of width and height must fall in the range `[407696, 8295044]`.

* > Size: Each video must not exceed 200 MB.

* > Frame rate (FPS): `[24, 60]`

|Container Format |Common Extension Name |**MIME** |Supported Encodings |
|---|---|---|---|
|MP4 |.mp4 |video/mp4 |Video: H.264/AVC, H.265/HEVC<br><br>Audio: AAC, MP3 |
|QuickTime |.mov |video/quicktime |Video: H.264/AVC, H.265/HEVC<br><br>Audio: AAC, MP3, PCM |

**role** `string` `Required`  |  Role/purpose

`content.role`

Position or purpose of the video. Fixed to `reference_video`.

**Supported models:**  Dreamina Seedance 2.5 and Dreamina Seedance 2.0 series.

**Audio** `object`

Audio provided to the model. Dreamina Seedance 2.5 and Dreamina Seedance 2.0 series models support audio input.

**type** `string` `Required`  |  Content type

`content.type`

Type of the input; in this case, set to `audio_url`. Supports audio URL or Base64\-encoded audio string.

**audio_url** `object` `Required`  |  Audio object

`content.audio_url`

Audio object provided to the model.

**url** `string` `Required`  |  Audio source

`content.audio_url.url`

URL, Base64\-encoded string or asset ID of the audio.

* Audio URL: The publicly accessible URL of the audio.

* Base64 encoding: Convert the local file to a Base64\-encoded string, then submit it to the model. Follow the format: `data:audio/<audio format>;base64,<Base64 encoding>`.

   `<audio format>` must be lowercase, for example, `data:audio/wav;base64,{base64_audio}`.

* Asset ID: The URI of the digital character used for video generation. It follows the format `asset://<ASSET_ID>` and can be obtained from the [digital character library](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/gen_video?model=dreamina-seedance-2-0-260128).

> **Audio input requirements**

* > Format: `wav`, `mp3`

* > Duration:

   * > **Dreamina Seedance 2.5:** Each audio clip must be 2–30 seconds long. You can submit up to 10 reference audio clips with a total duration of no more than 30 seconds.

   * > **Dreamina Seedance 2.0 series:** Each audio clip must be 2–15 seconds long. You can submit up to 3 reference audio clips with a total duration of no more than 15 seconds.

* > Size:

   * > Each audio file must not exceed 15 MB.

   * > The request body size must not exceed 64 MB.

   * > Do not use Base64 encoding for large files.

**role** `string` `Required`  |  Role/purpose

`content.role`

Position or purpose of the audio. Fixed to `reference_audio`.

**Supported models:**

* **Dreamina Seedance 2.5:**  Supports audio\-only input and combining audio with images or videos.

* **Dreamina Seedance 2.0 series:**  Audio\-only input is not supported; include at least one reference image or video.

**Draft information** `object`

Generate a final video based on a Draft video task ID. For instructions and important notes, see [Draft mode](video-generation-tutorial.md#5acd28c8).

**Supported models:**

* `Dreamina Seedance 2.5`

* `Seedance 1.5 pro`

**type** `string` `Required`  |  Content type

`content.type`

Type of input content. In this case, set it to `draft_task`.

**draft_task** `object` `Required`  |  Draft task object

`content.draft_task`

Draft task input to the model.

**id** `string` `Required`  |  Draft task ID

`content.draft_task.id`

Draft task ID.

When generating a final video based on a Draft video, the model automatically reuses some parameters. Some parameters must not be provided again, while others can be specified again.

Parameter handling rules vary by model. For details, see [Dreamina Seedance 2.5 Draft mode](seedance-2-5.md#2.5_draft_mode) and [Seedance 1.5 pro Draft mode](video-generation-tutorial.md#5acd28c8).

Two steps are required:

1. Call this API to generate a Draft video.

2. If the Draft video meets expectations, call this API to generate the final video based on the Draft video task ID returned in Step 1.

> **Note**

* > A Draft video task ID has a validity period, measured from its `created_at` timestamp. The ID automatically becomes invalid when this period expires.

* > Draft video task IDs created at or after 10:00 on October 28, 2026 (UTC+8) are valid for 14 days. IDs created before this time remain valid for seven days. After a Draft video task ID expires, it cannot be used to generate a final video.

**omni_reference_task_typenew** `string` `Default value: auto`  |  Task type hint

Seedance 2.5 **omni reference\-to\-video tasks** include three subtasks: reference\-to\-video, video editing, and video extension. Different task types have special parameter constraints. To reduce asynchronous errors after task creation, use this parameter to specify the subtask type and validate the corresponding constraints earlier.

* By default, when `omni_reference_task_type=auto`, the model automatically determines the task type from the input assets and prompt, and then validates the parameter values. If the parameters are incompatible with the actual task type, the task triggers an [asynchronous error](seedance-2-5.md#2.5_error_handling) (error code: [InvalidParameter.TaskTypeConstraint](error-codes.md)).

* When `omni_reference_task_type` is explicitly set to `reference`, `edit`, or `extend`, the API validates the special constraints for the specified task when you submit the task. If the request does not meet the requirements, the API immediately returns an error and the task is not created.

**Supported values:**

* `auto`: The model determines the task type from the input assets and prompt.

* `reference`: A reference\-to\-video task that generates a new video based on reference images, reference videos, or reference audio. When set to `reference`, `ratio` and `duration` have no special constraints.

* `edit`: A video editing task that edits the visuals or audio of an original video. When set to `edit`, `content` must contain at least one `reference_video`, the video must be 4–30 seconds long, `ratio` must be `adaptive`, and `duration` must be `-1`.

* `extend`: A video extension task that extends an original video forward or backward. When set to `extend`, `content` must contain at least one `reference_video`, and `ratio` must be `adaptive`.

> **Note**

> When actually processing the task, the model still further determines the task type based on the prompt. If the actual task type identified by the model does not match the specified value, the task still triggers an [asynchronous error](seedance-2-5.md#2.5_error_handling) (error code: [`InvalidParameter.TaskTypeMismatch`](error-codes.md)). Follow the [prompting guidance for each task type](seedance-2-5.md#2.5_param_constraints) to reduce the chance of errors.

**Supported models:**

* `Dreamina Seedance 2.5`

**resolution** `string`  |  Video resolution

Video resolution.

> **Tip**

* > Dreamina Seedance 2.5 1080p output and Dreamina Seedance 2.0 4K output both use 10\-bit color depth and H.265/HEVC encoding.

   * > Compared with standard 8\-bit color depth, 10\-bit color depth preserves richer color gradations and smoother tonal transitions, making it suitable for professional video production and HDR content.

   * > H.265/HEVC encoding may not be compatible with some playback environments. If playback fails, upgrade your system, use another device, or try a player such as VLC, mpv, or QuickTime Player. For details, see [Playback compatibility for videos with 10\-bit color depth and H.265/HEVC encoding](seedance-2-0.md#4k_player).

**Supported models and values:**

* **Dreamina Seedance 2.5:**  Default `720p`; supports `480p`, `720p`, and `1080p`.

* **Dreamina Seedance 2.0:**  Default `720p`; supports `480p`, `720p`, `1080p`, and `4k`.

* **Dreamina Seedance 2.0 fast:**  Default `720p`; supports `480p` and `720p`.

* **Dreamina Seedance 2.0 mini:**  Default `720p`; supports `480p` and `720p`.

* **Seedance 1.5 pro:**  Default `720p`; supports `480p`, `720p`, and `1080p`.

* **Seedance 1.0 pro:**  Default `1080p`; supports `480p`, `720p`, and `1080p`.

* **Seedance 1.0 pro fast:**  Default `1080p`; supports `480p`, `720p`, and `1080p`.

**ratio** `string`  |  Video aspect ratio

Aspect ratio of the generated video.

* Supported values: `16:9`, `4:3`, `1:1`, `3:4`, `9:16`, `21:9`, and `adaptive`. The `adaptive` value automatically selects an aspect ratio based on the task type and input.

Value restrictions and `adaptive` behavior by model

|Task type | |Dreamina Seedance 2.5 |Dreamina Seedance 2.0 series |Seedance 1.5 pro |Seedance 1.0 series |
|---|---|---|---|---|---|
|Text\-to\-video | |Supports specifying an available aspect ratio or allowing the model to select one based on the prompt<br><br>> Supports `adaptive` or a specified aspect ratio |Supports specifying an available aspect ratio or allowing the model to select one based on the prompt<br><br>> Supports `adaptive` or a specified aspect ratio |Supports specifying an available aspect ratio or allowing the model to select one based on the prompt<br><br>> Supports `adaptive` or a specified aspect ratio |Only supports specifying an available aspect ratio<br><br>> Does not support `adaptive` |
|First\-frame or first\-and\-last\-frame image\-to\-video | |**Automatically preserves the aspect ratio of the first\-frame image specified by ** **`first_frame`**<br><br>> Defaults to and only supports `adaptive` |Supports specifying an available aspect ratio or allowing the model to select one based on the first\-frame image<br><br>> Supports `adaptive` or a specified aspect ratio |Supports specifying an available aspect ratio or allowing the model to select one based on the first\-frame image<br><br>> Supports `adaptive` or a specified aspect ratio |Supports specifying an available aspect ratio or allowing the model to select one based on the first\-frame image<br><br>> Supports `adaptive` or a specified aspect ratio |
|Omni reference video generation |Video editing / video extension |**The model selects the video to edit or extend based on the prompt intent, then automatically preserves the aspect ratio of the selected video; you cannot specify another ratio**<br><br>> Defaults to and only supports `adaptive` |Supports specifying an available aspect ratio or allowing the model to select one based on the video to edit or extend<br><br>> Supports `adaptive` or a specified aspect ratio |— |— |
||Reference\-to\-video |Supports specifying an available aspect ratio or allowing the model to select one based on the prompt<br><br>> Supports `adaptive` or a specified aspect ratio |Supports specifying an available aspect ratio or allowing the model to select one based on the prompt<br><br>> Supports `adaptive` or a specified aspect ratio |— |— |

Width and height pixel values corresponding to different aspect ratios

For image to video tasks, when the selected video aspect ratio is inconsistent with the aspect ratio of your uploaded image, ModelArk will crop your image, and the cropping will be centered. See [Image cropping rules](video-generation-tutorial.md#f76aafc8) for detailed rules.

|Resolution |Aspect ratio |Dreamina Seedance 2.5 |Dreamina Seedance 2.0 series |Seedance 1.5 pro |Seedance 1.0 series |
|---|---|---|---|---|---|
|480p |`16:9` |854×480 |864×496 |864×496 |864×480 |
||`4:3` |752×560 |752×560 |752×560 |736×544 |
||`1:1` |640×640 |640×640 |640×640 |640×640 |
||`3:4` |560×752 |560×752 |560×752 |544×736 |
||`9:16` |480×854 |496×864 |496×864 |480×864 |
||`21:9` |992×432 |992×432 |992×432 |960×416 |
|720p |`16:9` |1280×720 |1280×720 |1280×720 |1248×704 |
||`4:3` |1112×834 |1112×834 |1112×834 |1120×832 |
||`1:1` |960×960 |960×960 |960×960 |960×960 |
||`3:4` |834×1112 |834×1112 |834×1112 |832×1120 |
||`9:16` |720×1280 |720×1280 |720×1280 |704×1248 |
||`21:9` |1470×630 |1470×630 |1470×630 |1504×640 |
|1080p<br><br>(Seedance 2.0 fast and Seedance 2.0 mini are not supported) |`16:9` |1920×1080 |1920×1080 |1920×1080 |1920×1088 |
||`4:3` |1664×1248 |1664×1248 |1664×1248 |1664×1248 |
||`1:1` |1440×1440 |1440×1440 |1440×1440 |1440×1440 |
||`3:4` |1248×1664 |1248×1664 |1248×1664 |1248×1664 |
||`9:16` |1080×1920 |1080×1920 |1080×1920 |1088×1920 |
||`21:9` |2206×946 |2206×946 |2206×946 |2176×928 |
|4k |`16:9` |— |3840×2160 |— |— |
||`4:3` |— |3326×2494 |— |— |
||`1:1` |— |2880×2880 |— |— |
||`3:4` |— |2494×3326 |— |— |
||`9:16` |— |2160×3840 |— |— |
||`21:9` |— |4398×1886 |— |— |

> **Note**

> Dreamina Seedance 2.5 has special constraints for **video editing, video extension, and first\-frame/first\-and\-last\-frame image\-to\-video tasks**. For task definitions, see [Task types and constraints](seedance-2-5.md#2.5_task_type_intro).

* > `ratio` only supports `adaptive`; you cannot specify another aspect ratio.

   * > For video editing and extension, the model selects the video to edit or extend based on the prompt intent, then automatically preserves the aspect ratio of the selected video.

   * > For first\-frame and first\-and\-last\-frame image\-to\-video, the model automatically preserves the aspect ratio of the first\-frame image specified by `first_frame`.

**Supported models and defaults:**

* **Dreamina Seedance 2.5:**  Default `adaptive`.

* **Dreamina Seedance 2.0 series:**  Default `adaptive`.

* **Seedance 1.5 pro:**  Default `adaptive`.

* **Seedance 1.0 pro:**  Defaults to `16:9` for text\-to\-video and `adaptive` for image\-to\-video.

* **Seedance 1.0 pro fast:**  Defaults to `16:9` for text\-to\-video and `adaptive` for image\-to\-video.

**duration** `integer`  |  Video duration

Generated video duration in seconds. Specify either `duration` or `frames`; `frames` takes precedence. To generate a whole\-second video, specify `duration`.

Behavior when `duration` is `-1`

|Model |Behavior when `duration` is `-1` |
|---|---|
|Dreamina Seedance 2.0 series and Seedance 1.5 pro |The model selects an appropriate whole\-second video length within the valid `duration` range. |
|Dreamina Seedance 2.5 (video editing) |The model selects the video to edit based on the prompt intent, then automatically keeps the output duration approximately the same as the selected video; you cannot specify another duration.<br><br>> The output duration may be a non\-integer number of seconds.<br><br>> The output may be about 0.4 seconds shorter than the selected video. |
|Dreamina Seedance 2.5 (other task types) |The model selects an appropriate whole\-second video length within the valid `duration` range. |

> **Returned duration**

> The `duration` returned by the [Retrieve a video generation task API](get-video-generation-task-api.md) is an integer approximation and may differ from the actual duration.

* > Calculation: returned `duration` = actual total frames / 24, rounded down.

* > Example: For a 133\-frame video, the actual duration is 133 / 24 = 5.54 seconds, while the returned `duration` is 5.

> **Note**

> Dreamina Seedance 2.5 has special constraints for **video editing tasks**. For task definitions, see [Task types and constraints](seedance-2-5.md#2.5_task_type_intro).

* > `duration` only supports `-1`; you cannot specify an output duration.

* > The video to edit must be 4–30 seconds long.

**Supported models and values:**

* **Dreamina Seedance 2.5:**  Default `-1`; supports `[4, 30]` or `-1`.

* **Dreamina Seedance 2.0 series:**  Default `5`; supports `[4, 15]` or `-1`.

* **Seedance 1.5 pro:**  Default `5`; supports `[4, 12]` or `-1`.

* **Seedance 1.0 pro:**  Default `5`; supports `[2, 12]`.

* **Seedance 1.0 pro fast:**  Default `5`; supports `[2, 12]`.

**frames** `integer`  |  Video frame count

> You only need to specify either duration or frames, and frames takes precedence over duration. If you want to generate a video with fractional seconds, it is recommended to specify frames.

Frame count of the generated video. By specifying the number of frames, you can flexibly control the length of the generated video and generate videos with fractional seconds.

Due to the value limit of frames, only certain fractional seconds are supported. You need to calculate the closest number of frames according to the formula.

* Calculation formula: Number of frames = duration × frame rate (24).

* Value range: All integer values in the range `[29, 289]` that fit the format `25 + 4n` are supported, where n is a positive integer.

Example: If you need to generate a 2.4\-second video, the number of frames = 2.4 × 24 = 57.6. However, since frames value cannot be 57.6, you can only select the closest value. The closest number of frames calculated according to 25 + 4n is 57, and the actually generated video is 57 / 24 = 2.375 seconds.

**Supported models**:

* `Seedance 1.0 pro`

* `Seedance 1.0 pro fast`

**generate_audio** `boolean` `Default: true`  |  Generate audio

Controls whether the generated video includes sound synchronized with the footage.

* `true`: The video output by the model includes synchronized audio. The model will automatically generate matching human voice, sound effects and background music based on the text prompt and visual content. It is recommended to put dialogue content in double quotes to optimize the audio generation effect. For example: The man stopped the woman and said: "Remember, you can't point at the moon with your finger in the future."

* `false`: The video output by the model is a silent video.

> **Note**

> Audio\-enabled videos generated by Seedance 2.0 and later versions are dual\-channel (stereo), regardless of the number of input audio channels.

**Supported models:**

* `Dreamina Seedance 2.5`

* `Dreamina Seedance 2.0 series`

* `Seedance 1.5 pro`

**watermark** `boolean` `Default value: false`  |  Video watermark

Whether the generated video contains a watermark. Valid values:

* `true`: An `AI Generated` watermark will be displayed in the lower right corner of the generated video.

* `false`: The generated video does not contain a watermark.

**output_formatnew** `string` `Default value: mp4`  |  Output format

Output video format.

* `mp4`: A general\-purpose format with broad compatibility and standard color precision. It can be played directly in browsers, on mobile devices, in media players, and on distribution platforms.

* `mov`: A high\-color\-precision format for professional workflows. It better preserves color and brightness consistency and is suitable for color grading, keying, compositing, and other professional post\-production tasks. For video editing and extension, using MOV for both input and output is recommended.

> **MOV playback compatibility**

> MOV output uses H.264 video encoding, YUV 4:4:4 chroma sampling, and PCM audio encoding. Some players may not support this combination.

> |Player |macOS |Windows |
> |---|---|---|
> |IINA |✓ |✕ |
> |VLC |✓ |✓ |
> |mpv |✓ |✓ |
> |ffplay |✓ |✓ |

**Supported models**

* `Dreamina Seedance 2.5`

**seed** `integer` `Default value: -1`  |  Random seed

Seed integer, used to control the randomness of generated content.

Value range: `[-1, 2147483647]`.

> **Note**

* > For the same request, if the model receives different seed values, such as not specifying a seed value, setting the seed to \-1 (which will be replaced by a random number), or manually changing the seed value, different results will be generated.

* > For the same request, if the model receives the same seed value, similar results will be generated, but complete consistency is not guaranteed.

**Supported models:**

* `Seedance 1.5 pro`

* `Seedance 1.0 pro`

* `Seedance 1.0 pro fast`

**camera_fixed** `boolean` `Default value: false`  |  Fix camera

> Reference\-image scenarios are not supported.

Whether to fix the camera. Valid values:

* `true`: Fix the camera. ModelArk will append the fixed camera instruction to the user's prompt, but the actual result is not guaranteed.

* `false`: Do not fix the camera.

**Supported models:**

* `Seedance 1.5 pro`

* `Seedance 1.0 pro`

* `Seedance 1.0 pro fast`

**return_last_frame** `boolean` `Default value: false`  |  Return last frame

* `true`: Return the last frame image of the generated video in `jpeg` format. After setting it to `true`, you can get the last frame image via the [Retrieve a video generation task API](get-video-generation-task-api.md). Its pixel dimensions are consistent with the generated video, and it has no watermark.

* `false`: Do not return the last frame image of the generated video.

> **Tip**

> You can use this parameter to generate multiple consecutive videos. Use the last frame of the previously generated video as the first frame of the next video task to quickly generate a sequence of videos. For an example, see [Generate multiple consecutive videos](video-generation-tutorial.md#141cf7fa).

**draft** `boolean` `Default: false`  |  Draft mode

Controls whether to enable draft mode. See [Draft mode](video-generation-tutorial.md#5acd28c8) for detailed instructions and notes.

* `true`: Enable draft mode and generate a preview video to confirm whether the scene structure, shot scheduling, subject motion, and prompt intent meet expectations.

* `false`: Disable draft mode and generate a video normally.

> **Tip**

* > After you enable draft mode, only 480p Draft videos can be generated. Using another resolution causes an error.

* > A Draft video task ID has a validity period, measured from its `created_at` timestamp. The ID automatically becomes invalid when this period expires. Draft video task IDs created at or after 10:00 on October 28, 2026 (UTC+8) are valid for 14 days. IDs created before this time remain valid for seven days. After a Draft video task ID expires, it cannot be used to generate a final video.

* > Parameter restrictions and billing rules vary by model. For details, see [Dreamina Seedance 2.5 Draft mode](seedance-2-5.md#2.5_draft_mode) and [Seedance 1.5 pro Draft mode](video-generation-tutorial.md#5acd28c8).

**Supported models:**

* `Dreamina Seedance 2.5`

* `Seedance 1.5 pro`

**service_tier** `string` `Default value: default`  |  Service tier

> Modifying the service tier of a submitted task is not supported.

Specify the service tier type for processing this request. Valid values:

* `default`: Online inference mode, with lower RPM and concurrency quotas (see [Model list](model-list.md)), suitable for scenarios with high requirements for inference efficiency.

* `flex`: Offline inference mode, with higher TPD quota (see [Model list](model-list.md)), priced at 50% of online inference, suitable for scenarios with high tolerance for inference latency. Dreamina Seedance 2.5 and Dreamina Seedance 2.0 series models are not currently supported.

**callback_url** `string`  |  Callback URL

Fill in the callback notification address for the result of this generation task. When the status of the video generation task changes, ModelArk will send a POST request to this address.

The callback request content structure is consistent with the response body of the [Retrieve a video generation task API](get-video-generation-task-api.md).

The status returned by the callback can be one of the following values:

* `queued`: In queue.

* `running`: Task is running.

* `succeeded`: Task succeeded. If sending fails, that is, no successful delivery confirmation is received within 5 seconds, the callback is retried three times.

* `failed`: Task failed. If sending fails, that is, no successful delivery confirmation is received within 5 seconds, the callback is retried three times.

* `expired`: Task timed out, that is, the task has been in the `running` or `queued` state for longer than the expiration time. You can set the expiration time via `execution_expires_after`.

**execution_expires_after** `integer` `Default value: 172800`  |  Task expiration threshold

Task timeout threshold. Specifies the expiration time of the task after submission (unit: second), calculated from the **created at** timestamp. The default value is 172800 seconds, which is 48 hours. Value range: `[3600, 259200]`.

No matter which **service_tier** you use, it is recommended to set an appropriate timeout according to your business scenario. After this time, the task will be automatically terminated and marked as `expired` status.

**priority** `integer` `Default 0`  |  Execution priority

> Supported by Dreamina Seedance 2.5 and Dreamina Seedance 2.0 series.

Sets the execution priority of the current request and determines its position in the queue.

Value range: `[0, 9]`.

A larger value indicates a higher priority.

By default, requests are executed in **FIFO** (First In, First Out) order. After you set a higher priority, the request is inserted before all lower\-priority requests under the same **Endpoint**.

**Example**:

Assume an Endpoint currently has three queued tasks (`status=queued`), all with the default priority of 0.

```text
Queue: [Task A: priority=0] → [Task B: priority=0] → [Task C: priority=0]
```

If you submit a new request with `priority=5`, the request is moved directly to the front of the queue:

```text
Queue: [New request: priority=5] → [Task A: priority=0] → [Task B: priority=0] → [Task C: priority=0]
```

> **Note**

* > Requests with the same priority are still ordered by FIFO.

* > Priority affects only the queue order. It does not interrupt tasks that are already running (status = `running`).

* > Priority takes effect only within the same Endpoint and does not affect other Endpoints.

* > Offline inference mode (`service_tier=flex`) does not support priority configuration.

**safety_identifier** `string`  |  End\-user identifier

Unique identifier of end users, used to help the platform detect users in your application who may violate the ModelArk usage policy. This identifier is an English string, which must be fixed and unique for a single user, and the length cannot exceed 64 characters.

It is recommended to pass in a string generated by hashing the username, user ID or email address to avoid leaking user privacy information.

## Response parameters

**id** `string`  |  Task ID

Video generation task IDs have a validity period, measured from the `created_at` timestamp. An ID automatically becomes invalid when this period expires.

* **Draft video task IDs:**  IDs created at or after 10:00 on October 28, 2026 (UTC+8) are valid for 14 days. IDs created before this time remain valid for seven days. After a Draft video task ID expires, it cannot be used to generate a final video.

* **Other video task IDs:**  Valid for seven days.

The video generation task creation is an asynchronous API. After obtaining the ID, you need to query the status of the video generation task through the [Retrieve a video generation task API](get-video-generation-task-api.md). After the task is successful, the `video_url` of the generated video will be output.
