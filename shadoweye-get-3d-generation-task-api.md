# Retrieve a 3D generation task

`GET https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks/{id}`

Retrieves the information of a 3D generation task.


<Tabs>
<Tab zoneid="YlIhbwaAey" title="Quick start">
<TabTitle>Quick start</TabTitle>

<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_b9c82890e851fc10cc31f48f9065abc6.png) </span> [Model Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/vision?projectName=default)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_2abecd05ca2779567c6d32f0ddc7874d.png) </span> [Model List](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1330310)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_a5fdd3028d35cc512a10bd71b982b6eb.png) </span> [Model Billing](model-pricing.md#8f25f772)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_afbcf38bdec05c05089d5de5c3fd8fc8.png) </span> [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D)

<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_57d0bca8e0d122ab1191b40101b5df75.png) </span> [API Call Guide](video-generation-tutorial.md)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_f45b5cd5863d1eed3bc3c81b9af54407.png) </span> [API Reference](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/Video_Generation_API)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_1609c71a747f84df24be1e6421ce58f0.png) </span> [FAQs](modelark-faq.md)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_bef4bc3de3535ee19d0c5d6c37b0ffdd.png) </span> [Model Activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&tab=ComputerVision)


</Tab>
<Tab zoneid="JrMuv3Rsnf" title="Authentication">
<TabTitle>Authentication</TabTitle>

This interface only supports API Key authentication. Obtain a long\-term API Key on the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D) page.


</Tab>
</Tabs>



---



<span id="3u5DOVUD"></span>
## Request parameters

> See [Response parameters](shadoweye-get-3d-generation-task-api.md#kgxRlOEW)



---



<span id="IkRdkzrU"></span>
### Path parameters

**id** `string` `Required`

The ID of the 3D generation task to query.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">This parameter is a Path Parameter. You need to replace the <code>{id}</code> placeholder in the URL with the ID of the 3D generation task to be queried.</div>



---



<span id="kgxRlOEW"></span>
## Response parameters

> See [Request parameters](shadoweye-get-3d-generation-task-api.md#3u5DOVUD)



---



**id** `string`

The ID of the 3D generation task.


---



**model** `string`

The name and version of the model used by the task (`Model name-Version`).


---



**status** `string`

The status of the task. Valid values:


* `queued`

* `running`

* `cancelled` (Only tasks in the queued state can be canceled)

* `succeeded`

* `failed`

* `expired`



---




* **error** `object / null`

   The error information. If the task succeeds, `null` is returned. If the task fails, the error information is returned. For more information, refer to [Error codes](error-codes.md).



Attributes


---



error.**code** `string`

The error code.


---



error.**message** `string`

The error message.



---



**created_at** `integer`

The time when the task was created. The value is a Unix timestamp in seconds.


---



**updated_at** `integer`

The time when the task was last updated. The value is a Unix timestamp in seconds.


---



**content** `object`

The output after the 3D generation task is completed.


Attributes


---



content.**file_url** `string`

The URL of the output 3D. For security purposes, the output 3D is deleted after 24 hours. Be sure to save it in time.



---



**usage** `object`

The token usage for the request.


Attributes


---



usage.**completion_tokens** `integer`

The number of tokens consumed for the 3D output by the model.


---



usage.**total_tokens** `integer`

Total tokens for this request. For 3D models, input tokens are always 0, therefore **total_tokens = completion_tokens**.
