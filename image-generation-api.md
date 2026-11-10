# Image generation API

`POST https://ark.ap-southeast.bytepluses.com/api/v3/images/generations`

This document describes the image generation API for Seedream 5.0 pro, Seedream 5.0 flash, Seedream 5.0 lite, Seedream 4.5, and Seedream 4.0, including input and output parameters, value ranges, and notes. Use this reference to understand the fields when calling the API.

**Image generation capabilities by model**

* **Seedream 5.0 pronew**, **Seedream 5.0 flashnew**

   * Layer decomposition: Supports decomposing a single image into one base image and multiple output layers, with up to 16 layers.

   * Interactive editing: Supports specifying edit locations in multiple ways, such as coordinates, selection boxes, and arrows, for precise image editing.

   * Single image generation: **sequential_image_generation** is not supported.

      * Generate a single image from **multiple reference images (2\-10)** + text prompt.

      * Generate a single image from a single reference image + text prompt.

      * Generate a single image from text prompt.

   * Sequential image generation, web search, and streaming output are not currently supported.

* **Seedream 5.0 lite**, **Seedream 4.5 / 4.0**

   * Generate multiple images in sequence \- i.e., a batch of related images generated based on your input; set **sequential_image_generation** to `auto`

      * Generate a batch of related images based on your input of **multiple reference images (2\-14) +** text prompt (the total number of input and output images ≤ 15).

      * Generate a batch of related images (up to 14) from a single reference image + text prompt.

      * Generate a batch of related images (up to 15) from a text prompt.

   * Generate a single image (set **sequential_image_generation** to `disabled`).

      * Generate a single image from **multiple reference images (2\-14)** + text prompt.

      * Generate a single image from a single reference image + text prompt.

      * Generate a single image from text prompt.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Body parameters

**prompt** `string`  |  Prompt

The text prompt used to generate an image or specify the intended layer decomposition.

> **Note**

* > **Prompt language support**: All models support Chinese and English prompts.

   * > `Seedream 5.0 pro` and `Seedream 5.0 flash` also support Russian, Arabic, Filipino, Thai, Turkish, Korean, Malay, Spanish, Portuguese, Indonesian, French, German, Vietnamese, and Japanese.

* > **Prompt length recommendation**: Use no more than 300 Chinese characters or 600 English words. Excessively long prompts may scatter information, causing the model to overlook details and focus only on major elements, which can result in missing details in the generated image.

**Image generation scenario ** **`Required`**

Describes the content to generate. The model generates the corresponding image based on the prompt. Prompt guide: [Seedream 4.0-5.0 prompt guide](seedream-4-0-5-0-prompt-guide.md).

**Layer decomposition scenario ** **`Optional`**

Specifies the intended layer decomposition. The model identifies and decomposes specified elements based on the prompt intent.

If you do not pass a prompt, the model automatically identifies all major elements in the image and decomposes them into separate layers.

**image** `string / string[]`  |  Reference image

Provide the image as a Base64 string or an accessible URL.

* Image URL: Ensure that the image URL is accessible.

* Base64 encoding: The format must be `data:image/<image format>;base64,<Base64 encoding>`. Note that `<image format>` must be in lowercase, e.g., `data:image/png;base64,<base64_image>`.

**Image generation scenario ** **`Optional`**

Seedream 5.0 pro and Seedream 5.0 flash support up to 10 reference images. Seedream 5.0 lite, 4.5, and 4.0 support up to 14 reference images.

**Single\-image input requirements**:

* Image format: jpeg, png, webp, bmp, tiff, gif, heic, or heif

* Aspect ratio (width / height): [1/16, 16]

* Width and height (px): \> 14

* Size: Up to 30 MB

* Total pixels: [196, `6000×6000` (36,000,000)]. The total pixel limit applies to the product of the single image's width and height, rather than to either dimension individually.

**Layer decomposition scenario ** **`Required`**

When layer decomposition mode is enabled (`layer_decomposition` is `true`), `image` is required. Only one image is supported. Passing multiple images causes an error.

**Single\-image input requirements**:

* Image format: png or jpeg

* Aspect ratio: [1/16, 16]

