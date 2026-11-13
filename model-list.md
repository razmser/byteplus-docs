# Model list

ModelArk provides models for text and code, video, image, 3D, and embedding workloads. Compare the capabilities and limits in each section to select a model.

## Featured models

[Dola Seed 2.1 Turbo](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seed-2-1-turbo)

![Dola Seed 2.1 Turbo](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/banner_thinking.png)

* **Best for:**  General\-purpose agentic workloads

* **Key capabilities:**  Deep reasoning, multimodal understanding, tool use, and structured output

* **Key limits:**  256K context, 256K max output (incl. CoT), and 0.5K max RPM

[Dreamina Seedance 2.5](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-5)

![Dreamina Seedance 2.5](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/banner_video_generation.png)

* **Best for:**  Video generation and editing

* **Key capabilities:**  Reference\-to\-video, video editing, video extension, image\-to\-video, and text\-to\-video

* **Key limits:**  Up to 1080p, 4\-30 seconds, and 0.6K default max RPM for enterprise users

[Dola Seedream 5.5](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-5)

![Dola Seedream 5.5](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/banner_image_generation.png)

* **Best for:**  Precise multi\-round image editing and layer decomposition

* **Key capabilities:**  Native RGBA image generation, rich text OCR, and up to 99 output layers

* **Key limit:**  500 max IPM

> **Tip**

* > **Region availability**:

   * > Currently, all the models listed in [Model list](model-list.md) are supported in the `ap-southeast-1` region.

   * > The seed\-2\-0 and seedream\-5\-0\-lite models are also supported in the `eu-west-1` region.

* > Base URL by region:

   * > `ap-southeast-1`: `https://ark.ap-southeast.bytepluses.com/api/v3`

   * > `eu-west-1`: `https://ark.eu-west.bytepluses.com/api/v3`

> For more information, see [Region availability](region-availability.md).

> **Rate limits**

> The rate limits in this document are theoretical maximum values. Actual limits depend on platform load and the invocation method. For details, see [Best practices for handling burst traffic](traffic-burst-handling-best-practices.md).

## Text and code generation

Tutorials: [Text generation](text-generation.md) | [Image understanding](image-understanding.md) | [Video understanding](video-understanding.md) | [Document understanding](document-understanding.md) | [Audio understanding](audio-understanding.md) | [Structured output (beta)](structured-output-beta.md)

API documentation: [Chat API](chat-api.md) | [Responses API](create-model-responses-api.md) | [Messages API](messages-api.md)

### Capabilities

