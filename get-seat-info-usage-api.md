# GetSeatInfoUsage - Query information of a seat

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=GetSeatInfoUsage&Version=2024-01-01`

Queries information and usage details for a single seat.

> **Tip**

> This API is used to view detailed usage for a specific seat. Pass a single `SeatID` obtained from the `ListSeatInfos` API as an input parameter.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**SeatID** `string` `Required`  |  Seat ID

The unique seat ID, which can be obtained by calling the *ListSeatInfos* API.

**ProjectName** `string` `Default default`  |  Project name

Name of the project to which the seat belongs. Enter the actual project name if the resource is not in the default project.

## Response parameters

**SeatInfoUsage** `object`  |  Seat information and usage

Basic information and usage statistics details for the seat.

**AccountID** `integer`  |  Main account ID

`SeatInfoUsage.AccountID`

The ID of the BytePlus main account to which the seat belongs.

**MonthlyResetMilestone** `integer`  |  Monthly quota refresh time

`SeatInfoUsage.MonthlyResetMilestone`

The time when the monthly quota resources are refreshed.

**MonthlySubscribeMilestone** `integer`  |  Monthly quota refresh start time

`SeatInfoUsage.MonthlySubscribeMilestone`

The start time for refreshing the monthly quota resources.

**MonthlyUsage** `string`  |  Accumulated usage this month

`SeatInfoUsage.MonthlyUsage`

The seat's accumulated usage for the current billing month, as defined by the subscription cycle.

**ProjectName** `string`  |  Project name

`SeatInfoUsage.ProjectName`

The name of the project to which the seat belongs.

**SeatID** `string`  |  Seat ID

`SeatInfoUsage.SeatID`

The unique ID of the seat.

**ShortTermUsage** `string`  |  Usage in the last 5 minutes

`SeatInfoUsage.ShortTermUsage`

The seat's short\-term usage in the last 5 minutes, used for real\-time throttling monitoring.

**UserID** `integer`  |  Bound sub\-user ID

`SeatInfoUsage.UserID`

The ID of the sub\-user currently bound to the seat.

**UserName** `string`  |  Bound sub\-user name

`SeatInfoUsage.UserName`

The name of the sub\-user currently bound to the seat.

**WeeklyUsage** `string`  |  Accumulated usage this week

`SeatInfoUsage.WeeklyUsage`

The seat's accumulated usage for the current billing week, as defined by the subscription cycle.
