# Multimodal Vectorization API

`POST https://ark.ap-southeast.bytepluses.com/api/v3/embeddings/multimodal` [Try](https://api.byteplus.com/api-explorer/?action=EmbeddingsMultimodal&groupName=Vectorization%20API&serviceCode=ark&version=2024-01-01)

When you need to process videos, images, and text via semantics, such as image search by image, semantic retrieval, etc., you can call the image vectorization service to convert videos, images, and text into vectors to analyze their semantic relationships. This topic describes the request and response parameters of the API operation for vectorizing images and text in detail.


<Tabs>
<Tab zoneid="IgU9kjQTLg" title="Quick start">
<TabTitle>Quick start</TabTitle>

<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_b9c82890e851fc10cc31f48f9065abc6.png) </span> [Experience center](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/chat) <span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_a5fdd3028d35cc512a10bd71b982b6eb.png) </span> [Billing](model-pricing.md) <span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_afbcf38bdec05c05089d5de5c3fd8fc8.png) </span> [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey)

<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_bef4bc3de3535ee19d0c5d6c37b0ffdd.png) </span> [Model activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement) <span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_2abecd05ca2779567c6d32f0ddc7874d.png) </span> [Model list](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1330310)


</Tab>
<Tab zoneid="s7fbXNolt7" title="Authentication">
<TabTitle>Authentication</TabTitle>

This API supports API key authentication. For more information, refer to [Authentication methods](base-url-and-authentication.md).

> If you want to use access key authentication, we recommend that you use the SDK. For more information, refer to [SDK overview](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1302007).


</Tab>
</Tabs>



---



<span id="YngOhBAp"></span>
## Request parameters

