# Overview

# Quick start

To make your first API call, see [Quick start](quick-start.md).

**Python**

```Python
import os
from arkruntime import Ark

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="hello", # Replace with your prompt
    # thinking={"type": "disabled"}, # Manually disable deep thinking
)
print(response)
```

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "model": "dola-seed-2-1-turbo-260628",
      "input": "hello"
  }'
```

In a Windows CMD environment, replace `$ARK_API_KEY` with `%ARK_API_KEY%`; in a PowerShell environment, replace it with `$env:ARK_API_KEY`.

**Go**

```Go
package main

import (
    "context"
    "fmt"
    "os"
    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/responses"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        // Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewStringResponsesInput("hello"),
    })
    if err != nil {
        fmt.Printf("response error: %v\n", err)
        return
    }
    fmt.Println(resp)
}
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
public class DemoSample {
    public static void main(String[] args) {
        // Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        String apiKey = System.getenv("ARK_API_KEY");
        // The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .input(ResponsesInput.ofString("hello")) // Replace with your prompt
                // .thinking(Thinking.builder().type(ThinkingMode.DISABLED).build()) // Manually disable deep thinking
                .build();

        Response resp = arkService.createResponse(request);
        System.out.println(resp);

        arkService.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
import os
from openai import OpenAI

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="hello", # Replace with your prompt
    extra_body={
        # "thinking": {"type": "disabled"}, # Manually disable deep thinking
    },
)

print(response)
```

# Models

Complete model list: [Model list](model-list.md)

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

# Basic usage

Quickly learn model usage rules, call limits, and examples of common use cases.

[Card](deep-thinking.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/deep-thinking.svg)

Deep reasoning

Think before answering for significantly improved response quality

[Card](video-generation-tutorial.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/video-generation.svg)

Video generation

Generates high\-definition professional videos

[Card](image-understanding.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/image-understanding.svg)

Image understanding

Accepts images and replies based on image information

[Card](image-generation-tutorial.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/image-generation.svg)

Image generation

Generates high\-quality images based on text and images

[Card](video-understanding.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/video-understanding.svg)

Video understanding

Accepts videos and replies based on video information

[Card](InfoQuest.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/web-search.svg)

Web search

Obtains real\-time knowledge via internet

[Card](document-understanding.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/document-understanding.svg)

Document understanding

Accepts PDFs and replies based on document information

[Card](function-calling.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/function-calling.svg)

Function calling

Calls custom tools to enhance model capabilities

# Advanced usage

Learn how to expand model capabilities, improve performance, and reduce costs.

[Card](continuation-mode.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/prefill.svg)

Prefill\-based response

Prefill part of the \`assistant\` message content to guide and control the model output

[Card](batch-inference.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/batch.svg)

Batch inference

Significantly improve throughput, reduce costs, and is suitable for tasks that do not require immediate response

[Card](grounding.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/grounding.svg)

Visual grounding

Find targets in the image and return the coordinates

[Card](context-cache.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/context-caching.svg)

Context caching

Cache fixed context to reduce repeated computing costs

[Card](file-api.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/file-api.svg)

File input

Use File API to upload and preprocess videos, images, and PDFs

[Card](mcp-remote-mcp.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/MCP.svg)

Cloud\-deployed MCP

Call various MCP tools in vertical domains to enhance model capabilities

[Card](shadoweye-create-3d-generation-task-api.md)

![Card](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/get-started/3D-generation.svg)

3D generation

Quickly generate high\-precision 3D assets with polygon meshes and PBR materials
