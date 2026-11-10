# Cancel or delete a video generation task

`DELETE https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks/{id}` [Try](https://api.byteplus.com/api-explorer/?action=DeleteContentsGenerationsTasks&groupName=Video%20Generation%20API&serviceCode=ark&version=2024-01-01)

Cancels a queued video generation task, or deletes a video generation task record.

**Try**

[去调试](https://api.byteplus.com/api-explorer/?action=DeleteContentsGenerationsTasks&groupName=Video%20Generation%20API&serviceCode=ark&version=2024-01-01)

**Authentication**

This API supports only API Key authentication. Obtain a long\-term API Key on the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D) page.

**Quick start**

![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_b9c82890e851fc10cc31f48f9065abc6.png) [Experience Center](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/gen_video?projectName=default)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_2abecd05ca2779567c6d32f0ddc7874d.png) [Model List](model-list.md)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_a5fdd3028d35cc512a10bd71b982b6eb.png) [Model Billing](model-pricing.md#8f25f772)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_afbcf38bdec05c05089d5de5c3fd8fc8.png) [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D)

![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_57d0bca8e0d122ab1191b40101b5df75.png) [API Call Guide](video-generation-tutorial.md)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_f45b5cd5863d1eed3bc3c81b9af54407.png) [API Reference](cancel-or-delete-video-generation-tasks-api.md)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_1609c71a747f84df24be1e6421ce58f0.png) [FAQs](modelark-faq.md)![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_bef4bc3de3535ee19d0c5d6c37b0ffdd.png) [Model Activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&tab=ComputerVision)

---

## Request parameters

> See [Response parameters](cancel-or-delete-video-generation-tasks-api.md#7mi8G8RI)

### Path parameters

**id** `string` `Required`

The ID of the video generation task to be canceled or deleted.

Operation performed by the `DELETE` API varies depending on the status of the video generation task:

|**Task status** |**Can it be deleted?**  |**Operation description** |**Post\-DELETE task status** |
|---|---|---|---|
|`queued` |Yes |The task is removed from the queue and its status is updated to `cancelled`. |`cancelled` |
|`running` |No |\- |\- |
|`succeeded` |Yes |The video generation task record is deleted and will no longer be queryable. |\- |
|`failed` |Yes |The video generation task record is deleted and will no longer be queryable. |\- |
|`cancelled` |Yes |The video generation task record is deleted and will no longer be queryable. |\- |
|`expired` |Yes |The video generation task record is deleted and will no longer be queryable. |\- |

---

## Response parameters

> See [Request parameters](cancel-or-delete-video-generation-tasks-api.md#RxN8G2nH)

This API operation has no response parameters.
