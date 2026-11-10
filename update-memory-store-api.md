# Update memory store

`POST https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/{memory_store_id}`

Update the memory store's name, description, or custom metadata by parameter. The request must include at least one of `name`, `description`, and `metadata`. Parameters that are not passed remain unchanged.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">When multiple requests update the same parameter at the same time, the value that is successfully processed last by the server takes effect.</div>


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

Used to identify the memory store to update. It starts with `memstore-`. Pass the full ID returned by the create or retrieve API. Do not concatenate it yourself.


&nbsp;

<span id="body-parameters"></span>
### Body parameters


**description** `string`  |  Memory store description

The purpose and stored content description used to replace the existing description. Passing an empty string clears the existing description.

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">Must not exceed 2048 bytes after UTF\-8 encoding.</div>




**metadata** `object`  |  Custom metadata

Used to replace all existing custom metadata of the memory store. If omitted or set to `null`, the original value is retained. If an empty object `{}` is passed, all keys are cleared.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">When passing an object, if you need to retain existing keys, query the existing metadata before updating, merge the keys to retain with the new values, and then pass the result. This object can contain up to 64 non\-empty keys. Keys and values must both be strings, and must not exceed 8192 bytes after UTF\-8 serialization.</div>




**name** `string`  |  Memory store name

The memory store name used to replace the existing name. Memory stores with the same name are allowed under the same account and project. They can be distinguished by their respective memory store IDs.

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">1–64 characters in length.</div>



&nbsp;

<span id="response-parameters"></span>
## Response parameters


**created_at** `string`  |  Creation time

The time when the memory store was created, in RFC 3339 UTC format.



**created_by** `object`  |  Creator

Information about the operator who created the memory store.


**type** `string`  |  Operator type

`created_by.type`

Creator type, fixed as `user_actor`.



**user_id** `string`  |  User ID

`created_by.user_id`

The ID of the user who created the memory store. It is automatically recorded by the server based on the identity of the current request and can be used to filter memory stores by creator.




**id** `string`  |  Memory store ID

The server\-generated memory store ID, starting with `memstore-`. Pass it as the `memory_store_id` path parameter when subsequently querying, updating, or deleting the memory store.



**memory_count** `integer`  |  Number of memory entries

The number of memory entries currently contained in the memory store.



**metadata** `object`  |  Custom metadata

Custom metadata set when the memory store was created or updated.



**name** `string`  |  Memory store name

The memory store name, used to identify and manage the memory store.



**session_count** `integer`  |  Active session count

Number of active sessions that currently have this memory store mounted.



**storage_bytes** `integer`  |  Total content bytes

The total number of bytes of all memory content currently in the memory store.



**type** `string`  |  Object type

Object type. Fixed to `memory_store`.



**updated_at** `string`  |  Update time

The update time of the memory store, in RFC 3339 UTC format.



**description** `string`  |  Memory store description

Description of the memory store's purpose and stored content.
