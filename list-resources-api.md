# List session resources

`GET https://ark.ap-southeast.bytepluses.com/api/v3/sessions/{session_id}/resources`

Query the mounted resource list of the specified session. This API returns all results, with a maximum of 1,000 entries and no pagination parameters.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**session_id** `string` `Required`  |  Session ID

Session ID. A string that starts with `sesn-`.

## Response parameters

**data** `object[]`  |  Resource list

The list of resources currently mounted to the specified session.

File resource `object`

A file uploaded through the Files API and mounted to the session.

**created_at** `string`  |  Mount time

`data.created_at`

The time when the resource was mounted to the session, in RFC 3339 format.

**id** `string`  |  Resource ID

`data.id`

Session resource ID.

**type** `string`  |  Resource type.

`data.type`

Resource type, fixed as `file`.

**updated_at** `string`  |  Update time

`data.updated_at`

The time when the session resource information was last updated, in RFC 3339 format.

**access** `string`  |  Access permissions

`data.access`

File access permissions, fixed as `read_only`.

**file_id** `string`  |  File ID

`data.file_id`

The currently available file ID.

**mount_path** `string`  |  Mount path

`data.mount_path`

The full mount path of the file in the session runtime environment.

Memory store resource `object`

A memory store mounted to the session.

**created_at** `string`  |  Mount time

`data.created_at`

The time when the resource was mounted to the session, in RFC 3339 format.

**id** `string`  |  Resource ID

`data.id`

Session resource ID.

**type** `string`  |  Resource type.

`data.type`

Resource type, fixed as `memory_store`.

**updated_at** `string`  |  Update time

`data.updated_at`

The time when the session resource information was last updated, in RFC 3339 format.

**access** `string`  |  Access permissions

`data.access`

The session's access permissions for the memory store.

* `read_only`: The agent can only read the memory store.

**memory_store_id** `string`  |  Memory store ID

`data.memory_store_id`

The ID of the memory store mounted to the session.

TOS resource `object`

A TOS object directory mounted to the session.

**created_at** `string`  |  Mount time

`data.created_at`

The time when the resource was mounted to the session, in RFC 3339 format.

**id** `string`  |  Resource ID

`data.id`

Session resource ID.

**type** `string`  |  Resource type.

`data.type`

Resource type, fixed as `tos`.

**updated_at** `string`  |  Update time

`data.updated_at`

The time when the session resource information was last updated, in RFC 3339 format.

**access** `string`  |  Access permissions

`data.access`

The session's access permissions for the TOS directory. Options: `read_only`, `read_write`.

**mount_path** `string`  |  Mount path

`data.mount_path`

The mount path of the TOS directory in the session runtime environment. If no mount path was specified when the session was created, this parameter is not returned.

**tos_bucket** `string`  |  TOS bucket

`data.tos_bucket`

The bucket name where the TOS mount source is located.

**tos_key** `string`  |  TOS directory prefix

`data.tos_key`

The object prefix of the mounted directory in TOS.

**tos_region** `string`  |  TOS region

`data.tos_region`

The ID of the region where the TOS mount source is located.
