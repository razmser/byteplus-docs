# Seedream 5.0 pro / flash tutorial

Dola Seedream 5.0 pro (hereafter referred to as Seedream 5.0 pro) and Dola Seedream 5.0 flash (hereafter referred to as Seedream 5.0 flash) support text\-to\-image generation and image\-to\-image generation with one or multiple reference images. Both models also support interactive editing through coordinates and free\-form markings, and can decompose one image into a base image and multiple layers. This tutorial introduces the capabilities of both models and helps you quickly get started with the [Image generation API](image-generation-api.md).

<span id="pro-featured"></span>
# Featured capabilities

Seedream 5.0 pro and Seedream 5.0 flash support the following featured capabilities:


<span aceTableMode="list" aceTableWidth="1.5,4,2.5"></span>
|Featured capability |Description |Preview |
|---|---|---|
|[Interactive editing](seedream-5-0-pro.md#interactive-edit) |Specify edit positions by using coordinates, bounding boxes, arrows, and other markers for precise operations such as replacing local elements, positioning objects, and generating content in selected regions. |<video src="https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream_5.0_editing_demo.mp4" controls></video><br> |
|[Layer decomposition](seedream-5-0-pro.md#layer-decomposition) |Automatically decompose subjects, backgrounds, text, decorative elements, and other content in one image into one base image and up to 16 independent layers with alpha channels for further editing, such as moving, scaling, replacing, or recoloring elements. |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_layer_output2.gif) </span> |
|Native multilingual generation |Natively generate text in 14 additional languages: Russian, Arabic, Filipino, Thai, Turkish, Korean, Malay, Spanish, Portuguese, Indonesian, French, German, Vietnamese, and Japanese. |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro-part2-tab3-group1-input1.png) </span> |


<span id="pro-overview"></span>
# Capability overview

Seedream 5.0 pro and Seedream 5.0 flash both support precise image editing by specifying edit locations with coordinates, bounding boxes, arrows, and other markers; decomposing a single image into one base image and up to 16 independent layers; and native multilingual generation. The models differ as follows:


* **Seedream 5.0 pro**: Provides higher image quality and is suitable for scenarios where image quality is the priority.

* **Seedream 5.0 flash**: Generates images faster and costs less, making it suitable for latency\- and cost\-sensitive scenarios.



<span aceTableMode="list" aceTableWidth="1.5,2,3,3"></span>
|Model name ||[Seedream 5.0 pro](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-0-pro) |[Seedream 5.0 flash](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dola-seedream-5-0-flash) |
|---|---|---|---|
|Model ID ||dola\-seedream\-5\-0\-pro\-260628 |dola\-seedream\-5\-0\-flash\-260915 |
|[Text-to-image](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/seedream-4-0-5-0#9695d195) ||✓ |✓ |
|[Single-image or multi-image image-to-image](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/seedream-4-0-5-0#8bc49063) ||✓ |✓ |
|[Interactive editing](seedream-5-0-pro.md#interactive-edit) ||✓ |✓ |
|[Layer decomposition](seedream-5-0-pro.md#layer-decomposition) ||✓ |✓ |
|Native multilingual generation ||✓ |✓ |
|Model parameters |Resolution |1K, 1.5K, 2K |1K, 1.5K, 2K |
||Output format |png, jpeg |png, jpeg |
||Prompt optimization mode |Standard mode, fast mode |Standard mode |
||Generation count |Supports generating one image or multiple layers (one base image and up to 16 layers) |Supports generating one image or multiple layers (one base image and up to 16 layers) |
|IPM rate limit (images/minute) ||500 |500 |


<span id="pro-basic-usage"></span>
# Basic usage

The basic usage of Seedream 5.0 pro and Seedream 5.0 flash, including text\-to\-image, image\-to\-image, and multi\-image blending, is the same as other Seedream models. Set the `model` parameter to the corresponding Model ID:


* Seedream 5.0 pro: `dola-seedream-5-0-pro-260628`

* Seedream 5.0 flash: `dola-seedream-5-0-flash-260915`


For detailed code examples and instructions, see:


* [Text-to-image](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/seedream-4-0-5-0#9695d195)

* [Image-to-image](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/seedream-4-0-5-0#8bc49063)

* [Multi-image blending](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/seedream-4-0-5-0#4a35e28f)


<span id="interactive-edit"></span>
# Interactive editing

Seedream 5.0 pro and Seedream 5.0 flash support specifying edit positions by using **bounding boxes, points, arrows, annotation boxes, coordinates**, and other methods to precisely generate or modify local areas. For detailed instructions, see [Seedream 5.0 pro / flash interactive editing guide](seedream-5-0-pro-editing-guide.md).

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="tip">The Java examples use <code>CreateImageGenerationRequestImage</code>, which requires <code>com.volcengine:ark-runtime</code> 0.6.0 or later. If your IDE cannot resolve this type or reports an <code>image</code> argument type mismatch, upgrade to the latest stable version available from <a href="https://central.sonatype.com/artifact/com.volcengine/ark-runtime">Maven Central</a> and reload the project dependencies.</div>


<span id="interactive-edit-freeform"></span>
## Example: Freeform marking

Use freehand sketches, doodles, circles, or other markings on the reference image to specify the edit region. The model recognizes the marked area and generates or replaces content within it, while blending the result naturally into the original scene.


<span aceTableMode="list" aceTableWidth="1,1,1"></span>
|Prompt |Input image |Output |
|---|---|---|
|Edit the image based on the hand\-drawn sketch. Add a stack of realistic magazines or art books in the marked area at the lower left, and add a ceramic cup of coffee with a saucer in the marked area on the right. Remove all sketch lines. Keep the composition unchanged. Let the newly added objects blend naturally into the original scene. |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_input2.png) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_output2.png) </span> |



<Tabs>
<Tab zoneid="wsWKFMuwVl" title="Curl">
<TabTitle>Curl</TabTitle>

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seedream-5-0-pro-260628",
    "prompt": "Edit the image based on the hand-drawn sketch. Add a stack of realistic magazines or art books in the marked area at the lower left, and add a ceramic cup of coffee with a saucer in the marked area on the right. Remove all sketch lines. Keep the composition unchanged. Let the newly added objects blend naturally into the original scene.",
    "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_input2.png",
    "size": "2K",
    "output_format": "png",
    "watermark": true
}'
```



</Tab>
<Tab zoneid="BxmsNWVVkO" title="Python">
<TabTitle>Python</TabTitle>

```Python
import os

# Install SDK:  python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv("ARK_API_KEY"),
)

imagesResponse = client.images.generate(
    # Replace with Model ID
    model="dola-seedream-5-0-pro-260628",
    prompt="Edit the image based on the hand-drawn sketch. Add a stack of realistic magazines or art books in the marked area at the lower left, and add a ceramic cup of coffee with a saucer in the marked area on the right. Remove all sketch lines. Keep the composition unchanged. Let the newly added objects blend naturally into the original scene.",
    image="https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_input2.png",
    size="2K",
    output_format="png",
    response_format="url",
    watermark=True,
)

print(imagesResponse.data[0].url)
```



</Tab>
<Tab zoneid="hD6H4CQLfS" title="Java">
<TabTitle>Java</TabTitle>

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.images.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

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
                .model("dola-seedream-5-0-pro-260628") // Replace with Model ID
                .prompt("Edit the image based on the hand-drawn sketch. Add a stack of realistic magazines or art books in the marked area at the lower left, and add a ceramic cup of coffee with a saucer in the marked area on the right. Remove all sketch lines. Keep the composition unchanged. Let the newly added objects blend naturally into the original scene.")
                .image(CreateImageGenerationRequestImage.ofString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_input2.png"))
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



</Tab>
<Tab zoneid="xdbAA62MUR" title="Go">
<TabTitle>Go</TabTitle>

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
        Model:          "dola-seedream-5-0-pro-260628",
        Prompt:         model.NewOptString("Edit the image based on the hand-drawn sketch. Add a stack of realistic magazines or art books in the marked area at the lower left, and add a ceramic cup of coffee with a saucer in the marked area on the right. Remove all sketch lines. Keep the composition unchanged. Let the newly added objects blend naturally into the original scene."),
        Image: model.NewOptCreateImageGenerationRequestImage(model.NewStringArrayCreateImageGenerationRequestImage([]string{"https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_input2.png"})),
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



</Tab>
<Tab zoneid="sjHnmXa7e1" title="OpenAI">
<TabTitle>OpenAI</TabTitle>

```Python
import os
from openai import OpenAI

client = OpenAI(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv("ARK_API_KEY"),
)

imagesResponse = client.images.generate(
    model="dola-seedream-5-0-pro-260628",
    prompt="Edit the image based on the hand-drawn sketch. Add a stack of realistic magazines or art books in the marked area at the lower left, and add a ceramic cup of coffee with a saucer in the marked area on the right. Remove all sketch lines. Keep the composition unchanged. Let the newly added objects blend naturally into the original scene.",
    size="2K",
    output_format="png",
    response_format="url",
    extra_body={
        "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_input2.png",
        "watermark": True,
    },
)

print(imagesResponse.data[0].url)
```



</Tab>
</Tabs>


<span id="example-coordinate-positioning"></span>
## Example: Coordinate positioning

Add `<point>` or `<bbox>` coordinate tags to the prompt to precisely specify a cross\-image edit area and position the subject. For complete steps and parameter descriptions, see [Seedream 5.0 pro / flash interactive editing guide](seedream-5-0-pro-editing-guide.md).


<span aceTableMode="list" aceTableWidth="1,1.5,1"></span>
|Prompt |Input image |Output |
|---|---|---|
|`Place the subject from Image 1 <bbox>179 283 796 986</bbox> at the position in Image 2 <bbox>118 331 933 871</bbox>.` |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_5.0_input_combined.png) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_5.0_output.jpeg) </span> |


<span id="usage-instructions"></span>
## Usage instructions

Interactive editing requires the following inputs: **the image to edit** and a **prompt** that contains location information and editing instructions. Depending on the location method, interactive editing supports the following two forms:


<span aceTableMode="list" aceTableWidth="1,1"></span>
|Form 1: Free\-form marker + natural\-language location |Form 2: Precise coordinate location |
|---|---|
|Mark the edit area on the image to edit by using hand\-drawn sketches, doodles, circles, or other methods, and then describe the marker position and editing intent in natural language in the prompt.<br><br>```JSON```<br>```{```<br>```    "prompt": "Add a TV inside the blue box"```<br>```}```<br> |Use a tool to frame the coordinates of the content to edit. For information about how to obtain coordinates, see [Seedream 5.0 pro / flash interactive editing guide](seedream-5-0-pro-editing-guide.md). In the prompt, use `<point>` or `<bbox>` coordinate tags to precisely specify the position.<br><br>```JSON```<br>```{```<br>```    "prompt": "Place the subject from Image 1 <bbox>179 283 796 986</bbox> at the position in Image 2 <bbox>118 331 933 871</bbox>"```<br>```}```<br> |


After preparing the inputs, pass **the image to edit** and the **prompt** to the API to generate the image editing result.

<span id="layer-decomposition"></span>
# Layer decomposition

Seedream 5.0 pro and Seedream 5.0 flash can automatically decompose subjects, backgrounds, text, decorative elements, and other content in one input image into one base image and up to 16 independently editable layers. Each layer is a PNG image with an alpha channel. The model also returns the position, stacking order, and content description of each layer, allowing you to move, scale, replace, recolor, and recompose the layers in design tools or frontend canvases.

<span id="workflow"></span>
## Workflow

The following diagram shows the layer decomposition workflow:

<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/image-generation/new-202608111128.svg) </span>

<span id="decompose-an-image"></span>
## Decompose an image

Set `layer_decomposition` to `true` to enable layer decomposition mode. In this mode, `image` is required and supports only one input image. `prompt` is optional and can specify the elements to decompose.

<span id="prompt-tips"></span>
### Prompt tips

The following prompt methods are recommended:


<span aceTableMode="list" aceTableWidth="1,2"></span>
|Goal |`prompt` |
|---|---|
|Automatically decompose the main elements |With cURL, Java, or Go, omit `prompt`. The model identifies the main subjects, text, background, decorative elements, and other content, and then decomposes them into independent layers. The Python and OpenAI SDKs require `prompt`; use a general instruction to decompose the main visual elements. |
|Specify elements to decompose |Describe the elements in natural language, for example, "Decompose the person, title text, and decorative icon in the lower\-right corner." You can also mark the elements in the input image with doodles or selections to help the model locate them. |
|Specify exact regions |Use normalized `<bbox>` coordinate tags in `prompt` to specify the regions to decompose. For details about obtaining coordinates, see [Seedream 5.0 pro / flash interactive editing guide](seedream-5-0-pro-editing-guide.md#key-steps). |


<span id="example-automatically-decompose-all-main-elements"></span>
### Example: Automatically decompose all main elements

To let the model identify and decompose the main elements automatically, provide the image to decompose and set `layer_decomposition` to `true`. With cURL, Java, or Go, omit `prompt`. The Python and OpenAI SDKs require `prompt`, so their examples use a general decomposition instruction.


<span aceTableMode="list" aceTableWidth="2,2,1.3,1.3,1.3"></span>
|One input image (without `prompt`) |Base image |Layers |||
|---|---|---|---|---|
|<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/layer_auto.png) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_auto_0_base.jpeg) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_auto_1.png) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_auto_2.png) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_auto_3.png) </span> |
|||<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_auto_4.png) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_auto_5.png) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_auto_6.png) </span> |
|||<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_auto_7.png) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_auto_8.png) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_auto_9.png) </span> |
|||<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_auto_10.png) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_auto_11.png) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_auto_12.png) </span> |



<Tabs>
<Tab zoneid="mqJtZzLekp" title="Curl">
<TabTitle>Curl</TabTitle>

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seedream-5-0-pro-260628",
    "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/layer_auto.png",
    "size": "2K",
    "layer_decomposition": true,
    "watermark": true
}'
```



</Tab>
<Tab zoneid="utEUJvZGnb" title="Python">
<TabTitle>Python</TabTitle>

```Python
import os

# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv("ARK_API_KEY"),
)

imagesResponse = client.images.generate(
    # Replace with Model ID
    model="dola-seedream-5-0-pro-260628",
    prompt="Decompose the main visual elements into separate layers.",
    image="https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/layer_auto.png",
    size="2K",
    layer_decomposition=True,
    response_format="url",
    watermark=True,
)

for item in imagesResponse.data:
    print(item)
```



</Tab>
<Tab zoneid="MQF0oYKTBk" title="Java">
<TabTitle>Java</TabTitle>

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.images.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

import java.util.concurrent.TimeUnit;

public class ImageGenerationsExample {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
        Dispatcher dispatcher = new Dispatcher();
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3") // The base URL for model invocation
                .dispatcher(dispatcher)
                .connectionPool(connectionPool)
                .apiKey(apiKey)
                .build();

        // CreateImageGenerationRequestImage requires ark-runtime 0.6.0 or later.
        CreateImageGenerationRequest generateRequest = CreateImageGenerationRequest.builder()
                .model("dola-seedream-5-0-pro-260628") // Replace with Model ID
                .image(CreateImageGenerationRequestImage.ofString(
                        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/layer_auto.png"))
                .size("2K")
                .layerDecomposition(true)
                .responseFormat(ResponseFormat.URL)
                .watermark(true)
                .build();

        ImageGenerationResponse imagesResponse = service.generateImages(generateRequest);
        imagesResponse.getData().forEach(item -> System.out.println(item));

        service.shutdownExecutor();
    }
}
```



</Tab>
<Tab zoneid="TkcUJ48txE" title="Go">
<TabTitle>Go</TabTitle>

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

    generateReq := &model.CreateImageGenerationRequest{
        Model:              "dola-seedream-5-0-pro-260628",
        Image: model.NewOptCreateImageGenerationRequestImage(model.NewStringArrayCreateImageGenerationRequestImage([]string{"https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/layer_auto.png"})),
        Size:               model.NewOptString("2K"),
        LayerDecomposition: model.NewOptBool(true),
        ResponseFormat: model.NewOptResponseFormat(model.ResponseFormatURL),
        Watermark:          model.NewOptBool(true),
    }

    imagesResponse, err := client.GenerateImages(ctx, generateReq)
    if err != nil {
        fmt.Printf("generate images error: %v\n", err)
        return
    }

    for _, item := range imagesResponse.Data {
        fmt.Printf("%+v\n", item)
    }
}
```



</Tab>
<Tab zoneid="UCodPAYX1I" title="OpenAI">
<TabTitle>OpenAI</TabTitle>

```Python
import os
from openai import OpenAI

client = OpenAI(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv("ARK_API_KEY"),
)

imagesResponse = client.images.generate(
    model="dola-seedream-5-0-pro-260628",
    prompt="Decompose the main visual elements into separate layers.",
    size="2K",
    response_format="url",
    extra_body={
        "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/layer_auto.png",
        "layer_decomposition": True,
        "watermark": True,
    },
)

for item in imagesResponse.data:
    print(item)
```



</Tab>
</Tabs>


<span id="example-specify-exact-regions-to-decompose"></span>
### Example: Specify exact regions to decompose

To precisely specify regions for decomposition, use normalized `<bbox>` coordinates to locate each element.


<span aceTableMode="list" aceTableWidth="2,2,1.5,1.5"></span>
|One input image and a prompt with normalized coordinates |Base image |Layers ||
|---|---|---|---|
|<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_layer_input.png) </span><br><br>&nbsp;<br><br>> Decompose the image into seven layers, including six text groups at:<br><br>> `<bbox>180 64 812 198</bbox>`, `<bbox>757 210 939 280</bbox>`, `<bbox>63 212 320 282</bbox>`, `<bbox>178 714 826 810</bbox>`, `<bbox>814 819 949 894</bbox>`, and `<bbox>326 824 669 930</bbox>`;<br><br>> and one parrot at `<bbox>347 305 642 997</bbox>`. |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_0.jpeg) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_1.png) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_4.png) </span> |
|||<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_2.png) </span> ||
|||<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_3.png) </span> ||
|||<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_6.png) </span> ||
|||<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_5.png) </span> ||
|||<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_7.png) </span> ||



