# Cancel or delete a 3D generation task

`DELETE https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks/{id}`

Cancels a queued 3D generation task, or deletes a 3D generation task record.


<Tabs>
<Tab zoneid="gvuBiw7YFu" title="Quick start">
<TabTitle>Quick start</TabTitle>

<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_b9c82890e851fc10cc31f48f9065abc6.png) </span> [Model Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/vision?projectName=default)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_2abecd05ca2779567c6d32f0ddc7874d.png) </span> [Model List](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1330310)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_a5fdd3028d35cc512a10bd71b982b6eb.png) </span> [Model Billing](model-pricing.md#8f25f772)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_afbcf38bdec05c05089d5de5c3fd8fc8.png) </span> [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D)

<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_57d0bca8e0d122ab1191b40101b5df75.png) </span> [API Call Guide](video-generation-tutorial.md)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_f45b5cd5863d1eed3bc3c81b9af54407.png) </span> [API Reference](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/Video_Generation_API)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_1609c71a747f84df24be1e6421ce58f0.png) </span> [FAQs](modelark-faq.md)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_bef4bc3de3535ee19d0c5d6c37b0ffdd.png) </span> [Model Activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&tab=ComputerVision)


</Tab>
<Tab zoneid="ndP0DQcson" title="Authentication">
<TabTitle>Authentication</TabTitle>

This interface only supports API Key authentication. Obtain a long\-term API Key on the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D) page.


</Tab>
</Tabs>



---



<span id="cZmqaHz0"></span>
## Request parameters

> See [Response parameters](shumei-delete-3d-api.md#CIc8kYME)


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">This parameter is a Path Parameter. You need to directly replace the <code>{id}</code> placeholder in the URL with the ID of the 3D generation task to be queried.</div>


<span id="azMuG6Le"></span>
### Path parameters

**id** `string` `Required`

The ID of the 3D generation task to be canceled or deleted.

The operation performed by the `DELETE` API varies depending on the status of the 3D generation task:


|**Task Status** |**Can it be deleted?**  |**Description** |**Post\-DELETE Task Status** |
|---|---|---|---|
|queued |Yes |The task is removed from the queue and its status is updated to 'cancelled'. |cancelled |
|running |No |\- |\- |
|succeeded |Yes |The 3D generation task record is deleted and will no longer be queryable. |\- |
|failed |Yes |The 3D generation task record is deleted and will no longer be queryable. |\- |
|cancelled |No |\- |\- |
|expired |Yes |The 3D generation task record is deleted and will no longer be queryable. |\- |



---



<span id="CIc8kYME"></span>
## Response parameters

> See [Request parameters](shumei-delete-3d-api.md#cZmqaHz0)


This API operation has no response parameters.
