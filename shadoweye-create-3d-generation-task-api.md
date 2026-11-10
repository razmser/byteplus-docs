# Create a 3D generation task

`POST https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks`

Create a Hyper3D generation task. The model will generate 3D content based on the input image information. Upon completion, you can query the task by conditions and retrieve the generated 3D model.

> **Tip**

> This API charges for output tokens at 0.0133 USD/1K Tokens. Each generated 3D model consumes a fixed 30,000 tokens, equivalent to $0.399 per model. See [Pricing](model-pricing.md#33538fff) for details.

**Model capabilities**

**Hyper3d\-Gen2**

* **Text\-to\-3D**: Generates a 3D model file based on your input text prompt + parameters (optional).

* **Image\-to\-3D**: Generates a 3D model file based on your input images (1–5 images) + text prompt (optional) + parameters (optional).

**Supported Countries & Regions**

Australia (AU), Cambodia (KH), Cayman Islands (KY), Egypt (EG), Fiji (FJ), Ghana (GH), Hong Kong, China (HK), India (IN), Indonesia (ID), Japan (JP), Kuwait (KW), Laos (LA), Macao, China (MO), Malaysia (MY), Morocco (MA), New Zealand (NZ), Pakistan (PK), Philippines (PH), Qatar (QA), Saudi Arabia (SA), Singapore (SG), Solomon Islands (SB), South Africa (ZA), South Korea (KR), Tanzania (TZ), Thailand (TH), Turkey (TR), Taiwan, China (TW), Vietnam (VN), Zambia (ZM)

---

**Quick start**

![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_b9c82890e851fc10cc31f48f9065abc6.png) [Model Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/vision?projectName=default)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_2abecd05ca2779567c6d32f0ddc7874d.png) [Model List](model-list.md)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_a5fdd3028d35cc512a10bd71b982b6eb.png) [Model Billing](model-pricing.md#8f25f772)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_afbcf38bdec05c05089d5de5c3fd8fc8.png) [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D)

![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_57d0bca8e0d122ab1191b40101b5df75.png) [API Call Guide](video-generation-tutorial.md)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_f45b5cd5863d1eed3bc3c81b9af54407.png) [API Reference](shadoweye-create-3d-generation-task-api.md)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_1609c71a747f84df24be1e6421ce58f0.png) [FAQs](modelark-faq.md)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_bef4bc3de3535ee19d0c5d6c37b0ffdd.png) [Model Activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&tab=ComputerVision)

**Authentication**

This interface only supports API Key authentication. Obtain a long\-term API Key on the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D) page.

---

## Request parameters

