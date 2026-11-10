# Create a 3D generation task

`POST https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks`

Create a Hitem3d Generation Task API. The model will generate 3D content based on the input image information. Upon completion, you can query the task by conditions and retrieve the generated 3D model.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">This API charges for output tokens at 0.02 USD/1K Tokens. Each generated 3D model costs between $0.80 and $1.80, depending on the generation mode selected. See <a href="https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/model-pricing#33538fff">3D generation models</a> for details.</div>



**Model capabilities**


* **Hitem3d\-2.0**

   * **Image\-to\-3D**: Generates a 3D model file based on your input images (1–4 images) + text prompt (optional) + parameters (optional).



**Supported Countries & Regions**

Australia (AU), Cambodia (KH), Cayman Islands (KY), Egypt (EG), Fiji (FJ), Ghana (GH), Hong Kong, China (HK), India (IN), Indonesia (ID), Japan (JP), Kuwait (KW), Laos (LA), Macao, China (MO), Malaysia (MY), Morocco (MA), New Zealand (NZ), Pakistan (PK), Philippines (PH), Qatar (QA), Saudi Arabia (SA), Singapore (SG), Solomon Islands (SB), South Africa (ZA), South Korea (KR), Tanzania (TZ), Thailand (TH), Turkey (TR), Taiwan, China (TW), Vietnam (VN), Zambia (ZM)



---




<Tabs>
<Tab zoneid="BhywIjfk8M" title="Quick start">
<TabTitle>Quick start</TabTitle>

<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_b9c82890e851fc10cc31f48f9065abc6.png) </span> [Model Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/vision?projectName=default)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_2abecd05ca2779567c6d32f0ddc7874d.png) </span> [Model List](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1330310)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_a5fdd3028d35cc512a10bd71b982b6eb.png) </span> [Model Billing](model-pricing.md#8f25f772)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_afbcf38bdec05c05089d5de5c3fd8fc8.png) </span> [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D)

<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_57d0bca8e0d122ab1191b40101b5df75.png) </span> [API Call Guide](video-generation-tutorial.md)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_f45b5cd5863d1eed3bc3c81b9af54407.png) </span> [API Reference](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/Video_Generation_API)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_1609c71a747f84df24be1e6421ce58f0.png) </span> [FAQs](modelark-faq.md)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_bef4bc3de3535ee19d0c5d6c37b0ffdd.png) </span> [Model Activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&tab=ComputerVision)


</Tab>
<Tab zoneid="LuZgixfBM5" title="Authentication">
<TabTitle>Authentication</TabTitle>

This interface only supports API Key authentication. Obtain a long\-term API Key on the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D) page.


</Tab>
</Tabs>



---



<span id="5ip6BrEa"></span>
## Request parameters

> See [Response parameters](shumei-create-3d-generation-task-api.md#QOPbUq1b)



---



<span id="F5FJSI57"></span>
### Request body

**model** `string` `Required`

The ID of the model to call. [Activate a model service](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&tab=ComputerVision) and [query the model ID](model-list.md).

You can choose to use an inference endpoint ID to call a model. Inference endpoints provide an easy way to query rate limits, billing method (prepaid or postpaid), and service status. Advanced capabilities such as monitoring and security are provided. For more information, see [Obtaining an endpoint ID](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/create-standard-inference-endpoint).


---



**content** `object[]` `Required`

The references provided to the model for 3D generation, supporting text and image. The following combinations are supported:

**Image to 3D：** 


* Image

* Image + Parameter (optional)



Information type

**Image** `object` `Required`

Information of the image provided to the model to generate a 3D content.


Attributes


---



content.**type** `string` `Required`

The type of the input content. In this case, set the value to `image_url`. Supports image URL.


---



content.**image_url** `object` `Required`

The input image object.


Attributes


---



content.image_url.**url** `string` `Required`

Accepts image URL.


* URL: The public accessible URL of the image.


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">Image input requirements</div>



* <div data-tips="true" data-tips-type="tip">Format: jpg, jpeg, png, webp</div>


* <div data-tips="true" data-tips-type="tip">Total pixels: Single image must be less than 4096×4096 px</div>


* <div data-tips="true" data-tips-type="tip">Size: Single image must be less than 10 MB</div>


* <div data-tips="true" data-tips-type="tip">Number of images: 1~4</div>





---



**Text** `object`

The text provided to the model to generate a 3D.


Attributes


---



content.**type** `string` `Required`

The type of the input content. In this case, set the value to `text`.


---



content.**text** `string` `Required`

The input text information for the model, which describes the 3D to be generated.


* **Parameters (optional)** : You can append `--[parameters]` after the text prompt to control the specifications of the output 3D. For more information, refer to **Text commands for models (optional)** .




---



**callback_url** `string`

Fills in the callback address to receive the result the generation task. When a status change happens in the 3D generation task, Ark will send a callback request containing the latest task status to this address.

The structure of the callback request is consistent with the response body of [Retrieve a 3D generation task](shumei-get-3d-generation-task-api.md).

The status returned by the callback includes the following states:


* queued: The task is in queue.

* running: The task is running.

* succeeded: The task is successful. (If the sending fails, that is, the information of successful sending is not received within 5 seconds, the callback will be made three times)

* failed: The task fails. (If the sending fails, that is, the information of successful sending is not received within 5 seconds, the callback will be made three times)



---



<span id="mtygdzcZ"></span>
### **Parameters (optional)** 

Manages the output specifications of the 3D model file.


Example

```JSON
{
    "content": [
        {
            "type": "text",
            "text": "--resolution 1536 --request_type 3 --ff 1 --face 2000000"

        }
    ]
}
```




---



**resolution** `string` `default 1536`

Controls the resolution of the 3D model. Available values:


* `1536`

* `1536pro`



---



**face** `integer`

Model polygon count. Supports custom configuration. Valid range: [100000, 2000000]. Recommended polygon count for different resolutions:


* 1536: `2000000`

* 1536 pro: `2000000`



---



**fileformat** `integer` `Default 1` `ff`

The format of the generated textured 3D model file. Optional values:


* `1`: obj

* `2`: glb

* `3`: stl

* `4`: fbx

* `5`: usdz



---



**request_type** `integer` `Default 3`

Request type. Available values:


* `1`: Generate geometry only (no textures)

* `3`: Generate geometry and textures in one request



---



**multi_images_bit** `string`

A bitmap identifier string for the uploaded image array. It is used to indicate the view type of each image in content.image_url. The string consists of 0 and 1: 1 means the image at that position is a valid view image, and 0 means there is no corresponding view image at that position.

View types are ordered as: **front view**, **back view**, **left view**, **right view**.

Example:


|Parameter value |Explanation |Array content order |
|---|---|---|
|1010 |Front view, left view exist |[front view, left view] |
|1011 |Front view, left view, right view exist |[front view, left view, right view] |
|1111 |All four views exist |[front view, back view, left view, right view] |
|0101 |Back view, right view exist |[back view, right view] |



---



<span id="QOPbUq1b"></span>
## Response parameters

> See [Request parameters](shumei-create-3d-generation-task-api.md#5ip6BrEa)


**id** `string`

The ID of the 3D generation task. Only retained for 7 days (calculated from the created at timestamp) and will be automatically deleted upon expiration.

Creating a 3D generation task is an asynchronous interface. After obtaining the ID, you need to query the status of the 3D generation task through [Retrieve a 3D generation task](shumei-get-3d-generation-task-api.md). When the task is successful, the `file_url` of the generated 3D will be output.


---
