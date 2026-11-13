# Dola Seedream 5.5 tutorial

Dola Seedream 5.5 (Seedream 5.5) supports text\-to\-image, single\-image\-to\-image, and multi\-reference\-to\-image generation. It further improves multi\-turn editing consistency, native RGBA image generation, and layer decomposition. Layer decomposition can return one base image and up to 99 layers, plus rich\-text OCR information such as text content, position, and approximate visual styles for regular text layers.

This document focuses on Seedream 5.5 capabilities. For complete Image Generation API field definitions, ranges, and response structures, see [Image generation API](image-generation-api.md).

## Featured capabilities

Seedream 5.5 provides the following featured capabilities.

|Featured capability |Description |Preview |
|---|---|---|
|[Multi-turn editing consistency](seedream-5-5.md#consistent-editing) |* Supports continuous editing of the same subject while preserving subject features, composition, and visual style across multiple turns. |[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/image-generation/seedream-5-5-multi-round-editing-demo.mp4)<br> |
|[Native RGBA images](seedream-5-5.md#native-rgba) |* Generates native RGBA images with alpha channels from text or images, for stickers, icons, product materials, and other alpha channel scenarios. |[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/image-generation/seedream-5-5-rgba-demo-en.mp4)<br> |
|[Fine-grained layer decomposition](seedream-5-5.md#fine-layer-decomposition) and [rich-text OCR](seedream-5-5.md#rich-text-ocr) |* Decomposes one image into one base image and up to 99 independently editable layers, and lets you configure the maximum output image count.<br><br>   * The model returns rich\-text OCR information for regular text layers, including text content, position, and approximate styling, enabling design tools or applications to further edit and composite the text layers. |[video](https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/image-generation/seedream-5-5-ocr-demo-en.mp4)<br> |

## Capability overview

Seedream 5.5 builds on the image\-generation and layer\-decomposition capabilities of Seedream 5.0 pro / flash. It improves multi\-turn editing consistency, expands RGBA image\-generation scenarios and input support, and provides finer layer decomposition and rich\-text OCR.

|Model ||[Seedream 5.5](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-5) |[Seedream 5.0 pro](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-0-pro) |[Seedream 5.0 flash](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-0-flash) |
|---|---|---|---|---|
|Model ID ||dola\-seedream\-5\-5\-261028 |dola\-seedream\-5\-0\-pro\-260628 |dola\-seedream\-5\-0\-flash\-260915 |
|[Text-to-image](image-generation-tutorial.md#9695d195) ||✓ |✓ |✓ |
|[Single-image-to-image](image-generation-tutorial.md#8bc49063) ||✓ |✓ |✓ |
|[Multi-reference-to-image](image-generation-tutorial.md#4a35e28f) ||✓, up to 10 images |✓, up to 10 images |✓, up to 10 images |
|Layer decomposition ||1 base image + up to 99 layers |1 base image + up to 16 layers |1 base image + up to 16 layers |
|[Native RGBA images](seedream-5-5.md#native-rgba) |Text\-to\-image |✓ |— |— |
||Image\-to\-image |✓, up to 10 reference images; no alpha channel required for inputs |✓, one PNG reference image with an alpha channel |✓, one PNG reference image with an alpha channel |
|Maximum output count for layer decomposition ||✓, 2\-100 images including the base image and layers |— |— |
|[Rich-text OCR](seedream-5-5.md#rich-text-ocr) ||✓ |— |— |
|Model parameters |Resolution<br><br>> Image generation<br><br><br> |* Method 1: Resolution preset: `1K`, `1.5K`, `2K`<br><br>* Method 2: Width and height in pixels: [`1280x720`, `2048x2048x1.1025`] |* Method 1: Resolution preset: `1K`, `1.5K`, `2K`<br><br>* Method 2: Width and height in pixels: [`1280x720`, `2048x2048x1.1025`] |* Method 1: Resolution preset: `1K`, `1.5K`, `2K`<br><br>* Method 2: Width and height in pixels: [`1280x720`, `2048x2048x1.1025`] |
||Resolution<br><br>> Layer decomposition<br><br><br> |1K, 1.5K, 2K, auto (default) |1K, 1.5K, 2K, auto (default) |1K, 1.5K, 2K, auto (default) |
||Output format |png (default), jpeg |png, jpeg (default) |png, jpeg (default) |
||Background type<br><br>> Image generation<br><br><br> |auto (default), transparent, opaque |transparent, opaque (default) |transparent, opaque (default) |
||Prompt optimization |standard mode (default) |standard mode (default), fast mode |standard mode (default) |

## Basic usage

Text\-to\-image, single\-image\-to\-image, multi\-reference\-to\-image, and interactive editing use the same workflow as Seedream 5.0 pro / flash. Set `model` to `dola-seedream-5-5-261028`.

For common workflows and code samples, see:

* [Text-to-image](image-generation-tutorial.md#9695d195)

* [Single-image-to-image](image-generation-tutorial.md#8bc49063)

* [Multi-reference-to-image](image-generation-tutorial.md#4a35e28f)

* [Interactive editing](seedream-5-0-pro.md#interactive-edit)

## Multi\-turn editing consistency

Seedream 5.5 supports continuous edits to the same image and improves consistency of subject features, composition, and unchanged regions across turns. Use the image generated in the preceding turn as the next input, and state clearly what to change and preserve.

> **Tip**

> For more consistent multi\-turn editing, we recommend using an image without an alpha channel (an RGB image) as the input and setting `background` to `opaque` while editing.

The following example edits the same portrait continuously. Each turn uses the preceding output as input and preserves the earlier edits.

> The code sample shows only the first request. For later turns, use the prior response image URL as `image` and replace `prompt`.

|Original input |Turn 1<br><br>> Add bubble gum |Turn 2<br><br>> Add sunglasses |Turn 3<br><br>> Change the sunglasses |Result |
|---|---|---|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-multi-round-editing-input.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-round-1-20261103.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-round-2-20261103.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-round-3-sunglasses-20261103.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-multi-round-editing-demo2.gif) |

**cURL**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seedream-5-5-261028",
    "prompt": "Have the person blow a round, semi-transparent pink bubblegum bubble. Preserve the identity, facial features, gaze, hairstyle, pearl earrings, clothing, pose, blue background, composition, and visual style of the person.",
    "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-multi-round-editing-input.png",
    "size": "2K",
    "output_format": "png",
    "background": "opaque",
    "response_format": "url",
    "watermark": true
  }'
```

**Python**

```Python
import os

# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.getenv("ARK_API_KEY"),
)

response = client.images.generate(
    model="dola-seedream-5-5-261028",
    prompt="Have the person blow a round, semi-transparent pink bubblegum bubble. Preserve the identity, facial features, gaze, hairstyle, pearl earrings, clothing, pose, blue background, composition, and visual style of the person.",
    image="https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-multi-round-editing-input.png",
    size="2K",
    output_format="png",
    background="opaque",
    response_format="url",
    watermark=True,
)

for item in response.data:
    print(item)
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.images.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

import java.util.concurrent.TimeUnit;

public class ImageGenerationsExample {
    public static void main(String[] args) {
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .dispatcher(new Dispatcher())
                .connectionPool(new ConnectionPool(5, 1, TimeUnit.SECONDS))
                .apiKey(System.getenv("ARK_API_KEY"))
                .build();

        CreateImageGenerationRequest request = CreateImageGenerationRequest.builder()
                .model("dola-seedream-5-5-261028")
                .prompt("Have the person blow a round, semi-transparent pink bubblegum bubble. Preserve the identity, facial features, gaze, hairstyle, pearl earrings, clothing, pose, blue background, composition, and visual style of the person.")
                .image(CreateImageGenerationRequestImage.ofString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-multi-round-editing-input.png"))
                .size("2K")
                .outputFormat(OutputFormat.PNG)
                .background(Background.OPAQUE)
                .responseFormat(ResponseFormat.URL)
                .watermark(true)
                .build();

        try {
            ImageGenerationResponse response = service.generateImages(request);
            response.getData().forEach(System.out::println);
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

    "github.com/volcengine/ark-runtime-go/arkruntime"
    model "github.com/volcengine/ark-runtime-go/arkruntime/model/images"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    request := &model.CreateImageGenerationRequest{
        Model:          "dola-seedream-5-5-261028",
        Prompt:         model.NewOptString("Have the person blow a round, semi-transparent pink bubblegum bubble. Preserve the identity, facial features, gaze, hairstyle, pearl earrings, clothing, pose, blue background, composition, and visual style of the person."),
        Image:          model.NewOptCreateImageGenerationRequestImage(model.NewStringCreateImageGenerationRequestImage("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-multi-round-editing-input.png")),
        Size:           model.NewOptString("2K"),
        OutputFormat:   model.NewOptOutputFormat(model.OutputFormatPNG),
        Background:     model.NewOptBackground(model.BackgroundOpaque),
        ResponseFormat: model.NewOptResponseFormat(model.ResponseFormatURL),
        Watermark:      model.NewOptBool(true),
    }
    response, err := client.GenerateImages(context.Background(), request)
    if err != nil {
        fmt.Fprintln(os.Stderr, err)
        os.Exit(1)
    }
    for _, item := range response.Data {
        fmt.Printf("%+v\n", item)
    }
}
```

**OpenAI**

```Python
import os
from openai import OpenAI

client = OpenAI(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.getenv("ARK_API_KEY"),
)

response = client.images.generate(
    model="dola-seedream-5-5-261028",
    prompt="Have the person blow a round, semi-transparent pink bubblegum bubble. Preserve the identity, facial features, gaze, hairstyle, pearl earrings, clothing, pose, blue background, composition, and visual style of the person.",
    size="2K",
    output_format="png",
    background="opaque",
    response_format="url",
    extra_body={
        "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-multi-round-editing-input.png",
        "watermark": True,
    },
)

for item in response.data:
    print(item)
```

## Native RGBA images

Seedream 5.5 supports generating RGBA images with alpha channels through text\-to\-image and image\-to\-image. Compared with Seedream 5.0 pro / flash, it expands the scenarios and input range for transparent\-background image generation:

|Comparison |Seedream 5.5 |Seedream 5.0 pro / flash |
|---|---|---|
|Text\-to\-RGBA |Supported |Not supported |
|Image\-to\-RGBA |Supported<br><br>> Up to 10 reference images |Supported<br><br>> One reference image only |
|Input image requirement |No alpha channel required |A PNG image with an alpha channel is required |

Set `background` to `transparent` and `output_format` to `png`.

### Text\-to\-RGBA

Do not pass `image`. Describe the subject and transparent\-background requirement in the prompt.

|Prompt |Output |
|---|---|
|A transparent acrylic standee with a Christmas theme, a rectangular outline, and a thin white edge on all sides that brings out the transparent material. Use a scrapbook collage style: place a red\-and\-green two\-tone vinyl record with a golden rim at the center, surrounded by a dimensional Christmas wreath of acorn leaves and pine branches. Decorate the wreath with hand\-drawn red berries, golden four\-pointed stars, reindeer, and snowflake stickers. Overlay a minimalist white music\-player UI below, with play/pause controls, a progress bar (01:24 / 04:32), and a volume icon. Use vintage red, forest green, champagne gold, and off\-white. Add occasional highlights refracted by the acrylic thickness for a realistic product finish. Set a transparent background. |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-text-to-image-output.png) |

**cURL**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seedream-5-5-261028",
    "prompt": "A transparent acrylic standee with a Christmas theme, a rectangular outline, and a thin white edge on all sides that brings out the transparent material. Use a scrapbook collage style: place a red-and-green two-tone vinyl record with a golden rim at the center, surrounded by a dimensional Christmas wreath of acorn leaves and pine branches. Decorate the wreath with hand-drawn red berries, golden four-pointed stars, reindeer, and snowflake stickers. Overlay a minimalist white music-player UI below, with play/pause controls, a progress bar (01:24 / 04:32), and a volume icon. Use vintage red, forest green, champagne gold, and off-white. Add occasional highlights refracted by the acrylic thickness for a realistic product finish. Set a transparent background.",
    "size": "2K",
    "output_format": "png",
    "background": "transparent",
    "response_format": "url",
    "watermark": true
  }'
```

**Python**

```Python
import os

# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.getenv("ARK_API_KEY"),
)

response = client.images.generate(
    model="dola-seedream-5-5-261028",
    prompt="A transparent acrylic standee with a Christmas theme, a rectangular outline, and a thin white edge on all sides that brings out the transparent material. Use a scrapbook collage style: place a red-and-green two-tone vinyl record with a golden rim at the center, surrounded by a dimensional Christmas wreath of acorn leaves and pine branches. Decorate the wreath with hand-drawn red berries, golden four-pointed stars, reindeer, and snowflake stickers. Overlay a minimalist white music-player UI below, with play/pause controls, a progress bar (01:24 / 04:32), and a volume icon. Use vintage red, forest green, champagne gold, and off-white. Add occasional highlights refracted by the acrylic thickness for a realistic product finish. Set a transparent background.",
    size="2K",
    output_format="png",
    background="transparent",
    response_format="url",
    watermark=True,
)

for item in response.data:
    print(item)
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.images.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

import java.util.concurrent.TimeUnit;

public class ImageGenerationsExample {
    public static void main(String[] args) {
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .dispatcher(new Dispatcher())
                .connectionPool(new ConnectionPool(5, 1, TimeUnit.SECONDS))
                .apiKey(System.getenv("ARK_API_KEY"))
                .build();

        CreateImageGenerationRequest request = CreateImageGenerationRequest.builder()
                .model("dola-seedream-5-5-261028")
                .prompt("A transparent acrylic standee with a Christmas theme, a rectangular outline, and a thin white edge on all sides that brings out the transparent material. Use a scrapbook collage style: place a red-and-green two-tone vinyl record with a golden rim at the center, surrounded by a dimensional Christmas wreath of acorn leaves and pine branches. Decorate the wreath with hand-drawn red berries, golden four-pointed stars, reindeer, and snowflake stickers. Overlay a minimalist white music-player UI below, with play/pause controls, a progress bar (01:24 / 04:32), and a volume icon. Use vintage red, forest green, champagne gold, and off-white. Add occasional highlights refracted by the acrylic thickness for a realistic product finish. Set a transparent background.")
                .size("2K")
                .outputFormat(OutputFormat.PNG)
                .background(Background.TRANSPARENT)
                .responseFormat(ResponseFormat.URL)
                .watermark(true)
                .build();

        try {
            ImageGenerationResponse response = service.generateImages(request);
            response.getData().forEach(System.out::println);
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

    "github.com/volcengine/ark-runtime-go/arkruntime"
    model "github.com/volcengine/ark-runtime-go/arkruntime/model/images"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    request := &model.CreateImageGenerationRequest{
        Model:          "dola-seedream-5-5-261028",
        Prompt:         model.NewOptString("A transparent acrylic standee with a Christmas theme, a rectangular outline, and a thin white edge on all sides that brings out the transparent material. Use a scrapbook collage style: place a red-and-green two-tone vinyl record with a golden rim at the center, surrounded by a dimensional Christmas wreath of acorn leaves and pine branches. Decorate the wreath with hand-drawn red berries, golden four-pointed stars, reindeer, and snowflake stickers. Overlay a minimalist white music-player UI below, with play/pause controls, a progress bar (01:24 / 04:32), and a volume icon. Use vintage red, forest green, champagne gold, and off-white. Add occasional highlights refracted by the acrylic thickness for a realistic product finish. Set a transparent background."),
        Size:           model.NewOptString("2K"),
        OutputFormat:   model.NewOptOutputFormat(model.OutputFormatPNG),
        Background:     model.NewOptBackground(model.BackgroundTransparent),
        ResponseFormat: model.NewOptResponseFormat(model.ResponseFormatURL),
        Watermark:      model.NewOptBool(true),
    }
    response, err := client.GenerateImages(context.Background(), request)
    if err != nil {
        fmt.Fprintln(os.Stderr, err)
        os.Exit(1)
    }
    for _, item := range response.Data {
        fmt.Printf("%+v\n", item)
    }
}
```

**OpenAI**

```Python
import os
from openai import OpenAI

client = OpenAI(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.getenv("ARK_API_KEY"),
)

response = client.images.generate(
    model="dola-seedream-5-5-261028",
    prompt="A transparent acrylic standee with a Christmas theme, a rectangular outline, and a thin white edge on all sides that brings out the transparent material. Use a scrapbook collage style: place a red-and-green two-tone vinyl record with a golden rim at the center, surrounded by a dimensional Christmas wreath of acorn leaves and pine branches. Decorate the wreath with hand-drawn red berries, golden four-pointed stars, reindeer, and snowflake stickers. Overlay a minimalist white music-player UI below, with play/pause controls, a progress bar (01:24 / 04:32), and a volume icon. Use vintage red, forest green, champagne gold, and off-white. Add occasional highlights refracted by the acrylic thickness for a realistic product finish. Set a transparent background.",
    size="2K",
    output_format="png",
    background="transparent",
    response_format="url",
    extra_body={"watermark": True},
)

for item in response.data:
    print(item)
```

### Image\-to\-RGBA

Pass one reference image or up to 10 reference images with `image`. This example extracts stickers from two images and places them in a single transparent\-background image.

|Prompt |Input image 1 |Input image 2 |Output |
|---|---|---|---|
|Extract the sticker elements from Image 1 and Image 2 separately, then place all extracted stickers in one transparent\-background image. Preserve each sticker's original text, colors, shapes, and details; remove all background content other than the stickers. Arrange the stickers separately without overlap, with sharp and intact edges. |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test3.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test4.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-transparent-stickers-output-20261103.png) |

**cURL**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seedream-5-5-261028",
    "prompt": "Extract the sticker elements from Image 1 and Image 2 separately, then place all extracted stickers in one transparent-background image. Preserve the original text, colors, shapes, and details of each sticker; remove all background content other than the stickers. Arrange the stickers separately without overlap, with sharp and intact edges.",
    "image": [
      "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test3.png",
      "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test4.png"
    ],
    "size": "2K",
    "output_format": "png",
    "background": "transparent",
    "response_format": "url",
    "watermark": true
  }'
```

**Python**

```Python
import os

# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.getenv("ARK_API_KEY"),
)

response = client.images.generate(
    model="dola-seedream-5-5-261028",
    prompt="Extract the sticker elements from Image 1 and Image 2 separately, then place all extracted stickers in one transparent-background image. Preserve the original text, colors, shapes, and details of each sticker; remove all background content other than the stickers. Arrange the stickers separately without overlap, with sharp and intact edges.",
    image=[
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test3.png",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test4.png",
    ],
    size="2K",
    output_format="png",
    background="transparent",
    response_format="url",
    watermark=True,
)

for item in response.data:
    print(item)
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.images.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

import java.util.Arrays;
import java.util.concurrent.TimeUnit;

public class ImageGenerationsExample {
    public static void main(String[] args) {
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .dispatcher(new Dispatcher())
                .connectionPool(new ConnectionPool(5, 1, TimeUnit.SECONDS))
                .apiKey(System.getenv("ARK_API_KEY"))
                .build();

        CreateImageGenerationRequest request = CreateImageGenerationRequest.builder()
                .model("dola-seedream-5-5-261028")
                .prompt("Extract the sticker elements from Image 1 and Image 2 separately, then place all extracted stickers in one transparent-background image. Preserve the original text, colors, shapes, and details of each sticker; remove all background content other than the stickers. Arrange the stickers separately without overlap, with sharp and intact edges.")
                .image(CreateImageGenerationRequestImage.ofList(Arrays.asList(
                        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test3.png",
                        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test4.png")))
                .size("2K")
                .outputFormat(OutputFormat.PNG)
                .background(Background.TRANSPARENT)
                .responseFormat(ResponseFormat.URL)
                .watermark(true)
                .build();

        try {
            ImageGenerationResponse response = service.generateImages(request);
            response.getData().forEach(System.out::println);
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

    "github.com/volcengine/ark-runtime-go/arkruntime"
    model "github.com/volcengine/ark-runtime-go/arkruntime/model/images"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    request := &model.CreateImageGenerationRequest{
        Model:  "dola-seedream-5-5-261028",
        Prompt: model.NewOptString("Extract the sticker elements from Image 1 and Image 2 separately, then place all extracted stickers in one transparent-background image. Preserve the original text, colors, shapes, and details of each sticker; remove all background content other than the stickers. Arrange the stickers separately without overlap, with sharp and intact edges."),
        Image: model.NewOptCreateImageGenerationRequestImage(model.NewStringArrayCreateImageGenerationRequestImage([]string{
            "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test3.png",
            "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test4.png",
        })),
        Size:           model.NewOptString("2K"),
        OutputFormat:   model.NewOptOutputFormat(model.OutputFormatPNG),
        Background:     model.NewOptBackground(model.BackgroundTransparent),
        ResponseFormat: model.NewOptResponseFormat(model.ResponseFormatURL),
        Watermark:      model.NewOptBool(true),
    }
    response, err := client.GenerateImages(context.Background(), request)
    if err != nil {
        fmt.Fprintln(os.Stderr, err)
        os.Exit(1)
    }
    for _, item := range response.Data {
        fmt.Printf("%+v\n", item)
    }
}
```

**OpenAI**

```Python
import os
from openai import OpenAI

client = OpenAI(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.getenv("ARK_API_KEY"),
)

response = client.images.generate(
    model="dola-seedream-5-5-261028",
    prompt="Extract the sticker elements from Image 1 and Image 2 separately, then place all extracted stickers in one transparent-background image. Preserve the original text, colors, shapes, and details of each sticker; remove all background content other than the stickers. Arrange the stickers separately without overlap, with sharp and intact edges.",
    size="2K",
    output_format="png",
    background="transparent",
    response_format="url",
    extra_body={
        "image": [
            "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test3.png",
            "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test4.png",
        ],
        "watermark": True,
    },
)

for item in response.data:
    print(item)
```

## Fine\-grained layer decomposition

The **layer\-decomposition workflow, prompting, and layer reconstruction** are the same as for Seedream 5.0 pro / flash. See [Layer decomposition](seedream-5-0-pro.md#layer-decomposition). This section covers the Seedream 5.5 enhancements.

|Enhancement |Description |
|---|---|
|More layers |A request can return up to 100 images: one base image and up to 99 layers. |
|Maximum output image count |Set `layer_decomposition_options.max_images` to control the total number of base image and layers.<br><br><br>* Range: [2, 100]<br><br>* Default: 30, which returns at most one base image and 29 layers.<br><br>   **Decomposition rules**<br><br>> When a prompt does not specify how to decompose the image, the model plans a result based on `max_images` and input\-image complexity. Larger `max_images` values generally produce finer decomposition results. |

### Example output

This example decomposes every animal in an illustrated scene into an individual layer and leaves other scene elements in the base image. With `max_images: 40`, the response returns 33 images: one base image and 32 animal layers.

> For display, the layer overview combines the 32 output layers into one image. It is not one image directly generated by the model.

|Input |Base image |Layer overview |
|---|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/animal-test.jpeg) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-animal-base-20261104.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-animal-layers-overview-transparent-20261104.png) |

**View complete outputs**

> **Tip**

> Download the [full example package (ZIP, approximately 67 MB)](https://arkdocs-en.tos-ap-southeast-1.volces.com/files/image-generation/seedream-5-5-animal-layers-20261104-v3.zip) for the base image, 32 transparent animal layers, and an interactive preview page.

> After extracting the package, open `preview.html` in a browser. Select animal\-layer cards to show or hide them and inspect the reconstructed image.

**Example layers**

The following table shows selected original transparent PNG layers. The numbers match the response `z_index` values.

|Layer 1: Bald eagle |Layer 7: Giant panda |Layer 11: Zebra |
|---|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-animal-layer-01-20261104.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-animal-layer-07-20261104.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-animal-layer-11-20261104.png) |

|Layer 17: Lion |Layer 24: Fox |Layer 32: Frog |
|---|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-animal-layer-17-20261104.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-animal-layer-24-20261104.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-animal-layer-32-20261104.png) |

### Request example

The following request uses the animal illustration and decomposes each animal into an independent layer.

**cURL**

```Bash
curl "https://ark.ap-southeast.bytepluses.com/api/v3/images/generations" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seedream-5-5-261028",
    "prompt": "Separate each animal into a complete, independent layer, including animals of the same species. Preserve the appearance, colors, pose, white outline, and original position of each animal. Keep all non-animal elements in the base image and naturally fill the areas left after the animals are removed.",
    "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/animal-test.jpeg",
    "layer_decomposition": true,
    "layer_decomposition_options": {
      "max_images": 40,
      "text_recognition": true
    },
    "size": "2K",
    "output_format": "png",
    "response_format": "url",
    "watermark": true
  }'
```

**Python**

```Python
import os

# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.getenv("ARK_API_KEY"),
)

response = client.images.generate(
    model="dola-seedream-5-5-261028",
    prompt="Separate each animal into a complete, independent layer, including animals of the same species. Preserve the appearance, colors, pose, white outline, and original position of each animal. Keep all non-animal elements in the base image and naturally fill the areas left after the animals are removed.",
    image="https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/animal-test.jpeg",
    layer_decomposition=True,
    layer_decomposition_options={
        "max_images": 40,
        "text_recognition": True,
    },
    size="2K",
    output_format="png",
    response_format="url",
    watermark=True,
)

for item in response.data:
    print(item)
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.images.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

import java.util.concurrent.TimeUnit;

public class ImageGenerationsExample {
    public static void main(String[] args) {
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .dispatcher(new Dispatcher())
                .connectionPool(new ConnectionPool(5, 1, TimeUnit.SECONDS))
                .apiKey(System.getenv("ARK_API_KEY"))
                .build();

        CreateImageGenerationRequest request = CreateImageGenerationRequest.builder()
                .model("dola-seedream-5-5-261028")
                .prompt("Separate each animal into a complete, independent layer, including animals of the same species. Preserve the appearance, colors, pose, white outline, and original position of each animal. Keep all non-animal elements in the base image and naturally fill the areas left after the animals are removed.")
                .image(CreateImageGenerationRequestImage.ofString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/animal-test.jpeg"))
                .layerDecomposition(true)
                .layerDecompositionOptions(LayerDecompositionOptions.builder()
                        .maxImages(40)
                        .textRecognition(true)
                        .build())
                .size("2K")
                .outputFormat(OutputFormat.PNG)
                .responseFormat(ResponseFormat.URL)
                .watermark(true)
                .build();

        try {
            ImageGenerationResponse response = service.generateImages(request);
            response.getData().forEach(System.out::println);
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

    "github.com/volcengine/ark-runtime-go/arkruntime"
    model "github.com/volcengine/ark-runtime-go/arkruntime/model/images"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    request := &model.CreateImageGenerationRequest{
        Model:              "dola-seedream-5-5-261028",
        Prompt:             model.NewOptString("Separate each animal into a complete, independent layer, including animals of the same species. Preserve the appearance, colors, pose, white outline, and original position of each animal. Keep all non-animal elements in the base image and naturally fill the areas left after the animals are removed."),
        Image:              model.NewOptCreateImageGenerationRequestImage(model.NewStringCreateImageGenerationRequestImage("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/animal-test.jpeg")),
        LayerDecomposition: model.NewOptBool(true),
        LayerDecompositionOptions: model.NewOptLayerDecompositionOptions(model.LayerDecompositionOptions{
            MaxImages:       model.NewOptInt32(40),
            TextRecognition: model.NewOptBool(true),
        }),
        Size:           model.NewOptString("2K"),
        OutputFormat:   model.NewOptOutputFormat(model.OutputFormatPNG),
        ResponseFormat: model.NewOptResponseFormat(model.ResponseFormatURL),
        Watermark:      model.NewOptBool(true),
    }
    response, err := client.GenerateImages(context.Background(), request)
    if err != nil {
        fmt.Fprintln(os.Stderr, err)
        os.Exit(1)
    }
    for _, item := range response.Data {
        fmt.Printf("%+v\n", item)
    }
}
```

**OpenAI**

```Python
import os
from openai import OpenAI

client = OpenAI(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.getenv("ARK_API_KEY"),
)

response = client.images.generate(
    model="dola-seedream-5-5-261028",
    prompt="Separate each animal into a complete, independent layer, including animals of the same species. Preserve the appearance, colors, pose, white outline, and original position of each animal. Keep all non-animal elements in the base image and naturally fill the areas left after the animals are removed.",
    size="2K",
    output_format="png",
    response_format="url",
    extra_body={
        "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/animal-test.jpeg",
        "layer_decomposition": True,
        "layer_decomposition_options": {
            "max_images": 40,
            "text_recognition": True,
        },
        "watermark": True,
    },
)

for item in response.data:
    print(item)
```

## Extract text\-layer content and styles (rich\-text OCR)

During layer decomposition, Seedream 5.5 can return rich\-text OCR results for regular text layers. The results include text content, line\-by\-line positions, and approximate visual styles. Design tools and applications can combine this data with decomposed layers to modify text, localize content, and reuse templates. Text editing and layer composition are performed by your tool or application.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/image-generation/seedream-5-5-ocr-workflow-overview.svg)

### Example output

This example decomposes a poster and obtains OCR results for text layers. The request sets `max_images: 15` and returns 13 images: one base image and 12 layers. Nine text layers return OCR results.

> **Try text\-layer editing**

> Download the [full example package (ZIP, approximately 6.7 MB)](https://arkdocs-en.tos-ap-southeast-1.volces.com/files/image-generation/seedream-5-5-ocr-interactive-example-20261104-v4.zip). It includes the input poster, base image, all layers, OCR data, and an interactive page.

* > Open `seedream-55-ocr-preview.html` in a browser after extracting the package, preserving its directory structure.

* > Select a text layer to inspect line text, coordinates, and styles; modify text, colors, and bold styling to preview results in real time.

* > Select or clear layers to inspect how they compose over the poster.

> Edits are performed in the browser. Fonts and font sizes are approximations, and edits are not retained after refreshing or closing the page.

|Input |Text layer |OCR result |
|---|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test2.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-ocr-regular-text-layer-20261103.png) |**Text**:<br><br><br>* Line 1: `Presented By :`<br><br>* Line 2: `Anna Katrina Marchesi`<br><br><br>**Position (absolute pixel coordinates)** :<br><br><br>* Line 1: `[1196, 629, 1358, 658]`<br><br>* Line 2: `[1195, 651, 1427, 681]`<br><br><br>**Similar font**: Helvetica Neue for both lines<br><br>**Color**:<br><br><br>* Line 1: `#193366`<br><br>* Line 2: `#46597E`<br><br><br>**Bold**:<br><br><br>* Line 1: `true`<br><br>* Line 2: `false` |

**View complete decomposition results**

**Base image**

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-ocr-base-20261103.png)

**Layers with OCR results**

The following numbers match the response `z_index` values.

|Layer 2: Date |Layer 3: Heading "THANK YOU" |Layer 5: Heading "SO MUCH!" |
|---|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-ocr-date-layer-20261103.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-ocr-thank-you-layer-20261103.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-ocr-so-much-layer-20261103.png) |

|Layer 6: Presenter |Layer 8: Recipient |Layer 9: Email address |
|---|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-ocr-regular-text-layer-20261103.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-ocr-presented-to-layer-20261103.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-ocr-email-layer-20261103.png) |

|Layer 10: Social media |Layer 11: Phone number |Layer 12: Website |
|---|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-ocr-social-media-layer-20261103.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-ocr-phone-number-layer-20261103.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-ocr-website-layer-20261103.png) |

**Layers without OCR results**

|Layer 1: Brand mark |Layer 4: Main building |Layer 7: Arrow |
|---|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-ocr-brand-layer-20261103.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-ocr-big-ben-layer-20261103.png) |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-ocr-arrow-layer-20261103.png) |

### Enable OCR recognition

For layer decomposition with OCR, set:

* **Layer decomposition**: Set `layer_decomposition` to `true` to enable layer decomposition.

* **Text recognition**: Set `layer_decomposition_options.text_recognition` to `true` to request OCR results for regular text layers.

**cURL**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seedream-5-5-261028",
    "prompt": "Decompose the elements in the image into independent layers.",
    "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test2.png",
    "layer_decomposition": true,
    "layer_decomposition_options": {
      "max_images": 15,
      "text_recognition": true
    },
    "size": "auto",
    "output_format": "png",
    "response_format": "url",
    "watermark": true
  }'
```

**Go**

```Go
package main

import (
    "context"
    "encoding/json"
    "fmt"
    "os"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    model "github.com/volcengine/ark-runtime-go/arkruntime/model/images"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    request := &model.CreateImageGenerationRequest{
        Model:              "dola-seedream-5-5-261028",
        Prompt:             model.NewOptString("Decompose the elements in the image into independent layers."),
        Image:              model.NewOptCreateImageGenerationRequestImage(model.NewStringCreateImageGenerationRequestImage("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test2.png")),
        LayerDecomposition: model.NewOptBool(true),
        LayerDecompositionOptions: model.NewOptLayerDecompositionOptions(model.LayerDecompositionOptions{
            MaxImages:       model.NewOptInt32(15),
            TextRecognition: model.NewOptBool(true),
        }),
        Size:           model.NewOptString("auto"),
        OutputFormat:   model.NewOptOutputFormat(model.OutputFormatPNG),
        ResponseFormat: model.NewOptResponseFormat(model.ResponseFormatURL),
        Watermark:      model.NewOptBool(true),
    }
    response, err := client.GenerateImages(context.Background(), request)
    if err != nil {
        fmt.Fprintln(os.Stderr, err)
        os.Exit(1)
    }
    for _, item := range response.Data {
        fmt.Printf("%+v\n", item)
        if ocr, ok := item.TextRecognition.Get(); ok {
            content, err := json.MarshalIndent(ocr, "", "  ")
            if err != nil {
                fmt.Fprintln(os.Stderr, err)
                os.Exit(1)
            }
            fmt.Println(string(content))
        }
    }
}
```

**OpenAI**

```Python
import json
import os
from openai import OpenAI

client = OpenAI(
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    api_key=os.getenv("ARK_API_KEY"),
)

response = client.images.generate(
    model="dola-seedream-5-5-261028",
    prompt="Decompose the elements in the image into independent layers.",
    size="auto",
    output_format="png",
    response_format="url",
    extra_body={
        "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/ocr-test2.png",
        "layer_decomposition": True,
        "layer_decomposition_options": {
            "max_images": 15,
            "text_recognition": True,
        },
        "watermark": True,
    },
)

for item in response.data:
    print(item)
    ocr = (item.model_extra or {}).get("text_recognition")
    if ocr is not None:
        print(json.dumps(ocr, ensure_ascii=False, indent=2))
```

### OCR response fields

The model returns OCR results for the current text layer in `data[].text_recognition`, including full plain text and position, text, and style information organized by text lines and style runs.

> For complete field definitions, coordinate rules, and response structures, see [Image generation API](image-generation-api.md).

|Response level |Field |Description |
|---|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/cengji.png) |`text_recognition` |Rich\-text OCR result for the current text layer. |
||`text_recognition.text` |Complete plain text in top\-to\-bottom, left\-to\-right order, with lines separated by `\n`. |
||`text_recognition.lines[]` |Ordered text lines. Each line contains positions and style runs. |
||`lines[].bounding_box` |The text\-line bounding box in the output\-base\-image coordinate system, including absolute `absolute` and normalized `normalized` coordinates. Use it to restore text to its bounding\-box area in the base image or target canvas. For coordinate conversion, see [Reconstruct layers](seedream-5-0-pro.md#layer-decomposition). |
||`bounding_box.absolute` |Absolute pixel coordinates for the text\-line bounding box in the output\-base\-image coordinate system, formatted as `[left, top, right, bottom]`. The top\-left of the output base image is `(0, 0)`. Use these coordinates to restore the text\-line position in the base image. |
||`bounding_box.normalized` |Normalized integer coordinates for the text\-line bounding box, scaled to `[0, 1000]` from the output base image dimensions and formatted as `[left, top, right, bottom]`. Use these coordinates to calculate the text\-line position in the target canvas. |
||`lines[].runs[]` |Style runs for the current text line. Each run has consistent style. A line with uniform styling contains one run; a line with changes to font, color, bold styling, or other styles contains multiple runs. |
||`runs[].text` |Text in the current run, without line breaks. |
||`runs[].style` |Approximate visual style for the text run. It includes the following eight fields. The model identifies styles automatically, so they can differ from the exact attributes in the original design file. |
||`style.similar_font_family` |Closest font name matched from appearance. Returns `unknown` when recognition is unavailable or confidence is insufficient. |
||`style.foreground_color` |Text color in uppercase `#RRGGBB` format, without an alpha channel. |
||`style.bold` |Whether the model recognized bold text. |
||`style.italic` |Whether the model recognized italic text. Returns `true` only when the glyphs themselves slant right; rotation or perspective skew does not count as italic. |
||`style.underline` |Whether the model recognized an underline. A separate divider, table rule, or decorative line does not count as an underline. |
||`style.strikethrough` |Whether the model recognized strikethrough text. |
||`style.shadow` |Whether the model recognized a visible drop shadow, shadow, or glow offset. A regular antialiasing transition does not count as a shadow. |
||`style.translucent` |Whether the text itself is visibly translucent and reveals background texture or color. Solid light\-gray or light\-blue text does not count as translucent. |

**Text\-layer response example**

The following example corresponds to the text layer containing `Presented By :` and `Anna Katrina Marchesi` in the preceding example output (`z_index: 6`). It shows selected fields from that layer's `data[]` item. `text_recognition` contains the complete OCR result, which corresponds to the field hierarchy in the preceding table.

```json
{
  "size": "1166x899",
  "z_index": 6,
  "bounding_box": {
    "absolute": [1142, 528, 1474, 784],
    "normalized": [643, 529, 830, 786]
  },
  "text_recognition": {
    "text": "Presented By : \nAnna Katrina Marchesi",
    "lines": [
      {
        "bounding_box": {
          "absolute": [1196, 629, 1358, 658],
          "normalized": [673, 630, 765, 659]
        },
        "runs": [
          {
            "text": "Presented By : ",
            "style": {
              "similar_font_family": "Helvetica Neue",
              "foreground_color": "#193366",
              "bold": true,
              "italic": false,
              "underline": false,
              "strikethrough": false,
              "shadow": false,
              "translucent": false
            }
          }
        ]
      },
      {
        "bounding_box": {
          "absolute": [1195, 651, 1427, 681],
          "normalized": [673, 652, 803, 682]
        },
        "runs": [
          {
            "text": "Anna Katrina Marchesi",
            "style": {
              "similar_font_family": "Helvetica Neue",
              "foreground_color": "#46597E",
              "bold": false,
              "italic": false,
              "underline": false,
              "strikethrough": false,
              "shadow": false,
              "translucent": false
            }
          }
        ]
      }
    ]
  },
  "background": "transparent"
}
```

### Limitations

* **Recognition coverage**: Only regular text layers return OCR results. See [Seedream 5.5 OCR supported fonts](ocr-supported-fonts.md) for supported fonts. Base images, non\-text layers, artistic text, and handwritten text do not return `text_recognition`.

* **Font recognition**: `similar_font_family` is an approximate match and is not guaranteed to equal the original font. It returns `unknown` when recognition is unavailable or confidence is insufficient.

* **Style recognition**: Style information is approximate. `false` means the model did not recognize the corresponding style.

## Customize image output

Use the following parameters to control image output:

* `size`: Output image size.

* `response_format`: How generated images are returned.

* `output_format`: Image file format. For layer decomposition, this controls the base image only.

* `background`: Whether image\-generation output includes an alpha channel.

* `watermark`: Whether the output image has a watermark.

### Image size

`size` has different values and defaults for image\-generation and layer\-decomposition scenarios.

**Image generation**

Use either resolution presets or explicit dimensions, but do not combine them.

**Method 1: Use a resolution preset (recommended)**

Describe aspect ratio, image shape, or use in `prompt`; the model determines the final dimensions.

* Default: `2K`

* Valid values: `1K`, `1.5K`, and `2K`

**Method 2: Specify pixel dimensions (** **`widthxheight`** **)**

* Total pixels: [`1280x720` (921600), `2048x2048x1.1025` (4624220)]

* Aspect ratio: [1/16, 16]

> **Pixel dimensions**

> When you use Method 2, both the total\-pixel and aspect\-ratio ranges apply. The total\-pixel limit applies to the product of width and height, not either dimension independently.

|Method 1 |Method 2 |
|---|---|
|```json```<br>```{```<br>```  "prompt": "Generate a 16:9 image",```<br>```  "size": "2K"```<br>```}```<br> |```json```<br>```{```<br>```  "prompt": "Generate an image",```<br>```  "size": "2880x1440"```<br>```}```<br> |

**Layer decomposition**

Only resolution presets are supported. The output\-image resolution follows these rules:

* **Base image**: Uses the resolution specified by `size` and retains the aspect ratio of the source image being decomposed.

* **Each layer**: Uses a resolution close to the value specified by `size` and retains its aspect ratio in the source image.

* Default: `auto`

* Valid values: `1K`, `1.5K`, `2K`, and `auto`

See [Image generation API](image-generation-api.md) for dimension mapping and `auto` adaptation.

### Response format

Set `response_format` to choose how generated images are returned:

* `url` (default): Returns an image download URL. The URL is valid for 24 hours after generation.

* `b64_json`: Returns Base64\-encoded image data in the response `b64_json` field.

### Output format

Set `output_format` to choose the output image file format:

* `png` (default)

* `jpeg`

> **Note**

* > In layer decomposition, `output_format` controls only the base image; layers are always output as PNG.

* > When `output_format` is `jpeg`, `background` cannot be `transparent`.

### Background type

Set `background` to control whether generated images include an alpha channel:

* `auto` (default): The model determines the background from the prompt and `output_format`. JPEG output has no alpha channel.

* `transparent`: Returns an RGBA image with an alpha channel.

* `opaque`: Returns an RGB image without an alpha channel.

> **Note**

* > `background` applies only to image generation. Layer decomposition automatically returns transparent PNG images, and passing this parameter causes an error.

* > When `background` is `transparent`, `output_format` must be `png`; setting it to `jpeg` causes an error.

### Watermark

Set `watermark` to control output watermarks. The default is `true`.

* `false`: Do not add a watermark.

* `true`: Add an "AI generated" watermark in the lower\-right corner.

## Usage limits

**Upgrade the SDK**

Use the latest SDK version so model capabilities work correctly. See [Install and upgrade SDK](install-and-upgrade-sdk.md).

**Image input**

For both scenarios, pass images in either of the following forms:

* **Image URL**: Ensure that the image URL is accessible.

   Example: `https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-5-5-transparent-input.png`

* **Base64 encoding**: Use the format `data:image/<image format>;base64,<Base64 encoding>`. `<image format>` must be lowercase, for example `data:image/png;base64,<base64_image>`.

|Constraint |Image generation |Layer decomposition |
|---|---|---|
|Image formats |jpeg, png, webp, bmp, tiff, gif, heic, heif |png, jpeg |
|Total pixels (width x height) |[196, `6000x6000` (36,000,000)] |[`512x512` (262,144), `6000x6000` (36,000,000)] |
|Width and height |Greater than 14 px |— |
|Aspect ratio |[1/16, 16] |[1/16, 16] |
|File size |Up to 30 MB |Up to 30 MB |
|Image count |Up to 10 reference images |One image only |

> **Note**

> The total\-pixel limit applies to the product of a single image's width and height, not either dimension independently.

**Retention**

Image URLs are retained for 24 hours and then removed automatically. Save generated images promptly.

**Rate limits**

* IPM rate limit: The maximum number of images generated per minute for the same model and model version in an account. Requests exceeding the limit fail.

   * For layer decomposition, each request pre\-deducts IPM based on `max_images`. After all images are generated, excess quota is returned based on the actual output count.

* Limits vary by model. See [Image generation capabilities](model-list.md#9df4d9fd).
