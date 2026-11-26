# ListSeatInfos - List seat basic information

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=ListSeatInfos&Version=2024-01-01`

Gets a list of basic information for all seats under the current account. You can filter by package type, seat status, billing status, and other criteria, and use pagination and sorting.

> **Tip**

> This API is used to query information about all seats under the current account that meet the specified criteria. The response contains the `SeatID` parameter, which can be used as an input parameter in subsequent calls to the `ListSeatInfoUsages` or `GetSeatInfoUsage` API.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Filter** `object` `Required`  |  Filter criteria

Filter criteria.

**BillingStatus** `integer[]`  |  Billing status

`Filter.BillingStatus`

Seat billing status. Supported values:

* `1`: pending (ordered but unpaid)

* `2`: running (activated)

* `3`: expired

* `4`: reclaimed

**BizInfo** `string`  |  Plan Type

`Filter.BizInfo`

The package type of the seat. Supported values:

* `Lite`

* `Pro`

**SeatIDs** `string[]`  |  Seat ID list

`Filter.SeatIDs`

Seat ID list. A single request can include up to 1,000 IDs.

**SeatStatus** `integer`  |  Binding status

`Filter.SeatStatus`

Seat binding status. Supported values:

* `1`: idle. The seat is not bound to an API key.

* `2`: active, indicating that the seat is bound to an API key.

**UserID** `string`  |  User ID

`Filter.UserID`

The ID of the sub\-user bound to the seat.

**UserName** `string`  |  Username

`Filter.UserName`

The name of the sub\-user bound to the seat.

**PageNum** `integer`  |  Page number

The pagination page number, starting from 1.

**PageSize** `integer`  |  Items per page

The number of seats returned per page.

**ProjectName** `string` `Default default`  |  Project name

The name of the project to which the resource belongs. If the resource is not in the default project, specify the actual project name.

**Scene** `string` `Default coding_plan_enterprise`  |  Scenario

Business scenario for team seats. Supported values:

* `coding_plan_enterprise`: Coding Plan team seat.

* `agent_plan_enterprise`: Agent Plan team seat. When querying Agent Plan team seats, you must explicitly pass this value. Otherwise, the returned result is empty.

**SortBy** `string`  |  Sort parameter

Sort parameter. For example, `OrderTimestamp` sorts by the time of placing the orders.

**SortOrder** `string`  |  Sort order

Sort order. Supported values:

* `Asc`: ascending

* `Desc`: descending

**TagFilters** `object[]`  |  Tag filter criteria

Filter by tags bound to seats. Multiple filters use an AND relationship, and multiple *Values* for the same *Key* use an OR relationship.

**Key** `string` `Required`  |  Tag key

`TagFilters.Key`

The tag key used to filter seats.

**Values** `string[]`  |  Tag value list

`TagFilters.Values`

The list of tag values used to filter seats. Multiple values are evaluated with OR logic.

## Response parameters

**BizSummaries** `object[]`  |  Package statistics summary

Statistics summary for seats in each package.

**ActiveCount** `integer`  |  Assigned seat count

`BizSummaries.ActiveCount`

The number of assigned seats in this package (*SeatStatus* is `Active`).

**BizInfo** `string`  |  Plan Type

`BizSummaries.BizInfo`

The business package type. Example values: Lite for Lite edition; Pro for Pro edition. Options: `Lite`, `Pro`.

**TotalCount** `integer`  |  Total running seats

`BizSummaries.TotalCount`

The total number of seats in this package whose billing status is running.

**Data** `object[]`  |  Seat details list

An array of seat details that match the query criteria.

**SeatID** `string`  |  Seat ID

`Data.SeatID`

The unique ID of the seat.

**AccountID** `string`  |  Main account ID

`Data.AccountID`

The ID of the BytePlus main account to which the seat belongs.

**ProjectName** `string`  |  Project name

`Data.ProjectName`

The name of the project to which the seat belongs. If the resource is not in the default project, specify the actual project name.

**BizInfo** `string`  |  Plan Type

`Data.BizInfo`

The package type of the seat. Supported values:

* `Lite`

* `Pro`

**BillingStatus** `integer`  |  Billing status

`Data.BillingStatus`

Seat billing status. Supported values:

* `1`: pending (ordered but unpaid)

* `2`: running (activated)

* `3`: expired

* `4`: reclaimed

**InstanceID** `string`  |  Billing instance ID

`Data.InstanceID`

The instance ID returned by the billing module.

**OrderTime** `integer`  |  Order time

`Data.OrderTime`

The most recent order time, as a Unix timestamp in seconds.

**ExpiredTime** `integer`  |  Expiration time

`Data.ExpiredTime`

The expiration time of the most recent order, as a Unix timestamp in seconds.

**AutoRenew** `boolean`  |  Auto\-renewal

`Data.AutoRenew`

Whether to enable auto\-renewal. Value:

* `true`: Enable auto\-renewal

* `false`: Disable auto\-renewal

**SubscribeMilestones** `string`  |  Resource refresh time

`Data.SubscribeMilestones`

The point in time when seat resources are refreshed.

**RateLimit** `object`  |  Rate limit configuration

`Data.RateLimit`

Rate\-limiting configuration for the seat.

**RPM** `integer`  |  RPM

`Data.RateLimit.RPM`

Requests per minute, the number of requests allowed per minute.

**TokenWindow** `object[]`  |  Rate\-limiting time window

`Data.RateLimit.TokenWindow`

List of token rate\-limiting time window configurations.

**DurationSeconds** `integer`  |  Window length (seconds)

`Data.RateLimit.TokenWindow.DurationSeconds`

The length of the rate\-limiting time window, in seconds.

**Token** `integer`  |  Window token limit

`Data.RateLimit.TokenWindow.Token`

The maximum number of tokens allowed in the current time window.

**TPM** `integer`  |  TPM

`Data.RateLimit.TPM`

Tokens per minute, the total number of tokens that can be processed per minute.

**ExtraConfig** `object`  |  Extension configuration

`Data.ExtraConfig`

Seat extension configuration.

**ArkCodeLatestMappingModelID** `string`  |  ark\-code\-latest mapping

`Data.ExtraConfig.ArkCodeLatestMappingModelID`

The specific model ID currently mapped to the alias `ark-code-latest`.

**SeatStatus** `string`  |  Binding status

`Data.SeatStatus`

Seat binding status. Supported values:

* `1`: idle, indicating that the seat is not bound to an API key.

* `2`: active, indicating that the seat is bound to an API key.

**IdentityType** `string`  |  Identity type

`Data.IdentityType`

The identity type bound to the seat. For example, `SubUser` indicates a subuser.

**IdentityId** `string`  |  Identity ID

`Data.IdentityId`

The identity ID bound to the seat, such as a subuser ID.

**IdentityDetail** `string`  |  Additional identity information

`Data.IdentityDetail`

Additional information about the identity bound to the seat, such as the username.

**BindCount** `integer`  |  Number of bindings this month

`Data.BindCount`

The number of times this seat was bound in the current calendar month.

**ApiKeySID** `string`  |  API Key SID

`Data.ApiKeySID`

The unique identifier of the API key, used to rotate the API key.

**ApiKey** `string`  |  API key secret

`Data.ApiKey`

The API key secret bound to the seat.

**CreateTime** `integer`  |  Creation time

`Data.CreateTime`

The seat creation time, as a Unix timestamp in seconds.

**UpdateTime** `string`  |  Update time

`Data.UpdateTime`

Seat update time in ISO 8601 format.

**Scene** `string`  |  Scenario

`Data.Scene`

The business scenario to which the seat belongs. Supported values:

* `coding_plan_enterprise`: Coding Plan team seat.

* `agent_plan_enterprise`: Agent Plan team seat.

**Version** `integer`  |  Version number

`Data.Version`

Optimistic locking version number.

**Total** `integer`  |  Total seats

The total number of seats that match the query criteria.
