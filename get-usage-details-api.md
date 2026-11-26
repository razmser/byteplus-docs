# Get package usage details

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=GetUsageDetails&Version=2024-01-01`

Query model call usage details for the personal edition `AgentPlan` package. Usage is aggregated by either day or hour, specified by *QueryInterval*. For team seat usage, use the *GetSeatUsageDetails* API instead.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**QueryInterval** `string` `Required`  |  Aggregation granularity

The aggregation granularity for usage data. Valid values: `Day`, `Hour`.

**Filter** `object`  |  Filter conditions

Filter conditions for usage details. You can filter by model name, package tier, and time range.

**EndTime** `string` `Required`  |  End date

`Filter.EndTime`

The end time, in `YYYY-MM-DD` format. A single query can cover up to 31 days, and historical data is available for up to 6 months.

**StartTime** `string` `Required`  |  Start date

`Filter.StartTime`

The start date of the query time window, in `YYYY-MM-DD` format.

**ObjectName** `string[]`  |  Model name

`Filter.ObjectName`

The customer\-facing model or harness names. You can pass multiple values.

**PlanType** `integer[]`  |  Package tier

`Filter.PlanType`

Limits the package tiers to query. You can pass multiple values. The integer enum values `1`, `2`, `3`, and `4` represent `Small`, `Medium`, `Large`, and `Max`, respectively.

## Response parameters

**Details** `object[]`  |  Details list

A list of call details. Each element represents the usage record of a model at a specific point in time.

**BillingType** `string`  |  Billing type

`Details.BillingType`

Billing type. Value:

* `WithinPlan`: usage within the package;

* `OutsideOfPlan`: usage outside the package.

**ObjectName** `string`  |  Model name

`Details.ObjectName`

Object name: the customer\-facing name of the model or harness.

**Time** `integer`  |  Point in time

`Details.Time`

The corresponding consumption date or point in time, as a Unix timestamp in milliseconds.

**Unit** `string`  |  Usage unit

`Details.Unit`

Usage unit. Value:

* `Tokens`: number of tokens;

* `Images`: number of images.

**Usage** `integer`  |  Usage value

`Details.Usage`

The billable usage at this point in time. The unit is determined by the *Unit* parameter.
