# List 3D generation tasks

`GET https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks?page_num={page_num}&page_size={page_size}&filter.status={filter.status}&filter.task_ids={filter.task_ids}&filter.model={filter.model}`

You can specify the filter parameters to retrieve a list of tasks.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">Instructions</div>


<div data-tips="true" data-tips-type="tip">Only the historical data from the past <strong>7 days</strong> can be queried. Time calculation is based on UTC timestamps. The 7\-day range is determined using the exact moment (to the second) when the batch query request is made, with the time window defined as <strong>[T − 7 days, T)</strong>.</div>



<Tabs>
<Tab zoneid="DWJrPjpc1u" title="Quick start">
<TabTitle>Quick start</TabTitle>

<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_b9c82890e851fc10cc31f48f9065abc6.png) </span> [Model Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/vision?projectName=default)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_2abecd05ca2779567c6d32f0ddc7874d.png) </span> [Model List](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1330310)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_a5fdd3028d35cc512a10bd71b982b6eb.png) </span> [Model Billing](model-pricing.md#8f25f772)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_afbcf38bdec05c05089d5de5c3fd8fc8.png) </span> [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D)

<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_57d0bca8e0d122ab1191b40101b5df75.png) </span> [API Call Guide](video-generation-tutorial.md)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_f45b5cd5863d1eed3bc3c81b9af54407.png) </span> [API Reference](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/Video_Generation_API)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_1609c71a747f84df24be1e6421ce58f0.png) </span> [FAQs](modelark-faq.md)<span>![图片](https://portal.volccdn.com/obj/volcfe/cloud-universal-doc/upload_bef4bc3de3535ee19d0c5d6c37b0ffdd.png) </span> [Model Activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&tab=ComputerVision)


</Tab>
<Tab zoneid="soSKwDQnMs" title="Authentication">
<TabTitle>Authentication</TabTitle>

This interface only supports API Key authentication. Obtain a long\-term API Key on the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D) page.


</Tab>
</Tabs>



---



<span id="zTSwER9F"></span>
## Request parameters

> See [Response parameters](shumei-get-3d-generation-task-list-api.md#fEKuoEzq)


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">These parameters are Path Parameter. You need to replace the placeholder in the URL with the ID of the 3D generation task to be queried.</div>


<span id="bduLXV0a"></span>
### Query parameters


---



**page_num** `integer / null`

The page number of the returned results.


* Valid values: [1, 500]



---



**page_size** `integer / null`

The number of entries per page.


* Valid values: [1, 500]



---



**filter** `object`

Filter parameters.


Attributes


---



filter.**status** `string`

The status of the task. Valid values:


* `queued`

* `running`

* `cancelled` (Only tasks in the queued state can be canceled)

* `succeeded`

* `failed`



---



filter.**task_ids** `string[]`

Search for specific 3D generation tasks using the task ID. Multiple IDs can be passed, separated with `&`.


Example: Query multiple tasks in one request using IDs.

```Bash
curl -X GET https://ark.ap-southeast.bytepluses.com/api/v3/contents/generations/tasks?filter.task_ids=id1&filter.task_ids=id2 \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $ARK_API_KEY"
```




---



filter.**model** `string`

The ID of the used inference endpoint for the exact search, which differs from the model parameter in the response.



---



<span id="fEKuoEzq"></span>
## Response parameters

> See [Request parameters](shumei-get-3d-generation-task-list-api.md#zTSwER9F)



---



**items** `object[]`

The retrieved 3D generation tasks.


Attributes


---



items.**id** `string`

The ID of the 3D generation task.


---



items.**model** `string`

The name and version of the model used by the task (`Model name-Version`).


---



items.**status** `string`

The state of the task. Valid values:


* `queued`

* `running`

* `cancelled` (The task has been canceled. Only tasks in the queued state can be canceled.)

* `succeeded`

* `failed`



---



items.**error** `object / null`

The error information. If the task succeeds, `null` is returned. If the task fails, the error information is returned. For more information, refer to [Error codes](error-codes.md).


Attributes


---



error.**code** `string`

The error code.


---



error.**message** `string`

The error message.



---



items.**created_at** `integer`

The time when the task was created. The value is a UNIX timestamp in seconds.


---



items.**updated_at** `integer`

The time when the task was last updated. The value is a UNIX timestamp in seconds.


---



items.**content** `object`

The output of the 3D generation task. It contains the download URL of the output 3D.


Attributes


---



content.**file_url** `string`

The URL of the output 3D. For security purposes, the output 3D is cleared after 24 hours. Save it in time.



---



items.**usage** `object`

The token usage for the request.


Attributes


---



usage.**completion_tokens** `integer`

The number of tokens consumed for the 3D output by the model.


---



usage.**total_tokens**`integer`

Total tokens for this request. For 3D models, input tokens are always 0, therefore **total_tokens = completion_tokens**.




---



**total** `integer`

The number of tasks that match the filtering conditions.
