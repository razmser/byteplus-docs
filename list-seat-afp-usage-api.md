# List seat AFP quota usage

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=ListSeatAFPUsage&Version=2024-01-01`

Queries the AFP quota usage of seats in `AgentPlanTeam` mode for team packages with pagination. Supports filtering by seat ID, bound user, and package tier, and sorting by usage in a specified window or by creation time.

> **Note**

> Only applicable to Agent Plan Team packages. For personal package usage, use GetAFPUsage instead. For `Filter`, you can pass in up to 1000 items each for `SeatIDs`, `UserNames`, and `PlanType` in a single request. Multiple filter conditions are combined with AND. The returned Quota / Used values are in AFP, and timestamps are epoch milliseconds.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**ProjectName** `string` `Required`  |  Project name

The name of the project to which the resource belongs. If the resource is not in the default project, specify the actual project name.

**Filter** `object`  |  Filter conditions

Seat filter conditions. Multiple filter conditions are combined with AND.

**PlanType** `string[]`  |  Package tier

`Filter.PlanType`

Filters by package tier. You can specify multiple tiers. Valid values: `Small`, `Medium`, `Large`, `Max`.

**SeatIDs** `string[]`  |  Seat ID list

`Filter.SeatIDs`

Filters by seat ID. You can pass in up to 1000 IDs in a single request.

**UserNames** `string[]`  |  Bound user name

`Filter.UserNames`

Filters by the user name bound to the seat. You can pass in up to 1000 user names in a single request.

**PageNumber** `integer` `Default 1`  |  Starting page number

The starting page number for the paginated query. Page numbers start from 1.

**Min**: `1`

**PageSize** `integer` `Default 10`  |  Page size

The number of seats returned per page in a paginated query.

**Range**: `[10, 100]`

**SortBy** `string` `Default CreateTime`  |  Sort parameter

Sort parameter. Value:

* `AFPFiveHour`: sorts by 5\-hour AFP usage;

* `AFPWeekly`: sorts by weekly AFP usage;

* `AFPMonthly`: sorts by monthly AFP usage;

* `CreateTime`: sorts by creation time.

**SortOrder** `string` `Default Desc`  |  Sort order

Sort order. Value:

* `Asc`: sorts in ascending order;

* `Desc`: sorts in descending order.

## Response parameters

**Items** `object[]`  |  Usage list

The list of seat AFP usage for the current page.

**AFPDaily** `object`  |  1\-day usage

`Items.AFPDaily`

AFP usage in the rolling 1\-day window.

**Quota** `string`  |  Total window quota

`Items.AFPDaily.Quota`

Total AFP quota in this window.

**ResetTime** `integer`  |  Next reset time

`Items.AFPDaily.ResetTime`

The time when the quota is next reset, as a Unix millisecond timestamp.

**SubscribeTime** `integer`  |  Window start time

`Items.AFPDaily.SubscribeTime`

The start time of the current billing window, as a Unix millisecond timestamp.

**Used** `string`  |  Used quota in the window

`Items.AFPDaily.Used`

AFP quota consumed in this window.

**AFPFiveHour** `object`  |  5\-hour usage

`Items.AFPFiveHour`

AFP usage in the rolling 5\-hour window.

**Quota** `string`  |  Total window quota

`Items.AFPFiveHour.Quota`

Total AFP quota in this window.

**ResetTime** `integer`  |  Next reset time

`Items.AFPFiveHour.ResetTime`

The time when the quota is next reset, as a Unix millisecond timestamp.

**SubscribeTime** `integer`  |  Window start time

`Items.AFPFiveHour.SubscribeTime`

The start time of the current billing window, as a Unix millisecond timestamp.

**Used** `string`  |  Used quota in the window

`Items.AFPFiveHour.Used`

AFP quota consumed in this window.

**AFPMonthly** `object`  |  1\-month usage

`Items.AFPMonthly`

AFP usage in the rolling 1\-month window.

**Quota** `string`  |  Total window quota

`Items.AFPMonthly.Quota`

Total AFP quota in this window.

**ResetTime** `integer`  |  Next reset time

`Items.AFPMonthly.ResetTime`

The time when the quota is next reset, as a Unix millisecond timestamp.

**SubscribeTime** `integer`  |  Window start time

`Items.AFPMonthly.SubscribeTime`

The start time of the current billing window, as a Unix millisecond timestamp.

**Used** `string`  |  Used quota in the window

`Items.AFPMonthly.Used`

AFP quota consumed in this window.

**AFPWeekly** `object`  |  1\-week usage

`Items.AFPWeekly`

AFP usage in the rolling 1\-week window.

**Quota** `string`  |  Total window quota

`Items.AFPWeekly.Quota`

Total AFP quota in this window.

**ResetTime** `integer`  |  Next reset time

`Items.AFPWeekly.ResetTime`

The time when the quota is next reset, as a Unix millisecond timestamp.

**SubscribeTime** `integer`  |  Window start time

`Items.AFPWeekly.SubscribeTime`

The start time of the current billing window, as a Unix millisecond timestamp.

**Used** `string`  |  Used quota in the window

`Items.AFPWeekly.Used`

AFP quota consumed in this window.

**PlanType** `string`  |  Package tier

`Items.PlanType`

The package tier that this seat belongs to. Valid values: `Small`, `Medium`, `Large`, `Max`.

**SeatID** `string`  |  Seat ID

`Items.SeatID`

The seat ID associated with this usage record.

**PageNumber** `integer`  |  Current page number

The page number corresponding to the current returned results.

**PageSize** `integer`  |  Page size

The page size corresponding to the current returned results.

**ProjectName** `string`  |  Project name

The name of the project to which the resource belongs. If the resource is not in the default project, specify the actual project name.

**TotalCount** `integer`  |  Total number of records

The total number of seat records that match the filter conditions.
