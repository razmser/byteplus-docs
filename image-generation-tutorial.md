# Image generation tutorial

The Dola Seedream model series supports text, single\-image, and multi\-image inputs for text\-to\-image generation, multi\-image blending, and image editing. This document explains how to use [Image generation API](image-generation-api.md) for image creation: use Seedream 5.5 for text\-to\-image, single\-image editing, and multi\-image blending; Seedream 5.0 lite for batch image generation and streaming output; and Seedream 5.0 pro for fast prompt optimization. To switch models, choose one from the model capability table below and configure the request according to its parameter constraints.

> **New model available**

> Dola Seedream 5.5 (Model ID: `dola-seedream-5-5-261028`) improves consistency across multi\-turn edits and supports natively generated RGBA images from text\-to\-image and image\-to\-image. Layer decomposition can output one base image and up to 99 layers, and can provide rich\-text OCR results for regular text layers.

> For its specialized capabilities and examples, see [Dola Seedream 5.5 tutorial](seedream-5-5.md).

# Showcases

For more examples, see [Showcases](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedream-5-0).

|Use cases |Input |Output |
|---|---|---|
|Layer decomposition<br><br> <br><br>\> Dola Seedream 5.5 and Seedream 5.0 pro / flash can decompose elements into independently editable layers for fine\-grained editing such as dragging, scaling, and recomposition. The following image shows layer decomposition with Seedream 5.0 pro. |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_layer_input.png)<br><br>Precisely decompose the image into layers. The text coordinates to separate are `<bbox>180 64 812 198</bbox>`, `<bbox>757 210 939 280</bbox>`, `<bbox>63 212 320 282</bbox>`, `<bbox>178 714 826 810</bbox>`, `<bbox>814 819 949 894</bbox>`, and `<bbox>326 824 669 930</bbox>`. The parrot coordinates are `<bbox>347 305 642 997</bbox>`. |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_layer_output2.gif) |
|Interactive editing<br><br> <br><br>\> Dola Seedream 5.5 and Seedream 5.0 pro / flash let you specify the edit region with markers. The model recognizes the marked area and generates content that blends naturally into the original scene. The following image shows editing with Seedream 5.0 pro. |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_input2.png)<br><br>Edit the image based on the hand\-drawn sketch. Add a stack of realistic magazines or art books in the marked area at the lower left, and add a ceramic cup of coffee with a saucer in the marked area on the right. Remove all sketch lines. Keep the composition unchanged. |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_output2.png) |
|Multi\-reference image\-to\-image generation<br><br> <br><br>\> Input multiple images as reference, blend styles and elements to generate new images |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/2198d4bef000400bbfea18025850ed82~tplv-goo7wpa0wc-image.image)<br><br>Replace the clothing in image 1 with the outfit from image 2. |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/94fa391195e248fbb709691892ea7eb9~tplv-goo7wpa0wc-image.image) |
|Image sequence generation<br><br>\> Seedream 5.0 lite / 4.5 / 4.0 can generate a set of content\-related images from user\-provided text and images. |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/a215e8241dd94f50901948790da121e1~tplv-goo7wpa0wc-image.image)<br><br>Referring to Figure 1, generate four images with characters wearing sunglasses, riding motorcycles, wearing hats, and holding lollipops |![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream-sequence-output.jpeg) |

# Model capabilities

The following table compares the capabilities and parameters of each Seedream model version to help you choose the model that best fits your business needs.