<Tabs>
<Tab zoneid="TPNJRjZ07R" title="Curl">
<TabTitle>Curl</TabTitle>

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seedream-5-0-pro-260628",
    "prompt": "Perform precise layer separation on the image. The text regions to separate are at <bbox>180 64 812 198</bbox>, <bbox>757 210 939 280</bbox>, <bbox>63 212 320 282</bbox>, <bbox>178 714 826 810</bbox>, <bbox>814 819 949 894</bbox>, and <bbox>326 824 669 930</bbox>; the parrot is at <bbox>347 305 642 997</bbox>.",
    "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_layer_input.png",
    "layer_decomposition": true,
    "size": "2K",
    "output_format": "jpeg",
    "response_format": "url",
    "watermark": true
}'
```



</Tab>
<Tab zoneid="ytgxWVwugA" title="Python">
<TabTitle>Python</TabTitle>

```Python
import os

# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv("ARK_API_KEY"),
)

imagesResponse = client.images.generate(
    # Replace with Model ID
    model="dola-seedream-5-0-pro-260628",
    prompt="Perform precise layer separation on the image. The text regions to separate are at <bbox>180 64 812 198</bbox>, <bbox>757 210 939 280</bbox>, <bbox>63 212 320 282</bbox>, <bbox>178 714 826 810</bbox>, <bbox>814 819 949 894</bbox>, and <bbox>326 824 669 930</bbox>; the parrot is at <bbox>347 305 642 997</bbox>.",
    image="https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_layer_input.png",
    layer_decomposition=True,
    size="2K",
    output_format="jpeg",
    response_format="url",
    watermark=True,
)

