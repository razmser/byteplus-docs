# List memory stores

`GET https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores`

Query memory stores under the current account and project. Filtering by creator and memory store name is supported, and results are sorted by creation time in descending order by default. Each list item includes information such as the number of memory entries and the number of active sessions.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Query parameters

> Passed as URL query string.

**created_by** `string[]`  |  Creator ID

Filter by creator user ID. The user ID can be obtained from `created_by.user_id` in the memory store response.

* When passing multiple user IDs, repeat the query parameter with the same name, for example, `created_by=<USER_ID_1>&created_by=<USER_ID_2>`.

* **Multiple IDs**: Multiple IDs have an OR relationship; memory stores whose creator ID matches any of the passed values are returned.

* If omitted, creator filtering is not applied.

**limit** `integer` `Default 20`  |  Items per page

The number of memory stores returned on a single page.

**Range**: `[1, 100]`

**name** `string`  |  Memory store name

Perform substring containment matching by memory store name. For example, `name=demo` can match memory stores whose names contain `demo`; `%` and `_` are treated as ordinary characters, not as wildcards.

**page** `string`  |  Next page identifier

The next page identifier returned from the previous page. Omit it for the first request. For subsequent requests, pass `next_page` as is, and the identifier is bound to the current filter conditions. If the filter conditions change or the identifier is invalid, 400 `InvalidParameter` is returned. Query from the first page.

## Response parameters

**data** `object[]`  |  Memory store list

The list of memory stores that match the filter conditions.

**created_at** `string`  |  Creation time

`data.created_at`

The time when the memory store was created, in RFC 3339 UTC format.

**created_by** `object`  |  Creator

`data.created_by`

Information about the operator who created the memory store.

**type** `string`  |  Operator type

`data.created_by.type`

Creator type, fixed as `user_actor`.

**user_id** `string`  |  User ID

`data.created_by.user_id`

The ID of the user who created the memory store. It is automatically recorded by the server based on the identity of the current request and can be used to filter memory stores by creator.

**id** `string`  |  Memory store ID

`data.id`

The server\-generated memory store ID, starting with `memstore-`. Pass it as the `memory_store_id` path parameter when subsequently querying, updating, or deleting the memory store.

**memory_count** `integer`  |  Number of memory entries

`data.memory_count`

The number of memory entries currently contained in the memory store.

**metadata** `object`  |  Custom metadata

`data.metadata`

Custom metadata set when the memory store was created or updated.

**name** `string`  |  Memory store name

`data.name`

The memory store name, used to identify and manage the memory store.

**session_count** `integer`  |  Active session count

`data.session_count`

Number of active sessions that currently have this memory store mounted.

**storage_bytes** `integer`  |  Total content bytes

`data.storage_bytes`

The total number of bytes of all memory content currently in the memory store.

**type** `string`  |  Object type

`data.type`

Object type. Fixed to `memory_store`.

**updated_at** `string`  |  Update time

`data.updated_at`

The update time of the memory store, in RFC 3339 UTC format.

**description** `string`  |  Memory store description

`data.description`

Description of the memory store's purpose and stored content.

**project_name** `string`  |  Project name

`data.project_name`

The name of the project to which the memory store actually belongs.

**next_page** `string`  |  Next page identifier

Next page identifier. If a non\-empty value is returned, pass it as is to the `page` parameter in the next request; this parameter is not returned when there is no more data.
