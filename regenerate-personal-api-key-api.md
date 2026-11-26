# Rotate personal API key

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=RegeneratePersonalApiKey&Version=2024-01-01`

Rotate the API key for the personal `AgentPlan` package. After rotation, the original API key becomes invalid immediately, and requests that use the old key will fail authentication. After rotation succeeds, obtain the new API key through the console or by calling the [GetApiKey](get-api-key-api.md) API. For security reasons, this API does not return the new key in the response body. Use this for emergency rotation after a key leak or for regular security rotation.

> **Note**

> The rotation operation is irreversible. The old API key becomes invalid immediately after rotation is complete. Before calling this API, make sure your business processes are ready for the key replacement. After rotation succeeds, obtain the new API key through the console or by calling [Get temporary API key](get-api-key-api.md). For security reasons, this API does not return the new key in the response body.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Plan** `string` `Required`  |  Product name

The package product name for the API key to rotate. Valid values: `AgentPlan` (Agent Plan personal edition).

**ProjectName** `string`  |  Project name

The name of the project space to which the API key belongs.

## Response parameters

**Message** `string`  |  Failure reason

The specific reason why rotation failed. An empty string if rotation succeeds.

**Status** `string`  |  Rotation result

Rotation result status. Valid values: `Success` (rotation succeeded; the new API key has taken effect and the old key has become invalid), `Failed` (rotation failed; the original API key remains valid. For details, see the *Message* parameter).
