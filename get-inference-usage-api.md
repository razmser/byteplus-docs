# Query inference usage

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=GetInferenceUsage&Version=2024-01-01`

Queries inference usage with filters across dimensions such as time range, endpoint, and model. Returns usage data such as token consumption and request counts.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**EndTime** `string` `Required`  |  End date

Query end date in `yyyy-MM-dd` format, in China Standard Time (UTC+8). It must be greater than or equal to *StartTime*.

**QueryInterval** `string` `Required`  |  Query granularity

Query granularity. Valid values:

* `Day`: Aggregate by day.

* `Hour`: Aggregate by hour.

**StartTime** `string` `Required`  |  Start date

Query start date in `yyyy-MM-dd` format, in China Standard Time (UTC+8).

**Filters** `object[]`  |  Filters

An array of filters supporting fields such as authentication token, model name, endpoint ID, and batch job ID. Multiple filters are combined with AND.

**Key** `string` `Required`  |  Filter field

`Filters.Key`

Filtering field. Currently supported fields:

> **Note**

> As part of a security upgrade, usage queries will transition to queries based on `ApiKeyID`.

* > API keys created on or after 2026\-08\-12 support usage queries only through `ApiKeyID`; `AuthToken` queries are no longer supported.

* > Existing API keys created before 2026\-08\-12 currently support both `AuthToken` and `ApiKeyID` usage queries. The `AuthToken` query method will be discontinued soon. **To avoid service disruption, update your application logic to query usage through****`ApiKeyID`****as soon as possible.**

* `AuthToken`: Authentication token.

   * For JWT token authentication, defaults to `****`.

   * For API key authentication:

      * Legacy API key format: First 8 characters + `****` + last 12 characters, for example, `37efcc63****92c8fe4c880e`.

      * New ModelArk API key format: First 3 characters + `****` + last 5 characters, for example, `ark****d5b88`.

* `ApikeyID`: Resource ID of the API key.

> **Description**

> When usage is aggregated by API key, different names may appear. **They refer to the same unique API key identifier** and differ only in naming:

* > Resource ID: The column name displayed on the **API Key Management** page in the ModelArk console.

* > `ApikeyID` / `apikey_id`: The split\-billing dimension names in the split\-billing system and the key names in the billing query API's *Filters* parameter.

> On the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey) page in the ModelArk console, open the API key details to view and copy its Resource ID. The Resource ID is not the API key secret itself. Do not pass the API key secret as `ApiKeyID`.

* `ModelUnitID`: Model unit ID.

* `BillingStatus`: Billing method. Valid values: `no_need_billing` (no payment required, generally trial usage before service activation), `free_for_model_unit` (model unit usage), `free_for_free_quota` / `free_for_limit_boundary` (usage beyond the worry\-free trial boundary), `normal` (standard postpaid pay\-as\-you\-go billing), and `free_for_viptier` (TPM guarantee package usage).

* `ModelName`: Model name.

* `ModelVersion`: Model version.

* `ModelEndpoint`: Endpoint ID.

* `BatchJobID`: Batch job ID.

* `EndpointName`: Endpoint name.

* `BatchType`: Batch inference type.

**ValueLike** `string` `Required`  |  Approximate match value

`Filters.ValueLike`

Approximate match value, used for filtering by rules such as prefix or substring matching. *Values* and *ValueLike* cannot both be supplied.

**Values** `string[]` `Required`  |  Filter values

`Filters.Values`

List of filter values. If the length of *Values* is 0, no filtering is performed by *Key*, but the returned columns still include that *Key*.

**ProjectName** `string` `Default default`  |  Project name

Name of the project to which the resource belongs. If the resource is not in the default project, you must specify the correct project name.

**ShowWindowDetail** `boolean` `Default false`  |  Show window details

Whether to include billing window details in the results, such as window sequence, window description, and window token limits.

## Response parameters

**Data** `string[]`  |  Usage data

Usage data as a two\-dimensional array. Each row represents a record, with columns in the same order as *Fields*.

**DataCount** `integer`  |  Row count

Number of returned data rows, equal to the length of the *Data* array.

**Fields** `object[]`  |  Column definitions

Column definitions for the returned data, describing the type and name of each column in the *Data* array.

**Name** `string`  |  Column name

`Fields.Name`

Column name. Includes all fields in the *Filters* request parameter. The following are included by default:

* `AccountID`: Account ID.

* `Day`: Date.

* `Hour`: Hour.

* `InputTokens`: Number of input tokens.

* `OutputTokens`: Number of output tokens.

* `TotalTokens`: Total number of tokens.

* `ReqCnt`: Number of requests.

When data is returned by window, the following are also included:

* `BillingStatus`: Billing type.

* `WindowMatchOrder`: Window sequence.

* `WindowDescription`: Window description.

* `WindowMaxInputTokens`: Maximum input tokens for the window.

* `WindowMaxOutputTokens`: Maximum output tokens for the window.

**Type** `string`  |  Column type

`Fields.Type`

Column data type, such as `string` or `integer`.