for item in imagesResponse.data:
    print(item)
```



</Tab>
<Tab zoneid="kd6rx2mHMZ" title="Java">
<TabTitle>Java</TabTitle>

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.images.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

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
                .model("dola-seedream-5-0-pro-260628") // Replace with Model ID
                .prompt("Perform precise layer separation on the image. The text regions to separate are at <bbox>180 64 812 198</bbox>, <bbox>757 210 939 280</bbox>, <bbox>63 212 320 282</bbox>, <bbox>178 714 826 810</bbox>, <bbox>814 819 949 894</bbox>, and <bbox>326 824 669 930</bbox>; the parrot is at <bbox>347 305 642 997</bbox>.")
                .image(CreateImageGenerationRequestImage.ofString("https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_layer_input.png"))
                .layerDecomposition(true)
                .size("2K")
                .outputFormat(OutputFormat.JPEG)
                .responseFormat(ResponseFormat.URL)
                .watermark(true)
                .build();

        ImageGenerationResponse imagesResponse = service.generateImages(generateRequest);
        imagesResponse.getData().forEach(item -> System.out.println(item));

        service.shutdownExecutor();
    }
}
```



</Tab>
<Tab zoneid="bneJ2eB5DZ" title="Go">
<TabTitle>Go</TabTitle>

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
    outputFormat := model.OutputFormatJpeg

    generateReq := &model.CreateImageGenerationRequest{
        Model:              "dola-seedream-5-0-pro-260628",
        Prompt:             model.NewOptString("Perform precise layer separation on the image. The text regions to separate are at <bbox>180 64 812 198</bbox>, <bbox>757 210 939 280</bbox>, <bbox>63 212 320 282</bbox>, <bbox>178 714 826 810</bbox>, <bbox>814 819 949 894</bbox>, and <bbox>326 824 669 930</bbox>; the parrot is at <bbox>347 305 642 997</bbox>."),
        Image: model.NewOptCreateImageGenerationRequestImage(model.NewStringArrayCreateImageGenerationRequestImage([]string{"https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_layer_input.png"})),
        LayerDecomposition: model.NewOptBool(true),
        Size:               model.NewOptString("2K"),
        OutputFormat: model.NewOptOutputFormat(outputFormat),
        ResponseFormat: model.NewOptResponseFormat(model.ResponseFormatURL),
        Watermark:          model.NewOptBool(true),
    }

    imagesResponse, err := client.GenerateImages(ctx, generateReq)
    if err != nil {
        fmt.Printf("generate images error: %v\n", err)
        return
    }

    for _, item := range imagesResponse.Data {
        fmt.Printf("%+v\n", item)
    }
}
```



</Tab>
<Tab zoneid="smHf7M0xVT" title="OpenAI">
<TabTitle>OpenAI</TabTitle>

```Python
import os
from openai import OpenAI

