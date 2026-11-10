# Query model inference limits

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=ListModelInferenceLimits&Version=2024-01-01`

Queries the cumulative inference usage limits and current usage of the specified foundation models.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**FoundationModelNames** `string[]` `Required`  |  Foundation model names

The names of the foundation models whose inference limits you want to query. You can specify up to 100 model names in one request. Models that are no longer available are omitted from the response.

## Response parameters

**Items** `object[]`  |  Model inference limits

A list of model inference limit details.

**CurrentUsage** `integer`  |  Current cumulative inference usage

`Items.CurrentUsage`

The model's current cumulative inference usage.

**FoundationModelName** `string`  |  Foundation model name

`Items.FoundationModelName`

The foundation model name.

**Limit** `integer`  |  Cumulative inference usage limit

`Items.Limit`

The cumulative inference usage limit for the model. This field is not returned when the inference limit is disabled.

**StartTime** `string`  |  Limit effective time

`Items.StartTime`

The time when the inference limit takes effect, in RFC3339 format.
