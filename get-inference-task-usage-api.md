# Get inference task usage

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=GetInferenceTaskUsage&Version=2024-01-01`

Queries usage for a specified asynchronous task in the current account based on `TaskID`.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**TaskID** `string` `Required`  |  Task ID

ID of the asynchronous task to query. It cannot be empty or contain only whitespace.

## Response parameters

**AccountantEndTime** `integer`  |  Billing period end time

Billing period end time as a Unix timestamp in seconds, exclusive. Returned only when `BillingStatus` is exactly `normal`.

**AccountantStartTime** `integer`  |  Billing period start time

Billing period start time as a Unix timestamp in seconds, inclusive. Returned only when `BillingStatus` is exactly `normal`.

**AccountID** `integer`  |  Account ID

ID of the account to which the record belongs. Returned when records exist.

**APIKeyID** `string`  |  API Key ID

API Key ID used by the task. This is not the API Key secret. Returned when records exist.

**BillingStatus** `string`  |  Billing status

Billing status. Returned when records exist.

**DeploymentType** `string`  |  Deployment type

Deployment type. Returned when non\-empty.

**ErrorCode** `string`  |  Error code

Task error code. Returned when non\-empty.

**HasInputVideo** `boolean`  |  Input video indicator

Whether the task contains input video. Returned when records exist. It may return `false`.

**ManagedAgentID** `string`  |  Managed Agent ID

Managed Agent ID. Returned when non\-empty.

**ModelEndpoint** `string`  |  Endpoint ID

Inference endpoint ID. Returned when records exist.

**ModelName** `string`  |  Model name

Model name. Returned when records exist.

**ModelUnitID** `string`  |  Model unit ID

Model unit ID. Returned when non\-empty.

**ModelVersion** `string`  |  Model version

Model version. Returned when records exist.

**OutputTokens** `integer`  |  Output tokens

Number of task output tokens. Returned when records exist. It may return `0`.

**PlanChargedAFP** `string`  |  Plan billed AFP

Plan billing value, preserved as a decimal string. Returned when the record belongs to a Plan, is not overdrafted, and the billing value exists.

**PlanEdition** `string`  |  Plan edition

Plan edition. Returned when the record belongs to a Plan and is not overdrafted.

**PlanIsOverdraft** `boolean`  |  Overdraft indicator

Whether the record is overdrafted. Currently, this field is returned only for non\-overdrafted Plan records, and the value is `false`.

**PlanLastestSubscribeMilestone** `string`  |  Plan subscription milestone

Plan subscription milestone. Returned when the record belongs to a Plan and is not overdrafted.

**PlanPack** `string`  |  Plan package identifier

Plan package identifier. Returned when the record belongs to a Plan and is not overdrafted.

**PlanScene** `string`  |  Plan scenario

Plan scenario. Returned when the record belongs to a Plan and is not overdrafted.

**PlanSeatID** `string`  |  Plan seat ID

Plan seat ID. Returned when the record belongs to a Plan and is not overdrafted.

**PlanSharedPackageID** `string`  |  Shared package ID

Shared package ID. Returned when the record belongs to a Plan and is not overdrafted.

**PlanUsedSharedPackageQuota** `boolean`  |  Shared package quota usage indicator

Whether shared package quota was used. Returned when the record belongs to a Plan and is not overdrafted.

**Resolution** `string`  |  Output resolution

Task output resolution, such as `480p` or `720p`. Returned when records exist.

**SessionID** `string`  |  Session ID

Session ID. Returned when non\-empty.

**TaskID** `string`  |  Task ID

Asynchronous task ID. Returned when records exist.

**UserID** `integer`  |  Caller identity ID

Caller identity ID. Returned when records exist.
