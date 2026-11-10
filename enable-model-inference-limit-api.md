# Enable model inference limits

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=EnableModelInferenceLimit&Version=2024-01-01`

Enables cumulative inference usage limits for the specified foundation models.

> **Tip**

> After enabling and then disabling an inference limit for a foundation model, wait at least two hours before enabling the limit again.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**FoundationModelNames** `string[]` `Required`  |  Foundation model names

The names of the foundation models for which you want to enable inference limits. You can specify up to 100 model names in one request.

**Limit** `integer` `Required`  |  Cumulative inference usage limit

The cumulative inference usage limit for the model. The value must be greater than 0.

**DryRun** `boolean` `Default false`  |  Whether to validate the request only

* `true`: Validates the request parameters without enabling model inference limits. If validation succeeds, the API returns HTTP status code `400` and error code `DryRunOperation`, indicating that parameter validation passed. You can then set `DryRun` to `false` and resend the request.

* `false`: Validates the request parameters and enables model inference limits.

## Response parameters

This API has no response\-specific parameters. On success the HTTP status is `200` and the response `Result` is an empty object `{}`.
