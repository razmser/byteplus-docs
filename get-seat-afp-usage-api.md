# Get AFP quota usage for one or more seats

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=GetSeatAFPUsage&Version=2024-01-01`

Query the AFP quota usage of one or more seats in `AgentPlanTeam` mode for the enterprise edition, including the quota and used amount for four rolling windows: 5 hours, the last day, the last week, and the last month. For personal usage, use the *GetAFPUsage* API instead.

> **Note**

> Only applicable to Agent Plan Team packages. For personal package usage, use GetAFPUsage instead. A single request can include up to 1000 seat IDs. The returned Quota / Used values are in AFP, and timestamps are epoch milliseconds.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**SeatIDs** `string[]` `Required`  |  Seat ID list

The list of seat IDs to query. A single request can include up to 1000 IDs.

## Response parameters

**SeatAFPUsages** `object[]`  |  Usage list

The list of seat AFP usage returned in the order of the request.

**AFPDaily** `object`  |  1\-day usage

`SeatAFPUsages.AFPDaily`

AFP usage in the rolling 1\-day window.

Applies to image generation models, video generation models, speech models, and Harness. The capabilities above are not subject to the 5\-hour quota or weekly quota limits, and are limited only by the model daily quota and the package monthly quota. For models to which daily quota limits do not apply, this parameter returns `0`.

**Quota** `string`  |  Total window quota

`SeatAFPUsages.AFPDaily.Quota`

Total AFP quota in this window.

**ResetTime** `integer`  |  Next reset time

`SeatAFPUsages.AFPDaily.ResetTime`

Next quota refresh time, as a Unix timestamp in milliseconds.

**SubscribeTime** `integer`  |  Window start time

`SeatAFPUsages.AFPDaily.SubscribeTime`

Start time of the current quota refresh, as a Unix timestamp in milliseconds.

**Used** `string`  |  Used quota in the window

`SeatAFPUsages.AFPDaily.Used`

AFP quota consumed in this window.

**AFPFiveHour** `object`  |  5\-hour usage

`SeatAFPUsages.AFPFiveHour`

AFP usage in the rolling 5\-hour window.

**Quota** `string`  |  Total window quota

`SeatAFPUsages.AFPFiveHour.Quota`

Total AFP quota in this window.

**ResetTime** `integer`  |  Next reset time

`SeatAFPUsages.AFPFiveHour.ResetTime`

Next quota refresh time, as a Unix timestamp in milliseconds.

**SubscribeTime** `integer`  |  Window start time

`SeatAFPUsages.AFPFiveHour.SubscribeTime`

Start time of the current quota refresh, as a Unix timestamp in milliseconds.

**Used** `string`  |  Used quota in the window

`SeatAFPUsages.AFPFiveHour.Used`

AFP quota consumed in this window.

**AFPMonthly** `object`  |  1\-month usage

`SeatAFPUsages.AFPMonthly`

AFP usage in the rolling 1\-month window.

**Quota** `string`  |  Total window quota

`SeatAFPUsages.AFPMonthly.Quota`

Total AFP quota in this window.

**ResetTime** `integer`  |  Next reset time

`SeatAFPUsages.AFPMonthly.ResetTime`

Next quota refresh time, as a Unix timestamp in milliseconds.

**SubscribeTime** `integer`  |  Window start time

`SeatAFPUsages.AFPMonthly.SubscribeTime`

Start time of the current quota refresh, as a Unix timestamp in milliseconds.

**Used** `string`  |  Used quota in the window

`SeatAFPUsages.AFPMonthly.Used`

AFP quota consumed in this window.

**AFPWeekly** `object`  |  1\-week usage

`SeatAFPUsages.AFPWeekly`

AFP usage in the rolling 1\-week window.

**Quota** `string`  |  Total window quota

`SeatAFPUsages.AFPWeekly.Quota`

Total AFP quota in this window.

**ResetTime** `integer`  |  Next reset time

`SeatAFPUsages.AFPWeekly.ResetTime`

Next quota refresh time, as a Unix timestamp in milliseconds.

**SubscribeTime** `integer`  |  Window start time

`SeatAFPUsages.AFPWeekly.SubscribeTime`

Start time of the current quota refresh, as a Unix timestamp in milliseconds.

**Used** `string`  |  Used quota in the window

`SeatAFPUsages.AFPWeekly.Used`

AFP quota consumed in this window.

**PlanType** `string`  |  Package tier

`SeatAFPUsages.PlanType`

The package tier that this seat belongs to. Valid values: `Small`, `Medium`, `Large`, `Max`.

**SeatID** `string`  |  Seat ID

`SeatAFPUsages.SeatID`

The seat ID associated with this usage record.
