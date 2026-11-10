# Delete file

`DELETE https://ark.ap-southeast.bytepluses.com/api/v3/files/{file_id}`

Deletes an uploaded file based on the file ID and removes the file from storage. After deletion, the file can no longer be used as model inference input and cannot be restored.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**file_id** `string` `Required`  |  File ID

The ID of the file to delete. You can obtain it from the `id` parameter returned by the [Upload file](upload-files-api.md) API.

> **Note**

> Files cannot be restored after deletion. Make sure the file is no longer referenced by inference tasks before deleting it.

## Response parameters

**deleted** `boolean`  |  Deletion result

Whether the file was deleted successfully. The value `true` indicates that the file has been successfully removed from storage.

**id** `string`  |  File ID

The unique identifier (ID) of the deleted file, which is the same as the `file_id` passed in the request.

**object** `string`  |  Object type

The object type. The value is fixed to `file`, which identifies the resource type corresponding to the returned result.
