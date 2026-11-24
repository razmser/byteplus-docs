# Multimodal embedding

Skylark\-embedding\-vision is a multimodal embedding model developed by BytePlus. It can convert mixed input content such as text, images, and videos into unified vector representations, thereby helping you more efficiently process cross\-modal data and achieve accurate text\-to\-image search, image\-to\-image search, and text\-image hybrid search.

The current model supports the following vector output types:

* Dense Vector (Dense Embedding): Supported by default in all versions.

* Sparse Vector (Sparse Embedding): Requires configuration to enable, and only supports text input.

# Prerequisites

* [Obtain an API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D) for subsequent authentication when calling the model inference services.

   * If you wish to use Access Key authentication, see [Base URL and authentication](base-url-and-authentication.md).

* Activate the required model service on the [Model activation Page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&tab=LLM).

* Obtain the required **Model ID** from the [Model list](model-list.md).

> If your scenarios require granular control and monitoring of model service calls (e.g., multi\-application model invocation), you can choose to call model services via **Endpoint ID** (inference access point). For details, see [Create Standard inference endpoint](online-inference-standard.md).

# Quick start

Multimodal embedding models accept and process image inputs, converting them into vectors. These images can be input via an accessible URL or after being converted to Base64 encoding.

**Mixed input of video, image, and text**

> * skylark\-embedding\-vision\-250615 and later versions support mixed input with any number of videos, images, and text items.

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/embeddings/multimodal \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "skylark-embedding-vision-250615",
    "encoding_format": "float",
    "input": [
        {
            "type": "video_url",
            "video_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/advanced-usage/ark_vlm_video_input.mp4"
            }
        },
        {
            "type": "image_url",
            "image_url": {
                "url": "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/domain-models/tower.png"
            }
        },
        {
            "type": "text",
            "text": "What is shown in the videos and images?"
        }
    ]
}'
```

**Enable sparse vector**

> * Supports text\-only input.

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/embeddings/multimodal \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "skylark-embedding-vision-250615",
    "instructions": "Target_modality: text and video.\nInstruction:Compress the text/video into one word.\nQuery:",
    "encoding_format": "float",
    "input": [
        {
            "type": "text",
            "text": "The sky is blue"
        }
    ],
    "dimensions": 1024,
    "sparse_embedding": {
        "type": "enabled"
    }
}'
```

Sample model response:

```Plain Text
{
    "created": 1752133360,
    "data": {
        "embedding": [
            -0.046875,-0.048828125,0.02001953125,0.064453125,......,0.003143310546875
        ],
        "sparse_embedding": [
            {
                "index": 1,
                "value": 0.0887451171875
            },
            {
                "index": 13,
                "value": 0.0887451171875
            },
            {
                "index": 149,
                "value": 0.0887451171875
            }
        ],
        "object": "embedding"
    },
    "id": "021752133359863906427fb4b36437c414d645f52206dfc398f85",
    "model": "skylark-embedding-vision-250615",
    "object": "list",
    "usage": {
        "prompt_tokens": 25,
        "prompt_tokens_details": {
            "image_tokens": 0,
            "text_tokens": 25
        },
        "total_tokens": 25
    }
}
```

> **Note**

> `sparse_embedding` is returned when sparse vector is enabled.

# Model list

