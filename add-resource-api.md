# Add session resource

`POST https://ark.ap-southeast.bytepluses.com/api/v3/sessions/{session_id}/resources`

Add a file from the Files API to the managed runtime environment of an existing session. Currently, only `file` is supported, and it is mounted as read\-only.

After the resource is added successfully, use the `file_id` in the response to query or reference the file. This API is applicable only to sessions bound to a managed runtime environment.

> **Tip**

> After mounting, the agent reads the file through `mount_path`; if this parameter is not specified, the path is `/mnt/session/uploads/<filename>`. If you also need to pass the same file into the model context, reference the `file_id` in the response in `content[]` of `user.message`.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**session_id** `string` `Required`  |  Session ID

Enter the ID of the session to which you want to add the resource, in the format `sesn-...`.

### Body parameters

**file_id** `string` `Required`  |  File ID

The ID of the file to mount to the session, obtained after uploading the file through the Files API.

> **Tip**

> The file cannot exceed 100 MiB. When uploading a file through the Files API, `purpose` must be `user_data` or `agent`; the file ID format must match `^[A-Za-z0-9_-]{1,128}$`.

**type** `string` `Required`  |  Resource type.

The type of resource to add, fixed as `file`, indicating a file uploaded through the Files API.

**mount_path** `string`  |  Mount path

The path of the file in the managed runtime environment. If omitted or an empty value is passed, the original file name used during file upload is used, and the final path is `/mnt/session/uploads/<filename>`.

> **Tip**

> A leading `/` in the path is automatically removed. The path must not contain standalone `..` or `.` path segments, such as `a/../b` and `a/./b`, which are both invalid; it must also not contain backslashes, double slashes (`//`), or control characters, and must not end with `/`.

## Response parameters

**access** `string`  |  Access mode

The session's access permission for the file, fixed as `read_only`, indicating that the agent can read the file but cannot modify it.

**type** `string`  |  Resource type.

The type of the mounted resource, fixed as `file`, indicating a file uploaded through the Files API.

**created_at** `string`  |  Creation time

The time when the file was mounted to the session, in RFC 3339 format.

**file_id** `string`  |  File ID

The currently available file ID. This value may differ from the `file_id` submitted in the request; use the value in the response for subsequent queries and references.

**id** `string`  |  Resource ID

The session resource ID, in the format `sesrsc-...`.

**mount_path** `string`  |  Mount path

The full mount path of the file in the managed runtime environment.

> **Tip**

> When `mount_path` is specified, the return value consists of `/mnt/session/uploads/` and the normalized request path. For example, if `data/report.pdf` is passed, `/mnt/session/uploads/data/report.pdf` is returned. If not specified, `/mnt/session/uploads/<filename>` is returned.

**updated_at** `string`  |  Update time

The time when the session resource information was last updated, in RFC 3339 format.
