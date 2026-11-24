# Update memory store

`POST https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/{memory_store_id}`

Update the memory store's name, description, or custom metadata by parameter. The request must include at least one of `name`, `description`, and `metadata`. Parameters that are not passed remain unchanged.

> **Tip**

> When multiple requests update the same parameter at the same time, the value that is successfully processed last by the server takes effect.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**memory_store_id** `string` `Required`  |  Memory store ID

Used to identify the memory store to update. It starts with `memstore-`. Pass the full ID returned by the create or retrieve API. Do not concatenate it yourself.

### Body parameters

**description** `string`  |  Memory store description

The purpose and stored content description used to replace the existing description. Passing an empty string clears the existing description.

> **Note**

> Must not exceed 2048 bytes after UTF\-8 encoding.

**metadata** `object`  |  Custom metadata

Used to replace all existing custom metadata of the memory store. If omitted or set to `null`, the original value is retained. If an empty object `{}` is passed, all keys are cleared.

> **Tip**

> When passing an object, if you need to retain existing keys, query the existing metadata before updating, merge the keys to retain with the new values, and then pass the result. This object can contain up to 64 non\-empty keys. Keys and values must both be strings, and must not exceed 8192 bytes after UTF\-8 serialization.

**name** `string`  |  Memory store name

The memory store name used to replace the existing name. Memory stores with the same name are allowed under the same account and project. They can be distinguished by their respective memory store IDs.

> **Note**

> 1–64 characters in length.

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

**dream_id** `string`  |  Dream task ID

Returned only for dream output memory stores. Indicates the dream ID that created the output memory store. This parameter is not returned for regular memory stores.

**dream_status** `string`  |  Dream task status

Returned only for dream output memory stores. Indicates the status of the dream that created the output memory store. This parameter is not returned for regular memory stores. Options: `pending`, `running`, `completed`, `failed`, `canceled`.

**mountable** `boolean`  |  Whether mountable

Indicates whether the memory store can currently be mounted, read, or modified. Regular memory stores return `true`. Dream output memory stores return `false` when in the `pending` or `running` state, and return `true` when in the `completed`, `failed`, or `canceled` state.

**project_name** `string`  |  Project name

The name of the project to which the memory store actually belongs.

**usage_ready** `boolean`  |  Whether usage is ready

Indicates whether `memory_count` and `storage_bytes` are available as accurate statistics. Regular memory stores return `true`. Dream output memory stores return `false` when in the `pending` or `running` state, and return `true` when in the `completed`, `failed`, or `canceled` state.
