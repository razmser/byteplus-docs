# Delete memory

`DELETE https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/{memory_store_id}/memories/{memory_id}`

Deletes a single memory entry. After deletion, the original `path` can be used to recreate a memory with the same name. Use this API only when deleting a single memory; when you need to delete an entire memory store and all memories in it, use the delete memory store API.

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">You can delete the memory even if its memory store is mounted to a session. After deletion, related sessions can no longer read this entry; confirm the business impact before deletion.</div>


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">If the memory store does not exist or is inaccessible to the current account or project, 404 <code>InvalidMemoryStore.NotFound</code> is returned; if the memory does not exist, 404 <code>ResourceNotFound</code> is returned. When repeatedly deleting a memory that no longer exists, you can treat 404 <code>ResourceNotFound</code> as indicating that the target memory no longer exists. If deletion fails due to concurrent modification, 409 <code>ResourceConflict</code> is returned; query again before retrying.</div>


<span id="authentication"></span>
## Authentication

This API supports the following authentication methods. For details, see [Base URL and authentication](base-url-and-authentication.md).


* API Key authentication. Go to the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.



---



<span id="request-parameters"></span>
## Request parameters

<span id="path-parameters"></span>
### Path parameters


**memory_id** `string` `Required`  |  Memory ID

Used to specify the memory to delete. It starts with `mem-`. Pass the full ID returned by the create, list, or update API. Do not concatenate it yourself.



**memory_store_id** `string` `Required`  |  Memory store ID

Used to specify the memory store to which the memory belongs. It starts with `memstore-`. Pass the full ID returned by the create or retrieve API. Do not concatenate it yourself.


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**id** `string`  |  Memory ID

The ID of the deleted memory, which is the same as `memory_id` in the request.



**type** `string`  |  Object type

Object type. Fixed to `memory_deleted`.
