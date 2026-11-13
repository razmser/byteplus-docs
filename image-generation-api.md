# Image generation API

`POST https://ark.ap-southeast.bytepluses.com/api/v3/images/generations`

This document describes the API for invoking image generation models, including input and output parameters, value ranges, and notes, for reference when using the API.

**Image generation capabilities by model**

* **Seedream 5.5new**

   * Layer decomposition: Supports decomposing a single image into one base image and multiple output layers, with up to 99 layers. You can control the maximum total number of output images.

   * Rich\-text OCR: During layer decomposition, supports recognizing and returning text content and style information for regular text, including font, color, bold, italic, underline, strikethrough, shadow, and translucency.

   * Native RGBA images: Supports directly generating RGBA images with alpha channels from text or reference images.

   * Single image generation: Configuring `sequential_image_generation` is not supported.

      * Generate a single image from **multiple reference images (2\-10)** + text prompt.

      * Generate a single image from a single reference image + text prompt.

      * Generate a single image from text prompt.

   * Sequential image generation, web search, and streaming output are not currently supported.

* **Seedream 5.0 pronew**, **Seedream 5.0 flashnew**

   * Layer decomposition: Supports decomposing a single image into one base image and multiple output layers, with up to 16 layers.

   * Interactive editing: Supports specifying edit locations in multiple ways, such as coordinates, selection boxes, and arrows, for precise image editing.

   * Single image generation: `sequential_image_generation` is not supported.

      * Generate a single image from **multiple reference images (2\-10)** + text prompt.

      * Generate a single image from a single reference image + text prompt.

      * Generate a single image from text prompt.

   * Sequential image generation, web search, and streaming output are not currently supported.

* **Seedream 5.0 lite**, **Seedream 4.5 / 4.0**

   * Generate multiple images in sequence \- i.e., a batch of related images generated based on your input; set `sequential_image_generation` to `auto`

      * Generate a batch of related images based on your input of **multiple reference images (2\-14) +** text prompt (the total number of input and output images ≤ 15).

      * Generate a batch of related images (up to 14) from a single reference image + text prompt.

      * Generate a batch of related images (up to 15) from a text prompt.

   * Generate a single image (set `sequential_image_generation` to `disabled`).

      * Generate a single image from **multiple reference images (2\-14)** + text prompt.

      * Generate a single image from a single reference image + text prompt.

      * Generate a single image from text prompt.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Body parameters

**model** `string` `Required`  |  Model ID

