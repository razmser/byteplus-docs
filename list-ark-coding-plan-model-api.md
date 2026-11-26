# List models supported by Coding Plan

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=ListArkCodingPlanModel&Version=2024-01-01`

List models supported by Coding Plan

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Response parameters

**Datas** `object[]`  |  Model list

List of models currently available for the Coding Plan package.

**ModelID** `string`  |  Model ID

`Datas.ModelID`

Model ID, for example `dola-seed-2-1-turbo-260628`. Pass it to the `model` parameter when calling the model.
