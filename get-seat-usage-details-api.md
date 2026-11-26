# Get model call data details for one or more seats

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=GetSeatUsageDetails&Version=2024-01-01`

Query model call usage details for one or more seats in team edition `AgentPlanTeam` mode. Usage is aggregated by either day or hour, specified by *QueryInterval*. For personal package usage, use the *GetUsageDetails* API instead.

> **Note**

> Only applicable to Agent Plan Team packages. For personal package usage, use GetUsageDetails instead. A single request can include up to 1000 seat IDs. The returned usage is aggregated at the time granularity specified by QueryInterval, by day or by hour, and timestamps are in epoch milliseconds.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**QueryInterval** `string` `Required`  |  Aggregation granularity

The aggregation granularity for usage data. Valid values: `Day`, `Hour`.

**SeatIDs** `string[]` `Required`  |  Seat ID list

The list of seat IDs to query. A single request can include up to 1000 IDs.

**Filter** `object`  |  Filter conditions

Filter conditions for usage details. You can filter by model, assigned user, package tier, and time range.

**EndTime** `string` `Required`  |  End time

`Filter.EndTime`

The end time of the query time window, as a date string in `YYYY-MM-DD` format.

**StartTime** `string` `Required`  |  Start time

`Filter.StartTime`

The start time of the query time window, as a date string in `YYYY-MM-DD` format.

**ModelID** `string`  |  Model ID

`Filter.ModelID`

The model ID to query.

**ModelName** `string`  |  Model card name

`Filter.ModelName`

The model card name to query.

**ObjectName** `string[]`  |  Items to query

`Filter.ObjectName`

The list of externally displayed model and harness names. If not specified, all are returned.

**PlanType** `string`  |  Package tier

`Filter.PlanType`

The package tier to query. Valid values: `Small`, `Medium`, `Large`, `Max`.

**UserName** `string`  |  Bound user name

`Filter.UserName`

The sub\-user name bound to the seat, used to filter usage data during the period when the user was bound.

## Response parameters

**SeatUsageDetails** `object[]`  |  Details list

The list of seat usage details returned in the order of the request.

**Details** `object[]`  |  Call details

`SeatUsageDetails.Details`

The list of call details for each point in time and each model under this seat.

**BillingType** `string`  |  Billing type

`SeatUsageDetails.Details.BillingType`

The billing type of the usage. Valid values: `WithinPlan` (usage within the package), `OutsideOfPlan` (usage outside the package).

**ObjectName** `string`  |  Model/harness

`SeatUsageDetails.Details.ObjectName`

The list of model and harness names to query. If not specified, all are returned.

**Time** `integer`  |  Point in time

`SeatUsageDetails.Details.Time`

The point in time to which this usage record belongs, as a Unix timestamp in milliseconds.

**Unit** `string`  |  Usage unit

`SeatUsageDetails.Details.Unit`

The unit of measurement for *usage*. Common values: `Tokens`, `Images`.

**Usage** `integer`  |  Usage value

`SeatUsageDetails.Details.Usage`

The billable usage at this point in time. The unit is determined by the *Unit* parameter.

**SeatID** `string`  |  Seat ID

`SeatUsageDetails.SeatID`

The seat ID corresponding to this detail record.
