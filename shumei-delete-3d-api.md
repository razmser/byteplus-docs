# Cancel or delete a 3D generation task

`DELETE https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks/{id}`

Cancels a queued 3D generation task, or deletes a 3D generation task record.

**Quick start**

![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_b9c82890e851fc10cc31f48f9065abc6.png) [Model Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/vision?projectName=default)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_2abecd05ca2779567c6d32f0ddc7874d.png) [Model List](model-list.md)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_a5fdd3028d35cc512a10bd71b982b6eb.png) [Model Billing](model-pricing.md#video-generation-models)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_afbcf38bdec05c05089d5de5c3fd8fc8.png) [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D)

![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_57d0bca8e0d122ab1191b40101b5df75.png) [API Call Guide](video-generation-tutorial.md)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_f45b5cd5863d1eed3bc3c81b9af54407.png) [API Reference](shumei-delete-3d-api.md)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_1609c71a747f84df24be1e6421ce58f0.png) [FAQs](modelark-faq.md)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_bef4bc3de3535ee19d0c5d6c37b0ffdd.png) [Model Activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&tab=ComputerVision)

**Authentication**

This interface only supports API Key authentication. Obtain a long\-term API Key on the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D) page.

---

## Request parameters

> See [Response parameters](shumei-delete-3d-api.md#CIc8kYME)

> **Tip**

> This parameter is a Path Parameter. You need to directly replace the `{id}` placeholder in the URL with the ID of the 3D generation task to be queried.

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

## Response parameters

> See [Request parameters](shumei-delete-3d-api.md#cZmqaHz0)

This API operation has no response parameters.
