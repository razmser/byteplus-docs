# Create memory store

`POST https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores`

Create a memory store to centrally store an agent's long\-term memory. After creation, you can write memory entries with paths to the store and mount the memory store to a session.

> **Tip**

* > The `memory_count`, `storage_bytes`, and `session_count` of a newly created memory store are all 0.

* > **Common causes of failures**: the name is empty or exceeds 64 characters, the description exceeds 2048 bytes after UTF\-8 encoding, the metadata does not meet the limits, or the current account and project have reached 300 memory stores.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Body parameters

**name** `string` `Required`  |  Memory store name

A name used to identify and manage the memory store. Memory stores with the same name are allowed under the same account and project. They can be distinguished by their respective memory store IDs.

> **Note**

> 1–64 characters in length.

**description** `string`  |  Memory store description

Used to describe the purpose and stored content of the memory store.

> **Note**

> Must not exceed 2048 bytes after UTF\-8 encoding.

**metadata** `object`  |  Custom metadata

Custom key\-value pairs used to tag and classify the memory store.

> **Note**

> Can contain up to 64 non\-empty keys. Keys and values must both be strings, and must not exceed 8192 bytes after UTF\-8 serialization.

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

**project_name** `string`  |  Project name

The name of the project to which the memory store actually belongs.
