# Get inference request usage

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=GetInferenceRequestUsage&Version=2024-01-01`

Queries detailed usage for a synchronous inference request in the current account for a specified hour based on `RequestID`.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**QueryHour** `string` `Required`  |  Query hour

Start time of the hour that contains the request record, in `YYYY-MM-DD HH:00:00` format. Minutes and seconds must be `00`. Beijing time (Asia/Shanghai) is used by default; other environments use their configured query time zone.

**RequestID** `string` `Required`  |  Request ID

ID of the synchronous inference request to query.

> **Note**

> `RequestID` is the `id` returned in the response body for a synchronous inference request.

## Response parameters

**Records** `object[]`  |  Usage details

Array of synchronous request usage details. A single `RequestID` may correspond to multiple usage records. This API returns all details in this array.

**AccountantEndTime** `integer`  |  Billing period end time

`Records.AccountantEndTime`

Billing period end time as a Unix timestamp in seconds, exclusive. Returned only when `BillingStatus` is exactly `normal`.

**AccountantStartTime** `integer`  |  Billing period start time

`Records.AccountantStartTime`

Billing period start time as a Unix timestamp in seconds, inclusive. Returned only when `BillingStatus` is exactly `normal`.

**AccountID** `integer`  |  Account ID

`Records.AccountID`

ID of the account to which the record belongs. Returned when records exist.

**APIKeyID** `string`  |  API Key ID

`Records.APIKeyID`

API Key ID used by the request. This is not the API Key secret. Returned when records exist.

**AudioCachedTokens** `integer`  |  Cached audio tokens

`Records.AudioCachedTokens`

Number of cached audio tokens. Returned when the token usage group is returned.

**AudioOriginalDurationMs** `integer`  |  Original audio duration

`Records.AudioOriginalDurationMs`

Original audio duration in milliseconds. Returned when the audio usage group is returned.

**AudioTaskType** `string`  |  Audio task type

`Records.AudioTaskType`

Audio task type. Returned when the audio usage group is returned.

**AudioTokens** `integer`  |  Audio tokens

`Records.AudioTokens`

Number of audio tokens. Returned when the token usage group is returned.

**BatchJobID** `string`  |  Batch job ID

`Records.BatchJobID`

Batch job ID. Returned when non\-empty.

**BillingStatus** `string`  |  Billing status

`Records.BillingStatus`

Billing status, such as `normal`. Returned when records exist.

**CacheHitTokens** `integer`  |  Cache\-hit tokens

`Records.CacheHitTokens`

Number of cache\-hit tokens. Returned when the token usage group is returned.

**DeploymentType** `string`  |  Deployment type

`Records.DeploymentType`

Deployment type. Returned when non\-empty.

**ErrorCode** `string`  |  Error code

`Records.ErrorCode`

Error code in the original inference request record. Returned when non\-empty.

**ImageNumber** `integer`  |  Image count

`Records.ImageNumber`

Number of images. Returned when the image usage group is returned.

**InputImageCount** `integer`  |  Input image count

`Records.InputImageCount`

Number of input images. Returned when the image usage group is returned.

**InputTokens** `integer`  |  Input tokens

`Records.InputTokens`

Number of input tokens. Returned when the token usage group is returned.

**IsBatch** `boolean`  |  Batch request indicator

`Records.IsBatch`

Whether the request is a batch request. Returned only when the value is `true`.

**IsFast** `boolean`  |  Fast request indicator

`Records.IsFast`

Whether the request is a fast request. Returned only when the value is `true`.

**IsFlex** `boolean`  |  Flex request indicator

`Records.IsFlex`

Whether the request is a flex request. Returned only when the value is `true`.

**IsOffpeak** `boolean`  |  Off\-peak request indicator

`Records.IsOffpeak`

Whether the request is an off\-peak request. Returned only when the value is `true`.

**ManagedAgentID** `string`  |  Managed Agent ID

`Records.ManagedAgentID`

Managed Agent ID. Returned when non\-empty.

**ModelEndpoint** `string`  |  Endpoint ID

`Records.ModelEndpoint`

Inference endpoint ID. Returned when records exist.

**ModelName** `string`  |  Model name

`Records.ModelName`

Model name. Returned when records exist.

**ModelUnitID** `string`  |  Model unit ID

`Records.ModelUnitID`

Model unit ID. Returned when non\-empty.

**ModelVersion** `string`  |  Model version

`Records.ModelVersion`

Model version. Returned when records exist.

**OutputTokens** `integer`  |  Output tokens

`Records.OutputTokens`

Number of output tokens. Returned when the token usage group is returned.

**PlanChargedAFP** `string`  |  Plan billed AFP

`Records.PlanChargedAFP`

Plan billing value, preserved as a decimal string. Returned when the record belongs to a Plan, is not overdrafted, and the billing value exists.

**PlanEdition** `string`  |  Plan edition

`Records.PlanEdition`

Plan edition. Returned when the record belongs to a Plan and is not overdrafted.

**PlanPack** `string`  |  Plan package identifier

`Records.PlanPack`

Plan package identifier. Returned when the record belongs to a Plan and is not overdrafted.

**PlanScene** `string`  |  Plan scenario

`Records.PlanScene`

Plan scenario. Returned when the record belongs to a Plan and is not overdrafted.

**PlanSeatID** `string`  |  Plan seat ID

`Records.PlanSeatID`

Plan seat ID. Returned when the record belongs to a Plan and is not overdrafted.

**PlanSharedPackageID** `string`  |  Shared package ID

`Records.PlanSharedPackageID`

Shared package ID. Returned when the record belongs to a Plan and is not overdrafted.

**PlanUsedSharedPackageQuota** `boolean`  |  Shared package quota usage indicator

`Records.PlanUsedSharedPackageQuota`

Whether shared package quota was used. Returned when the record belongs to a Plan and is not overdrafted. It may return `false`.

**RequestID** `string`  |  Request ID

`Records.RequestID`

Synchronous inference request ID. Returned when records exist.

**SessionID** `string`  |  Session ID

`Records.SessionID`

Session ID. Returned when non\-empty.

**Timestamp** `integer`  |  Request reporting time

`Records.Timestamp`

Request reporting time as a Unix timestamp in seconds. Returned when records exist.

**UserID** `integer`  |  Caller identity ID

`Records.UserID`

Caller identity ID. Returned when records exist.
