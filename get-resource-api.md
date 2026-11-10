# Get session resource

`GET https://ark.ap-southeast.bytepluses.com/api/v3/sessions/{session_id}/resources/{resource_id}`

Query a single mounted resource by `session_id` and `resource_id`. The response parameters vary by `type`; for subsequent references to a `file` resource, use the `file_id` returned by the query as the source of truth.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**resource_id** `string` `Required`  |  Resource ID

Session resource ID, in the form `sesrsc-...`. It can be obtained from the `id` parameter in the add resource or list resources response.

**session_id** `string` `Required`  |  Session ID

Enter the session ID that the resource belongs to, in the form `sesn-...`.

## Response parameters

**created_at** `string`  |  Mount time

The time when the resource was mounted to the session, in RFC 3339 format.

**id** `string`  |  Resource ID

Session resource ID.

**type** `string`  |  Resource type.

Resource type, used to determine which type\-specific parameters are returned. Options: `file`, `memory_store`, `tos`.

**updated_at** `string`  |  Update time

The time when the session resource information was last updated, in RFC 3339 format.

**access** `string`  |  Access permissions

Resource access permissions. For `file`, this is fixed as `read_only`; for other resources, the corresponding mount configuration is returned. Options: `read_only` / `read_write`.

**file_id** `string`  |  File ID

Returned when `type` is `file`, indicating the currently available file ID.

**memory_store_id** `string`  |  Memory store ID

Returned when `type` is `memory_store`, indicating the memory store ID mounted to the session.

**mount_path** `string`  |  Mount path

Returned when `type` is `file` or `tos`, indicating the mount path of the resource in the session runtime environment. If no TOS mount path was specified when the session was created, this parameter is not returned.

**tos_bucket** `string`  |  TOS bucket

Returned when `type` is `tos`, indicating the name of the TOS bucket where the mount source is located.

**tos_key** `string`  |  TOS directory prefix

Returned when `type` is `tos`, indicating the object prefix of the mounted directory in TOS.

**tos_region** `string`  |  TOS region

Returned when `type` is `tos`, indicating the ID of the region where the mount source is located.