* Size: Up to 30 MB

* Total pixels (width × height): [`512×512` (262,144), `6000×6000` (36,000,000)]. The total pixel limit applies to the product of the single image's width and height, rather than to either dimension individually.

**layer_decompositionnew** `boolean` `Default false`  |  Layer decomposition

Controls whether to enable layer decomposition.

Layer decomposition automatically decomposes subjects, backgrounds, text, and other content in a single image into one base image and up to 16 independently editable layers. Each layer is a PNG image with an alpha channel.

* `true`: Layer decomposition mode. The model decomposes the input image into one base image and multiple layers.

* `false`: Image generation mode. Layer decomposition is not performed.

> **Layer decomposition mode usage notes**

* > Only one image to be decomposed is supported. Passing multiple images causes an error.

* > If any layer fails to generate, the entire request fails. Partial success is not supported.

* > If the prompt requests more layers than the upper limit, some layer information may be lost.

* > The `data` response object returns the position and content information of each output layer, including the layer order (`z_index`), bounding box information (`bounding_box`), name (`name`), and description (`description`).

**Supported models**:

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

**size** `string`  |  Image dimensions

Specifies the dimensions of the generated image. The configuration methods, supported resolutions, default values, total pixel ranges, and aspect ratio ranges vary by model and scenario. Expand the corresponding section below for details.

**Seedream 5.0 pro / Seedream 5.0 flash (image generation scenario)**

Two methods are available, but they cannot be used at the same time.

* Method 1: Specify the resolution level of the generated image, and describe its aspect ratio, shape, or purpose in the prompt using natural language. The model determines the final image size.

   * Default: `2K`

   * Available values: `1K`, `1.5K`, `2K`

> **Pricing note**

