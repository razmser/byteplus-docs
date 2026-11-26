# ListSeatInfoUsages - Query seat information and usage

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=ListSeatInfoUsages&Version=2024-01-01`

Query information and usage for multiple seats in batches.

> **Tip**

> This API is intended for scenarios where you need to query seat usage in batches. You must pass in the `SeatID` list obtained from the `ListSeatInfos` API as a request parameter.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**SeatIDs** `string[]` `Required`  |  Seat ID list

The list of seat IDs to query. A single request can include up to 1000 IDs.

**ProjectName** `string` `Default default`  |  Project name

Project name. Enter the actual project name if the resource is not in the default project.

## Response parameters

**Data** `object[]`  |  Seat usage list

An array of seat details that match the query criteria.

**AccountID** `integer`  |  Main account ID

`Data.AccountID`

The ID of the BytePlus main account to which the seat belongs.

**MonthlyResetMilestone** `integer`  |  Monthly quota refresh time

`Data.MonthlyResetMilestone`

The time when the monthly quota resources are refreshed.

**MonthlySubscribeMilestone** `integer`  |  Monthly quota refresh start time

`Data.MonthlySubscribeMilestone`

The start time for refreshing the monthly quota resources.

**MonthlyUsage** `string`  |  Accumulated usage this month

`Data.MonthlyUsage`

The seat's accumulated usage for the current billing month, as defined by the subscription cycle.

**ProjectName** `string`  |  Project name

`Data.ProjectName`

The name of the project to which the seat belongs.

**SeatID** `string`  |  Seat ID

`Data.SeatID`

The unique ID of the seat.

**ShortTermUsage** `string`  |  Usage in the last 5 minutes

`Data.ShortTermUsage`

The seat's short\-term usage in the last 5 minutes, used for real\-time throttling monitoring.

**UserID** `integer`  |  Bound sub\-user ID

`Data.UserID`

The ID of the sub\-user bound to the seat.

**UserName** `string`  |  Bound sub\-user name

`Data.UserName`

The name of the sub\-user bound to the seat.

**WeeklyUsage** `string`  |  Accumulated usage this week

`Data.WeeklyUsage`

The seat's accumulated usage for the current billing week, as defined by the subscription cycle.

**Total** `integer`  |  Total seats

The total number of seats that match the query criteria.