> See [Response parameters](shadoweye-create-3d-generation-task-api.md#EAVvaaIM)

---

### Request body

**model** `string` `Required`

The ID of the model to call. [Activate a model service](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&tab=ComputerVision) and [query the model ID](model-list.md).

You can choose to use an inference endpoint ID to call a model. Inference endpoints provide an easy way to query rate limits, billing method (prepaid or postpaid), and service status. Advanced capabilities such as monitoring and security are provided. For more information, see [Obtaining an endpoint ID](online-inference-standard.md).

---

**content** `object[]` `Required`

The references provided to the model for 3D generation, supporting text and image input. The following combinations are supported:

**Text to 3D：**

* Text

* Text + Parameter (optional)

   **Image to 3D：**

* Image

* Image + Text (optional)

* Image + Parameter (optional)

* Image + Text (optional) + Parameter (optional)

Information type

**Image** `object` `Required`

Information of the image provided to the model to generate a 3D content.

Attributes

---

content.**type** `string` `Required`

The type of the input content. In this case, set the value to `image_url`.

Supports image URL or image Base64 encoding.

---

content.**image_url** `object` `Required`

The input image object.

Attributes

---

content.image_url.**url** `string` `Required`

Accepts image URL, Base64\-encoded image.

* URL: Enter the public accessible URL of the image.

* Base64 encoding: Convert the local file to a Base64\-encoded string, and then submit to the model. Format: `data:image/<image format>;base64,<Base64 encoding>`.

> **Tip**

> Image input requirements

* > Format: jpg, jpeg, png

* > Total pixels: Single image must be less than 4096×4096 px

* > Size: Single image must be less than 30 MB

* > Number of images: 1~5

---

**Text** `object`

The text provided to the model to generate a 3D.

Attributes

---

content.**type** `string` `Required`

The type of the input content. In this case, set the value to `text`.

---

content.**text** `string` `Required`

The text provided to the model, which describes the 3D content to be generated. Input should include:

* **Text prompt (required)** : Only English is supported, with a maximum length of 400 characters; long inputs will be truncated.

* **Parameters (optional)** : You can append `--[parameters]` after the text prompt to manage the specifications of the output 3D. For more information, refer to **Text commands for models (optional)** .

---

**seed** `integer`

An integer that controls the randomness of the output content. Valid values: integers within the range of `[0, 65535]`.

> **Note**

> warning

* > You can change the seed value to receive different outputs using the same request.

* > Using the same seed value doesn't guarantee identical outputs.

---

**callback_url** `string`

Fills in the callback address to receive the result the generation task. When a status change happens in the 3D generation task, Ark will send a callback request containing the latest task status to this address.

The structure of the callback request is consistent with the response body of [Retrieve a 3D generation task](shadoweye-get-3d-generation-task-api.md).

The status returned by the callback includes the following states:

* queued: The task is in queue.

* running: The task is running.

* succeeded: The task is successful. (If the sending fails, i.e., the information of successful sending is not received within 5 seconds, the callback will be made three times)

* failed: The task fails. (If the sending fails, i.e., the information of successful sending is not received within 5 seconds, the callback will be made three times)

---

### **Parameters (optional)**

Manages the output specifications of the 3D model file.

Example

```JSON
{
    "content": [
        {
            "type": "text",
            "text": "Complete full-body quadrupedal mech robot, orange and black armored, rounded head with glowing blue sensors, 4 articulated mechanical legs, hard-surface sci-fi design, high-detail joints, photorealistic 8K, fully assembled, no missing parts. --mesh_mode Raw --hd_texture true --material PBR --addons HighPack --quality_override 1000000 --use_original_alpha false --bbox_condition [100,100,100] --TAPose false"
        }
    ]
}
```

---

**material** `string` `default PBR`

Controls the material type of the 3D model. Optional values:

* `PBR`: Physically based rendering material, including base color texture, metallic texture, normal texture, and roughness texture, providing high realism and physical accuracy under dynamic lighting.

* `Shaded`: Stylized material, including base color texture and baked lighting.

* `All`: Generate both PBR and Shaded materials.

* `None`: No texture, i.e., generates a white mesh model.

---

**mesh_mode** `string` `default Quad`

Controls the mesh shape of the generated model. Optional values:

* `Raw`: Generates a triangle mesh model.

* `Quad`: Generates a quad mesh model.

---

**quality_override** `number`

Customizes the polygon count (number of faces) of the 3D model.

* When **mesh_mode** is `Raw`: range [500, 1000000], default: `500000`

* When **mesh_mode** is `Quad`: range [1000, 200000], default: `18000`

> **Tip**

* > When both **quality_override**and**subdivisionlevel** are provided, **subdivisionlevel** will be ignored.

* > It is recommended to specify a face count ≥ 150,000.

---

**addons** `string`

Enhances the resolution of texture maps. Optional value:

* `HighPack`: Provides 4K resolution texture maps.

> **Tip**

> If this parameter is not specified, texture maps will default to 2K resolution.

---

**use_original_alpha** `boolean` `default false`

Controls transparency of the 3D model.

* `true`: The transparent parts of the uploaded image are preserved.

* `false`: The transparent parts are automatically filled, which may lose the original transparency effect.

---

**bbox_condition** `integer[]`

An array specifying the bounding box dimensions and scaling factor. The array contains three elements: width (x\-axis), height (y\-axis), and length (z\-axis). Example: [100,100,100]

> **Tip**

> tip

> It is recommended not to specify this parameter unless you have a specific requirement; the model will determine the bounding box automatically.

---

**TAPose** `boolean` `default false`

Controls whether the result is in T/A Pose when generating humanoid models.

* `false`: T/A Pose is not enforced; the model decides the pose.

* `true`: Enforces a standard binding pose, and the model determines whether it is a T\-Pose or A\-Pose.

---

**subdivisionlevel** `string` `sl`

Controls the polygon count of the 3D model. Optional values:

When **mesh_mode** is `Raw`, default value is `high`.

* `high`: 500k

* `medium`: 150k

* `low`: 20k

   When **mesh_mode** is `Quad`, default value is `medium`.

* `high`: 50k

* `medium`: 18k

* `low`: 8k

---

**fileformat** `string` `default glb` `ff`

The format of the generated textured 3D model file. Optional values:

* `glb`

* `obj`

* `usdz`

* `fbx`

* `stl`

---

**hd_texture** `boolean` `default false`

Whether to enable HD texture.

* `true`: HD texture enabled.

* `false`: HD texture disabled.

---

## Response parameters

> See [Request parameters](shadoweye-create-3d-generation-task-api.md#IzMcuGUn)

**id** `string`

The ID of the 3D generation task. Only retained for 7 days (calculated from the created timestamp) and will be automatically deleted upon expiration.

Creating a 3D generation task is an asynchronous interface. After obtaining the ID, you need to query the status of the 3D generation task through [Retrieve a 3D generation task](shadoweye-get-3d-generation-task-api.md). When the task is successful, the `file_url` of the generated 3D will be output.

---
