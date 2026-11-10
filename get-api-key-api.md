# GetApiKey

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=GetApiKey&Version=2024-01-01`

Obtains a temporary API key for authenticating requests to specified resources, such as inference endpoints and agents, during its validity period.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**DurationSeconds** `integer` `Required`  |  Validity period

The validity period of the temporary API key, in seconds.

**Range**: `[0, 2592000]`

**ResourceIds** `string[]` `Required`  |  Resource ID list

The list of resource IDs for the type specified by *ResourceType*.

* When *ResourceType* is `endpoint`, obtain the endpoint ID from ModelArk console \> Inference.

* When *ResourceType* is `bot`, obtain the bot ID from ModelArk console \> Application.

**ResourceType** `string` `Required`  |  Resource type

The resource type. Possible values:

* `endpoint`: Model inference endpoint

* `bot`: Agent

* `presetendpoint`: Preset inference endpoint

**ProjectName** `string`  |  Project name

The project name. Required when *ResourceType* is `presetendpoint`.

## Response parameters

**ApiKey** `string`  |  Temporary API key

The generated temporary API key string, which can be used to access ModelArk resources during the validity period specified by *DurationSeconds*.

**ExpiredTime** `integer`  |  Expiration time

The expiration time of the temporary API key, as a Unix timestamp in seconds.
