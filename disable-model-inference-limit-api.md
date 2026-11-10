# Disable model inference limits

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=DisableModelInferenceLimit&Version=2024-01-01`

Disables cumulative inference usage limits for the specified foundation models.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**FoundationModelNames** `string[]` `Required`  |  Foundation model names

The names of the foundation models for which you want to disable inference limits. You can specify up to 100 model names in one request.

**DryRun** `boolean` `Default false`  |  Whether to validate the request only

* `true`: Validates the request parameters without disabling model inference limits. If validation succeeds, the API returns HTTP status code `400` and error code `DryRunOperation`, indicating that parameter validation passed. You can then set `DryRun` to `false` and resend the request.

* `false`: Validates the request parameters and disables model inference limits.

## Response parameters

This API has no response\-specific parameters. On success the HTTP status is `200` and the response `Result` is an empty object `{}`.