client = OpenAI(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv("ARK_API_KEY"),
)

imagesResponse = client.images.generate(
    model="dola-seedream-5-0-pro-260628",
    prompt="Perform precise layer separation on the image. The text regions to separate are at <bbox>180 64 812 198</bbox>, <bbox>757 210 939 280</bbox>, <bbox>63 212 320 282</bbox>, <bbox>178 714 826 810</bbox>, <bbox>814 819 949 894</bbox>, and <bbox>326 824 669 930</bbox>; the parrot is at <bbox>347 305 642 997</bbox>.",
    size="2K",
    output_format="jpeg",
    response_format="url",
    extra_body={
        "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream_50_pro_layer_input.png",
        "layer_decomposition": True,
        "watermark": True,
    },
)

for item in imagesResponse.data:
    print(item)
```



</Tab>
</Tabs>


<span id="about-the-returned-response"></span>
### About the returned response

In layer decomposition mode, the `data` array contains the base image and all generated layers. Download each output through its `url`, use `z_index` to distinguish the base image from the layers and determine their stacking order, and use `bounding_box` and `z_index` to restore, edit, or recompose the layers.


<span aceTableMode="list" aceTableWidth="2,1.5,2.5"></span>
|Field |Returned for |Description |
|---|---|---|
|`url` |Base image and layers |Download URL of the base image or layer. The URL is retained for 24 hours. |
|`z_index` |Base image and layers |Stacking order. The base image has a fixed value of `0`; layers start at `1` and increment in stacking order. |
|`bounding_box.absolute` |Layers |Absolute pixel position of the layer in the output base image coordinate system. Use it to restore the layer to its original position in the base image. |
|`bounding_box.normalized` |Layers |Normalized position of the layer in the output base image coordinate system. Use it to restore the layer to a custom canvas of any size. |
|`name`, `description` |Layers |Name and semantic description of the layer. |


**Response example:** 

```Plain Text
{
  "model": "dola-seedream-5-0-pro-260628",
  "created": 1784696685,
  "data": [
    {
      "url": "https://...",
      "size": "2048x2048",
      "output_format": "jpeg",
      "z_index": 0
    },
    {
      "url": "https://...",
      "size": "1273x265",
      "output_format": "png",
      "z_index": 1,
      "bounding_box": {
        "absolute": [383, 120, 1655, 384],
        "normalized": [187, 59, 808, 188]
      },
      "name": "Seedream title text",
      "description": "Large yellow Seedream title text in a serif font"
    },
    {
      "url": "https://...",
      "size": "492x98",
      "output_format": "png",
      "z_index": 2,
      "bounding_box": {
        "absolute": [140, 451, 631, 548],
        "normalized": [68, 220, 308, 268]
      },
      "name": "Upper-left slogan",
      "description": "Two lines of white text: EXPLORE THE WILD, INSPIRE THE SOUL."
    }
  ],
  "usage": {
    "input_images": 1,
    "generated_images": 8,
    "output_tokens": 23107,
    "total_tokens": 23107
  }
}
```


<span id="use-decomposed-layers"></span>
## Use decomposed layers

After obtaining the base image and layers, you can restore the layers to their original positions or adjust their positions, sizes, and stacking order. You can also edit an individual layer to change an element's color, style, or details.

<span id="restore-and-recompose-layers"></span>
### Restore and recompose layers

Process the response as follows:


1. Use the object whose `z_index` is `0` as the canvas background.

2. Extract layer objects whose `z_index` is greater than `0` and sort them in ascending order of `z_index`.

3. Download each layer PNG and place it on the output base image or target canvas according to `bounding_box`.

4. Adjust layer coordinates, dimensions, or stacking order as needed, and render the recomposed image.


**Restore a layer to the output base image**

Use `bounding_box.absolute` to restore a layer to its original position in the output base image. This field contains the layer's absolute pixel coordinates in the output base image coordinate system, in the format `[left, top, right, bottom]`:


* `left` and `top`: Position of the layer's upper\-left corner relative to the upper\-left corner of the output base image.

* `right` and `bottom`: Position of the layer's lower\-right corner relative to the upper\-left corner of the output base image.


Calculate the layer's position and dimensions in the base image as follows. Here, `x` and `y` specify the position of the layer's upper\-left corner in the base image, while `w` and `h` specify the displayed width and height of the layer:

```text
x = left
y = top
w = right - left
h = bottom - top
```


Scale the layer to `w × h`, and then place it at `(x, y)` on the base image. When combining multiple layers, stack them in ascending order of `z_index`.

**Restore a layer to a custom canvas**

Use `bounding_box.normalized` to restore a layer to a frontend or design\-tool canvas of a different size. This field contains the layer's normalized coordinates in the output base image coordinate system and indicates the relative positions of the layer boundaries along the width and height of the output base image, in the format `[left, top, right, bottom]`:


* `left` and `right`: Relative positions of the left and right layer boundaries along the width of the base image.

* `top` and `bottom`: Relative positions of the top and bottom layer boundaries along the height of the base image.


For a target canvas with width `W` and height `H`, calculate the layer's position and dimensions as follows. Here, `x` and `y` specify the position of the layer's upper\-left corner in the target canvas, while `w` and `h` specify the displayed width and height of the layer:

```text
x = left / 1000 × W
y = top / 1000 × H
w = (right - left) / 1000 × W
h = (bottom - top) / 1000 × H
```


Scale the layer to `w × h`, and then place it at `(x, y)` on the target canvas. Because normalized coordinates are integers, conversion may introduce rounding errors.

For the complete definition of `bounding_box` and coordinate examples, see [Image generation API](image-generation-api.md).

<span id="edit-a-transparent-layer"></span>
### Edit a transparent layer

Layers returned by layer decomposition are PNG images with alpha channels. You can use an output layer as the input image in another Seedream request to edit it independently, for example, to recolor it, change its style, or add details.

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">To preserve the transparent background in the edited layer, set <code>background</code> to <code>transparent</code> and <code>output_format</code> to <code>png</code>. For restrictions, see <a href="https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/seedream-5-0-pro#transparent-background">Transparent background</a>.</div>



<span aceTableMode="list" aceTableWidth="2,1.5,1.5"></span>
|Prompt |Input layer |Output |
|---|---|---|
|Change the parrot in the image into a peacock. |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_4.png) </span> |<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_4_re.png) </span> |



<Tabs>
<Tab zoneid="cC4jPGHmdq" title="Curl">
<TabTitle>Curl</TabTitle>

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seedream-5-0-pro-260628",
    "prompt": "Change the parrot in the image into a peacock.",
    "image": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_4.png",
    "size": "2K",
    "background": "transparent",
    "watermark": true
}'
```