> Jump to [Response parameters](multimodal-vectorization-api.md#RjMp8eRI).


<span id="s7U8vHFr"></span>
### Request body


---



**model** `String` `Required`

The ID of the model that you want to call. You can [activate a model service](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement) and [query the model ID](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/model_id).

You can also use an endpoint ID to call a model, querying its rate limits, billing method (prepaid or postpaid), and runtime status, and using its advanced capabilities such as monitoring and security. For more information, refer to [Obtain an endpoint ID](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/create-standard-inference-endpoint).


---



**input** `object[]` `Required`

The list of content to be vectorized. List elements support text information and picture information. **Some models support video information**. For details, see the list:


* `skylark-embedding-vision-250615` and subsequent versions: input supports an unlimited number of `text information`, `picture information`, and `video information` for mixed input. The incoming information is used as`1`Vectorize as a whole.

* `skylark-embedding-vision-250328` model: The input currently supports only 3 combinations: `1 piece of text information`, `1 piece of picture information`, and `1 piece of picture information + 1 piece of text information`. The input information is vectorized as a whole. For example, if your input is one image and one text, only one vector is returned.



Attributes

**Text information** `object`

The text in the input for the model to vectorize.


Attributes


---



input.**type** `string` `Required`

The type of the input content. In this case, it is `text`.


---



input.**text** `string` `Required`

The text input to the model must meet the following requirements:


* Each text must be encoded in UTF\-8, without its length exceeding 100,000 bytes.

* Each text consumes no more than 8,000 tokens, the token limit of the model.

   For better performance, we recommend that you limit the total number of tokens in the text to no more than 4,096 or restrict the number of texts to no more than 4.



---



**Image information** `Object`

The content input to the model for vectorization, the image information part.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">An input image must meet the following requirements:</div>



* <div data-tips="true" data-tips-type="tip">Format: <code>jpeg</code>, <code>png</code>, <code>webp</code>, <code>bmp</code>, <code>tiff</code>, <code>ico</code>, <code>dib</code>, <code>icns</code>, <code>sgi</code>, <code>jpeg2000</code>. For images of the <code>tiff</code>, <code>sgi</code>, <code>icns</code>, or <code>jpeg2000</code> format, you must ensure alignment with metadata, such as correctly setting file metadata in object storage. Otherwise, parsing will fail.</div>


* <div data-tips="true" data-tips-type="tip">Its aspect ratio must be in the range of [1/100, 100].</div>


* <div data-tips="true" data-tips-type="tip">Image size limit: Total pixel size must not exceed 36 million.</div>


   <div data-tips="true" data-tips-type="tip">For example, if the image's width and height are 6000 px and 7000 px, the total pixel size exceeds 36 million and an error message will be returned.   </div>
   


For example, if the image's width and height are 6000 px and 7000 px, the total pixel size exceeds 36 million and an error message will be returned.


Attributes


---



input.**type** `string` `Required`

The type of the input content. In this case, it is `image_url`.


---



input.**image_url** `object` `Required`

The image input to the model.


Attributes


---



input.image_url.**url** `string` `Required`

The image information, which can be an image URL or the Base64\-encoded content of an image.


* Image URL: Make sure that the image URL is accessible.

* Base64\-encoded content: The format must be `data:image/{image format};base64,{Base64-encoded content of the image}`.




---



**Video information** `Object`

The input video information for the model to vectorize.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">The uploaded video needs to meet the following conditions:</div>



* <div data-tips="true" data-tips-type="tip">Format:<code>.mp4</code>,<code>.avi</code>, <code>.mov</code>, video format must be lowercase.</div>


* <div data-tips="true" data-tips-type="tip">When passing in Base64 encoding, use: <a href="https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/image-understanding#477e51ce">Pass Base64 string</a>.</div>


* <div data-tips="true" data-tips-type="tip">Single video file must be within 50 MB</div>


* <div data-tips="true" data-tips-type="tip">Audio understanding is temporarily not supported for video files.</div>




---



input.**type** `string` `Required`

The type of entered content, here should be `video_url`.


---



input.**video_url** `Object` `Required`

The video object input to the model.


Attributes

input.video_url.**url** `string` `Required`

You can pass in a video URL or the Base64\-encoded content of the video. For specific usage, refer to [Pass Base64 string](video-understanding.md#22314028).




---



**encoding_format** `string / null` `default value: float`

Valid values: `float` and `base64`

The format in which the embedding is to be returned.


---



**dimensions** `integer` `default value: 2048`

Valid range: `1024` or `2048`.

Used to specify the output vector dimension. This parameter is only supported by `skylark-embedding-vision-250615` and subsequent versions; for historical versions, please refer to [Set vector ](vectorization.md#b9c355d7)[dimensions](vectorization.md#b9c355d7).


---



**instructions** `string`

Inference prompt; used directly if provided by the user, otherwise a default value is generated based on the input modality. See [Set the instructions field (recommended)](vectorization.md#91d6bcd1) for details.


---



**sparse_embedding** `object`

Sparse vector switch configuration; this field can only be configured for pure text input.

Possible values:


* type="disabled": Only outputs dense vectors, not sparse vectors;

* type="enabled": Outputs both dense and sparse vectors.

    &nbsp;


<span id="response-parameters"></span>
## Response parameters

> Jump to [Request parameters](multimodal-vectorization-api.md#YngOhBAp)



---



**id** `string`

The unique identifier of the request.


---



**model** `String`

The name and version of the model used in the request.


---



**created** `integer`

The Unix timestamp in seconds of the creation time of the request.


---



**Object** `String`

It is fixed to `list`.


---



**data** `Object`

The algorithm output for the request.


Attributes

data.**embedding** `float[]`

The vector into which the corresponding content was converted.


---



data.**sparse_embedding** `array`

Sparse vector, returned only when sparse_embedding.type="enabled"; each member has the structure {"index": dimension index,"value": non\-zero value}, and only non\-zero elements are returned.


---



data.**object** `String`

It is fixed to `embedding`.



---



**usage** `Object`

The token usage for the request.


Attributes


---



usage.**prompt_tokens** `integer`

The number of input tokens.


---



usage.**total_tokens** `integer`

The total number of input and output tokens consumed by this request.


---



usage.**prompt_tokens_details** `object`

The usage details of the input tokens.


Attributes


---



usage.prompt_tokens_details.**text_tokens** `integer`

In the input content, the number of tokens corresponding to the text content and the tokens generated by the timeline of the video content.

To ensure optimal model performance, a small number of tokens is generated for the preset text when you input an image or video, resulting in additional **text_tokens**.


---



usage.prompt_tokens_details.**image_tokens** `integer`

The number of tokens corresponding to the image content and frame\-extracted images from video content in the input.
