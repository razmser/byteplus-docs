# Delete memory store

`DELETE https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/{memory_store_id}`

Deletes the specified memory store and all memory entries in it. Deletion cannot be undone. To continue using it, you must create a memory store again and write content to it. Use this API when you need to delete an entire memory store and all of its memories. To delete only one memory, use the delete memory entry API.

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">You can delete a memory store even if it is mounted to a session. After deletion, related sessions will no longer be able to access the memory store or the memories in it. Confirm the business impact before deleting it.</div>


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">When the memory store does not exist, or the current account or project does not have permission to access it, 404 <code>InvalidMemoryStore.NotFound</code> is returned. When you delete a memory store that no longer exists, you can treat this error as indicating that the target memory store no longer exists. When the request path parameter is invalid, 400 <code>InvalidParameter</code> is returned.</div>


<span id="authentication"></span>
## Authentication

This API supports the following authentication methods. For details, see [Base URL and authentication](base-url-and-authentication.md).


* API Key authentication. Go to the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.



---



<span id="request-parameters"></span>
## Request parameters

<span id="path-parameters"></span>
### Path parameters


**memory_store_id** `string` `Required`  |  Memory store ID

Used to identify the memory store to delete. It starts with `memstore-`. Pass the full ID returned by the create or retrieve API. Do not concatenate it yourself.


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**id** `string`  |  Memory store ID

The ID of the deleted memory store, which is the same as `memory_store_id` in the request.



**type** `string`  |  Object type

Object type. Fixed to `memory_store_deleted`.