</Tab>
<Tab zoneid="vm69jmCPXB" title="Python">
<TabTitle>Python</TabTitle>

```Python
import os

# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv("ARK_API_KEY"),
)

imagesResponse = client.images.generate(
    # Replace with Model ID
    model="dola-seedream-5-0-pro-260628",
    prompt="Change the parrot in the image into a peacock.",
    image="https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_4.png",
    size="2K",
    background="transparent",
    response_format="url",
    watermark=True,
)

print(imagesResponse.data[0].url)
```



</Tab>
<Tab zoneid="hNblx5FfWn" title="Go">
<TabTitle>Go</TabTitle>

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

    generateReq := &model.CreateImageGenerationRequest{
        Model:          "dola-seedream-5-0-pro-260628",
        Prompt:         model.NewOptString("Change the parrot in the image into a peacock."),
        Image: model.NewOptCreateImageGenerationRequestImage(model.NewStringArrayCreateImageGenerationRequestImage([]string{"https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_4.png"})),
        Size:           model.NewOptString("2K"),

        ResponseFormat: model.NewOptResponseFormat(model.ResponseFormatURL),
        Background:     model.NewOptBackground(model.BackgroundTransparent),
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



</Tab>
<Tab zoneid="ApQhojkCfa" title="Java">
<TabTitle>Java</TabTitle>

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.images.*;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;

import java.util.concurrent.TimeUnit;

public class ImageGenerationsExample {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
        Dispatcher dispatcher = new Dispatcher();
        ArkService service = ArkService.builder()
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3") // The base URL for model invocation
                .dispatcher(dispatcher)
                .connectionPool(connectionPool)
                .apiKey(apiKey)
                .build();

        // CreateImageGenerationRequestImage requires ark-runtime 0.6.0 or later.
        CreateImageGenerationRequest generateRequest = CreateImageGenerationRequest.builder()
                .model("dola-seedream-5-0-pro-260628") // Replace with Model ID
                .prompt("Change the parrot in the image into a peacock.")
                .image(CreateImageGenerationRequestImage.ofString(
                        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/Seedream50_layer_4.png"))
                .size("2K")
                .background(Background.TRANSPARENT)
                .responseFormat(ResponseFormat.URL)
                .watermark(true)
                .build();

        ImageGenerationResponse imagesResponse = service.generateImages(generateRequest);
        System.out.println(imagesResponse.getData().get(0).getUrl());

        service.shutdownExecutor();
    }
}
```



</Tab>
</Tabs>


<span id="pro-prompt-optimize"></span>
# Configure faster image generation

To increase image generation speed, set `optimize_prompt_options.mode` to select a faster prompt optimization mode. **For latency\-sensitive applications, use Seedream 5.0 pro in ** **`fast`** ** mode or use Seedream 5.0 flash for faster image generation.** 


* `standard` (default): Standard mode. This mode produces higher\-quality content but takes longer.

* `fast`: Fast mode. This mode takes less time to generate content. **Only Seedream 5.0 pro supports this mode.** 


```JSON
{
    "optimize_prompt_options": {
        "mode": "fast"
    }
}
```


<span id="pro-output-spec"></span>
# Customize image output specifications

You can configure the following parameters to control image output specifications:


* **size**: Specifies the size of the output image.

* **response_format**: Specifies the return format of the generated image.

* **output_format**: Specifies the file format of the generated image.

* **background**: Specifies whether to generate an image with an alpha channel.

* **watermark**: Specifies whether to add a watermark to the output image.


<span id="image-output-size"></span>
## Image output size

The supported values and default value of `size` differ between image generation and layer decomposition. Select a value based on your scenario.

<span id="image-generation"></span>
### Image generation

The following size specification methods are supported. Do not use the two methods at the same time.

**Method 1: Specify a resolution tier (recommended)** 

Use natural language in the prompt to describe the image aspect ratio, image shape, or image purpose. The model then determines the final image size.


* Default value: `2K`

* Valid values: `1K`, `1.5K`, `2K`

   <div data-tips="true" data-tips-type="tip" data-tips-is-title="true" data-wrapper-indent="1">Note   </div>
   

   <div data-tips="true" data-tips-type="tip" data-wrapper-indent="1"><code>1.5K</code> has the same price as <code>1K</code> and provides better image generation quality.   </div>
   

When you use Method 1 and describe a specific aspect ratio in the prompt, the actual width and height values mapped by the model are shown in the following table. The model supports more aspect ratios than the standard values listed here. The following table uses common aspect ratios only as examples.


<span aceTableMode="list" aceTableWidth="1,1,1.5"></span>
|Resolution |Aspect ratio |Width and height |
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


**Method 2: Specify width and height in pixels (** **`widthxheight`** **)** 


* Total pixel range: [`1280x720` (921600), `2048x2048x1.1025` (4624220)]

* Aspect ratio range: [1/16, 16]

   <div data-tips="true" data-tips-type="tip" data-tips-is-title="true" data-wrapper-indent="1">Width and height examples   </div>
   

   <div data-tips="true" data-tips-type="tip" data-wrapper-indent="1">When you use Method 2, both the total pixel range and the aspect ratio range must be met. Total pixels refer to the product of the width and height of a single image, not a separate limit on width or height.   </div>
   

   * <div data-tips="true" data-tips-type="tip" data-wrapper-indent="1"><strong>Valid example</strong>: <code>2048x1024</code>   </div>
   

   <div data-tips="true" data-tips-type="tip" data-wrapper-indent="1">The total pixel value is 2048x1024=2097152, which falls within [921600, 4624220]. The aspect ratio is 2048/1024=2, which falls within [1/16, 16]. Therefore, this value is valid.   </div>
   

   * <div data-tips="true" data-tips-type="tip" data-wrapper-indent="1"><strong>Invalid example</strong>: <code>512x512</code>   </div>
   

   <div data-tips="true" data-tips-type="tip" data-wrapper-indent="1">The total pixel value is 512x512=262144, which is below the minimum value of 921600. Therefore, this value is invalid.   </div>
   



<span aceTableMode="list" aceTableWidth="1,1"></span>
|Method 1 |Method 2 |
|---|---|
|```JSON```<br>```{```<br>```    "prompt": "Generate a set of four cohesive illustrations with a 3:2 aspect ratio, centered on the seasonal transformation of the same corner of a courtyard, using a unified style to present the distinct colors, elements, and atmosphere of each season.",```<br>```    "size": "2K"```<br>```}```<br> |```JSON```<br>```{```<br>```    "prompt": "Generate a series of 4 coherent illustrations focusing on the same corner of a courtyard across the four seasons, presented in a unified style that captures the unique colors, elements, and atmosphere of each season.",```<br>```    "size": "2048x2048"```<br>```}```<br> |


<span id="layer-decomposition"></span>
### Layer decomposition

Only resolution tiers are supported. The output resolution follows these rules:


* **Base image**: The output base image uses the resolution specified by `size` and keeps the aspect ratio of the original image.

* **Layers**: Each output layer uses a resolution close to the value specified by `size` and keeps the aspect ratio of its corresponding region in the original image.


Default and valid values:


* Default: `auto`

* Valid values: `1K`, `1.5K`, `2K`, and `auto`. With `auto`, the model determines the output dimensions based on the input dimensions and aspect ratio.


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="tip"><code>1.5K</code> has the same price as <code>1K</code> and provides better image generation quality.</div>


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">auto adaptation rules</div>


<div data-tips="true" data-tips-type="tip">In <code>auto</code> mode, the model determines the dimensions of the output base image and each layer based on the input image dimensions:</div>



* <div data-tips="true" data-tips-type="tip">If the input image is between [<code>1280x720</code> (921,600), <code>2048x2048x1.1025</code> (4,624,220)] pixels, the base image and layers are output at the original input dimensions. Each output keeps its aspect ratio in the original image.</div>


* <div data-tips="true" data-tips-type="tip">If the input image is smaller than 1K, the base image and layers are output at 1K. Each output keeps its aspect ratio in the original image.</div>


* <div data-tips="true" data-tips-type="tip">If the input image is larger than 2K, the base image and layers are output at 2K. Each output keeps its aspect ratio in the original image.</div>



For common width and height mappings, see the [resolution mapping table for image generation](seedream-5-0-pro.md#pro-output-spec).

<span id="image-output-method"></span>
## Image output method

Set `response_format` to specify how generated image data is returned:


* `url`: Returns an image download URL.

* `b64_json`: Returns image data as a Base64\-encoded string in JSON format.


```JSON
{
    "response_format": "url"
}
```


<span id="image-file-format"></span>
## Image file format

Set `output_format` to specify the generated image file format:


* `png`

* `jpeg`


```JSON
{
    "output_format": "png"
}
```


<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">In layer decomposition, <code>output_format</code> controls only the base image format. Layers are always returned in <code>png</code> format.</div>


<span id="transparent-background"></span>
## Transparent background

Set `background` to control whether the generated image has an alpha channel:


* `transparent`: Generates an image with a transparent background.

* `opaque` (default): Generates an image with a standard, opaque background.


```json
{
  "background": "transparent"
}
```


<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Usage restrictions</div>



* <div data-tips="true" data-tips-type="warning">This parameter is supported only for image\-to\-image generation with exactly one input image that has an alpha channel.</div>


* <div data-tips="true" data-tips-type="warning">In transparent background mode, the output format defaults to <code>png</code>. If <code>output_format</code> is set to <code>jpeg</code>, the request returns an error.</div>


* <div data-tips="true" data-tips-type="warning">If the input image uses a format that does not support an alpha channel, such as <code>jpeg</code>, the request returns an error.</div>



<span id="add-a-watermark-to-images"></span>
## Add a watermark to images

Use `watermark` to control whether to add a watermark to the generated image.


* `false`: Do not add a watermark.

* `true`: Add an "AI\-generated" watermark in the lower\-right corner of the image.


```JSON
{
    "watermark": true
}
```


<span id="pro-limits"></span>
# Limits

**SDK version upgrade**

To ensure that the model works properly, upgrade to the latest SDK version. For more information, see [Install and upgrade SDK](install-and-upgrade-sdk.md).

**Image input limits**

The following image input methods apply to both image generation and layer decomposition:


* Image URL: Make sure that the image URL is accessible.

   Example: `https://arkdocs-en.tos-ap-southeast-1.volces.com/images/image-generation/seedream4_5_imageToimage.png`

* Base64 encoding: Use `data:image/<image_format>;base64,<base64_image>`. `<image_format>` must be lowercase, for example, `data:image/png;base64,<base64_image>`.

   You can use a third\-party tool such as https://base64.guru/converter/encode/image to encode an image.


Input image constraints differ by scenario:


<span aceTableMode="list" aceTableWidth="2,3,3"></span>
|Constraint |Image generation |Layer decomposition |
|---|---|---|
|Image format |jpeg, png, webp, bmp, tiff, gif, heic, heif |png, jpeg |
|Total pixels (width × height) |[196, `6000×6000` (36,000,000)] |[`512×512` (262,144), `6000×6000` (36,000,000)] |
|Width and height (px) |Greater than 14 |Not applicable |
|Aspect ratio (width / height) |[1/16, 16] |[1/16, 16] |
|File size |Up to 30 MB |Up to 30 MB |
|Number of input images |Up to 10 reference images |Exactly one image |


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="tip">The total pixel limit applies to the product of the width and height of a single image, not to either dimension separately.</div>


**Retention period**

Image URLs are retained for only 24 hours. They are automatically cleared after they expire. Save generated images in time.

**Rate limit**


* IPM rate limit: The maximum number of images that can be generated per minute for the same model version under an account. If this limit is exceeded, image generation returns an error.

   * For layer decomposition, each request initially deducts 17 IPM, reserving quota for the maximum output of one base image and 16 layers. After generation is complete, quota deducted beyond the actual number of generated images is returned.

* Limits vary by model. For more information, see [Image generation capabilities](model-list.md#9df4d9fd).
