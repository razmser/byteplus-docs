# List memories

`GET https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/{memory_store_id}/memories`

Queries memory entries in the specified memory store. Supports filtering by `path_prefix`, and collapsing subdirectories by `depth`. List items contain only the path and simplified information without `content`; call the query memory details API if you need the content.

> **Tip**

> Returns 404 `InvalidMemoryStore.NotFound` when the memory store does not exist or is inaccessible. Returns 400 `InvalidParameter` when `path_prefix`, `depth`, `order_by`, `limit`, or the next page identifier is invalid; keep the filters and items per page unchanged during pagination.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**memory_store_id** `string` `Required`  |  Memory store ID

Used to specify the target memory store. It starts with `memstore-`. Pass the full ID returned by the create or query memory store API. Do not concatenate it yourself.

### Query parameters

> Passed as URL query string.

**depth** `integer`  |  Collapse depth

Controls the number of directory levels to display after `path_prefix`. If omitted, all matching memories are returned in a flat list.

For example, when `path_prefix` is `/preferences/` and `depth` is `1`:

* `/preferences/language.md` is returned as `memory`.

* Memories at deeper levels are merged into `memory_prefix` items such as `/preferences/editors/`.

> **Tip**

> The value must be greater than or equal to 1. If the value is greater than the actual directory depth of the matching path, all matching memories are returned with the `memory` type, and `memory_prefix` collapsed items are no longer generated.

**Min**: `1`

**limit** `integer` `Default 100`  |  Items per page

The number of memory entries returned on a single page.

**Range**: `[1, 2000]`

**order_by** `string` `Default path`  |  Sort parameter

Specifies how the list is sorted. Options: `path`, `updated_at`, `created_at`.

> **Tip**

* > `path`: sorted in lexicographic order by path.

* > `updated_at`: sorted in descending order by update time.

* > `created_at`: sorted in descending order by creation time.

**page** `string`  |  Next page identifier

The next page identifier returned in the previous page response. Omit it for the first request; for subsequent requests, pass the value of `next_page` as is.

> **Tip**

> The next page identifier is bound to `path_prefix`, `depth`, `order_by`, and `limit`. Changing any parameter during pagination returns 400 `InvalidParameter`.f

**path_prefix** `string`  |  Path prefix

Filter by the directory prefix of the memory `path` parameter. Non\-empty values must start with `/`; the server matches on directory boundaries, so `/foo` is equivalent to `/foo/` and does not match `/foobar.md`.

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

**content_size_bytes** `integer`  |  Content bytes

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

**prefixes** `string[]`  |  Collapsed prefix list

Currently returns an empty array. Collapsed directories are returned through list items in `data[]` whose `type` is `memory_prefix`.