|Provider |Model ID |Deep<br><br>reasoning |Visual<br><br>understanding |Audio<br><br>understanding |Structured<br><br>output |
|---|---|---|---|---|---|
|ByteDance |[dola-seed-2-1-turbo-260628](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seed-2-1-turbo) |Yes |Yes |No |Yes |
||[seed-2-0-lite-260428](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-lite) [LTS](lts-mechanism.md#lts-model-list) |Yes |Yes |Yes |`json_schema` recommended |
||[seed-2-0-mini-260428](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-mini) [LTS](lts-mechanism.md#lts-model-list) |Yes |Yes |Yes |`json_schema` recommended |
||[seed-2-0-code-preview-260328](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-code) |Yes |Yes |No |No |
||[seed-2-0-pro-260328](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-pro) |Yes |Yes |No |No |
|DeepSeek |[deepseek-v4-1-flash-economy-261017](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-1-flash-economy) |Yes |Yes |No |Yes |
||[deepseek-v4-1-flash-261017](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-1-flash) |Yes |Yes |No |Yes |
||[deepseek-v4-1-flash-260910](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-1-flash) |Yes |Yes |No |Yes |
||[deepseek-v4-pro-ga-260813](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-pro-ga) |Yes |No |No |Yes |
||[deepseek-v4-flash-ga-260731](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-flash-ga) |Yes |No |No |Yes |
||[deepseek-v4-flash-260425](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-flash) |Yes |No |No |No |
||[deepseek-v4-pro-260425](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-pro) |Yes |No |No |No |
|Z.ai |[glm-5-3-flash-260828](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=glm-5-3-flash) |Yes |Yes |No |No |
||[glm-5-2-260617](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=glm-5-2) |Yes |No |No |Yes |

### Limits

The default output is 4K for all models unless otherwise specified. Max output includes CoT.

|Provider |Model ID |Context |Max input |Max output |Max CoT |Max RPM |Max TPM |
|---|---|---|---|---|---|---|---|
|ByteDance |[dola-seed-2-1-turbo-260628](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seed-2-1-turbo) |256K |256K |256K |256K |0.5K |1000K |
||[seed-2-0-lite-260428](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-lite) [LTS](lts-mechanism.md#lts-model-list) |256K |224K |128K |128K |30K |1500K |
||[seed-2-0-mini-260428](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-mini) [LTS](lts-mechanism.md#lts-model-list) |256K |224K |128K |128K |30K |1500K |
||[seed-2-0-code-preview-260328](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-code) |256K |224K |128K |128K |30K |1500K |
||[seed-2-0-pro-260328](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-pro) |256K |224K |128K |128K |30K |1500K |
|DeepSeek |[deepseek-v4-1-flash-economy-261017](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-1-flash-economy) |1024K |1024K |384K |128K |3K |5000K |
||[deepseek-v4-1-flash-261017](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-1-flash) |1024K |1024K |384K |128K |3K |5000K |
||[deepseek-v4-1-flash-260910](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-1-flash) |1024K |1024K |384K |128K |3K |5000K |
||[deepseek-v4-pro-ga-260813](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-pro-ga) |1024K |1024K |384K |128K |0.5K |1000K |
||[deepseek-v4-flash-ga-260731](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-flash-ga) |1024K |1024K |384K |128K |0.5K |1000K |
||[deepseek-v4-flash-260425](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-flash) |1024K |1024K |384K |128K |15K |1500K |
||[deepseek-v4-pro-260425](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-pro) |1024K |1024K |384K |128K |15K |1500K |
|Z.ai |[glm-5-3-flash-260828](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=glm-5-3-flash) |1024K |1024K |128K |128K |0.5K |1000K |
||[glm-5-2-260617](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=glm-5-2) |1024K |1024K |128K |128K |0.5K |1000K |

### Tool use

Tutorials: [Function call](function-calling.md) | [Cloud-deployed MCP / remote MCP](mcp-remote-mcp.md)

|Provider |Model ID |Function calling |MCP |
|---|---|---|---|
|ByteDance |[dola-seed-2-1-turbo-260628](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seed-2-1-turbo) |Yes |Yes |
||[seed-2-0-lite-260428](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-lite) [LTS](lts-mechanism.md#lts-model-list) |Yes |Yes |
||[seed-2-0-mini-260428](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-mini) [LTS](lts-mechanism.md#lts-model-list) |Yes |Yes |
||[seed-2-0-code-preview-260328](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-code) |Yes |No |
||[seed-2-0-pro-260328](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-pro) |Yes |Yes |
|DeepSeek |[deepseek-v4-1-flash-economy-261017](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-1-flash-economy) |Yes |Yes |
||[deepseek-v4-1-flash-261017](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-1-flash) |Yes |Yes |
||[deepseek-v4-1-flash-260910](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-1-flash) |Yes |Yes |
||[deepseek-v4-pro-ga-260813](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-pro-ga) |Yes |Yes |
||[deepseek-v4-flash-ga-260731](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-flash-ga) |Yes |Yes |
||[deepseek-v4-flash-260425](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-flash) |Yes |Yes |
||[deepseek-v4-pro-260425](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-pro) |Yes |Yes |
|Z.ai |[glm-5-3-flash-260828](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=glm-5-3-flash) |Yes |Yes |
||[glm-5-2-260617](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=glm-5-2) |Yes |Yes |

### Context caching

Tutorials: [Context cache](context-cache.md)

API documentation: [Chat API](chat-api.md) | [Responses API](create-model-responses-api.md) | [Messages API](messages-api.md) | [Batch API](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/batch-chat-api)

> **Tip**

> For available models, implicit caching can be used with the [Chat API](chat-api.md), [Responses API](create-model-responses-api.md), [Messages API](messages-api.md), or [Batch API](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/batch-chat-api), depending on the model. Explicit caching uses the [Responses API](create-model-responses-api.md), and every model that supports explicit caching supports both prefix caching and session caching.

|Provider |Model ID |Implicit caching ||||Explicit caching |
|---|---|---|---|---|---|---|
|Supported API ||Chat |Responses |Messages |Batch |Responses |
|ByteDance |[dola-seed-2-1-turbo-260628](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seed-2-1-turbo) |Yes |Yes |No |Yes |Yes |
||[seed-2-0-lite-260428](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-lite) [LTS](lts-mechanism.md#lts-model-list) |Yes |Yes |No |No |Yes |
||[seed-2-0-mini-260428](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-mini) [LTS](lts-mechanism.md#lts-model-list) |Yes |Yes |No |No |Yes |
||[seed-2-0-code-preview-260328](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-code) |No |No |No |No |Yes |
||[seed-2-0-pro-260328](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seed-2-0-pro) |No |No |No |No |Yes |
|DeepSeek |[deepseek-v4-1-flash-economy-261017](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-1-flash-economy) |Yes |Yes |No |No |Yes |
||[deepseek-v4-1-flash-261017](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-1-flash) |Yes |Yes |No |No |Yes |
||[deepseek-v4-1-flash-260910](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-1-flash) |Yes |Yes |No |No |Yes |
||[deepseek-v4-pro-ga-260813](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-pro-ga) |Yes |Yes |No |No |Yes |
||[deepseek-v4-flash-ga-260731](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-flash-ga) |Yes |Yes |No |No |Yes |
||[deepseek-v4-flash-260425](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-flash) |Yes |No |No |No |Yes |
||[deepseek-v4-pro-260425](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=deepseek-v4-pro) |Yes |No |No |No |Yes |
|Z.ai |[glm-5-3-flash-260828](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=glm-5-3-flash) |Yes |Yes |Yes |No |Yes |
||[glm-5-2-260617](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=glm-5-2) |Yes |Yes |No |No |Yes |

## Video generation

Tutorials: [Video generation tutorial](video-generation-tutorial.md) | [Dreamina Seedance 2.5 tutorial](seedance-2-5.md) | [Dreamina Seedance 2.5 prompt guide](seedance-2-5-prompt-guide.md)

API documentation: [Create task](create-video-generation-task-api.md) | [List tasks](list-video-generation-tasks-api.md) | [Get task](get-video-generation-task-api.md) | [Cancel or delete task](cancel-or-delete-video-generation-tasks-api.md)

### Capabilities

|Model ID |Text\-to\-video |First\-frame\-to\-video |First\-last\-frame\-to\-video |Reference\-to\-video |Video editing |Video extension |
|---|---|---|---|---|---|---|
|[dreamina-seedance-2-5-260628](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-5) |Yes |Yes |Yes |Yes |Yes |Yes |
|[dreamina-seedance-2-0-mini-260615](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0-mini) |Yes |Yes |Yes |Yes |Yes |Yes |
|[dreamina-seedance-2-0-fast-260128](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0-fast) |Yes |Yes |Yes |Yes |Yes |Yes |
|[dreamina-seedance-2-0-260128](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0) |Yes |Yes |Yes |Yes |Yes |Yes |
|[seedance-1-0-pro-fast-251015](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedance-1-0-pro-fast) |Yes |Yes |No |No |No |No |
|[seedance-1-0-pro-250528](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedance-1-0-pro) |Yes |Yes |Yes |No |No |No |

### Output

|Model ID |Format |Resolution |Frame rate |Duration |
|---|---|---|---|---|
|[dreamina-seedance-2-5-260628](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-5) |`.mp4`<br><br>`.mov` |480p (8\-bit)<br><br>720p (8\-bit)<br><br>1080p (10\-bit) |24 fps |4\-30 seconds |
|[dreamina-seedance-2-0-mini-260615](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0-mini) |`.mp4` |480p (8\-bit)<br><br>720p (8\-bit) |24 fps |4\-15 seconds |
|[dreamina-seedance-2-0-fast-260128](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0-fast) |`.mp4` |480p (8\-bit)<br><br>720p (8\-bit) |24 fps |4\-15 seconds |
|[dreamina-seedance-2-0-260128](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0) |`.mp4` |480p (8\-bit)<br><br>720p (8\-bit)<br><br>1080p (8\-bit)<br><br>4K (10\-bit) |24 fps |4\-15 seconds |
|[seedance-1-0-pro-fast-251015](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedance-1-0-pro-fast) |`.mp4` |480p<br><br>720p<br><br>1080p |24 fps |2\-12 seconds |
|[seedance-1-0-pro-250528](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedance-1-0-pro) |`.mp4` |480p<br><br>720p<br><br>1080p |24 fps |2\-12 seconds |

### Limits

|Model ID |Default ||Flex |
|---|---|---|---|
| |Max RPM |Max concurrency | |
|[dreamina-seedance-2-5-260628](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-5) |Enterprise: 600<br><br>Individual: 180 |Enterprise: 10<br><br>Individual: 3 |No |
|[dreamina-seedance-2-0-mini-260615](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0-mini) |Enterprise: 600<br><br>Individual: 180 |Enterprise: 10<br><br>Individual: 3 |No |
|[dreamina-seedance-2-0-fast-260128](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0-fast) |Enterprise: 600<br><br>Individual: 180 |Enterprise: 10<br><br>Individual: 3 |No |
|[dreamina-seedance-2-0-260128](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0) |Non\-4K enterprise: 600<br><br>Non\-4K individual: 180<br><br>4K, all users: 15 |Non\-4K enterprise: 10<br><br>Non\-4K individual: 3<br><br>4K, all users: 1 |No |
|[seedance-1-0-pro-fast-251015](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedance-1-0-pro-fast) |600 |10 |TPD 500B |
|[seedance-1-0-pro-250528](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedance-1-0-pro) |600 |10 |TPD 500B |

## Image generation

Tutorials: [Image generation tutorial](image-generation-tutorial.md) | [Seedream 5.0 pro / flash tutorial](seedream-5-0-pro.md)

API documentation: [Generate images](image-generation-api.md)

### Capabilities

|Model ID |Interactive editing |Layer decomposition |Single\-image generation |Batch\-image generation |
|---|---|---|---|---|
|[dola-seedream-5-5-261028](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-5) |Yes |1 base image + up to 99 layers (rich\-text OCR supported) |* Text\-to\-image<br><br>* Single\-reference image\-to\-image<br><br>* Multi\-reference image\-to\-image<br><br>* Native RGBA images are supported for all three task types. |No |
|[dola-seedream-5-0-flash-260915](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-0-flash) |Yes |1 base image + up to 16 layers |* Text\-to\-image<br><br>* Single\-reference image\-to\-image<br><br>* Multi\-reference image\-to\-image<br><br>* Native RGBA images are supported only for single\-reference image\-to\-image. |No |
|[dola-seedream-5-0-pro-260628](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-0-pro) |Yes |1 base image + up to 16 layers |* Text\-to\-image<br><br>* Single\-reference image\-to\-image<br><br>* Multi\-reference image\-to\-image<br><br>* Native RGBA images are supported only for single\-reference image\-to\-image. |No |
|[seedream-5-0-260128](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedream-5-0)<br><br>Also supports `seedream-5-0-lite-260128` |No |No |* Text\-to\-image<br><br>* Single\-reference image\-to\-image<br><br>* Multi\-reference image\-to\-image<br><br>* Web search supported. |* Text\-to\-multiple images<br><br>* Single\-reference image\-to\-multiple images<br><br>* Multi\-reference image\-to\-multiple images |
|[seedream-4-5-251128](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedream-4-5) |No |No |* Text\-to\-image<br><br>* Single\-reference image\-to\-image<br><br>* Multi\-reference image\-to\-image |* Text\-to\-multiple images<br><br>* Single\-reference image\-to\-multiple images<br><br>* Multi\-reference image\-to\-multiple images |
|[seedream-4-0-250828](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedream-4-0) |No |No |* Text\-to\-image<br><br>* Single\-reference image\-to\-image<br><br>* Multi\-reference image\-to\-image |* Text\-to\-multiple images<br><br>* Single\-reference image\-to\-multiple images<br><br>* Multi\-reference image\-to\-multiple images |

### Limits

|Model ID |Max IPM |
|---|---|
|[dola-seedream-5-5-261028](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-5) |500 |
|[dola-seedream-5-0-flash-260915](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-0-flash) |500 |
|[dola-seedream-5-0-pro-260628](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-0-pro) |500 |
|[seedream-5-0-260128](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedream-5-0)<br><br>Also supports `seedream-5-0-lite-260128` |500 |
|[seedream-4-5-251128](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedream-4-5) |500 |
|[seedream-4-0-250828](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedream-4-0) |500 |

## 3D generation

### Capabilities

|Provider |Model ID |Input |Output types |File formats |
|---|---|---|---|---|
|Deemos |[hyper3d-gen2-260112](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=hyper3d-gen2) |Text<br><br>Images |White model<br><br>Textured model<br><br>PBR material model<br><br>Textured PBR material model |`.glb`<br><br>`.obj`<br><br>`.stl`<br><br>`.fbx`<br><br>`.usdz` |
|Hitem3D |[hitem3d-2-0-251223](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=hitem3d-2-0) |Images |Standard white model<br><br>Standard textured model<br><br>High\-precision white model<br><br>High\-precision textured model |`.glb`<br><br>`.obj`<br><br>`.stl`<br><br>`.fbx`<br><br>`.usdz` |

### Limits

|Provider |Model ID |Output limits ||Rate limits ||Free quota |
|---|---|---|---|---|---|---|
| | |Polygon count |Resolution |Max RPM |Max concurrency | |
|Deemos |[hyper3d-gen2-260112](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=hyper3d-gen2) |Triangular mesh: 0.5K\-1000K<br><br>Quad mesh: 1K\-200K |No |0.06K |3 |150K |
|Hitem3D |[hitem3d-2-0-251223](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=hitem3d-2-0) |100K\-2000K |1536<br><br>1536pro |0.6K |30 |500K |

## Embedding

Tutorials: [Multimodal embedding](vectorization.md)

API documentation: [Embeddings Multimodal API](multimodal-vectorization-api.md)

### Capabilities

|Model ID |Input |Languages |
|---|---|---|
|[skylark-embedding-vision-251215](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=skylark-embedding-vision) |Video<br><br>Image<br><br>Text |Chinese<br><br>English |

### Limits

|Model ID |Context |Max vector dimension |Max RPM |Max TPM |
|---|---|---|---|---|
|[skylark-embedding-vision-251215](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=skylark-embedding-vision) |128K |2048 |1.2K |1200K |
