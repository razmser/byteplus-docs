# List models supported by Agent Plan

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=ListArkAgentPlanModel&Version=2024-01-01`

Queries the list of models currently supported by the `AgentPlan` package. You can use `Edition` to distinguish between personal and enterprise editions. The result is used to show users on the front end which models they can call within the package.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Edition** `string`  |  Plan edition

Plan edition. Valid values: `personal` (personal edition) or `enterprise` (enterprise edition). If not passed or an unrecognized value is passed, the public model list is returned by default.

## Response parameters

**Datas** `object[]`  |  Model list

List of models supported by the `AgentPlan` package.

**ModelID** `string`  |  Model ID

`Datas.ModelID`

Model ID.
