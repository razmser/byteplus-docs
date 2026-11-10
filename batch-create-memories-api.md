# Batch create memories

`POST https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/{memory_store_id}/memories/batch_create`

Batch create multiple text memories in the specified memory store, suitable for importing multiple files at once. When writing only one memory, you can use the create memory API. Before calling this API, create a memory store and use the ID returned by the create or query memory store API.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip"><strong>Batch creation limits</strong></div>



* <div data-tips="true" data-tips-type="tip">A single batch must contain 1–100 memories, and the total content must not exceed 50 MiB. Each content item must be valid UTF\-8 text and must not exceed 100 KiB.</div>


* <div data-tips="true" data-tips-type="tip">If paths are duplicated within a batch, the entire request returns 400 <code>InvalidParameter</code>. If the total request size exceeds the limit, the batch parameters are invalid, or the memory store is inaccessible, the entire request fails.</div>


* <div data-tips="true" data-tips-type="tip">Each memory store can store up to 2000 memories.</div>



<div data-tips="true" data-tips-type="tip"><strong>Processing results</strong></div>



* <div data-tips="true" data-tips-type="tip">After the batch passes overall validation, each entry is processed independently. The <code>results</code> in the response correspond one\-to\-one with the request <code>items</code> in order. If validation of a single path, content item, or conflict fails, the request still returns 200, and the error is provided in the corresponding <code>results[].error</code>.</div>



<div data-tips="true" data-tips-type="tip"><strong>Retry</strong></div>



* <div data-tips="true" data-tips-type="tip">Check each result before retrying. Retrying successful entries with the default <code>overwrite</code> will update their current version identifiers again.</div>



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

Used to specify the target memory store. It starts with `memstore-`. Pass the full ID returned by the create or query memory store API. Do not concatenate it yourself.


&nbsp;

<span id="body-parameters"></span>
### Body parameters


**items** `object[]` `Required`  |  Memory list

The memory entries created in this batch.

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">A single batch contains 1–100 memories. The <code>path</code> values in a batch must not be duplicated; if duplicates exist, the entire request returns 400 <code>InvalidParameter</code>.</div>



**content** `string` `Required`  |  Memory content

`items.content`

The body written to the memory file.

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">Memory content must be valid UTF\-8 text, and the content of each memory must not exceed 102400 bytes (100 KiB).</div>




**path** `string` `Required`  |  Memory path

`items.path`

The file path of the memory in the memory store.

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">The path must start with <code>/</code> and end with <code>.md</code> or <code>.txt</code>. It must not exceed 1024 bytes after UTF\-8 encoding, and must not contain control characters, <code>%</code>, <code>;</code>, or <code>?</code>. Path segments must not be empty, <code>.</code>, or <code>..</code>.</div>





**on_conflict** `string` `Default overwrite`  |  Conflict policy

The handling policy when a memory entry with the same name already exists at the specified target `path`:


* `overwrite`: Overwrite the existing memory with the new content and update its current version identifier.

* `fail`: Keep the existing memory. This entry returns a conflict error, and other entries continue to be processed.


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**results** `object[]`  |  Creation results

Creation results for each memory, corresponding one\-to\-one to the request `items` in order. Returns `memory` on success and `error` on failure.


**path** `string`  |  Memory path

`results.path`

Corresponds to `items[].path` in the request, and is used to associate the input entry with the creation result.



**error** `object`  |  Error information

`results.error`

Error information for this memory when it fails; not returned on success.


**message** `string`  |  Error description

`results.error.message`

Description of why this memory failed, used to pinpoint and correct the input.



**type** `string`  |  Error type

`results.error.type`

Error type identifier, used to classify the reason why this memory failed.




**memory** `object`  |  Memory details

`results.memory`

Details of the memory after successful creation or overwrite; not returned if the request for this memory fails.


**content_sha256** `string`  |  Content digest

`results.memory.content_sha256`

The SHA\-256 digest of the memory body, represented as a lowercase hexadecimal string. It can be used to verify content integrity.



**content_size_bytes** `integer`  |  Content size in bytes

`results.memory.content_size_bytes`

The number of bytes in the memory body after UTF\-8 encoding.



**created_at** `string`  |  Creation time

`results.memory.created_at`

The time when the memory was created, in RFC 3339 UTC format.



**created_by** `object`  |  Creator

`results.memory.created_by`

Information about the operator who created the memory.


**type** `string`  |  Operator type

`results.memory.created_by.type`

Operator type.`user_actor` indicates that the memory was written with a user identity, and `session_actor` indicates that it was written by an agent in a session.



**session_id** `string`  |  Session ID

`results.memory.created_by.session_id`

The ID of the session that wrote this memory. Returned only when `type` is `session_actor`.



**user_id** `string`  |  User ID

`results.memory.created_by.user_id`

The ID of the user who wrote this memory. Returned only when `type` is `user_actor`.




**id** `string`  |  Memory ID

`results.memory.id`

The memory ID generated by the server, starting with `mem-`. Pass it as the `memory_id` path parameter when subsequently querying, updating, or deleting the memory.



**memory_store_id** `string`  |  Memory store ID

`results.memory.memory_store_id`

The ID of the memory store to which this memory belongs.



**memory_version_id** `string`  |  Version ID

`results.memory.memory_version_id`

The current version identifier of the memory entry. This value changes after the content or path is successfully updated.



**path** `string`  |  Memory path

`results.memory.path`

The file path of the memory in the memory store.



**type** `string`  |  Object type

`results.memory.type`

Object type. Fixed to `memory`.



**updated_at** `string`  |  Update time

`results.memory.updated_at`

The time when the memory was last updated, in RFC 3339 UTC format.
