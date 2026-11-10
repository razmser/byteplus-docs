# List memories

`GET https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/{memory_store_id}/memories`

Queries memory entries in the specified memory store. Supports filtering by `path_prefix`, and collapsing subdirectories by `depth`. List items contain only the path and simplified information without `content`; call the query memory details API if you need the content.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">Returns 404 <code>InvalidMemoryStore.NotFound</code> when the memory store does not exist or is inaccessible. Returns 400 <code>InvalidParameter</code> when <code>path_prefix</code>, <code>depth</code>, <code>order_by</code>, <code>limit</code>, or the next page identifier is invalid; keep the filters and items per page unchanged during pagination.</div>


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

<span id="query-parameters"></span>
### Query parameters

> Passed as URL query string.



**depth** `integer`  |  Collapse depth

Controls the number of directory levels to display after `path_prefix`. If omitted, all matching memories are returned in a flat list.

For example, when `path_prefix` is `/preferences/` and `depth` is `1`:


* `/preferences/language.md` is returned as `memory`.

* Memories at deeper levels are merged into `memory_prefix` items such as `/preferences/editors/`.


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">The value must be greater than or equal to 1. If the value is greater than the actual directory depth of the matching path, all matching memories are returned with the <code>memory</code> type, and <code>memory_prefix</code> collapsed items are no longer generated.</div>


**Min**: `1`



**limit** `integer` `Default 100`  |  Items per page

The number of memory entries returned on a single page.

**Range**: `[1, 2000]`



**order_by** `string` `Default path`  |  Sort parameter

Specifies how the list is sorted. Options: `path`, `updated_at`, `created_at`.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>



* <div data-tips="true" data-tips-type="tip"><code>path</code>: sorted in lexicographic order by path.</div>


* <div data-tips="true" data-tips-type="tip"><code>updated_at</code>: sorted in descending order by update time.</div>


* <div data-tips="true" data-tips-type="tip"><code>created_at</code>: sorted in descending order by creation time.</div>




**page** `string`  |  Next page identifier

The next page identifier returned in the previous page response. Omit it for the first request; for subsequent requests, pass the value of `next_page` as is.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">The next page identifier is bound to <code>path_prefix</code>, <code>depth</code>, <code>order_by</code>, and <code>limit</code>. Changing any parameter during pagination returns 400 <code>InvalidParameter</code>.f</div>




**path_prefix** `string`  |  Path prefix

Filter by the directory prefix of the memory `path` parameter. Non\-empty values must start with `/`; the server matches on directory boundaries, so `/foo` is equivalent to `/foo/` and does not match `/foobar.md`.


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**data** `object[]`  |  Memory list

A memory list item. When `type` is `memory`, the file metadata is returned; when `type` is `memory_prefix`, the `path` and `memory_count` of the collapsed directory are returned.


**path** `string`  |  Memory path

`data.path`

The path of a memory file or collapsed directory; when the type is `memory_prefix`, it ends with `/`.



**type** `string`  |  List item type

`data.type`

List item type.`memory` indicates a memory file, and `memory_prefix` indicates a collapsed directory.



**content_sha256** `string`  |  Content digest

`data.content_sha256`

The SHA\-256 digest of the memory content, represented as a lowercase hexadecimal string and returned only when `type` is `memory`.



**content_size_bytes** `integer`  |  Content size in bytes

`data.content_size_bytes`

The number of bytes after the memory content is encoded in UTF\-8, returned only when `type` is `memory`.



**created_at** `string`  |  Creation time

`data.created_at`

Creation time. Returned only when `type` is `memory`, in RFC 3339 UTC format.



**id** `string`  |  Memory ID

`data.id`

The server\-generated memory ID, returned only when `type` is `memory`. Pass it as the `memory_id` path parameter when subsequently querying, updating, or deleting the memory.



**memory_count** `integer`  |  Memory count

`data.memory_count`

The number of memories in the collapsed directory, returned only when `type` is `memory_prefix`.



**memory_store_id** `string`  |  Memory store ID

`data.memory_store_id`

The ID of the memory store to which this memory belongs, returned only when `type` is `memory`.



**memory_version_id** `string`  |  Version ID

`data.memory_version_id`

The current version identifier of the memory entry, returned only when `type` is `memory`. This value changes after the content or path is successfully updated.



**updated_at** `string`  |  Update time

`data.updated_at`

Last update time, returned only when `type` is `memory`, in RFC 3339 UTC format.




**next_page** `string`  |  Next page identifier

Next page identifier. If a non\-empty value is returned, pass it as is to the `page` parameter in the next request; this parameter is not returned when there is no more data.
