# DisableAutoModelActivation - Turn off automatic activation of new models

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=DisableAutoModelActivation&Version=2024-01-01`

Disables automatic activation of new models. After this feature is disabled, newly released foundation models on the platform will not be automatically activated for the current account.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Response parameters

This API has no response\-specific parameters. On success the HTTP status is `200` and the response `Result` is an empty object `{}`.