|Model Name | |[Seedream 5.5](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-5) |[Seedream 5.0 pro](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-0-pro) |[Seedream 5.0 flash](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-0-flash) |[Seedream 5.0 lite](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedream-5-0) |[Seedream 4.5](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedream-4-5) |[Seedream 4.0](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=seedream-4-0) |
|---|---|---|---|---|---|---|---|
|Model ID | |dola\-seedream\-5\-5\-261028 |dola\-seedream\-5\-0\-pro\-260628 |dola\-seedream\-5\-0\-flash\-260915 |seedream\-5\-0\-260128 (also supports: seedream\-5\-0\-lite\-260128) |seedream\-4\-5\-251128 |seedream\-4\-0\-250828 |
|[Text-to-image](image-generation-tutorial.md#9695d195) | |✓ |✓ |✓ |✓ |✓ |✓ |
|[Text-to-multiple images](image-generation-tutorial.md#ec79cfda) | |✗ |✗ |✗ |✓ |✓ |✓ |
|[Single/multiple images to image](image-generation-tutorial.md#8bc49063) | |✓, up to 10 reference images |✓, up to 10 reference images |✓, up to 10 reference images |✓, up to 14 reference images |✓, up to 14 reference images |✓, up to 14 reference images |
|[Single/multiple images to multiple images](image-generation-tutorial.md#fc9f85e4) | |✗ |✗ |✗ |✓ |✓ |✓ |
|[Interactive editing](image-generation-tutorial.md#interactive_edit) | |✓ |✓ |✓ |✗ |✗ |✗ |
|[Layer decomposition](seedream-5-0-pro.md#layer-decomposition) | |✓ |✓ |✓ |✗ |✗ |✗ |
|[Native RGBA images](seedream-5-5.md#native-rgba) | |✓, text\-to\-image and image\-to\-image |✓, single transparent PNG image\-to\-image only |✓, single transparent PNG image\-to\-image only |✗ |✗ |✗ |
|[Rich-text OCR](seedream-5-5.md#rich-text-ocr) | |✓ |✗ |✗ |✗ |✗ |✗ |
|[Streaming output](image-generation-tutorial.md#e5bef0d7) | |✗ |✗ |✗ |✓ |✓ |✓ |
|Model parameters |Resolution<br><br>> Image generation<br><br><br> |* Method 1: Resolution preset: `1K`, `1.5K`, `2K`<br><br>* Method 2: Width and height in pixels: [`1280x720`, `2048x2048x1.1025`] |* Method 1: Resolution preset: `1K`, `1.5K`, `2K`<br><br>* Method 2: Width and height in pixels: [`1280x720`, `2048x2048x1.1025`] |* Method 1: Resolution preset: `1K`, `1.5K`, `2K`<br><br>* Method 2: Width and height in pixels: [`1280x720`, `2048x2048x1.1025`] |* Method 1: Resolution preset: `2K`, `3K`, `4K`<br><br>* Method 2: Width and height in pixels: [`1920x1920`, `4096x4096`] |* Method 1: Resolution preset: `2K`, `4K`<br><br>* Method 2: Width and height in pixels: [`1920x1920`, `4096x4096`] |* Method 1: Resolution preset: `1K`, `2K`, `4K`<br><br>* Method 2: Width and height in pixels: [`1280x720`, `4096x4096`] |
||Resolution<br><br>> Layer decomposition<br><br><br> |1K, 1.5K, 2K, auto |1K, 1.5K, 2K, auto |1K, 1.5K, 2K, auto |✗ |✗ |✗ |
||Output format |png, jpeg |png, jpeg |png, jpeg |png, jpeg |jpeg |jpeg |
||Prompt optimization mode |standard mode |standard mode, fast mode |standard mode |standard mode |standard mode |standard mode, fast mode |
||Number of generated images |Single image; layer decomposition supports up to one base image and 99 layers |Supports single\-image and multi\-layer image generation (one base image + 16 layers) |Supports single\-image and multi\-layer image generation (one base image + 16 layers) |Number of input reference images + number of generated images ≤ 15. | | |
||Maximum output image count<br><br>> Layer decomposition<br><br><br> |2\-100 images, including the base image |✗ |✗ |✗ |✗ |✗ |
|Max Images per Minute | |[Image generation](model-list.md#9df4d9fd) |500 |500 |500 |500 |500 |

# Prerequisites

> **Tip**

> If you're new to ModelArk, see [Quick start](quick-start.md) to get up and running quickly.

# Quick start

You can try the image generation feature on the ModelArk platform using [API Explorer](https://api.byteplus.com/api-explorer/?action=ImageGenerations&groupName=Image%20Generation%20API&serviceCode=ark&version=2024-01-01). It supports custom parameters configuration (e.g. watermark settings, output image size), and effect & performance evaluation.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/2eea8e183d5d424cbd1f707bef9351d1~tplv-goo7wpa0wc-image.image)

# Basic usage

## Text\-to\-image (Text input, single\-image output)

Provide clear and accurate text instructions to the model to quickly generate a high\-quality image that matches the description.

|Prompt |Output |
|---|---|
|Vibrant close\-up editorial portrait, model with piercing gaze, wearing a sculptural hat, rich color blocking, sharp focus on eyes, shallow depth of field, Vogue magazine cover aesthetic, shot in medium format, dramatic studio lighting. |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/512e8df7233f486389cbd5a6ac1e4e59~tplv-goo7wpa0wc-image.image) |

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seedream-5-5-261028",
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
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

imagesResponse = client.images.generate(
    # Replace with Model ID
    model="dola-seedream-5-5-261028",
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
        // Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        String apiKey = System.getenv("ARK_API_KEY");
        ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
        Dispatcher dispatcher = new Dispatcher();
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                // The base URL for model invocation
                .dispatcher(dispatcher)
                .connectionPool(connectionPool)
                .apiKey(apiKey)
                .build();

        CreateImageGenerationRequest generateRequest = CreateImageGenerationRequest.builder()
                .model("dola-seedream-5-5-261028") // Replace with Model ID
                .prompt("Vibrant close-up editorial portrait, model with piercing gaze, wearing a sculptural hat, rich color blocking, sharp focus on eyes, shallow depth of field, Vogue magazine cover aesthetic, shot on medium format, dramatic studio lighting.")

                .size("2K")
                .outputFormat(OutputFormat.PNG)
                .responseFormat(ResponseFormat.URL)
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
        // Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        os.Getenv("ARK_API_KEY"),
        // The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    outputFormat := model.OutputFormatPNG


    generateReq := &model.CreateImageGenerationRequest{
       Model:          "dola-seedream-5-5-261028", // Replace with Model ID
       Prompt:         model.NewOptString("Vibrant close-up editorial portrait, model with piercing gaze, wearing a sculptural hat, rich color blocking, sharp focus on eyes, shallow depth of field, Vogue magazine cover aesthetic, shot on medium format, dramatic studio lighting."),

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
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

imagesResponse = client.images.generate(
    # Replace with Model ID
    model="dola-seedream-5-5-261028",
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

## Image\-to\-image (single\-image input, single\-image output)

Edit an existing image using text instructions, including adding or removing elements, changing style or texture, adjusting color tone, and modifying the background, perspective, or size.

|Prompt |Input image |Output |
|---|---|---|
|Keep the model's pose and the flowing shape of the liquid dress unchanged. Change the clothing material from silver metal to completely transparent clear water (or glass). Through the liquid, the model's skin details are visible. Lighting changes from reflection to refraction. |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/816153e67d3c4478886276154d78b22e~tplv-goo7wpa0wc-image.image) |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/3ce782f3fea14b099103be608a78fe43~tplv-goo7wpa0wc-image.image) |

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seedream-5-5-261028",
    "prompt": "Keep the model’s pose and the flowing shape of the liquid dress unchanged. Change the clothing material from silver metal to completely transparent clear water (or glass). Through the liquid water, the model’s skin details are visible. Lighting changes from reflection to refraction.",
    "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_5_imageToimage.png",
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
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

imagesResponse = client.images.generate(
    # Replace with Model ID
    model="dola-seedream-5-5-261028",
    prompt="Keep the model's pose and the flowing shape of the liquid dress unchanged. Change the clothing material from silver metal to completely transparent clear water (or glass). Through the liquid water, the model's skin details are visible. Lighting changes from reflection to refraction.",
    image="https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_5_imageToimage.png",
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
        String apiKey = System.getenv("ARK_API_KEY");
        ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
        Dispatcher dispatcher = new Dispatcher();
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                // The base URL for model invocation
                .dispatcher(dispatcher)
                .connectionPool(connectionPool)
                .apiKey(apiKey)
                .build();

        // CreateImageGenerationRequestImage requires ark-runtime 0.6.0 or later.
        CreateImageGenerationRequest generateRequest = CreateImageGenerationRequest.builder()
                .model("dola-seedream-5-5-261028") // Replace with Model ID
                .prompt("Keep the model's pose and the flowing shape of the liquid dress unchanged. Change the clothing material from silver metal to completely transparent clear water (or glass). Through the liquid water, the model's skin details are visible. Lighting changes from reflection to refraction.")

                .image(CreateImageGenerationRequestImage.ofString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_5_imageToimage.png"))

                .size("2K")
                .outputFormat(OutputFormat.PNG)
                .responseFormat(ResponseFormat.URL)
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
        os.Getenv("ARK_API_KEY"),
        // The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    outputFormat := model.OutputFormatPNG

    generateReq := &model.CreateImageGenerationRequest{
       Model:          "dola-seedream-5-5-261028",
       Prompt:         model.NewOptString("Keep the model's pose and the flowing shape of the liquid dress unchanged. Change the clothing material from silver metal to completely transparent clear water (or glass). Through the liquid water, the model's skin details are visible. Lighting changes from reflection to refraction."),

       Image: model.NewOptCreateImageGenerationRequestImage(model.NewStringArrayCreateImageGenerationRequestImage([]string{"https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_5_imageToimage.png"})),

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
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

imagesResponse = client.images.generate(
    model="dola-seedream-5-5-261028",
    prompt="Keep the model's pose and the flowing shape of the liquid dress unchanged. Change the clothing material from silver metal to completely transparent clear water (or glass). Through the liquid water, the model's skin details are visible. Lighting changes from reflection to refraction.",
    size="2K",
    output_format="png",
    response_format="url",
    extra_body = {
        "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_5_imageToimage.png",
        "watermark": True
    }
)

print(imagesResponse.data[0].url)
```

## Multi\-image blending (multi\-image input, single\-image output)

Generate a new image by blending styles and visual elements from your prompt and multiple reference images. For example, you can merge clothing, shoes, and accessories with model photos to create outfit images, or combine people with landscapes to produce portrait scenes.

|Prompt |Input image 1 |Input image 2 |Output |
|---|---|---|---|
|Replace the clothing in image 1 with the outfit from image 2. |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/4b4464161cf3463db6f9463b10939178~tplv-goo7wpa0wc-image.image) |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/c23d1b0528a14cb08b684307eabdcc9b~tplv-goo7wpa0wc-image.image) |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/94fa391195e248fbb709691892ea7eb9~tplv-goo7wpa0wc-image.image) |

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seedream-5-5-261028",
    "prompt": "Replace the clothing in image 1 with the outfit from image 2.",
    "image": ["https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimage_1.png", "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_5_imagesToimage_2.png"],
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
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)
imagesResponse = client.images.generate(
    # Replace with Model ID
    model="dola-seedream-5-5-261028",
    prompt="Replace the clothing in image 1 with the outfit from image 2.",
    image=["https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimage_1.png", "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_5_imagesToimage_2.png"],
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
        String apiKey = System.getenv("ARK_API_KEY");
        ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
        Dispatcher dispatcher = new Dispatcher();
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                // The base URL for model invocation
                .dispatcher(dispatcher)
                .connectionPool(connectionPool)
                .apiKey(apiKey)
                .build();

        // CreateImageGenerationRequestImage requires ark-runtime 0.6.0 or later.
        CreateImageGenerationRequest generateRequest = CreateImageGenerationRequest.builder()
                .model("dola-seedream-5-5-261028") // Replace with Model ID
                .prompt("Replace the clothing in image 1 with the outfit from image 2.")

                .image(CreateImageGenerationRequestImage.ofList(Arrays.asList(
                    "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimage_1.png",
                    "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_5_imagesToimage_2.png"
                )))
                .size("2K")
                .outputFormat(OutputFormat.PNG)
                .responseFormat(ResponseFormat.URL)
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
        os.Getenv("ARK_API_KEY"),
        // The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    outputFormat := model.OutputFormatPNG

    generateReq := &model.CreateImageGenerationRequest{
       Model:          "dola-seedream-5-5-261028",
       Prompt:         model.NewOptString("Replace the clothing in image 1 with the outfit from image 2."),

       Image:         model.NewOptCreateImageGenerationRequestImage(model.NewStringArrayCreateImageGenerationRequestImage([]string{
           "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimage_1.png",
           "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_5_imagesToimage_2.png",


       })),
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
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

imagesResponse = client.images.generate(
    model="dola-seedream-5-5-261028",
    prompt="Replace the clothing in image 1 with the outfit from image 2.",
    size="2K",
    output_format="png",
    response_format="url",
    extra_body = {
        "image": ["https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimage_1.png", "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_5_imagesToimage_2.png"],
        "watermark": True,
    }
)

print(imagesResponse.data[0].url)
```

## Batch image output

> Seedream 5.5, Seedream 5.0 pro and Seedream 5.0 flash do not support this capability.

Generate a set of thematically related images—such as comic storyboards or brand visuals—using one or more images combined with text descriptions.

Specify the parameter **sequential_image_generation** as `auto`.

### Text\-to\-batch\-image (text input, batch\-image output)

|Prompt |Output (four pictures will be generated) |
|---|---|
|Generate a set of four cinematic sci\-fi realistic film storyboard scenes:<br><br>Scene 1: An astronaut repairs a spacecraft at a space station, featuring intricate external mechanical structures, a deep starry sky + Milky Way background. The astronaut wears a highly detailed white spacesuit, holds professional repair tools, and focuses on inspecting the spacecraft's exterior. Medium full shot, rim\-lit by side\-backlighting, cool\-toned sci\-fi lighting with space station lights accenting the scene, a zero\-gravity environment, exquisite metallic textures, and a serene yet precise atmosphere.<br><br>Scene 2: Suddenly hit by a meteorite belt. Wide\-angle epic shot, with numerous meteorites of varying sizes rushing in at high speed. The meteorite surfaces are sharply textured, with burning tails, motion blur emphasizing speed, and an overwhelming sense of pressure. The spacecraft and space station are positioned on one side of the frame, with the dark, deep space background creating strong light\-shadow contrast. Intense disaster atmosphere with powerful visual impact.<br><br>Scene 3: The astronaut dodges urgently. Close\-up dynamic capture, showing the astronaut in zero gravity swiftly twisting to avoid impact, with full dynamic tension in their posture. They reach out to grab a fixed handrail, with meteorites streaking past in the background. Slight camera shake enhances the sense of immediacy. Details like spacesuit creases and tubing are clearly visible. Tense and urgent, with cold, sharp lighting and a focused subject without clutter.<br><br>Scene 4: The astronaut, injured, escapes back to the spacecraft in a thrilling sequence. Medium\-close narrative shot. The astronaut's spacesuit shows minor abrasions and scratches, looking slightly disheveled yet determined, stumbling toward the open spacecraft hatch. The warm interior light contrasts with the cold light of space, with meteorites fading into the background. A tense escape atmosphere, with realistic details and full emotional intensity. |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/d745adde44954b859f6603a799948726~tplv-goo7wpa0wc-image.image) |

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "seedream-5-0-lite-260128",
    "prompt": "Generate a set of four cinematic sci-fi realistic film storyboard scenes:Scene 1: An astronaut repairs a spacecraft at a space station, featuring intricate external mechanical structures, a deep starry sky + Milky Way background. The astronaut wears a highly detailed white spacesuit, holds professional repair tools, and focuses on inspecting the spacecraft'\''s exterior. Medium full shot, rim-lit by side-backlighting, cool-toned sci-fi lighting with space station lights accenting the scene, a zero-gravity environment, exquisite metallic textures, and a serene yet precise atmosphere. Scene 2: Suddenly hit by a meteorite belt. Wide-angle epic shot, with numerous meteorites of varying sizes rushing in at high speed. The meteorite surfaces are sharply textured, with burning tails, motion blur emphasizing speed, and an overwhelming sense of pressure. The spacecraft and space station are positioned on one side of the frame, with the dark, deep space background creating strong light-shadow contrast. Intense disaster atmosphere with powerful visual impact. Scene 3: The astronaut dodges urgently. Close-up dynamic capture, showing the astronaut in zero gravity swiftly twisting to avoid impact, with full dynamic tension in their posture. They reach out to grab a fixed handrail, with meteorites streaking past in the background. Slight camera shake enhances the sense of immediacy. Details like spacesuit creases and tubing are clearly visible. Tense and urgent, with cold, sharp lighting and a focused subject without clutter. Scene 4: The astronaut, injured, escapes back to the spacecraft in a thrilling sequence. Medium-close narrative shot. The astronaut'\''s spacesuit shows minor abrasions and scratches, looking slightly disheveled yet determined, stumbling toward the open spacecraft hatch. The warm interior light contrasts with the cold light of space, with meteorites fading into the background. A tense escape atmosphere, with realistic details and full emotional intensity.",
    "size": "2K",
    "sequential_image_generation": "auto",
    "sequential_image_generation_options": {
        "max_images": 4
    },
    "stream": false,
    "output_format":"png",
    "response_format": "url",
    "watermark": true
}'
```

**Python**

```Python
import os
# Install SDK:  python -m pip install --upgrade arkruntime
from arkruntime import Ark
from arkruntime.types.images import SequentialImageGenerationOptionsParam

client = Ark(
    # The base URL for model invocation .
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

imagesResponse = client.images.generate(
    # Replace with Model ID
    model="seedream-5-0-lite-260128",
    prompt="Generate a set of four cinematic sci-fi realistic film storyboard scenes:Scene 1: An astronaut repairs a spacecraft at a space station, featuring intricate external mechanical structures, a deep starry sky + Milky Way background. The astronaut wears a highly detailed white spacesuit, holds professional repair tools, and focuses on inspecting the spacecraft's exterior. Medium full shot, rim-lit by side-backlighting, cool-toned sci-fi lighting with space station lights accenting the scene, a zero-gravity environment, exquisite metallic textures, and a serene yet precise atmosphere. Scene 2: Suddenly hit by a meteorite belt. Wide-angle epic shot, with numerous meteorites of varying sizes rushing in at high speed. The meteorite surfaces are sharply textured, with burning tails, motion blur emphasizing speed, and an overwhelming sense of pressure. The spacecraft and space station are positioned on one side of the frame, with the dark, deep space background creating strong light-shadow contrast. Intense disaster atmosphere with powerful visual impact. Scene 3: The astronaut dodges urgently. Close-up dynamic capture, showing the astronaut in zero gravity swiftly twisting to avoid impact, with full dynamic tension in their posture. They reach out to grab a fixed handrail, with meteorites streaking past in the background. Slight camera shake enhances the sense of immediacy. Details like spacesuit creases and tubing are clearly visible. Tense and urgent, with cold, sharp lighting and a focused subject without clutter. Scene 4: The astronaut, injured, escapes back to the spacecraft in a thrilling sequence. Medium-close narrative shot. The astronaut's spacesuit shows minor abrasions and scratches, looking slightly disheveled yet determined, stumbling toward the open spacecraft hatch. The warm interior light contrasts with the cold light of space, with meteorites fading into the background. A tense escape atmosphere, with realistic details and full emotional intensity.",
    size="2K",
    sequential_image_generation="auto",
    sequential_image_generation_options=SequentialImageGenerationOptionsParam(max_images=4),
    output_format="png",
    response_format="url",
    watermark=True
)

# Iterate through all image data
for image in imagesResponse.data:
    # Output the current image's URL and size
    print(f"URL: {image.url}, Size: {image.size}")
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
        String apiKey = System.getenv("ARK_API_KEY");
        ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
        Dispatcher dispatcher = new Dispatcher();
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                // The base URL for model invocation
                .dispatcher(dispatcher)
                .connectionPool(connectionPool)
                .apiKey(apiKey)
                .build();

        SequentialImageGenerationOptions sequentialImageGenerationOptions = new SequentialImageGenerationOptions();
        sequentialImageGenerationOptions.setMaxImages(4);
        CreateImageGenerationRequest generateRequest = CreateImageGenerationRequest.builder()
                 .model("seedream-5-0-lite-260128")  // Replace with Model ID
                 .prompt("Generate a set of four cinematic sci-fi realistic film storyboard scenes:Scene 1: An astronaut repairs a spacecraft at a space station, featuring intricate external mechanical structures, a deep starry sky + Milky Way background. The astronaut wears a highly detailed white spacesuit, holds professional repair tools, and focuses on inspecting the spacecraft's exterior. Medium full shot, rim-lit by side-backlighting, cool-toned sci-fi lighting with space station lights accenting the scene, a zero-gravity environment, exquisite metallic textures, and a serene yet precise atmosphere. Scene 2: Suddenly hit by a meteorite belt. Wide-angle epic shot, with numerous meteorites of varying sizes rushing in at high speed. The meteorite surfaces are sharply textured, with burning tails, motion blur emphasizing speed, and an overwhelming sense of pressure. The spacecraft and space station are positioned on one side of the frame, with the dark, deep space background creating strong light-shadow contrast. Intense disaster atmosphere with powerful visual impact. Scene 3: The astronaut dodges urgently. Close-up dynamic capture, showing the astronaut in zero gravity swiftly twisting to avoid impact, with full dynamic tension in their posture. They reach out to grab a fixed handrail, with meteorites streaking past in the background. Slight camera shake enhances the sense of immediacy. Details like spacesuit creases and tubing are clearly visible. Tense and urgent, with cold, sharp lighting and a focused subject without clutter. Scene 4: The astronaut, injured, escapes back to the spacecraft in a thrilling sequence. Medium-close narrative shot. The astronaut's spacesuit shows minor abrasions and scratches, looking slightly disheveled yet determined, stumbling toward the open spacecraft hatch. The warm interior light contrasts with the cold light of space, with meteorites fading into the background. A tense escape atmosphere, with realistic details and full emotional intensity.")
                 .size("2K")
                 .sequentialImageGeneration(SequentialImageGenerationMode.AUTO)
                 .sequentialImageGenerationOptions(sequentialImageGenerationOptions)
                 .outputFormat(OutputFormat.PNG)
                 .responseFormat(ResponseFormat.URL)
                 .stream(false)
                 .watermark(true)
                 .build();
        ImageGenerationResponse imagesResponse = service.generateImages(generateRequest);
        // Iterate through all image data
        if (imagesResponse != null && imagesResponse.getData() != null) {
            for (int i = 0; i < imagesResponse.getData().size(); i++) {
                // Retrieve image information
                String url = imagesResponse.getData().get(i).getUrl();
                String size = imagesResponse.getData().get(i).getSize();
                System.out.printf("Image %d:%n", i + 1);
                System.out.printf("  URL: %s%n", url);
                System.out.printf("  Size: %s%n", size);
                System.out.println();
            }


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
        // The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    outputFormat := model.OutputFormatPNG
    var sequentialImageGeneration model.SequentialImageGenerationMode = "auto"
    maxImages := 4

    generateReq := &model.CreateImageGenerationRequest{
       Model:          "seedream-5-0-lite-260128",
       Prompt:         model.NewOptString("Generate a set of four cinematic sci-fi realistic film storyboard scenes:Scene 1: An astronaut repairs a spacecraft at a space station, featuring intricate external mechanical structures, a deep starry sky + Milky Way background. The astronaut wears a highly detailed white spacesuit, holds professional repair tools, and focuses on inspecting the spacecraft's exterior. Medium full shot, rim-lit by side-backlighting, cool-toned sci-fi lighting with space station lights accenting the scene, a zero-gravity environment, exquisite metallic textures, and a serene yet precise atmosphere. Scene 2: Suddenly hit by a meteorite belt. Wide-angle epic shot, with numerous meteorites of varying sizes rushing in at high speed. The meteorite surfaces are sharply textured, with burning tails, motion blur emphasizing speed, and an overwhelming sense of pressure. The spacecraft and space station are positioned on one side of the frame, with the dark, deep space background creating strong light-shadow contrast. Intense disaster atmosphere with powerful visual impact. Scene 3: The astronaut dodges urgently. Close-up dynamic capture, showing the astronaut in zero gravity swiftly twisting to avoid impact, with full dynamic tension in their posture. They reach out to grab a fixed handrail, with meteorites streaking past in the background. Slight camera shake enhances the sense of immediacy. Details like spacesuit creases and tubing are clearly visible. Tense and urgent, with cold, sharp lighting and a focused subject without clutter. Scene 4: The astronaut, injured, escapes back to the spacecraft in a thrilling sequence. Medium-close narrative shot. The astronaut's spacesuit shows minor abrasions and scratches, looking slightly disheveled yet determined, stumbling toward the open spacecraft hatch. The warm interior light contrasts with the cold light of space, with meteorites fading into the background. A tense escape atmosphere, with realistic details and full emotional intensity."),
       Size:           model.NewOptString("2K"),
       OutputFormat: model.NewOptOutputFormat(outputFormat),
       ResponseFormat: model.NewOptResponseFormat(model.ResponseFormatURL),
       Watermark:      model.NewOptBool(true),
       SequentialImageGeneration: model.NewOptSequentialImageGenerationMode(sequentialImageGeneration),
       SequentialImageGenerationOptions: model.NewOptSequentialImageGenerationOptions(model.SequentialImageGenerationOptions{
          MaxImages: model.NewOptInt32(int32(maxImages)),
       }),
    }

    resp, err := client.GenerateImages(ctx, generateReq)
    if err != nil {
        fmt.Printf("call GenerateImages error: %v\n", err)
        return
    }

    if resp.Error.IsSet() {
        fmt.Printf("API returned error: %s - %s\n", resp.Error.Value.Code, resp.Error.Value.Message)
        return
    }

    // Output the generated image information
    fmt.Printf("Generated %d images:\n", len(resp.Data))
    for i, image := range resp.Data {
        var url string
        if image.URL.IsSet() {
            url = image.URL.Or("")
        } else {
            url = "N/A"
        }
        fmt.Printf("Image %d: Size: %s, URL: %s\n", i+1, image.Size.Or(""), url)
    }
}
```

**OpenAI**

```Python
import os
from openai import OpenAI

client = OpenAI(
    # The base URL for model invocation .
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

imagesResponse = client.images.generate(
    model="seedream-5-0-lite-260128",
    prompt="Generate a set of four cinematic sci-fi realistic film storyboard scenes:Scene 1: An astronaut repairs a spacecraft at a space station, featuring intricate external mechanical structures, a deep starry sky + Milky Way background. The astronaut wears a highly detailed white spacesuit, holds professional repair tools, and focuses on inspecting the spacecraft's exterior. Medium full shot, rim-lit by side-backlighting, cool-toned sci-fi lighting with space station lights accenting the scene, a zero-gravity environment, exquisite metallic textures, and a serene yet precise atmosphere. Scene 2: Suddenly hit by a meteorite belt. Wide-angle epic shot, with numerous meteorites of varying sizes rushing in at high speed. The meteorite surfaces are sharply textured, with burning tails, motion blur emphasizing speed, and an overwhelming sense of pressure. The spacecraft and space station are positioned on one side of the frame, with the dark, deep space background creating strong light-shadow contrast. Intense disaster atmosphere with powerful visual impact. Scene 3: The astronaut dodges urgently. Close-up dynamic capture, showing the astronaut in zero gravity swiftly twisting to avoid impact, with full dynamic tension in their posture. They reach out to grab a fixed handrail, with meteorites streaking past in the background. Slight camera shake enhances the sense of immediacy. Details like spacesuit creases and tubing are clearly visible. Tense and urgent, with cold, sharp lighting and a focused subject without clutter. Scene 4: The astronaut, injured, escapes back to the spacecraft in a thrilling sequence. Medium-close narrative shot. The astronaut's spacesuit shows minor abrasions and scratches, looking slightly disheveled yet determined, stumbling toward the open spacecraft hatch. The warm interior light contrasts with the cold light of space, with meteorites fading into the background. A tense escape atmosphere, with realistic details and full emotional intensity.",
    size="2K",
    output_format="png",
    response_format="url",
    extra_body={
        "watermark": True,
        "sequential_image_generation": "auto",
        "sequential_image_generation_options": {
            "max_images": 4
        },
    },
)

# Iterate through all image data
for image in imagesResponse.data:
    # Output the current image's URL and size
    print(f"URL: {image.url}, Size: {image.size}")
```

### Image\-to\-batch\-image (single\-image input, batch\-image output)

|Prompt |Input image |Output (four pictures will be generated) |
|---|---|---|
|Using this LOGO as a reference, create a visual design system for an outdoor sports brand named GREEN, including packaging bags, hats, cards, lanyards, etc. Main visual tone is green, with a fun, simple, and modern style. |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/c724450228a94a909580c0400fbf503b~tplv-goo7wpa0wc-image.image) |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/9444f4bb109145ad95b4de7596407af6~tplv-goo7wpa0wc-image.image) |

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "seedream-5-0-lite-260128",
    "prompt": "Using this LOGO as a reference, create a visual design system for an outdoor sports brand named GREEN, including packaging bags, hats, cards, lanyards, etc. Main visual tone is green, with a fun, simple, and modern style.",
    "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imageToimages.png",
    "size": "2K",
    "sequential_image_generation": "auto",
    "sequential_image_generation_options": {
        "max_images": 4
    },
    "stream": false,
    "output_format":"png",
    "response_format": "url",
    "watermark": true
}'
```

**Python**

```Python
import os
# Install SDK:  python -m pip install --upgrade arkruntime
from arkruntime import Ark
from arkruntime.types.images import SequentialImageGenerationOptionsParam

client = Ark(
    # The base URL for model invocation .
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

imagesResponse = client.images.generate(
    # Replace with Model ID .
    model="seedream-5-0-lite-260128",
    prompt="Using this LOGO as a reference, create a visual design system for an outdoor sports brand named GREEN, including packaging bags, hats, cards, lanyards, etc. Main visual tone is green, with a fun, simple, and modern style.",
    image="https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imageToimages.png",
    size="2K",
    sequential_image_generation="auto",
    sequential_image_generation_options=SequentialImageGenerationOptionsParam(max_images=4),
    output_format="png",
    response_format="url",
    watermark=True
)

# Iterate through all image data
for image in imagesResponse.data:
    # Output the current image's URL and size
    print(f"URL: {image.url}, Size: {image.size}")
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
        String apiKey = System.getenv("ARK_API_KEY");
        ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
        Dispatcher dispatcher = new Dispatcher();
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                // The base URL for model invocation
                .dispatcher(dispatcher)
                .connectionPool(connectionPool)
                .apiKey(apiKey)
                .build();

        SequentialImageGenerationOptions sequentialImageGenerationOptions = new SequentialImageGenerationOptions();
        sequentialImageGenerationOptions.setMaxImages(4);
        // CreateImageGenerationRequestImage requires ark-runtime 0.6.0 or later.
        CreateImageGenerationRequest generateRequest = CreateImageGenerationRequest.builder()
                 .model("seedream-5-0-lite-260128") // Replace with Model ID
                 .prompt("Using this LOGO as a reference, create a visual design system for an outdoor sports brand named GREEN, including packaging bags, hats, cards, lanyards, etc. Main visual tone is green, with a fun, simple, and modern style.")
                 .image(CreateImageGenerationRequestImage.ofString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imageToimages.png"))
                 .size("2K")
                 .sequentialImageGeneration(SequentialImageGenerationMode.AUTO)
                 .sequentialImageGenerationOptions(sequentialImageGenerationOptions)
                 .outputFormat(OutputFormat.PNG)
                 .responseFormat(ResponseFormat.URL)
                 .stream(false)
                 .watermark(true)
                 .build();
        ImageGenerationResponse imagesResponse = service.generateImages(generateRequest);
        // Iterate through all image data
        if (imagesResponse != null && imagesResponse.getData() != null) {
            for (int i = 0; i < imagesResponse.getData().size(); i++) {
                // Retrieve image information
                String url = imagesResponse.getData().get(i).getUrl();
                String size = imagesResponse.getData().get(i).getSize();
                System.out.printf("Image %d:%n", i + 1);
                System.out.printf("  URL: %s%n", url);
                System.out.printf("  Size: %s%n", size);
                System.out.println();
            }


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
        // The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    outputFormat := model.OutputFormatPNG
    var sequentialImageGeneration model.SequentialImageGenerationMode = "auto"
    maxImages := 4

    generateReq := &model.CreateImageGenerationRequest{
       Model:          "seedream-5-0-lite-260128",
       Prompt:         model.NewOptString("Using this LOGO as a reference, create a visual design system for an outdoor sports brand named GREEN, including packaging bags, hats, cards, lanyards, etc. Main visual tone is green, with a fun, simple, and modern style."),
       Image: model.NewOptCreateImageGenerationRequestImage(model.NewStringArrayCreateImageGenerationRequestImage([]string{"https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imageToimages.png"})),
       Size:           model.NewOptString("2K"),
       OutputFormat: model.NewOptOutputFormat(outputFormat),
       ResponseFormat: model.NewOptResponseFormat(model.ResponseFormatURL),
       Watermark:      model.NewOptBool(true),
       SequentialImageGeneration: model.NewOptSequentialImageGenerationMode(sequentialImageGeneration),
       SequentialImageGenerationOptions: model.NewOptSequentialImageGenerationOptions(model.SequentialImageGenerationOptions{
          MaxImages: model.NewOptInt32(int32(maxImages)),
       }),
    }

    resp, err := client.GenerateImages(ctx, generateReq)
    if err != nil {
        fmt.Printf("call GenerateImages error: %v\n", err)
        return
    }

    if resp.Error.IsSet() {
        fmt.Printf("API returned error: %s - %s\n", resp.Error.Value.Code, resp.Error.Value.Message)
        return
    }

    // Output the generated image information
    fmt.Printf("Generated %d images:\n", len(resp.Data))
    for i, image := range resp.Data {
        var url string
        if image.URL.IsSet() {
            url = image.URL.Or("")
        } else {
            url = "N/A"
        }
        fmt.Printf("Image %d: Size: %s, URL: %s\n", i+1, image.Size.Or(""), url)
    }
}
```

**OpenAI**

```Python
import os
from openai import OpenAI

client = OpenAI(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

imagesResponse = client.images.generate(
    model="seedream-5-0-lite-260128",
    prompt="Using this LOGO as a reference, create a visual design system for an outdoor sports brand named GREEN, including packaging bags, hats, cards, lanyards, etc. Main visual tone is green, with a fun, simple, and modern style.",
    size="2K",
    output_format="png",
    response_format="url",
    extra_body={
        "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imageToimages.png",
        "watermark": True,
        "sequential_image_generation": "auto",
        "sequential_image_generation_options": {
            "max_images": 4
        },
    }
)

# Iterate through all image data
for image in imagesResponse.data:
    # Output the current image's URL and size
    print(f"URL: {image.url}, Size: {image.size}")
```

### Multi\-image\-to\-batch\-image (multi\-image input, batch\-image output)

|Prompt |Input image 1 |Input image 2 |Output (three images will be generated) |
|---|---|---|---|
|Generate 3 images of a girl and a cow plushie happily riding a roller coaster in an amusement park, depicting morning, noon, and night. |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/77024d8e03f24862b066bfc385301120~tplv-goo7wpa0wc-image.image) |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/2cbc5cf5a68d44899fc52f177fb9cf51~tplv-goo7wpa0wc-image.image) |![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/79f02de3dbaa4b5f9767b91c3fee0471~tplv-goo7wpa0wc-image.image) |

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
  "model": "seedream-5-0-lite-260128",
    "prompt": "Generate 3 images of a girl and a cow plushie happily riding a roller coaster in an amusement park, depicting morning, noon, and night.",
    "image": ["https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimages_1.png", "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimages_2.png"],
    "sequential_image_generation": "auto",
    "sequential_image_generation_options": {
        "max_images": 3
    },
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
from arkruntime.types.images import SequentialImageGenerationOptionsParam

client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

imagesResponse = client.images.generate(
    # Replace with Model ID
    model="seedream-5-0-lite-260128",
    prompt="Generate 3 images of a girl and a cow plushie happily riding a roller coaster in an amusement park, depicting morning, noon, and night.",
    image=["https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimages_1.png", "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimages_2.png"],
    size="2K",
    sequential_image_generation="auto",
    sequential_image_generation_options=SequentialImageGenerationOptionsParam(max_images=3),
    output_format="png",
    response_format="url",
    watermark=True
)

# Iterate through all image data
for image in imagesResponse.data:
    # Output the current image's URL and size
    print(f"URL: {image.url}, Size: {image.size}")
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
        String apiKey = System.getenv("ARK_API_KEY");
        ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
        Dispatcher dispatcher = new Dispatcher();
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                // The base URL for model invocation
                .dispatcher(dispatcher)
                .connectionPool(connectionPool)
                .apiKey(apiKey)
                .build();

        SequentialImageGenerationOptions sequentialImageGenerationOptions = new SequentialImageGenerationOptions();
        sequentialImageGenerationOptions.setMaxImages(3);
        // CreateImageGenerationRequestImage requires ark-runtime 0.6.0 or later.
        CreateImageGenerationRequest generateRequest = CreateImageGenerationRequest.builder()
                 .model("seedream-5-0-lite-260128") // Replace with Model ID
                 .prompt("Generate 3 images of a girl and a cow plushie happily riding a roller coaster in an amusement park, depicting morning, noon, and night.")
                 .image(CreateImageGenerationRequestImage.ofList(Arrays.asList(
                     "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimages_1.png",
                     "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimages_2.png"
                 )))
                 .outputFormat(OutputFormat.PNG)
                 .size("2K")
                 .sequentialImageGeneration(SequentialImageGenerationMode.AUTO)
                 .sequentialImageGenerationOptions(sequentialImageGenerationOptions)

                 .responseFormat(ResponseFormat.URL)
                 .stream(false)
                 .watermark(true)
                 .build();
        ImageGenerationResponse imagesResponse = service.generateImages(generateRequest);

        // Iterate through all image data
        if (imagesResponse != null && imagesResponse.getData() != null) {
            for (int i = 0; i < imagesResponse.getData().size(); i++) {
                // Retrieve image information
                String url = imagesResponse.getData().get(i).getUrl();
                String size = imagesResponse.getData().get(i).getSize();
                System.out.printf("Image %d:%n", i + 1);
                System.out.printf("  URL: %s%n", url);
                System.out.printf("  Size: %s%n", size);
                System.out.println();
            }


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
        // The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    outputFormat := model.OutputFormatPNG
    var sequentialImageGeneration model.SequentialImageGenerationMode = "auto"
    maxImages := 3

    generateReq := &model.CreateImageGenerationRequest{
       Model:          "seedream-5-0-lite-260128",
       Prompt:         model.NewOptString("Generate 3 images of a girl and a cow plushie happily riding a roller coaster in an amusement park, depicting morning, noon, and night."),
       Image:         model.NewOptCreateImageGenerationRequestImage(model.NewStringArrayCreateImageGenerationRequestImage([]string{
           "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimages_1.png",
           "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimages_2.png",
       })),

       Size:           model.NewOptString("2K"),
       OutputFormat: model.NewOptOutputFormat(outputFormat),
       ResponseFormat: model.NewOptResponseFormat(model.ResponseFormatURL),
       Watermark:      model.NewOptBool(true),
       SequentialImageGeneration: model.NewOptSequentialImageGenerationMode(sequentialImageGeneration),
       SequentialImageGenerationOptions: model.NewOptSequentialImageGenerationOptions(model.SequentialImageGenerationOptions{
          MaxImages: model.NewOptInt32(int32(maxImages)),
       }),
    }

    resp, err := client.GenerateImages(ctx, generateReq)
    if err != nil {
        fmt.Printf("call GenerateImages error: %v\n", err)
        return
    }

    if resp.Error.IsSet() {
        fmt.Printf("API returned error: %s - %s\n", resp.Error.Value.Code, resp.Error.Value.Message)
        return
    }

    // Output the generated image information
    fmt.Printf("Generated %d images:\n", len(resp.Data))
    for i, image := range resp.Data {
        var url string
        if image.URL.IsSet() {
            url = image.URL.Or("")
        } else {
            url = "N/A"
        }
        fmt.Printf("Image %d: Size: %s, URL: %s\n", i+1, image.Size.Or(""), url)
    }
}
```

**OpenAI**

```Python
import os
from openai import OpenAI

client = OpenAI(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

imagesResponse = client.images.generate(
    model="seedream-5-0-lite-260128",
    prompt="Generate 3 images of a girl and a cow plushie happily riding a roller coaster in an amusement park, depicting morning, noon, and night.",
    size="2K",
    output_format="png",
    response_format="url",
    extra_body={
        "image": ["https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimages_1.png", "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imagesToimages_2.png"],
        "watermark": True,
        "sequential_image_generation": "auto",
        "sequential_image_generation_options": {
            "max_images": 3
        },
    }
)

# Iterate through all image data
for image in imagesResponse.data:
    # Output the current image's URL and size
    print(f"URL: {image.url}, Size: {image.size}")
```

## **Prompt recommendations**

* Use coherent natural language to describe the **subject + action + environment**. If aesthetics matter, include descriptors of **style,**  **color,**  **lighting,**  or **composition**. For details, see [Seedream 4.0-5.0 prompt guide](seedream-4-0-5-0-prompt-guide.md).

* Keep text prompts under 600 English words. Very long prompts may scatter the information, causing the model to overlook details and focus only on key points, which can result in missing elements in the generated image.

# Advanced usage

## Streaming output

> Seedream 5.5, Seedream 5.0 pro, and Seedream 5.0 flash do not support this capability.

The models support streaming image generation. Results are returned as soon as an image is generated, enabling faster browsing and improving the waiting experience.

Enable streaming output mode by setting the **stream** parameter to `true`.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/3a6bf2b6c3f0493e8eef28e76ce62784~tplv-goo7wpa0wc-image.image)

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "seedream-5-0-lite-260128",
    "prompt": "Referring to Figure 1, generate four images with characters wearing sunglasses, riding motorcycles, wearing hats, and holding lollipops",
    "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imageToimages_1.png",
    "sequential_image_generation": "auto",
    "sequential_image_generation_options": {
        "max_images": 4
    },
    "size": "2K",
    "stream": true,
    "output_format":"png",
    "watermark": true
}'
```

**Python**

```Python
import os
# Install SDK:  python -m pip install --upgrade arkruntime
from arkruntime import Ark
from arkruntime.types.images import SequentialImageGenerationOptionsParam

client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

if __name__ == "__main__":
    stream = client.images.generate(
        # Replace with Model ID
        model="seedream-5-0-lite-260128",
        prompt="Referring to Figure 1, generate four images with characters wearing sunglasses, riding motorcycles, wearing hats, and holding lollipops",
        image="https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imageToimages_1.png",
        size="2K",
        sequential_image_generation="auto",
        sequential_image_generation_options=SequentialImageGenerationOptionsParam(max_images=4),
        output_format="png",
        response_format="url",
        stream=True,
        watermark=True
    )
    for event in stream:
        if event is None:
            continue
        if event.type == "image_generation.partial_failed":
            print(f"Stream generate images error: {event.error}")
            if event.error is not None and event.error.code.equal("InternalServiceError"):
                break
        elif event.type == "image_generation.partial_succeeded":
            if event.error is None and event.url:
                print(f"recv.Size: {event.size}, recv.Url: {event.url}")
        elif event.type == "image_generation.completed":
            if event.error is None:
                print("Final completed event:")
                print("recv.Usage:", event.usage)
        elif event.type == "image_generation.partial_image":
            print(f"Partial image index={event.partial_image_index}, size={len(event.b64_json)}")
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
        String apiKey = System.getenv("ARK_API_KEY");
        ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
        Dispatcher dispatcher = new Dispatcher();
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                // The base URL for model invocation
                .dispatcher(dispatcher)
                .connectionPool(connectionPool)
                .apiKey(apiKey)
                .build();

        SequentialImageGenerationOptions sequentialImageGenerationOptions = new SequentialImageGenerationOptions();
        sequentialImageGenerationOptions.setMaxImages(4);
        // CreateImageGenerationRequestImage requires ark-runtime 0.6.0 or later.
        CreateImageGenerationRequest generateRequest = CreateImageGenerationRequest.builder()
                 .model("seedream-5-0-lite-260128") //Replace with Model ID .
                 .prompt("Referring to Figure 1, generate four images with characters wearing sunglasses, riding motorcycles, wearing hats, and holding lollipops")
                 .image(CreateImageGenerationRequestImage.ofString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imageToimages_1.png"))
                 .size("2K")
                 .sequentialImageGeneration(SequentialImageGenerationMode.AUTO)
                 .sequentialImageGenerationOptions(sequentialImageGenerationOptions)
                 .outputFormat(OutputFormat.PNG)
                 .responseFormat(ResponseFormat.URL)
                 .stream(true)
                 .watermark(true)
                 .build();

        service.streamGenerateImages(generateRequest)
                .doOnError(Throwable::printStackTrace)
                .blockingForEach(
                        choice -> {
                            if (choice == null) return;
                            if (ImageGenerationStreamEventType.IMAGE_GENERATION_PARTIAL_FAILED.equals(choice.getType())) {
                                if (choice.getError() != null) {
                                    System.err.println("Stream generate images error: " + choice.getError());
                                    if (choice.getError().getCode() != null && choice.getError().getCode().equals("InternalServiceError")) {
                                        throw new RuntimeException("Server error, terminating stream.");
                                    }
                                }
                            }
                            else if (ImageGenerationStreamEventType.IMAGE_GENERATION_PARTIAL_SUCCEEDED.equals(choice.getType())) {
                                if (choice.getError() == null && choice.getUrl() != null && !choice.getUrl().isEmpty()) {
                                    System.out.printf("recv.Size: %s, recv.Url: %s%n", choice.getSize(), choice.getUrl());
                                }
                            }
                            else if (ImageGenerationStreamEventType.IMAGE_GENERATION_COMPLETED.equals(choice.getType())) {
                                if (choice.getError() == null && choice.getUsage() != null) {
                                    System.out.println("recv.Usage: " + choice.getUsage().toString());
                                }
                            }
                        }
                );
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
    "io"
    "os"
    "strings"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    model "github.com/volcengine/ark-runtime-go/arkruntime/model/images"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        // The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    outputFormat := model.OutputFormatPNG
    var sequentialImageGeneration model.SequentialImageGenerationMode = "auto"
    maxImages := 4

    generateReq := &model.CreateImageGenerationRequest{
       Model:          "seedream-5-0-lite-260128",
       Prompt:         model.NewOptString("Referring to Figure 1, generate four images with characters wearing sunglasses, riding motorcycles, wearing hats, and holding lollipops"),
       Image: model.NewOptCreateImageGenerationRequestImage(model.NewStringArrayCreateImageGenerationRequestImage([]string{"https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imageToimages_1.png"})),
       Size:           model.NewOptString("2K"),
       OutputFormat: model.NewOptOutputFormat(outputFormat),
       ResponseFormat: model.NewOptResponseFormat(model.ResponseFormatURL),
       Watermark:      model.NewOptBool(true),
       SequentialImageGeneration: model.NewOptSequentialImageGenerationMode(sequentialImageGeneration),
       SequentialImageGenerationOptions: model.NewOptSequentialImageGenerationOptions(model.SequentialImageGenerationOptions{
          MaxImages: model.NewOptInt32(int32(maxImages)),
       }),
    }

    stream, err := client.GenerateImagesStreaming(ctx, generateReq)
    if err != nil {
       fmt.Printf("call GenerateImagesStreaming error: %v\n", err)
       return
    }
    defer stream.Close()
    for {
       recv, err := stream.Recv()
       if err == io.EOF {
          break
       }
       if err != nil {
          fmt.Printf("Stream generate images error: %v\n", err)
          break
       }
       if recv.Type == model.ImageGenerationStreamEventTypeImageGenerationPartialFailed {
          fmt.Printf("Stream generate images error: %v\n", recv.Error)
          if strings.EqualFold(recv.Error.Value.Code, "InternalServiceError") {
             break
          }
       }
       if recv.Type == model.ImageGenerationStreamEventTypeImageGenerationPartialSucceeded {
          if !recv.Error.IsSet() && recv.URL.IsSet() {
             fmt.Printf("recv.Size: %s, recv.Url: %s\n", recv.Size.Or(""), recv.URL.Or(""))
          }
       }
       if recv.Type == model.ImageGenerationStreamEventTypeImageGenerationCompleted {
          if !recv.Error.IsSet() {
             fmt.Printf("recv.Usage: %v\n", recv.Usage.Or(model.Usage{}))
          }
       }
    }
}
```

**OpenAI**

```Python
import os
from openai import OpenAI

client = OpenAI(
    # The base URL for model invocation .
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

if __name__ == "__main__":
    stream = client.images.generate(
        model="seedream-5-0-lite-260128",
        prompt="Referring to Figure 1, generate four images with characters wearing sunglasses, riding motorcycles, wearing hats, and holding lollipops",
        size="2K",
        output_format="png",
        response_format="b64_json",
        stream=True,
        extra_body={
            "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_imageToimages_1.png",
            "watermark": True,
            "sequential_image_generation": "auto",
            "sequential_image_generation_options": {
                "max_images": 4
            },
        },
    )
    for event in stream:
        if event is None:
            continue
        elif event.type == "image_generation.partial_succeeded":
            if event.b64_json is not None:
                print(f"size={len(event.b64_json)}, base_64={event.b64_json}")
        elif event.type == "image_generation.completed":
            if event.usage is not None:
                print("Final completed event:")
                print("recv.Usage:", event.usage)
```

## Configure faster image generation

Set **optimize_prompt_options.mode** to choose between `standard` and `fast` based on your image quality and generation speed requirements.

* To balance generation speed and image quality, Seedream 5.0 pro and Seedream 4.0 support setting **optimize_prompt_options.mode** to `fast`. This significantly increases generation speed but may reduce image quality.

* Dola Seedream 5.5, Seedream 5.0 flash, Seedream 5.0 lite, and Seedream 4.5 support only `standard`.

The following example uses `fast` mode with Seedream 5.0 pro to generate a single image without batch\-image generation or streaming parameters. When switching to Seedream 4.0, you can use the same single\-image request; output images use JPEG format.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seedream-5-0-pro-260628",
    "prompt": "Generate one illustration of a courtyard in spring, with blooming flowers, green trees, soft lighting, and fresh colors.",
    "size": "2K",
    "optimize_prompt_options": {
        "mode": "fast"
    },
    "response_format": "url",
    "watermark": true
}'
```

**Python**

```Python
import os
# Install SDK:  python -m pip install --upgrade arkruntime
from arkruntime import Ark
from arkruntime.types.images import OptimizePromptOptionsParam

client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

imagesResponse = client.images.generate(
    # Replace with Model ID
    model="dola-seedream-5-0-pro-260628",
    prompt="Generate one illustration of a courtyard in spring, with blooming flowers, green trees, soft lighting, and fresh colors.",
    size="2K",
    optimize_prompt_options=OptimizePromptOptionsParam(mode="fast"),
    response_format="url",
    watermark=True
)

# Iterate through all image data
for image in imagesResponse.data:
    # Output the current image's URL and size
    print(f"URL: {image.url}, Size: {image.size}")
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
        String apiKey = System.getenv("ARK_API_KEY");
        ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
        Dispatcher dispatcher = new Dispatcher();
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                // The base URL for model invocation
                .dispatcher(dispatcher)
                .connectionPool(connectionPool)
                .apiKey(apiKey)
                .build();

        OptimizePromptOptions optimizePromptOptions = new OptimizePromptOptions();
        optimizePromptOptions.setMode(OptimizePromptMode.FAST);

        CreateImageGenerationRequest generateRequest = CreateImageGenerationRequest.builder()
                 .model("dola-seedream-5-0-pro-260628")  //Replace with Model ID
                 .prompt("Generate one illustration of a courtyard in spring, with blooming flowers, green trees, soft lighting, and fresh colors.")

                 .size("2K")
                 .optimizePromptOptions(optimizePromptOptions)
                 .responseFormat(ResponseFormat.URL)
                 .watermark(true)
                 .build();
        ImageGenerationResponse imagesResponse = service.generateImages(generateRequest);
        // Iterate through all image data
        if (imagesResponse != null && imagesResponse.getData() != null) {
            for (int i = 0; i < imagesResponse.getData().size(); i++) {
                // Retrieve image information
                String url = imagesResponse.getData().get(i).getUrl();
                String size = imagesResponse.getData().get(i).getSize();
                System.out.printf("Image %d:%n", i + 1);
                System.out.printf("  URL: %s%n", url);
                System.out.printf("  Size: %s%n", size);
                System.out.println();
            }


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
        // The base URL for model invocation .
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()
    mode := model.OptimizePromptModeFast

    generateReq := &model.CreateImageGenerationRequest{
        Model:  "dola-seedream-5-0-pro-260628",
        Prompt: model.NewOptString("Generate one illustration of a courtyard in spring, with blooming flowers, green trees, soft lighting, and fresh colors."),

        Size:                      model.NewOptString("2K"),
        ResponseFormat:            model.NewOptResponseFormat(model.ResponseFormatURL),
        Watermark:                 model.NewOptBool(true),
        OptimizePromptOptions: model.NewOptOptimizePromptOptions(model.OptimizePromptOptions{
            Mode: model.NewOptOptimizePromptMode(mode),
        }),
    }

    resp, err := client.GenerateImages(ctx, generateReq)
    if err != nil {
        fmt.Printf("call GenerateImages error: %v\n", err)
        return
    }

    if resp.Error.IsSet() {
        fmt.Printf("API returned error: %s - %s\n", resp.Error.Value.Code, resp.Error.Value.Message)
        return
    }

    // Output the generated image information
    fmt.Printf("Generated %d images:\n", len(resp.Data))
    for i, image := range resp.Data {
        var url string
        if image.URL.IsSet() {
            url = image.URL.Or("")
        } else {
            url = "N/A"
        }
        fmt.Printf("Image %d: Size: %s, URL: %s\n", i+1, image.Size.Or(""), url)
    }
}
```

**OpenAI**

```Python
import os
from openai import OpenAI

client = OpenAI(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

imagesResponse = client.images.generate(
    model="dola-seedream-5-0-pro-260628",
    prompt="Generate one illustration of a courtyard in spring, with blooming flowers, green trees, soft lighting, and fresh colors.",
    size="2K",
    response_format="url",
    extra_body={
        "watermark": True,
        "optimize_prompt_options": {"mode": "fast"}
    },
)

# Iterate through all image data
for image in imagesResponse.data:
    # Output the current image's URL and size
    print(f"URL: {image.url}, Size: {image.size}")
```

## Customize image output

The following parameters control image output specifications. Available values and defaults vary by model and scenario. For complete field definitions, see [Image generation API](image-generation-api.md).

|Parameter |Purpose |Configuration |
|---|---|---|
|`size` |Controls the dimensions of the output image. |Image generation and layer decomposition use different configuration methods. See [Image output dimensions](image-generation-tutorial.md#034e4a46). |
|`response_format` |Controls how generated images are returned. |Supported by all models. See [Image output methods](image-generation-tutorial.md#b4306703). |
|`output_format` |Controls the output image file format. |Supported by only some models. For layer decomposition, it controls only the base\-image format. See [Image output format](image-generation-tutorial.md#cbef7bec). |
|`background` |Controls whether the generated image has a transparent background. |Supported for image generation only by Dola Seedream 5.5 and Seedream 5.0 pro / flash. See [Image background type](image-generation-tutorial.md#image-background). |
|`watermark` |Controls whether generated images include a watermark. |Supported by all models. See [Add a watermark to the image](image-generation-tutorial.md#6be7edc7). |

### Image output dimensions

The `size` parameter specifies output image dimensions. Different models and scenarios support resolution presets or explicit width and height in pixels.

**Image generation scenarios**

Choose either a resolution preset or explicit pixel dimensions for each request.

**Method 1: Specify a resolution preset (recommended)**

Describe the image aspect ratio, shape, or purpose in natural language in the `prompt`. The model determines the final image size. The following presets are supported:

|Model |Available resolution presets |
|---|---|
|Dola Seedream 5.5, Seedream 5.0 pro / flash |`1K`, `1.5K`, `2K` |
|Seedream 5.0 lite |`2K`, `3K`, `4K` |
|Seedream 4.5 |`2K`, `4K` |
|Seedream 4.0 |`1K`, `2K`, `4K` |

When selecting a resolution preset, the following are pixel mappings for common aspect ratios.

**Dola Seedream 5.5 and Seedream 5.0 pro / flash: resolution\-to\-pixel mappings**

|Aspect ratio |1K |1.5K |2K |
|---|---|---|---|
|1:1 |1024x1024 |1536x1536 |2048x2048 |
|4:3 |1152x864 |1792x1344 |2368x1776 |
|3:4 |864x1152 |1344x1792 |1776x2368 |
|16:9 |1424x800 |2048x1152 |2816x1584 |
|9:16 |800x1424 |1152x2048 |1584x2816 |
|3:2 |1248x832 |1872x1248 |2496x1664 |
|2:3 |832x1248 |1248x1872 |1664x2496 |
|21:9 |1568x672 |2352x1008 |3136x1344 |

**Seedream 5.0 lite: resolution\-to\-pixel mappings**

|Aspect ratio |2K |3K |4K |
|---|---|---|---|
|1:1 |2048x2048 |3072x3072 |4096x4096 |
|4:3 |2304x1728 |3456x2592 |4704x3520 |
|3:4 |1728x2304 |2592x3456 |3520x4704 |
|16:9 |2848x1600 |4096x2304 |5504x3040 |
|9:16 |1600x2848 |2304x4096 |3040x5504 |
|3:2 |2496x1664 |3744x2496 |4992x3328 |
|2:3 |1664x2496 |2496x3744 |3328x4992 |
|21:9 |3136x1344 |4704x2016 |6240x2656 |

**Seedream 4.5: resolution\-to\-pixel mappings**

|Aspect ratio |2K |4K |
|---|---|---|
|1:1 |2048x2048 |4096x4096 |
|4:3 |2304x1728 |4704x3520 |
|3:4 |1728x2304 |3520x4704 |
|16:9 |2848x1600 |5504x3040 |
|9:16 |1600x2848 |3040x5504 |
|3:2 |2496x1664 |4992x3328 |
|2:3 |1664x2496 |3328x4992 |
|21:9 |3136x1344 |6240x2656 |

**Seedream 4.0: resolution\-to\-pixel mappings**

|Aspect ratio |1K |2K |4K |
|---|---|---|---|
|1:1 |1024x1024 |2048x2048 |4096x4096 |
|4:3 |1152x864 |2304x1728 |4704x3520 |
|3:4 |864x1152 |1728x2304 |3520x4704 |
|16:9 |1280x720 |2848x1600 |5504x3040 |
|9:16 |720x1280 |1600x2848 |3040x5504 |
|3:2 |1248x832 |2496x1664 |4992x3328 |
|2:3 |832x1248 |1664x2496 |3328x4992 |
|21:9 |1512x648 |3136x1344 |6240x2656 |

**Method 2: Specify the width and height in pixels (** **`widthxheight`** **)**

When setting explicit pixel dimensions, both the total pixel range and the aspect ratio range must be satisfied. Total pixels are the product of width and height, not a limit on either dimension.

|Model |Total pixel range |Aspect ratio range |
|---|---|---|
|Dola Seedream 5.5, Seedream 5.0 pro / flash |[921,600, 4,624,220] |[1/16, 16] |
|Seedream 5.0 lite / 4.5 |[3,686,400, 16,777,216] |[1/16, 16] |
|Seedream 4.0 |[921,600, 16,777,216] |[1/16, 16] |

The following examples show the two size configuration methods:

**Specify a resolution preset**

```JSON
{
  "prompt": "Generate an illustration of a spring courtyard with a 3:2 aspect ratio, blooming flowers, and green trees",
  "size": "2K"
}
```

**Specify pixel dimensions**

```JSON
{
  "prompt": "Generate an illustration of a spring courtyard with blooming flowers and green trees",
  "size": "2048x2048"
}
```

**Layer decomposition scenarios**

This scenario supports only a resolution preset or `auto`; explicit pixel dimensions are not supported.

|Model |Available `size` values |
|---|---|
|Dola Seedream 5.5, Seedream 5.0 pro / flash |`1K`, `1.5K`, `2K`, `auto` |
|Seedream 5.0 lite / 4.5 / 4.0 |Layer decomposition is not supported. |

The output image resolution follows these rules:

* **Base image**: The output base image resolution is the same as the resolution specified by `size`. The output base image keeps the same aspect ratio as the original image to decompose.

* **Each layer**: The output layer resolution is close to the resolution specified by `size`. Each output layer keeps the same aspect ratio as its region in the original image.

**Layer decomposition: auto adaptation rules**

In `auto` mode, the model outputs the base image and each layer based on their original sizes in the input image, while preserving their respective aspect ratios in the original image:

* If the original size of the base image and each layer in the input image is between [`1280x720` (921,600), `2048x2048x1.1025` (4,624,220)], the base image and each layer are output at their original sizes.

* If the original size of the base image and each layer in the input image is smaller than 1K, the base image and each layer are output at 1K.

* If the original size of the base image and each layer in the input image is larger than 2K, the base image and each layer are output at 2K.

### Image output methods

The `response_format` parameter specifies how generated images are returned.

* `url`: Returns an image download link that is valid for 24 hours after generation.

* `b64_json`: Returns Base64\-encoded image data in the `b64_json` response field.

|Model |Available `response_format` values |Default |
|---|---|---|
|All Seedream models |`url`, `b64_json` |`url` |

### Image output format

The `output_format` parameter specifies the generated image file format.

* `png`: PNG format, which preserves the alpha channel.

* `jpeg`: JPEG format, which does not preserve the alpha channel.

|Model |Available `output_format` values |Default |
|---|---|---|
|Dola Seedream 5.5 |`png`, `jpeg` |`png` |
|Seedream 5.0 pro / flash / lite |`png`, `jpeg` |`jpeg` |
|Seedream 4.5 / 4.0 |Not configurable; fixed as `jpeg` |`jpeg` |

> **Note**

> In layer decomposition scenarios, `output_format` controls only the output format of the base image. Layers are always output in PNG format.

### Image background type

The `background` parameter controls whether a generated image has an alpha channel. It applies only to image generation scenarios.

* `auto`: The model determines the background type from the prompt and `output_format`.

* `transparent`: Generates an RGBA image with an alpha channel.

* `opaque`: Generates an RGB image without an alpha channel.

|Model |Available `background` values |Default |
|---|---|---|
|Dola Seedream 5.5 |`auto`, `transparent`, `opaque` |`auto` |
|Seedream 5.0 pro / flash |`transparent`, `opaque` |`opaque` |
|Seedream 5.0 lite / 4.5 / 4.0 |Not configurable |— |

> **Tip**

* > When `background` is `transparent`, `output_format` must be `png`; `jpeg` returns an error.

* > Dola Seedream 5.5 supports text\-to\-image RGBA generation and RGBA generation from up to 10 ordinary reference images. Seedream 5.0 pro / flash support only one transparent PNG reference image.

* > Layer decomposition automatically generates transparent PNG layers. Do not pass `background` in this scenario; doing so returns an error.

### Add a watermark to the image

Control whether to add a watermark to the generated image by setting the **watermark** parameter.

* `false`: No watermark.

* `true`: Add an "AI generated" watermark on the bottom\-right corner of the image.

|Model |Available `watermark` values |Default |
|---|---|---|
|All Seedream models |`false`, `true` |`true` |

# Usage limitations

**SDK version upgrade**

To ensure model functionalities, upgrade to the latest SDK version. Refer to [Install and upgrade SDK](install-and-upgrade-sdk.md) for details.

**Image input limitations**

Image input methods are the same for both scenarios:

* Image URL: Make sure that the image URL is accessible.

   Example: `https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_5_imageToimage.png`

* Base64 encoding: Use the format `data:image/<image_format>;base64,<Base64_encoding>`. Note that `<image_format>` must use lowercase letters, such as `data:image/png;base64,<base64_image>`.

   To obtain the Base64 encoding of an image, you can use a third\-party tool, such as https://base64.guru/converter/encode/image.

Input image constraints vary by scenario, as shown in the following table:

|Constraint |Image generation scenarios |Layer decomposition scenarios |
|---|---|---|
|Image format |jpeg, png, webp, bmp, tiff, gif, heic, heif |png, jpeg |
|Total pixels (width × height) |[196, `6000×6000` (36 million)] |[`512×512` (262,144), `6000×6000` (36 million)] |
|Width and height (px) |Greater than 14 |— |
|Aspect ratio (width/height) |[1/16, 16] |[1/16, 16] |
|Size |Up to 30 MB |Up to 30 MB |
|Number of input images |Dola Seedream 5.5, Seedream 5.0 pro, and Seedream 5.0 flash support up to 10 images. Seedream 5.0 lite, Seedream 4.5, and Seedream 4.0 support up to 14 images. |Only one image is supported. |

> **Note**

> Total pixels refer to the product of the width and height of a single image, rather than a limit on either dimension individually.

**Retention period**

Image URL is retained for 24 hours and will be automatically cleared after expiration. Be sure to save your generated images in time.

We recommend configuring [BytePlus TOS data subscription](https://docs.byteplus.com/en/docs/tos/Data_subscription) to automatically transfer model outputs to your own TOS bucket for long\-term backup or further processing.

**Rate limits information**

* IPM rate limit: The maximum number of images that can be generated per minute by a specific version of a model under an account. If the limit is exceeded, an error will occur.

   * In layer decomposition scenarios, Dola Seedream 5.5 pre\-deducts IPM based on `layer_decomposition_options.max_images` (default: 30); Seedream 5.0 pro / flash pre\-deduct 17 IPM for each request. After all images are generated, excess quota is returned based on the actual number of generated images.

* The limit values vary by model. For more details, see [Image generation](model-list.md#9df4d9fd).
