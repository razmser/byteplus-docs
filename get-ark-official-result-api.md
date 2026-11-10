# GetArkOfficialResult - Obtain ModelArk artifact verification result

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=GetArkOfficialResult&Version=2024-01-01`

Retrieves the final conclusion of official ModelArk output verification by query ID, including whether the content is official output, the model name, and other information.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**QueryID** `string` `Required`  |  Query ID

The query ID returned when the official verification query was created.

## Response parameters

**IsOfficial** `string`  |  Whether the content is official output

The verification conclusion, indicating whether the content is official output.

**Message** `string`  |  Additional message

Additional information about the verification result.

**ModelName** `string`  |  Model name

The name of the model used to generate the content.

**QueryID** `string`  |  Query ID

The unique ID of this verification query.

**Resolution** `string`  |  Resolution

The resolution of the content being verified.

**ResourceType** `string`  |  Resource type

The resource type of the content being verified.

**Size** `string`  |  File size

The file size of the content being verified.

**Status** `string`  |  Query status

The current status of the verification query.