The ID of the model to call. Activate the model on the [Model activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenTokenDrawer=false) page, and then find its [Model ID](model-list.md#9df4d9fd).

You can also call the model by using an Endpoint ID to access advanced capabilities such as rate limits, payment options, runtime status, monitoring, and security. For details, see [Get an Endpoint ID](online-inference-standard.md).

**prompt** `string`  |  Prompt

The text prompt used to generate an image or specify the intended layer decomposition.

> **Note**

* > **Prompt language support**: All models support Chinese and English prompts.

   * > `Seedream 5.5`, `Seedream 5.0 pro`, and `Seedream 5.0 flash` also support Russian, Arabic, Filipino, Thai, Turkish, Korean, Malay, Spanish, Portuguese, Indonesian, French, German, Vietnamese, and Japanese.

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

Seedream 5.5, Seedream 5.0 pro, and Seedream 5.0 flash support up to 10 reference images. Seedream 5.0 lite, 4.5, and 4.0 support up to 14 reference images.

**Single\-image input requirements**:

* Image format: jpeg, png, webp, bmp, tiff, gif, heic, or heif

* Aspect ratio (width / height): [1/16, 16]

* Width and height (px): \> 14

* Size: Up to 30 MB

* Total pixels: [196, `6000×6000` (36,000,000)]. The total pixel limit applies to the product of the single image's width and height, rather than to either dimension individually.

**Layer decomposition scenario ** **`Required`**

When layer decomposition mode is enabled for Seedream 5.5, Seedream 5.0 pro, or Seedream 5.0 flash (`layer_decomposition` is `true`), `image` is required. Only one image is supported. Passing multiple images causes an error.

**Single\-image input requirements**:

* Image format: png or jpeg

* Aspect ratio: [1/16, 16]

* Size: Up to 30 MB

* Total pixels (width × height): [`512×512` (262,144), `6000×6000` (36,000,000)]. The total pixel limit applies to the product of the single image's width and height, rather than to either dimension individually.

**layer_decompositionnew** `boolean` `Default false`  |  Layer decomposition

Controls whether to enable layer decomposition.

Layer decomposition automatically decomposes subjects, backgrounds, text, and other content in a single image into one base image and multiple independently editable layers. Each layer is a PNG image with an alpha channel.

* `true`: Layer decomposition mode. The model decomposes the input image into one base image and multiple layers.

* `false`: Image generation mode. Layer decomposition is not performed.

> **Layer decomposition mode usage notes**

* > Only one image to be decomposed is supported. Passing multiple images causes an error.

* > If any layer fails to generate, the entire request fails. Partial success is not supported.

* > If the prompt requests more layers than the upper limit, some layer information may be lost.

* > The `data` response object returns the position and content information of each output layer, including the layer order (`z_index`), bounding box information (`bounding_box`), name (`name`), and description (`description`).

**Supported models**:

* **Seedream 5.5**: Up to one base image and 99 layers; supports setting the maximum output image count (including the base image) and recognizing rich\-text OCR for regular text.

* **Seedream 5.0 pro**: Up to one base image and 16 layers.

* **Seedream 5.0 flash**: Up to one base image and 16 layers.

**layer_decomposition_optionsnew** `object`  |  Layer decomposition configuration

Configuration for the layer decomposition feature. Takes effect only when `layer_decomposition` is `true`.

**Supported models**:

* `Seedream 5.5`

**max_imagesnew** `integer` `Default 30`  |  Maximum output image count

`layer_decomposition_options.max_images`

Sets the maximum total number of images generated by this request, including the base image and layers.

**Range**: `[2, 100]`

> **Note**

* > Each request pre\-deducts IPM based on `max_images`. If fewer images are actually generated, the excess quota is returned.

* > When the prompt does not specify how to decompose the image or is unclear, the model plans the decomposition based on `max_images` and the complexity of the input image. A larger `max_images` value generally produces a more fine\-grained decomposition.

**text_recognitionnew** `boolean` `Default false`  |  Rich\-text OCR return switch

`layer_decomposition_options.text_recognition`

Controls whether to additionally return rich\-text OCR results for regular text layers. It supports recognizing text content and text styles, including bold, italic, underline, strikethrough, shadow, and translucency.

* `true`: Returns rich\-text OCR results through `data[].text_recognition`.

* `false`: Does not return rich\-text OCR results.

> **Note**

* > For the supported font range, see [Seedream 5.5 OCR supported fonts](ocr-supported-fonts.md).

* > OCR results are not returned for non\-text layers or for irregular text layers such as artistic text and handwriting.

**size** `string`  |  Image dimensions

Specifies the dimensions of the generated image. The configuration methods, supported resolutions, default values, total pixel ranges, and aspect ratio ranges vary by model and scenario. Expand the corresponding section below for details.

**Seedream 5.5 (image generation)**

Choose one of the following methods; do not combine them:

* Method 1 (recommended): Specify a resolution tier and describe the image aspect ratio, shape, or intended use in the prompt. The model determines the final image size.

   * Default: `2K`

   * Values: `1K`, `1.5K`, `2K`

* Method 2: Specify width and height in pixels (`widthxheight`).

   * Total pixels: [`1280x720` (921,600), `2048x2048x1.1025` (4,624,220)]

   * Aspect ratio: [1/16, 16]

> **tip**

> For method 2, both the total\-pixel and aspect\-ratio ranges must be met. Total pixels are the product of a single image's width and height, not limits on either dimension separately.

* > **Valid example**: `2880x1440`. It has 4,147,200 pixels, within [921,600, 4,624,220], and an aspect ratio of 2, within [1/16, 16].

* > **Invalid example**: `512x512`. It has 262,144 pixels, below the minimum of 921,600.

For method 1, common reference values mapped by the model are:

|Resolution |Aspect ratio |Dimensions |
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

**Seedream 5.5 (layer decomposition)**

Only the resolution tier can be specified. The resolution rules for output images are as follows:

* **Base image**: The resolution of the output base image is the same as the resolution specified by `size`; the aspect ratio of the base image is the same as that of the original image.

* **Each layer**: The resolution of the output layer is close to the resolution specified by `size`; each layer preserves its aspect ratio in the original image.

Default and available values of `size`:

* Default: `auto`

* Values: `1K`, `1.5K`, `2K`, `auto` (adjusts output based on the input image's dimensions and aspect ratio)

> **auto adaptation rules**

> In auto mode, the model uses the original size of the base image and each layer:

* > For sizes between [`1280x720` (921,600), `2048x2048x1.1025` (4,624,220)], it outputs the original size and preserves the original aspect ratio.

* > For sizes below 1K, it outputs at 1K and preserves the original aspect ratio.

* > For sizes above 2K, it outputs at 2K and preserves the original aspect ratio.

Common width and height pixel values mapped by the model (not limited to the standard values below):

|Resolution |Aspect ratio |Dimensions |
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

**Seedream 5.0 pro / Seedream 5.0 flash (image generation scenario)**

Two methods are available, but they cannot be used at the same time.

* Method 1: Specify the resolution level of the generated image, and describe its aspect ratio, shape, or purpose in the prompt using natural language. The model determines the final image size.

   * Default: `2K`

   * Available values: `1K`, `1.5K`, `2K`

* Method 2: Specify the width and height of the generated image in pixels (`widthxheight`):

   * Total pixels range: [`1280x720`(921,600), `2048x2048x1.1025`(4,624,220)]

   * Aspect ratio range: [1/16, 16]

> **tip**

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

* Supported values: `1K`, `1.5K`, `2K`, and `auto`. auto generates output based on the input image dimensions and aspect ratio.

> **auto adaptation rules**

> In auto mode, the model outputs the base image and each layer based on their original dimensions in the input image:

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

> **tip**

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
||3:2 |3744x2496 |
||2:3 |2496x3744 |
||21:9 |4704x2016 |
|4K |1:1 |4096x4096 |
||4:3 |4704x3520 |
||3:4 |3520x4704 |
||16:9 |5504x3040 |
||9:16 |3040x5504 |
||3:2 |4992x3328 |
||2:3 |3328x4992 |
||21:9 |6240x2656 |

**Seedream 4.5**

Specify the output image dimensions. Two methods are available, but they cannot be used at the same time.

* Method 1: Specify the resolution of the generated image, and describe its aspect ratio, shape, or purpose in the prompt using natural language. You let the model determine the width and height.

   * Optional values: `2K`, `4K`

* Method 2: Specify the width and height of the generated image in pixels:

   * Default value: `2048x2048`

   * Total pixels range: [`2560x1440`, `4096x4096`]

   * Aspect ratio range: [1/16, 16]

> **tip**

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
||4:3 |4704x3520 |
||3:4 |3520x4704 |
||16:9 |5504x3040 |
||9:16 |3040x5504 |
||3:2 |4992x3328 |
||2:3 |3328x4992 |
||21:9 |6240x2656 |

**Seedream 4.0**

Specify the output image dimensions. Two methods are available, but they cannot be used at the same time.

* Method 1: Specify the resolution of the generated image, and describe its aspect ratio, shape, or purpose in the prompt using natural language. You let the model determine the width and height.

   * Optional values: `1K`, `2K`, `4K`

* Method 2: Specify the width and height of the generated image in pixels:

   * Default value: `2048x2048`

   * Total pixels range: [`1280x720`, `4096x4096`]

   * Aspect ratio range: [1/16, 16]

> **tip**

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
||4:3 |4704x3520 |
||3:4 |3520x4704 |
||16:9 |5504x3040 |
||9:16 |3040x5504 |
||3:2 |4992x3328 |
||2:3 |3328x4992 |
||21:9 |6240x2656 |

**optimize_prompt_options** `object`  |  Prompt optimization settings

Configuration for prompt optimization.

**mode** `string` `Default standard`  |  Optimization mode

`optimize_prompt_options.mode`

Sets the mode used for prompt optimization.

* `standard`: Standard mode. Produces higher\-quality content but takes longer.

* `fast`: Fast mode. Produces content more quickly, with slightly lower quality than standard mode.

**Supported models**:

* **Seedream 5.5**: Default `standard`; Options `standard`

* **Seedream 5.0 pro**: Default `standard`; Options `standard`, `fast`

* **Seedream 5.0 flash**: Default `standard`; Options `standard`

* **Seedream 5.0 lite**: Default `standard`; Options `standard`

* **Seedream 4.5**: Default `standard`; Options `standard`

* **Seedream 4.0**: Default `standard`; Options `standard`, `fast`

**output_format** `string`  |  Image format

Specifies the file format of generated images. Valid values: `png` and `jpeg`.

> **Note**

* > In layer decomposition, `output_format` controls only the base image's output format. Layers are always returned in `png` format.

* > The request fails when `output_format` is `jpeg` and `background` is `transparent`.

**Supported models**:

* **Seedream 5.5**: Default `png`; Options `png`, `jpeg`

* **Seedream 5.0 pro**: Default `jpeg`; Options `png`, `jpeg`

* **Seedream 5.0 flash**: Default `jpeg`; Options `png`, `jpeg`

* **Seedream 5.0 lite**: Default `jpeg`; Options `png`, `jpeg`

**backgroundnew** `string`  |  Transparency mode

Controls whether the generated image includes an alpha (transparency) channel. Valid values:

* auto: The model determines the value based on the prompt and `output_format`. If this format is `jpeg`, the generated image has no alpha channel.

* `transparent`: Returns an image with an alpha channel (an RGBA image).

* `opaque`: Returns an image without an alpha channel (an RGB image).

> **Usage notes**

* > This parameter is not supported for layer decomposition. Passing it causes an error.

* > When using `transparent` mode, requirements vary by model:

> |Model |Seedream 5.5 |Seedream 5.0 pro / 5.0 flash |
> |---|---|---|
> |Text\-to\-RGBA |Supported |Not supported |
> |Image\-to\-RGBA |Supported<br><br>Up to 10 reference images |Supported<br><br>Only one reference image is supported |
> |Input image requirements |Alpha channel not required |Alpha channel required |

**Supported models**:

* **Seedream 5.5**: Default `auto`; Options `auto`, `transparent`, `opaque`

* **Seedream 5.0 pro**: Default `opaque`; Options `transparent`, `opaque`

* **Seedream 5.0 flash**: Default `opaque`; Options `transparent`, `opaque`

**response_format** `string` `Default url`  |  Response format

Specifies how generated images are returned. Valid values:

* `url`: Returns a download URL for the image. **The URL is valid for 24 hours after the image is generated. Download the image promptly.**

* `b64_json`: Returns the image data as a Base64\-encoded string in JSON.

**sequential_image_generation** `string` `Default disabled`  |  Sequential image generation

Controls sequential image generation, which generates a set of related images based on your input.

* `auto`: The model determines whether to return multiple images and how many images to return based on the prompt.

* `disabled`: Disables sequential image generation. The model generates only one image.

For an example, see [Sequential image generation](image-generation-tutorial.md#ec79cfda).

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

**Range**: `[1, 15]`

> **tip**

* > Each request pre\-deducts IPM based on `max_images`. If fewer images are actually generated, the excess quota is returned.

* > The actual number of generated images is controlled by `max_images` and also depends on the number of input reference images. **Number of input reference images + number of generated images ≤ 15.**

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

**safety_identifiernew** `string`  |  End\-user identifier

A unique identifier for the end user, used to help the platform detect users in your application that may violate the ModelArk Use Policy. This identifier must be an English string that is stable and unique for each user and no longer than 64 characters.

We recommend passing a string generated by hashing the username, user ID, or email address to avoid exposing user privacy.

**Supported models**:

* `Seedream 5.5`

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

## Response parameters

### Non\-streaming response

**created** `integer`  |  Creation time

The Unix timestamp in seconds of the creation time of the request.

**model** `string`  |  Model ID

The model ID used for image generation (model name\-version).

**data** `object[]`  |  Image data

Information about output images. Returned fields vary by scenario.

Image return format (`url` or `b64_json`), image size (`size`), output format (`output_format`), and background type (`background`) are common response fields.

* **Sequential image generation**: If an image fails to generate, returns error information for that image (`error`).

* **Layer decomposition**: Returns the stacking order (`z_index`), name (`name`), description (`description`), and bounding box coordinates (`bounding_box`) of each layer. Seedream 5.5 can additionally return the rich\-text OCR results (`text_recognition`) for regular text layers.

**Common fields**

**url** `string`  |  Image URL

`data.url`

The URL of the image, returned when **response_format** is specified as `response_format` is `url`. This link will expire **within 24 hours** of generation. Be sure to save the image before expiration.

We recommend configuring [BytePlus TOS data subscription](https://docs.byteplus.com/en/docs/tos/Data_subscription) to automatically transfer model outputs to your own TOS bucket for long\-term backup or further processing.

**b64_json** `string`  |  Image Base64 data

`data.b64_json`

The Base64 information of the image; returned when **response_format** is specified as `response_format` is `b64_json`.

**size** `string`  |  Image dimensions

`data.size`

The width and height of the image in pixels, in the format `<width>x<height>`, such as `2048x2048`.

**output_formatnew** `string`  |  Output format

`data.output_format`

The output image format (`png` or `jpeg`).

> **Note**

> In layer decomposition, `output_format` controls only the base image's output format. Layers are always returned in `png` format.

**Supported models**:

* `Seedream 5.5`

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

**backgroundnew** `string`  |  Actual transparency type

`data.background`

The actual transparency type of the generated image, which can differ from the requested `background` value.

* `transparent`: The image contains transparent or translucent pixels.

* `opaque`: The entire image is opaque.

**Supported models**:

* `Seedream 5.5`

**Special fields (layer decomposition)**

**z_indexnew** `integer`  |  Layer stacking order

`data.z_index`

The stacking order index of the layer. The base image is fixed to `0`. Layers start from `1` and increment in order. A larger value indicates a higher layer.

**Supported models**:

* `Seedream 5.5`

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

**namenew** `string`  |  Layer name

`data.name`

The name or label of the current decomposed layer. The model generates this field from the characteristics of the decomposed subject to identify the main content of the layer.

**Supported models**:

* `Seedream 5.5`

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

**descriptionnew** `string`  |  Layer description

`data.description`

The detailed description of the current decomposed layer. This model\-generated semantic description provides richer layer characteristics than `name`, such as color, state, and material.

**Supported models**:

* `Seedream 5.5`

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

* `Seedream 5.5`

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

**text_recognitionnew** `object`  |  Rich\-text OCR result

`data.text_recognition`

The rich\-text OCR result for the current regular text layer, including the complete text, line\-by\-line positions, and visual styles for each text run.

> **Return conditions**

> This field is returned only when all of the following conditions are met:

* > `layer_decomposition` is `true`.

* > `layer_decomposition_options.text_recognition` is `true`.

* > The model identifies the text in the current layer as **regular text**.

> This field is not returned for non\-text layers or for irregular text layers such as artistic text and handwriting.

**Supported models**:

* `Seedream 5.5`

**text** `string`  |  Complete text

`data.text_recognition.text`

The complete plain text of the current text layer, ordered from top to bottom and left to right, with lines separated by `\n`.

**lines** `object[]`  |  Text lines

`data.text_recognition.lines`

An array of text lines in order.

> **Note**

* > If the current text layer contains multiple lines, `lines[]` contains multiple elements, one for each line.

* > Each element includes the line's position in the base image (`bounding_box`) and a list of style runs (`runs[]`).

**bounding_box** `object`  |  Text\-line bounding box

`data.text_recognition.lines.bounding_box`

Bounding box information for the current text line in the output base\-image coordinate system, including absolute and normalized coordinates. The coordinate format and conversion rules are the same as the corresponding fields in `data.bounding_box`.

**absolute** `array`  |  Absolute coordinates

`data.text_recognition.lines.bounding_box.absolute`

The absolute pixel coordinates of the text\-line bounding box in the output base\-image coordinate system. The top\-left coordinate of the output base image is `(0, 0)`. The format is an array of four integers: `[left, top, right, bottom]`.

* `left`: The horizontal distance from the text line's left edge to the output base image's left edge.

* `top`: The vertical distance from the text line's top edge to the output base image's top edge.

* `right`: The horizontal distance from the text line's right edge to the output base image's left edge.

* `bottom`: The vertical distance from the text line's bottom edge to the output base image's top edge.

**normalized** `array`  |  Normalized coordinates

`data.text_recognition.lines.bounding_box.normalized`

The normalized coordinates of the text\-line bounding box in the output base\-image coordinate system. They are converted from absolute coordinates to the discrete integer range `[0, 1000]` using the output base image's width and height. The format is an array of four integers: `[left, top, right, bottom]`.

* `left` and `right`: The relative positions of the text line's left and right edges along the base image's width. `0` is the left edge, and `1000` is the right edge.

* `top` and `bottom`: The relative positions of the text line's top and bottom edges along the base image's height. `0` is the top edge, and `1000` is the bottom edge.

**runs** `object[]`  |  Style runs

`data.text_recognition.lines.runs`

An array of style runs for the current text line.

> **Note**

* > When the entire line has the same style, `runs[]` contains only one element.

* > When styles such as font, color, or bold vary within a line, `runs[]` contains multiple elements.

**text** `string`  |  Run text

`data.text_recognition.lines.runs.text`

The text segment corresponding to the current element, excluding line breaks.

**style** `object`  |  Visual style

`data.text_recognition.lines.runs.style`

The approximate visual style of the current text segment, including the similar font, text color, bold, italic, underline, strikethrough, shadow, and translucency.

> **Note**

> Style information is identified automatically by the model and may differ from the exact attributes in the original design file.

**similar_font_family** `string`  |  Similar font

`data.text_recognition.lines.runs.style.similar_font_family`

The name of the most similar font matched by the model based on the text's appearance.

> **Note**

* > An exact match to the original font is not guaranteed.

* > Returns `unknown` when the font cannot be recognized or confidence is insufficient.

* > For the supported font range, see [Seedream 5.5 OCR supported fonts](ocr-supported-fonts.md).

**foreground_color** `string`  |  Text color

`data.text_recognition.lines.runs.style.foreground_color`

The color of the current text segment, in uppercase hexadecimal `#RRGGBB` format without an alpha (transparency) channel.

**bold** `boolean`  |  Bold

`data.text_recognition.lines.runs.style.bold`

Whether the model identifies the current text segment as using bold styling.

* `true`: The model identifies bold styling.

* `false`: The model does not identify bold styling.

**italic** `boolean`  |  Italic

`data.text_recognition.lines.runs.style.italic`

Whether the model identifies the current text segment as using italic styling. Returns `true` only when the glyph itself slants to the right; rotation of the entire text layer or slant caused by perspective is not considered italic.

* `true`: The model identifies italic styling.

* `false`: The model does not identify italic styling.

**underline** `boolean`  |  Underline

`data.text_recognition.lines.runs.style.underline`

Whether the model identifies the current text segment as having a text underline. Separate divider lines, table lines, and decorative horizontal lines are not considered text underlines.

* `true`: The model identifies underline styling.

* `false`: The model does not identify underline styling.

**strikethrough** `boolean`  |  Strikethrough

`data.text_recognition.lines.runs.style.strikethrough`

Whether the model identifies the current text segment as having a strikethrough.

* `true`: The model identifies strikethrough styling.

* `false`: The model does not identify strikethrough styling.

**shadow** `boolean`  |  Shadow

`data.text_recognition.lines.runs.style.shadow`

Whether the model identifies the current text segment as having a distinct drop shadow, shadow, or glow offset. Ordinary antialiasing transitions along text edges are not considered shadows.

* `true`: The model identifies shadow styling.

* `false`: The model does not identify shadow styling.

**translucent** `boolean`  |  Translucent

`data.text_recognition.lines.runs.style.translucent`

Whether the text ink itself is visibly translucent, allowing the background texture or color beneath it to show through. Solid light\-colored text, such as light gray or light blue, is not considered translucent.

* `true`: The model identifies translucent styling.

* `false`: The model does not identify translucent styling.

**Special fields (sequential image generation)**

**error** `object`  |  Single\-image error

`data.error`

Error information returned when an individual image fails to generate. Other successfully generated images are not affected.

> **Note**

> When an image fails during sequential image generation:

* > If content moderation rejects the image, the model continues with the next image generation task. Other image generation tasks in the same request are not affected.

* > If an internal service error (500) occurs, the model does not continue with the next image generation task.

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

**safety_identifiernew** `string`  |  End\-user identifier

The unique identifier for the end user. If `safety_identifier` is set in the request, the API returns it unchanged.

**Supported models**:

* `Seedream 5.5`

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

**usage** `object`  |  Usage information

Usage information for this request, including the number of generated images and consumed tokens.

**generated_images** `integer`  |  Successfully generated images

`usage.generated_images`

The number of images successfully generated by the model, excluding failed images. Billing is based only on successfully generated images.

**input_imagesnew** `integer`  |  Input images

`usage.input_images`

The number of images input to the model.

**Supported models**:

* `Seedream 5.5`

* `Seedream 5.0 pro`

* `Seedream 5.0 flash`

**output_tokens** `integer`  |  Output tokens

`usage.output_tokens`

The number of tokens consumed to generate images. It is calculated as `sum(image height * image width) / 256`, rounded down.

**total_tokens** `integer`  |  Total tokens

`usage.total_tokens`

The total number of tokens consumed by this request. Input tokens are not currently calculated, so this value is the same as `output_tokens`.

### Streaming response

See [Image generation streaming response events](image-generation-streaming-responses.md) for the nested field structure.