> `1.5K` has the same price as `1K`. For details, see [Model pricing](model-pricing.md#c02be6ee). `1.5K` also provides better image generation quality.

* Method 2: Specify the width and height of the generated image in pixels (`widthxheight`):

   * Total pixels range: [`1280x720`(921,600), `2048x2048x1.1025`(4,624,220)]

   * Aspect ratio range: [1/16, 16]

   > **Note**

   > When using method 2, both the total pixel range and the aspect ratio range must be satisfied. The total pixel limit applies to the **product of the single image’s width and height**, rather than to either dimension individually.

   * > **Valid example**: `2048x1024`

   > Total pixel count: 2048x1024=2,097,152, which is within the acceptable range of [921,600, 4,624,220]. Aspect ratio: 2048/1024=2, which is within the acceptable range of [1/16, 16].

   * > **Invalid example**: `512x512`

   > Total pixel count: 512x512=262,144, which does not meet the minimum requirement of 921,600.

When using method 1 and describing a specific aspect ratio in the prompt, refer to the table below for width and height pixel values of the generated image:

|Resolution |Aspect ratio |Width and Height Pixel Values |
|---|---|---|
|1K |1:1 |1024x1024 |
||4:3 |1152x864 |
||3:4 |864x1152 |
||16:9 |1424x800 |
||9:16 |800x1424 |
||3:2 |1248x832 |
||2:3 |832x1248 |
||21:9 |1568x672 |
|1.5K |1:1 |1536x1536 |
||4:3 |1792x1344 |
||3:4 |1344x1792 |
||16:9 |2048x1152 |
||9:16 |1152x2048 |
||3:2 |1872x1248 |
||2:3 |1248x1872 |
||21:9 |2352x1008 |
|2K |1:1 |2048x2048 |
||4:3 |2368x1776 |
||3:4 |1776x2368 |
||16:9 |2816x1584 |
||9:16 |1584x2816 |
||3:2 |2496x1664 |
||2:3 |1664x2496 |
||21:9 |3136x1344 |

**Seedream 5.0 pro / Seedream 5.0 flash (layer decomposition scenario)**

Only resolution levels are supported. The output image resolution rules are as follows:

* **Base image**: The output base image resolution is consistent with the resolution specified by `size`. The output base image keeps the same aspect ratio as the original image to be decomposed.

* **Layers**: Each output layer has a resolution close to the resolution specified by `size`. Each output layer keeps the same aspect ratio as its corresponding region in the original image.

Default value and supported values of `size`:

* Default: `auto`

* Supported values: `1K`, `1.5K`, `2K`, and `auto`. `auto` generates output based on the input image dimensions and aspect ratio.

   > **Pricing note**

   > `1.5K` has the same price as `1K`. For details, see [Model pricing](model-pricing.md#c02be6ee).

> **auto adaptation rules**

> In `auto` mode, the model outputs the base image and each layer based on their original dimensions in the input image:

* > If the original dimensions of the base image and each layer are between [`1280x720` (921,600) and `2048x2048x1.1025` (4,624,220)], each is output at its original dimensions and keeps its own aspect ratio in the input image.

* > If the original dimensions of the base image or a layer are smaller than 1K, that image is output at 1K and keeps its own aspect ratio in the input image.

* > If the original dimensions of the base image or a layer are larger than 2K, that image is output at 2K and keeps its own aspect ratio in the input image.

The following table lists common reference mappings for the actual width and height. The mappings are not limited to these standard values.

|Resolution |Aspect ratio |Width and height pixel values |
|---|---|---|
|1K |1:1 |1024x1024 |
||4:3 |1152x864 |
||3:4 |864x1152 |
||16:9 |1424x800 |
||9:16 |800x1424 |
||3:2 |1248x832 |
||2:3 |832x1248 |
||21:9 |1568x672 |
|1.5K |1:1 |1536x1536 |
||4:3 |1792x1344 |
||3:4 |1344x1792 |
||16:9 |2048x1152 |
||9:16 |1152x2048 |
||3:2 |1872x1248 |
||2:3 |1248x1872 |
||21:9 |2352x1008 |
|2K |1:1 |2048x2048 |
||4:3 |2368x1776 |
||3:4 |1776x2368 |
||16:9 |2816x1584 |
||9:16 |1584x2816 |
||3:2 |2496x1664 |
||2:3 |1664x2496 |
||21:9 |3136x1344 |

**Seedream 5.0 lite**

Specify the output image dimensions. Two methods are available, but they cannot be used at the same time.

* Method 1: Specify the resolution of the generated image, and describe its aspect ratio, shape, or purpose in the prompt using natural language. You let the model determine the width and height.

   * Optional values: `2K`, `3K`, `4K`

* Method 2: Specify the width and height of the generated image in pixels:

   * Default value: `2048x2048`

   * Total pixels range: [`2560x1440`(3,686,400), `4096x4096`(16,777,216)]

   * Aspect ratio range: [1/16, 16]

   > **Note**

   > When using method 2, both the total pixel range and the aspect ratio range must be satisfied simultaneously. The total pixel limit applies to the **product of the single image’s width and height**, rather than to either dimension individually.

   * > **Valid example**: `3750x1250`

   > Total pixel count: 3750x1250=4,687,500, which is within the acceptable range of [3686400, 16777216]. Aspect ratio: 3750/1250=3, which is within the acceptable range of [1/16, 16].

   * > **Invalid example**: `1500x1500`

   > Total pixel count: 1500x1500 = 2,250,000, which does not meet the minimum requirement of 3,686,400. Aspect ratio: 1500/1500 = 1, which meets the range of [1/16, 16]. But it's invalid as it only meets one of the two requirements.

When using method 1 and describing a specific aspect ratio in the prompt, refer to the table below for width and height pixel values of the generated image:

|Resolution |Aspect ratio |Width and Height Pixel Values |
|---|---|---|
|2K |1:1 |2048x2048 |
||4:3 |2304x1728 |
||3:4 |1728x2304 |
||16:9 |2848x1600 |
||9:16 |1600x2848 |
||3:2 |2496x1664 |
||2:3 |1664x2496 |
||21:9 |3136x1344 |
|3K |1:1 |3072x3072 |
||4:3 |3456x2592 |
||3:4 |2592x3456 |
||16:9 |4096x2304 |
||9:16 |2304x4096 |
||2:3 |2496x3744 |
||3:2 |3744x2496 |
||21:9 |4704x2016 |
|4K |1:1 |4096x4096 |
||3:4 |3520x4704 |
||4:3 |4704x3520 |
||16:9 |5504x3040 |
||9:16 |3040x5504 |
||2:3 |3328x4992 |
||3:2 |4992x3328 |
||21:9 |6240x2656 |

**Seedream 4.5**

Specify the output image dimensions. Two methods are available, but they cannot be used at the same time.

* Method 1: Specify the resolution of the generated image, and describe its aspect ratio, shape, or purpose in the prompt using natural language. You let the model determine the width and height.

   * Optional values: `2K`, `4K`

* Method 2: Specify the width and height of the generated image in pixels:

   * Default value: `2048x2048`

   * Total pixels range: [`2560x1440=3,686,400`, `4096x4096=16,777,216`]

   * Aspect ratio range: [1/16, 16]

   > **Note**

   > When using Method 2, both the total pixel range and the aspect ratio range must be satisfied simultaneously. The total pixel limit applies to the **product of the single image’s width and height**, rather than to either dimension individually.

   * > **Valid example**: `3750x1250`

   > Total pixel count: 3750x1250=4,687,500, which is within the acceptable range of [3,686,400, 16,777,216]. Aspect ratio: 3750/1250=3, which is within the acceptable range of [1/16, 16].

   * > **Invalid example**: `1500x1500`

   > Total pixel count: 1500x1500 = 2,250,000, which does not meet the minimum requirement of 3,686,400. Aspect ratio: 1500/1500 = 1, which meets the range of [1/16, 16]. But it's invalid as it only meets one of the two requirements.

When using method 1 and describing a specific aspect ratio in the prompt, refer to the table below for width and height pixel values of the generated image:

|Resolution |Aspect ratio |Width and Height Pixel Values |
|---|---|---|
|2K |1:1 |2048x2048 |
||4:3 |2304x1728 |
||3:4 |1728x2304 |
||16:9 |2848x1600 |
||9:16 |1600x2848 |
||3:2 |2496x1664 |
||2:3 |1664x2496 |
||21:9 |3136x1344 |
|4K |1:1 |4096x4096 |
||3:4 |3520x4704 |
||4:3 |4704x3520 |
||16:9 |5504x3040 |
||9:16 |3040x5504 |
||2:3 |3328x4992 |
||3:2 |4992x3328 |
||21:9 |6240x2656 |

**Seedream 4.0**

Specify the output image dimensions. Two methods are available, but they cannot be used at the same time.

* Method 1: Specify the resolution of the generated image, and describe its aspect ratio, shape, or purpose in the prompt using natural language. You let the model determine the width and height.

   * Optional values: `1K`, `2K`, `4K`

* Method 2: Specify the width and height of the generated image in pixels:

   * Default value: `2048x2048`

   * Total pixels range: [`1280x720=921,600`, `4096x4096=16,777,216`]

   * Aspect ratio range: [1/16, 16]

   > **Note**

   > When using method 2, both the total pixel range and the aspect ratio range must be satisfied simultaneously. The total pixel limit applies to the **product of the single image’s width and height**, rather than to either dimension individually.

   * > **Valid example**: `1600x600`

   > Total pixel count: 1600x600 = 960,000, which is within the acceptable range of [921,600, 16,777,216]. Aspect ratio: 1600/600 = 8/3, which is within the acceptable range of [1/16, 16].

   * > **Invalid example**: `800x800`

   > Total pixel count: 800x800 = 640,000, which does not meet the minimum requirement of 921,600. Aspect ratio: 800/800 = 1, which meets the range of [1/16, 16]. But it's invalid as it only meets one of the two requirements.

When using method 1 and describing a specific aspect ratio in the prompt, the model maps it to the following actual width and height pixel values:

|Resolution |Aspect ratio |Width and Height Pixel Values |
|---|---|---|
|1K |1:1 |1024x1024 |
||4:3 |1152x864 |
||3:4 |864x1152 |
||16:9 |1280x720 |
||9:16 |720x1280 |
||3:2 |1248x832 |
||2:3 |832x1248 |
||21:9 |1512x648 |
|2K |1:1 |2048x2048 |
||4:3 |2304x1728 |
||3:4 |1728x2304 |
||16:9 |2848x1600 |
||9:16 |1600x2848 |
||3:2 |2496x1664 |
||2:3 |1664x2496 |
||21:9 |3136x1344 |
|4K |1:1 |4096x4096 |
||3:4 |3520x4704 |
||4:3 |4704x3520 |
||16:9 |5504x3040 |
||9:16 |3040x5504 |
||2:3 |3328x4992 |
||3:2 |4992x3328 |
||21:9 |6240x2656 |

**optimize_prompt_options** `object`  |  Prompt optimization settings

Configuration for prompt optimization.

**mode** `string` `Default standard`  |  Optimization mode

`optimize_prompt_options.mode`

Specifies the prompt optimization mode.

* `standard`: Standard mode, which provides higher\-quality output but takes longer.

* `fast`: Fast mode, which reduces generation latency but may provide slightly lower\-quality output than standard mode. Seedream 5.0 flash, Seedream 5.0 lite, and Seedream 4.5 do not currently support this mode.

> **Tip**

> If your application is latency\-sensitive, use Seedream 5.0 pro in `fast` mode or use Seedream 5.0 flash for faster image generation.

**output_format** `string` `Default jpeg`  |  Image format

Specifies the file format of the generated image. Valid values:

* `png`

* `jpeg`.

> **Note**

* > In layer decomposition scenarios, `output_format` controls only the output format of the base image. Layers are always output in `png` format.

**Supported models**:

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

* `Seedream 5.0 lite`

**backgroundnew** `string` `Default opaque`  |  Image alpha channel

Controls whether to generate an image with an alpha channel. Valid values:

* `transparent`: Transparent background mode. The output image has a transparent background.

* `opaque`: Opaque background mode. The output image has a standard, opaque background.

> **Requirements for transparent background mode**

* > This parameter is supported only for image\-to\-image generation with exactly one input image that has an alpha channel.

* > The output image format defaults to `png`. If `output_format` is set to `jpeg`, the request returns an error.

* > If the input image uses a format that does not support an alpha channel, such as `jpeg`, the request returns an error.

**Supported models**:

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

**response_format** `string` `Default url`  |  Response format

Specifies how generated images are returned. Valid values:

* `url`: Returns a download URL for the image. **The URL is valid for 24 hours after the image is generated. Download the image promptly.**

* `b64_json`: Returns the image data as a Base64\-encoded string in JSON.

**sequential_image_generation** `string` `Default disabled`  |  Sequential image generation

Controls sequential image generation, which generates a set of related images based on your input.

* `auto`: The model determines whether to return multiple images and how many images to return based on the prompt.

* `disabled`: Disables sequential image generation. The model generates only one image.

For an example, see [Sequential image generation](image-generation-tutorial.md#b4da5e23).

**Supported models**:

* `Seedream 5.0 lite`

* `Seedream 4.5`

* `Seedream 4.0`

**sequential_image_generation_options** `object`  |  Sequential image generation settings

Configuration for sequential image generation. This parameter takes effect only when `sequential_image_generation` is set to `auto`.

**Supported models**:

* `Seedream 5.0 lite`

* `Seedream 4.5`

* `Seedream 4.0`

**max_images** `integer` `Default 15`  |  Maximum number of generated images

`sequential_image_generation_options.max_images`

Specifies the maximum number of images that can be generated for this request.

> **Tip**

> The actual number of generated images also depends on the number of input reference images. **Number of input reference images + number of generated images ≤ 15.**

**Range**: `[1, 15]`

**stream** `boolean` `Default false`  |  Streaming output

Controls whether to enable streaming output.

* `false`: Non\-streaming mode. Returns all information after all images have been generated.

* `true`: Streaming mode. Returns each image result immediately after it is generated. This mode applies to both single\-image and sequential image generation.

For an example, see [Streaming output](image-generation-tutorial.md#e5bef0d7).

**Supported models**:

* `Seedream 5.0 lite`

* `Seedream 4.5`

* `Seedream 4.0`

**watermark** `boolean` `Default true`  |  Watermark

Controls whether to add a watermark to the generated image.

* `false`: Does not add a watermark.

* `true`: Adds an "AI\-generated" watermark to the lower\-right corner of the image.

**model** `string` `Required`  |  Model ID

The ID of the model to call. Activate the model on the [Model activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenTokenDrawer=false) page, and then find its [Model ID](model-list.md#9df4d9fd).

You can also call the model by using an Endpoint ID to access advanced capabilities such as rate limits, payment options, runtime status, monitoring, and security. For details, see [Get an Endpoint ID](online-inference-standard.md).

## Response parameters

### Non\-streaming response

**created** `integer`  |  Creation time

The Unix timestamp in seconds of the creation time of the request.

**model** `string`  |  Model ID

The model ID used for image generation (`model name-version`).

**data** `object[]`  |  Image data

Information about the output images. The returned fields vary by task. `url`, `b64_json`, `size`, and `output_format` are common response fields.

* In sequential image generation, if an image fails to generate, the response additionally includes error information for that image in `error`.

* In layer decomposition, the response additionally includes the stacking order (`z_index`), name (`name`), description (`description`), and bounding box coordinates (`bounding_box`) of each layer.

**Common fields**

**url** `string`  |  Image URL

`data.url`

The URL of the image, returned when **response_format** is specified as `url`. This link will expire **within 24 hours** of generation. Be sure to save the image before expiration.

We recommend configuring [BytePlus TOS data subscription](https://docs.byteplus.com/en/docs/tos/Data_subscription) to automatically transfer model outputs to your own TOS bucket for long\-term backup or further processing.

**b64_json** `string`  |  Image Base64 data

`data.b64_json`

The Base64 information of the image; returned when **response_format** is specified as `b64_json`.

**size** `string`  |  Image dimensions

`data.size`

The width and height of the image in pixels, in the format `<width>x<height>`, such as `2048x2048`.

**output_formatnew** `string`  |  Output format

`data.output_format`

The output image format (`png` or `jpeg`).

> **Note**

* > In layer decomposition scenarios, `output_format` controls only the output format of the base image. Layers are always output in `png` format.

**Supported models**:

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

**Special fields (layer decomposition)**

**z_indexnew** `integer`  |  Layer stacking order

`data.z_index`

The stacking order index of the layer. The base image is fixed to `0`. Layers start from `1` and increment in order. A larger value indicates a higher layer.

**Supported models**:

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

**namenew** `string`  |  Layer name

`data.name`

The name or label of the current decomposed layer. The model generates this field from the characteristics of the decomposed subject to identify the main content of the layer.

**Supported models**:

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

**descriptionnew** `string`  |  Layer description

`data.description`

The detailed description of the current decomposed layer. This model\-generated semantic description provides richer layer characteristics than `name`, such as color, state, and material.

**Supported models**:

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

**bounding_boxnew** `object`  |  Layer bounding box

`data.bounding_box`

The bounding box of the output layer in the coordinate system of the output base image. This field is returned only for layers. The base image covers the entire canvas and does not return this field.

> **Usage notes**

* > `bounding_box` indicates the relative position and aspect ratio of the layer in the image to be decomposed, scaled proportionally to the coordinate system of the output base image.

   * > To restore a layer to the output base image, use `absolute` coordinates first.

   * > To restore a layer to any custom canvas, use `normalized` coordinates.

**Supported models**:

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

**absolute** `array`  |  Absolute coordinates

`data.bounding_box.absolute`

The absolute pixel coordinates of the output layer bounding box in the coordinate system of the output base image, in pixels. The top\-left corner of the output base image is `(0, 0)`. The format is an array of four integers: `[left, top, right, bottom]`.

* `left`: The horizontal distance from the left boundary of the layer to the left boundary of the output base image.

* `top`: The vertical distance from the top boundary of the layer to the top boundary of the output base image.

* `right`: The horizontal distance from the right boundary of the layer to the left boundary of the output base image.

* `bottom`: The vertical distance from the bottom boundary of the layer to the top boundary of the output base image.

> **Coordinate example**

> For example, `[225, 442, 796, 1414]` indicates that the top\-left corner of the layer is `(225, 442)` and the bottom\-right corner is `(796, 1414)`.

> **Restore a layer by using bounding_box.absolute**

> You can use `bounding_box.absolute` to restore the output layer to its bounding box area in the coordinate system of the output base image. Calculate the placement as follows:

* > Top\-left coordinates of the layer in the output base image: `x = left`, `y = top`.

* > Width occupied by the layer in the output base image: `w = right - left`.

* > Height occupied by the layer in the output base image: `h = bottom - top`.

> Scale the output layer to `w × h`, and then place it at `(x, y)` on the output base image. When combining multiple layers, stack them in ascending order of `z_index`.

**normalized** `array`  |  Normalized coordinates

`data.bounding_box.normalized`

The normalized coordinates of the layer bounding box in the coordinate system of the output base image. These coordinates are converted from absolute coordinates based on the width and height of the output base image to discrete integers in the range `[0, 1000]`. The format is an array of four integers: `[left, top, right, bottom]`.

* `left` and `right`: The relative positions of the left and right boundaries of the layer along the width of the base image. `0` indicates the leftmost side of the base image, and `1000` indicates the rightmost side.

* `top` and `bottom`: The relative positions of the top and bottom boundaries of the layer along the height of the base image. `0` indicates the top side of the base image, and `1000` indicates the bottom side.

> **Coordinate example**

> For example, `[220, 432, 777, 1000]` indicates that the left boundary of the layer is 22.0% of the base image width away from the left boundary, the top boundary is 43.2% of the base image height away from the top boundary, the right boundary is 77.7% of the base image width away from the left boundary, and the bottom boundary aligns with the bottom edge of the base image.

> **Restore a layer by using bounding_box.normalized**

> You can use `bounding_box.normalized` to restore the output layer to the bounding box area in a target canvas. Assume that the target canvas size is `W × H`:

* > Top\-left coordinates of the layer in the target canvas: `x = left / 1000 × W`, `y = top / 1000 × H`.

* > Width occupied by the layer in the target canvas: `w = (right - left) / 1000 × W`.

* > Height occupied by the layer in the target canvas: `h = (bottom - top) / 1000 × H`.

> Scale the output layer to `w × h`, and then place it at `(x, y)` on the target canvas. Normalized coordinates are integers, so rounding errors may occur after conversion.

**Special fields (sequential image generation)**

**error** `object`  |  Single\-image error

`data.error`

Error information returned when an individual image fails to generate. Other successfully generated images are not affected.

> **Note**

> When an image fails during sequential image generation:

* > If content moderation rejects the image, the model continues with the next image generation task. Other image generation tasks in the same request are not affected.

* > If an internal service error (`500`) occurs, the model does not continue with the next image generation task.

**Supported models**:

* `Seedream 5.0 lite`

* `Seedream 4.5`

* `Seedream 4.0`

**code** `string`  |  Error code

`data.error.code`

The error code. For details, see [Error codes](error-codes.md).

**message** `string`  |  Error message

`data.error.message`

The error message used for troubleshooting.

**error** `object`  |  Error information

Top\-level error information returned when the request fails to generate any images.

**code** `string`  |  Error code

`error.code`

The error code. For details, see [Error codes](error-codes.md).

**message** `string`  |  Error message

`error.message`

The error message used for troubleshooting.

**usage** `object`  |  Usage information

Usage information for this request, including the number of generated images and consumed tokens.

**generated_images** `integer`  |  Successfully generated images

`usage.generated_images`

The number of images successfully generated by the model, excluding failed images. Billing is based only on successfully generated images.

**input_imagesnew** `integer`  |  Input images

`usage.input_images`

The number of images input to the model.

**Supported models**:

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

**output_tokens** `integer`  |  Output tokens

`usage.output_tokens`

The number of tokens consumed for images generated by the model. The value is calculated by rounding `sum(image width × image height) / 256` to an integer.

**total_tokens** `integer`  |  Total tokens

`usage.total_tokens`

The total number of tokens consumed by this request. Input tokens are not currently calculated, so this value is the same as `output_tokens`.

### Streaming response

See [Image generation streaming response events](image-generation-streaming-responses.md) for the nested field structure.