For models supporting multimodal embedding, see [Embedding](model-list.md#embedding).

**Note**: `skylark-embedding-vision-251215` and the subsequent versions support the `instructions` field.

> **Note**

> The configuration of the `instructions` field directly determines model inference performance. **Do not use the system default value directly**. Customize the instructions to improve the precision of vector representations.

# Tutorial

> **Tip**

> For data security reasons, it is recommended to use the multimodal embedding model launched by ModelArk. After processing, images are deleted from the ModelArk servers. ModelArk does not retain videos, images, text, or any other user\-submitted data for model training.

## Enable sparse embedding

Sparse embedding only supports text input.

Use the `sparse_embedding` field to enable it.

```Plaintext
{
    "model": "skylark-embedding-vision-251215",
    "input": [
        {
            "type":"text",
            "text":"The sky is blue"
        }
    ],
    "sparse_embedding": {
        "type":"enabled"     # Enables sparse vectors; the default value is "disabled".
    },
    "encoding_format":"float"
}
```

## Set the instructions field (recommended)

The `instructions` field is key to the model's effectiveness. To significantly improve the accuracy of vector representations, customize this instruction according to your specific business scenario. **Do not use the system default value directly.**

By properly setting `instructions`, you can guide the model to focus more accurately on the key information in the input content, thereby adapting to specific task requirements. This is especially effective in cross\-modal retrieval and specific domain data processing scenarios.

> Note: Only `skylark-embedding-vision-251215` and the later model versions support the `instructions` field.

### **Configuration rules**

Before constructing instructions, it is essential to clarify two core roles, as their configuration rules differ significantly across tasks:

* **Query (query side)** : The entity that initiates the retrieval or query, such as a user's input question, search keywords, or images/videos to be matched.

* **Corpus (corpus side)** : The target being queried, such as an individual data item in a document library, image library, or video library.

Depending on task types, the `instructions` field is divided into two main scenarios: **recall/sorting** and **clustering/classification/semantic textual similarity (STS)** .

The specific configuration templates are shown in the table below:

|Task Type |Distinguish Between Query and Corpus |Key Configuration Template |
|---|---|---|
|Recall or ranking |Supported |Query: `Target_modality: {}.\nInstruction:{}\nQuery:`<br><br>Corpus: `Instruction:Compress the {} into one word.\nQuery:` |
|Clustering, classification, or STS |Not supported |All data: `Target_modality: {}.`<br><br>`Instruction:{}`<br><br>`Query:` |

**General requirements**: The `{}` section in all templates needs to be filled in; all the other content **must not be modified**.

### Configuration rules for recall or sorting tasks

These tasks are used to calculate the similarity between Query and Corpus, enabling recall or sorting of target content. Query and Corpus must each be configured with `Instruction` separately.

#### **Query\-side configuration**

```Plaintext
Target_modality: {}.\nInstruction:{}\nQuery:
```

**Fields**:

1. **`Target_modality`**:

   * This field is irrelevant to the modality of the Query itself, but entirely determined by the modality type of the Corpus library to be recalled.

   * Multimodal mixed scenarios:

      * If the Corpus library contains samples of different modalities (e.g., text, image, video, or text and video), use `/` to separate all modalities;

      * If each sample in the Corpus library contains multiple modalities (for example, each sample has text + video), use `and` to connect the modalities.

   * Common examples:

      |Corpus Library Modality |Target_modality Value |
      |---|---|
      |All samples are text only |text |
      |All samples are a combination of images and text |text and image |
      |All samples are video |video |
      |All samples are a combination of text and videos |text and video |
      |Samples include three modalities: text, images, and videos |text/image/video |
      |Samples include three modalities: text, videos, and text and video |text/video/text and video |

   > **Note**

   > Incorrect entry of `Target_modality` will directly lead to reduced retrieval accuracy. Please strictly match the modality of the Corpus library or dataset.

2. **`Instruction`**

   * **Do not use the default value** **`Compress the text into one word`**; instead, always customize based on the specific scenario.

   * Examples:

      * Text retrieval: `Generate a representation for this sentence to retrieve related articles`

      * Cross\-modal question answering: `Based on this question, find the corresponding text or image that can answer this question`

#### Corpus\-side configuration

```Plaintext
Instruction:Compress the {} into one word.\nQuery:
```

* Only the modality of the **current individual corpus item** needs to be matched; the modality distribution of the entire corpus library does not need to be considered.

* Common examples: `text`, `image`, `video`, `text and image`, `text and video`, `image and video`

### Configuration rules for clustering, classification, or STS tasks

These tasks do not distinguish between Query and Corpus; all data use the same `Instruction` configuration.

```Plaintext
Target_modality: {}.\nInstruction:{}\nQuery:
```

1. **`Target_modality`**:

   * This field represents the **unified modality type of the entire dataset**; all data entries must use the same value.

* Format is consistent with `Target_modality` in the recall tasks: use the corresponding value for single\-modality, and connect modalities with "and" for multi\-modality.

2. **`Instruction`**:

   * **Do not use the default value**; always customize to fit your scenario.

   * Example:

      * STS semantic similarity task: `Retrieve semantically similar text`

### Advanced usage

To meet requirements not covered by the above instructions, refer to the [example](https://github.com/embeddings-benchmark/mteb/blob/main/mteb/models/model_implementations/seed_1_6_embedding_models.py) instructions provided by MTEB (Massive Text Embedding Benchmark).

### **Example configurations for typical scenarios**

#### Text\-only tasks

##### Scenario 1: Symmetric retrieval (STS semantic similarity matching)

* **Scenario**: Retrieve semantically similar sentences, such as comparing "A panda is sliding down a slide" and "The panda slides down the slide".

* **Configuration rules**: The `Instruction` field for both samples must be identical.

* **Instruction example**:

   ```Plaintext
   Target_modality: text.\nInstruction:Retrieve semantically similar text\nQuery:
   ```

* **Input field**: Enter the sample text (such as the two sentences above).

##### Scenario 2: Asymmetric retrieval (question answering, abstract\-to\-full\-text search)

* **Scenario description**: Use a short query (question / abstract) to retrieve a long corpus (answer / full text).

* **Configuration example**:

   |Role |Instruction field configuration |
   |---|---|
   |Query |`Target_modality: text.\nInstruction:Generate an embedding for this sentence to retrieve related articles\nQuery:` |
   |Corpus |`Instruction:Compress the text into one word.\nQuery:` |

#### Multimodal task

##### Scenario 1: Symmetric retrieval (cross\-search between text / image / video)

* **General prerequisite**: When the text is a **complete description** of an image or video (for example, "Under a blue sky, a dog is running on a lawn with several tents"), the default instruction configuration can be used.

* **Configuration example**:

   |Retrieval Type |Query\-side Instruction Configuration |Corpus\-side Instruction Configuration |
   |---|---|---|
   |Image search by text |`Target_modality: image.\nInstruction:Compress the text into one word.\nQuery:` |`Instruction:Compress the image into one word.\nQuery:` |
   |Text\-to\-video retrieval |`Target_modality: video.\nInstruction:Compress the text into one word.\nQuery:` |`Instruction:Compress the video into one word.\nQuery:` |
   |Image\-to\-text retrieval |`Target_modality: text.\nInstruction:Compress the image into one word.\nQuery:` |`Instruction:Compress the text into one word.\nQuery:` |
   |Video\-to\-text retrieval |`Target_modality: text.\nInstruction:Compress the video into one word.\nQuery:` |`Instruction:Compress the text into one word.\nQuery:` |
   |Image\-to\-image retrieval (overall content matching) |`Target_modality: image.\nInstruction:Compress the image into one word.\nQuery:` |`Instruction:Compress the image into one word.\nQuery:` |

* **Note**: If the query is a **short text for image search** (such as "blue sea view"), it is recommended to replace the query\-side instruction with: `Find me an everyday image that matches the given caption`.

* **Caution**: Cropping a part of an image to retrieve the original image does not belong to symmetric retrieval and must be configured as asymmetric retrieval.

##### Scenario 2: Asymmetric retrieval

* **General rule**: Only customize instructions on the query side; use the default template on the corpus side. Instructions must specify the matching rules clearly.

* **Typical scenario example**

   |Business Scenario |Query\-side Instruction Configuration |Corpus\-side instruction configuration |
   |---|---|---|
   |Cross\-modal question answering (Query: text question; Corpus: text / image) |`Target_modality: text/image.`<br><br>`Instruction: Find the corresponding text or image that can answer this question`<br><br>`Query:` |Text corpus: `Instruction:Compress the text into one word.`<br><br>`Query:`<br><br>Image corpus: `Instruction:Compress the image into one word.`<br><br>`Query:` |
   |Original image retrieval (ignore PS processing) |`Target_modality: image.`<br><br>`Instruction: Find the image that is exactly the same as this one. It may have undergone PS processing, including scaling, cropping, and watermarking. Please ignore traces of PS processing`<br><br>`Query:` |`Instruction:Compress the image into one word.\nQuery:` |
   |E\-commerce clothing retrieval (ignore background / subject) |`Target_modality: image.`<br><br>`Instruction: Ignore the background and main subject, and find images of the same product appearing in this image`<br><br>`Query:` |`Instruction:Compress the image into one word.\nQuery:` |
   |E\-commerce product retrieval (text description to image) |`Target_modality: image.`<br><br>`Instruction: Based on the description of the product in the following text, find the corresponding product image that meets the criteria.`<br><br>`Query:` |`Instruction:Compress the image into one word.\nQuery:` |
   |Dish image search (by text description) |`Target_modality: image.`<br><br>`Instruction: Based on the relevant dishes mentioned in this text, find images of the relevant dishes.`<br><br>`Query:` |`Instruction:Compress the image into one word.\nQuery:` |

## Set vector `dimensions`

Vectorization is the process of representing unstructured data, such as text or images, as vectors so that computers can understand their meanings.

The dimensionality of a vector refers to the number of dimensions that the vector contains for annotating word meanings or image features.

In multimodal vectorization scenarios, each dimension corresponds to a feature of the text or to visual features such as the pixels, colors, and so on of the image.

> **Note**

* > Currently, only setting the dimension for dense vectors is supported. Sparse vectors (fixed dimension) are not supported.

```Plaintext
{
    "model": "skylark-embedding-vision-250615",
    "input": [
        {
            "type":"text",
            "text":"The sky is blue"
        }
    ],
    "dimensions": 1024,    # Set vector dimensions
    "encoding_format":"float"
}
```

## Notes on image input

### Image input methods

You can input the URL or Base64\-encoded content of an image. When using an image URL, make sure that it is accessible.

### Image pixel requirements

The models support images of flexible sizes. However, an input image must meet the following requirements:

* Its width and height are greater than 14 pixels.

* The total pixel (width × height) count must be less than 36 million.

### Image count limit

* `skylark-embedding-vision-250615` and subsequent model versions support unlimited input of videos, text, and images.

* Models prior to `skylark-embedding-vision-250615` only support a maximum of 1 text and 1 image input.

### Token usage

During processing, the model tokenizes an image before inference. Token usage is calculated based on the image's width and height in pixels. The formula is as follows:

```Plaintext
min(image width * image height / 784, single image token limit)
```

* Suppose your image is 1,280 pixels wide and 720 pixels high and the model has a limit of 1,312 tokens for input images.

   Since the expected token usage, calculated as `1280 × 720/784 = 1176`, is less than the token limit, the model consumes 1,176 tokens to understand the image.

* Suppose your image is 1,920 pixels wide and 1,080 pixels high and the model has a limit of 1,312 tokens for input images.

   Since the expected token usage, calculated as `1920 × 1080/784 = 2645`, is greater than the token limit, the model consumes 1,312 tokens to understand the image. In this case, compression is required, resulting in the loss of some image details. This may prevent the models from accurately recognizing the text content of images with small fonts.

### Supported image format

Supported image formats are displayed in the following table. Make sure that the image file extension specified in the image URL or the format declared in the Base64\-encoded content is consistent with the actual image information.

|**Image Format** |**File Extension** |**Content Type**<br><br><br>> * [Upload image to object storage settings](https://docs.byteplus.com/en/docs/tos/docs-managing-file-metadata).<br><br>> * When passing in image Base64 encoding: [Pass Base64 string](image-understanding.md#477e51ce). |
|---|---|---|
|JPEG |.jpg or .jpeg |`image/jpeg` |
|PNG |.apng or .png |`image/png` |
|GIF |.gif |`image/gif` |
|WEBP |.webp |`image/webp` |
|BMP |.bmp |`image/bmp` |
|TIFF |.tiff or .tif |`image/tiff` |
|ICO |.ico |`image/x-icon` |
|DIB |.dib |`image/bmp` |
|ICNS |.icns |`image/icns` |
|SGI |.sgi |`image/sgi` |
|JPEG2000 |.j2c, .j2k, .jp2, .jpc, .jpf, or .jpx |`image/jp2` |

> **Tip**

> For images of the TIFF, SGI, ICNS, or JPEG2000 format, you must set valid file metadata in object storage for metadata alignment. Otherwise, the images cannot be parsed.

## Notes on video input

Extract frames from the video at fixed intervals and send the extracted frames to the model for understanding.

> Only skylark\-embedding\-vision\-250615 and subsequent model versions support video input.

### Supported video format

|**Video Format** |**File Extension** |**Content Type**<br><br><br>> * Set when uploading videos to object storage.<br><br>> * When passing in Base64 encoding: [Pass Base64 string](image-understanding.md#477e51ce).<br><br>> * Video formats must be in lowercase. |
|---|---|---|
|MP4 |.mp4 |`video/mp4` |
|AVI |.avi |`video/avi` |
|MOV |.mov |`video/quicktime` |

### Size limit

A single video file must be within 50MB.

### Audio understanding

Understanding audio information in video files is **NOT** supported.

### Token usage

The token usage range per video is [10k, 80k].

The maximum token amount for a single request video is also limited by the model's maximum context window and maximum input length (when deep inference mode is enabled). If exceeded, adjust the number of incoming videos or video length.

ModelArk compresses frame images (referring to the frame images input to the model) based on the number of frames (video duration \* **fps**) to balance video analysis accuracy with token usage. Frame images will be proportionally compressed to [128 tokens, 640 tokens], with a corresponding pixel range of [100000 pixels, 500000 pixels].

* If `fps` is too high or the video length is too long, and the number of frame images to be processed exceeds 640 frames (80×1024 token ÷ 128 token/frame = 640 frames), 640 frames will be uniformly sampled at a time interval of 128 token per frame image `video duration/640`. In this case, it is inconsistent with the configuration in the request. It is recommended to evaluate the output effect and adjust the video duration or **fps** field configuration as needed.

* If `fps` is too low or the video duration is too short, resulting in fewer than 16 frames being processed, (calculated as: 10 × 1024 tokens ÷ 640 tokens per frame = 16 frames), the system will uniformly sample 16 frames across the video duration at equal time intervals, with each frame consuming 640 tokens.

# Relevant techniques

## Similarity calculation

* Skylark embedding models use [cosine similarity](https://en.wikipedia.org/wiki/Cosine_similarity) to calculate the similarity scores of vectors. The process involves the following two steps:

   1. Call the API of the skylark embedding model to generate vectors, and apply L2 normalization to the vectors.

   2. Calculate the dot product of the normalized vectors to obtain the cosine similarity.

## Base64 encoded input

If the video/image you need to input is stored locally, you can convert them into Base64 encoded strings to provide to the model. See the example code in Python below for details.

> **Note**

> When passing in the Base64 encoding format, please follow the following rules:

* > When passing in an image:

   * > The format follows `data:image/<IMAGE_FORMAT>;base64,<BASE64_ENCODING>`.

   * > **IMAGE_FORMAT**: `jpeg`, `png`, `gif`, etc. For detailed supported image formats, see [Supported image formats](image-understanding.md#51efc45f).

   * > **BASE64_ENCODING**: The Base64 encoding of the image.

* > When passing in a video:

   * > The format follows `data:video/<VIDEO_FORMAT>;base64,<BASE64_ENCODING>`.

   * > **VIDEO_FORMAT**: `mp4`, `avi`, etc. For detailed supported video formats, see [Supported video formats](video-understanding.md#1d125314).

   * > **BASE64_ENCODING**: The Base64 encoding of the video.

```Python
# Define a method to Base64 encode the image in the specified path.
def encode_image(image_path):
  with open(image_path, "rb") as image_file:
    return base64.b64encode(image_file.read()).decode('utf-8')

# Specify the image to be input to the LLM.
image_path = "path_to_your_image.jpg"
# Encode the image.
base64_image = encode_image(image_path)
```

After Base64 encoding, the image URL must be in the following format:

```Python
{
        "type": "image_url",
        "image_url": {
            "url":  f"data:image/<IMAGE_FORMAT>;base64,{base64_image}"
        }
    },
```

# Best practices: multimodal similarity matching

## Overview

The following program demonstrates how to retrieve relevant assets from an image library using a text description.

The program reads a list of five fruit image URLs and uses the skylark\-embedding\-vision model to generate vector representations for each image. When the user enters the query text "banana", the program converts the text into a vector and matches it against the image vector library using cosine similarity, then outputs the most similar image along with its similarity score.

This workflow implements semantic text\-to\-image retrieval, enabling users to quickly locate target content within an image library. It is well suited for scenarios such as e\-commerce product search, media asset management, and similar applications.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/8a24e352e2bf4d139e7c88f956ede96d~tplv-goo7wpa0wc-image.image)

Fruit1.jpg

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/f52f28ec7c714f6891eab4854f0f1fad~tplv-goo7wpa0wc-image.image)

Fruit2.jpg

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/0daea3ea0ab24d2096e583212921fbf3~tplv-goo7wpa0wc-image.image)

Fruit3.jpg

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/5534103c2e4740cd926c95808c191c59~tplv-goo7wpa0wc-image.image)

Fruit4.jpg

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/7ba9bbd9ebbb4db59e2b62bd7f0c6e31~tplv-goo7wpa0wc-image.image)

Fruit5.jpg

## Step 1: Import dependencies

Import necessary libraries and packages.

```Python
import os
import numpy as np
from arkruntime import Ark
from sklearn.metrics.pairwise import cosine_similarity
```

## Step 2: Obtain vectors

Define functions for converting a text or image into a vector, which supports two types of input: text and image URL. Call the skylark\-embedding\-vision\-250615 model to obtain a vector of the float format, convert the vector into a NumPy array, and flatten the array to a one\-dimensional vector.

**Note**: Configure an API key to prepare for subsequent data processing and analytics.

```Python
def get_embedding(input_data, input_type="text"):
    """Call the BytePlus API to get the vector representation of a single text or image"""
    client = Ark(
        api_key=os.environ.get("ARK_API_KEY"),
        base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    )
    if input_type == "text":
        input_item = {"type": "text", "text": input_data}
    elif input_type == "image_url":
        input_item = {"type": "image_url", "image_url": {"url": input_data}}
    else:
        raise ValueError("Only 'text' or 'image_url' are supported for input type")

    try:
        resp = client.multimodal_embeddings.create(
            multi_embedding={"type": "disabled"},
            model="skylark-embedding-vision-250615",
            encoding_format="float",
            input=[input_item]
        )
        if hasattr(resp, "data") and hasattr(resp.data, "embedding"):
            embedding = resp.data.embedding
            # Convert the vector into a NumPy array and flatten it to one dimension.
            embedding = np.array(embedding).flatten()
            return embedding
        else:
            raise ValueError("API response format is not as expected, cannot obtain embedding vector")
    except Exception as e:
        print(f"Failed to get vector, input type: {input_type}, error: {str(e)}")
        raise
```

## Step 3: Build a vector database

Batch process the image URL list to generate vectors and build a vector database.

```Python
def generate_image_embeddings(image_urls):
    """Batch generate image embeddings and build a vector library"""
    print(f"[1/3] Start generating embeddings for {len(image_urls)} images...")
    embeddings = []
    for i, url in enumerate(image_urls):
        try:
            embedding = get_embedding(url, "image_url")
            embeddings.append({
                "image_url": url,
                "embedding": embedding
            })
            print(f" [{i+1}/{len(image_urls)}] Success: {url}")
        except Exception as e:
            print(f" [{i+1}/{len(image_urls)}] Failed: {url} - {str(e)}")
            continue
    if not embeddings:
        raise ValueError("All image embeddings generation failed")
    print(f"[2/3] Completed: {len(embeddings)} valid embeddings")
    return embeddings
```

## Step 4: Calculate image similarity

Measure the similarity between text and images based on cosine similarity to achieve content\-based image search. This allows a user to retrieve the image most relevant to the textual description that they enter.

```Python
def search_similar_images(query_embedding, embeddings, top_n=1, query_type="text"):
    """Search for images most similar to the query vector"""
    print(f"\n[3/3] Start searching for images most similar to the {query_type}...")
    results = []
    # Convert the query vector into a NumPy array with a correct number of dimensions.
    query_vec = np.array(query_embedding).reshape(1, -1)
    for item in embeddings:
        # Convert the vector into a two-dimensional NumPy array for similarity calculation.
        item_vec = np.array(item["embedding"]).reshape(1, -1)

        similarity = cosine_similarity(query_vec, item_vec)[0][0]
        results.append({
            "image_url": item["image_url"],
            "similarity": similarity
        })

    results.sort(key=lambda x: x["similarity"], reverse=True)
    print(f" - Similarity calculation completed, total {len(results)} results")
    return results[:top_n]
```

## Step 5: Test the search function

Test the search function. Call the `generate_image_embeddings` function to build a vector database for the images, and search by textual description for the most relevant image.

```Python
if __name__ == "__main__":
    image_urls = [
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/domain-models/Fruit1.jpg",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/domain-models/Fruit2.jpg",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/domain-models/Fruit3.jpg",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/domain-models/Fruit4.jpg",
        "https://arkdocs-en.tos-ap-southeast-1.volces.com/images/domain-models/Fruit5.jpg"
    ]
    query_text = "banana"

    try:
        # Convert images to vectors.
        image_embs = generate_image_embeddings(image_urls)
        # Convert text to a vector.
        text_emb = get_embedding(query_text, "text")
        print(f"Text embedding dimension: {len(text_emb)}")
        # Search for similar images.
        similar_images = search_similar_images(text_emb, image_embs)
        print(f"Most similar image: {similar_images[0]['image_url']}")
        print(f"Similarity score: {similar_images[0]['similarity']:.4f}")

    except Exception as e:
        print(f"Program failed: {e}")
```

## Sample result

* After being called, the model started generating vectors for the five images by sequentially requesting the five URLs. Success was returned for all requests, indicating that the API call functioned properly and the image URLs were valid and accessible.

* The model generated five valid image vectors and one text vector whose dimensionality is 3,072, the same as the default output dimensionality of the model. The number of image vectors matched the number of images, without any data loss.

* The model measured the similarity between the text vector and the five image vectors and returned Fruit2.jpg, the image most relevant to the text in the feature space.

```Plain Text
[1/3] Start generating embeddings for 5 images...
 (1/5) Success: https://arkdocs-en.tos-ap-southeast-1.volces.com/images/domain-models/Fruit1.jpg
 (2/5) Success: https://arkdocs-en.tos-ap-southeast-1.volces.com/images/domain-models/Fruit2.jpg
 (3/5) Success: https://arkdocs-en.tos-ap-southeast-1.volces.com/images/domain-models/Fruit3.jpg
 (4/5) Success: https://arkdocs-en.tos-ap-southeast-1.volces.com/images/domain-models/Fruit4.jpg
 (5/5) Success: https://arkdocs-en.tos-ap-southeast-1.volces.com/images/domain-models/Fruit5.jpg
[2/3] Completed: 5 valid embeddings
Text embedding dimension: 3072

[3/3] Start searching for images most similar to the text...
 - Similarity calculation completed, total 5 results
Most similar image: https://arkdocs-en.tos-ap-southeast-1.volces.com/images/domain-models/Fruit2.jpg
Similarity score: 0.6462
```

# Scenarios

|Domain |Functionality |Business Scenario |Example |
|---|---|---|---|
|Batch embedding |\- |When substantial data needs to be converted offline into vectors, use a multimodal embedding model for batch embedding. |The system batch processes product images on an e\-commerce platform to convert them into vectors for similarity matching. |
|Search by textual description |Image search by text |Search an image library by textual description for relevant images. |After a user enters "blue sea", the system will search and display images of blue sea. |
| |Text search by text |Search a knowledge base by textual description for relevant documents or knowledge base content. |After a user enters "how to reset the password", the system will search the knowledge base for documents containing password reset methods. |
|Search by image feature |Image search by image |Search for similar items by image. Search for the same type of items based on a user\-provided image in different scenarios, such as image\-based product image search. |After a user uploads an image of a chair, the system will return the product images of similar chairs. |
| |Image search by image and text |Search by image feature and text. This allows users to adjust product features, such as product color, to retrieve more accurate search results on an e\-commerce platform. |After a user specifies the product color as "dark blue", the system will filter product images by color. |
| |Image and text search by image and text |Perform multimodal search in knowledge bases. This allows users to search knowledge bases by image and issue description for relevant content. |After a user uploads a mobile screenshot with the description "unable to connect to the network", the system will search solutions for the network connection failure. |

# Appendix: Solution for batch embedding

The following code leverages asynchronous concurrency and a grouping strategy for efficient multimodal embedding in batches, addressing the limitation of the API supporting only one image per request. It uses `asyncio` to create asynchronous tasks, and groups images based on the specified batch size for concurrent API calls. It employs an atomicity mode where **the full group is rolled back upon failure** to ensure the consistency of each group of tasks. It supports retrying failed tasks and saves success and failure results separately. A failure result contains vector details, error information, and the input URL.

```Python
import asyncio
from arkruntime import AsyncArk
from pathlib import Path
import os

class MultimodalEmbedder:
    """Multimodal Embedding Batch Processing Tool (All-or-Nothing Failure Mode)"""
    def __init__(self, api_key: str, model: str = "skylark-embedding-vision-250615",
                 batch_size: int = 10, retries: int = 2):
        self.api_key = api_key
        self.model = model
        self.batch_size = batch_size
        self.retries = retries

    async def process(self, items_list):
        """Process a list of text-image data asynchronously"""
        async with AsyncArk(
            max_retries=self.retries,
            base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
        ) as client:
            # Create and start all tasks.
            tasks = [
                asyncio.create_task(client.multimodal_embeddings.create(multi_embedding={"type": "disabled"}, model=self.model, input=items))
                for items in items_list
            ]
            try:
                # Wait for all tasks to complete. If any one of the tasks fails, an exception is raised.
                return await asyncio.gather(*tasks)
            except Exception:
                # Cancel all unfinished tasks.
                for task in tasks:
                    if not task.done():
                        task.cancel()
                # Raise the original exception.
                raise

    def save_results(self, results, output_dir: str = "embedding_results"):
        """Save embedding results to text files"""
        Path(output_dir).mkdir(exist_ok=True)

        with open(f"{output_dir}/success.txt", "w", encoding="utf-8") as f:
            f.write("===== embedding Results =====\n")
            for idx, result in enumerate(results, 1):
                f.write(f"Result #{idx}\nID: {result.id}\n")
                f.write(f"Creation Time: {result.created}\n")
                f.write(f"Model: {self.model}\n")
                embedding = result.data.embedding
                f.write(f"Vector Length: {len(embedding)}\n")
                f.write(f"Partial Vector Values: {embedding[:20]}...\n\n")

        print(f"Results saved to {output_dir}/success.txt")

if __name__ == "__main__":
    ARK_API_KEY = os.environ.get("ARK_API_KEY")
    if not ARK_API_KEY:
        print("No ARK_API_KEY environment variable found, please enter manually")
        ARK_API_KEY = input("API Key: ").strip()
        if not ARK_API_KEY:
            raise ValueError("API Key cannot be empty")

    embedder = MultimodalEmbedder(
        api_key=ARK_API_KEY,
        batch_size=10,
        retries=2
    )

    sample_data = [
        [
            {"type": "text", "text": "The sky is blue, the sea is deep"},
            {"type": "image_url", "image_url": {"url": "https://example.com/image1.jpg"}}
        ],
        [
            {"type": "text", "text": "Sunny beach"},
            {"type": "image_url", "image_url": {"url": "https://example.com/image2.jpg"}}
        ]
    ]

    try:
        # Start the embedding.
        results = asyncio.run(embedder.process(sample_data))
        # Save the results (only when all tasks succeed).
        embedder.save_results(results)
        print(f"All succeeded: {len(results)}")
    except Exception as e:
        print(f"Processing failed: {str(e)}")
```
