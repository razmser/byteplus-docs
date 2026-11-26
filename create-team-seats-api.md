# Create team seats

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=CreateTeamSeats&Version=2024-01-01`

Team administrators purchase Agent Plan Team / Coding Plan Team seats in batches (`AgentPlanTeam` / `CodingPlanTeam`).

> **Service Terms**

> Before purchasing or using the Service through the API, please carefully read and understand the [General Terms for AI Services](https://docs.byteplus.com/en/docs/legal/AI-Services-terms), [Specific Terms for Coding Plan and Agent Plan](https://console.byteplus.com/ark-stg/region:ap-southeast-1/docs/2278469), [Specific Terms for the Searchinfinity Service](https://docs.byteplus.com/en/docs/searchinfinity/Specific_Terms_for_the_Searchinfinity_Service), [Byteplus Searchinfinity Service Level Agreement](https://docs.byteplus.com/en/docs/searchinfinity/Byteplus_Searchinfinity_Service_Level_Agreement), and other applicable platform rules. If you do not agree to the applicable terms, please discontinue use of the relevant Service.

> **Note**

* > You can order multiple plan tiers in a single request (the `Seats` array). Within the seat array, each tier can be submitted only once.

* > You can pass the `DryRun=true` parameter to perform only subscription validity checks without actually creating a seat order.

* > You are responsible for ensuring idempotency. Do not send concurrent requests with the same parameters, to prevent duplicate seat creation.

* > The API runs asynchronously and deducts fees automatically. Make sure your cloud account has sufficient balance before placing an order.

* > After you call `CreateTeamSeats` to submit a seat order request, the order may be processed asynchronously or its status may be delayed. After calling the `CreateTeamSeats` API to create Team seats, we recommend that you call the `ListSeatInfos` API to confirm the final seat purchase result.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Plan** `string` `Required`  |  Product name

The Team product to purchase. Valid values: `AgentPlanTeam` (Agent Plan Team) and `CodingPlanTeam` (Coding Plan Team).

**Seats** `object[]` `Required`  |  Seat configuration list

The list of seat configuration details. If *Plan* is `AgentPlanTeam`, a maximum of 4 items are supported (one each for `Small`, `Medium`, `Large`, and `Max`). If *Plan* is `CodingPlanTeam`, a maximum of 2 items are supported (`Lite` and `Pro`). Duplicate *PlanType* values are not allowed in a single request.

**Duration** `number` `Required`  |  Purchase duration

`Seats.Duration`

The purchase duration. The unit is specified by *PricingCycle*.

**Range**: `[1, 12]`

**PlanType** `string` `Required`  |  Plan tier

`Seats.PlanType`

The plan tier. If *Plan* is `AgentPlanTeam`, valid values are `Small`, `Medium`, `Large`, and `Max`. If *Plan* is `CodingPlanTeam`, valid values are `Lite` and `Pro`. Duplicate values are not allowed in a single request. A duplicate value returns a `Duplicated.PlanType` error.

**PricingCycle** `string` `Required`  |  Billing cycle

`Seats.PricingCycle`

The billing cycle. Only `Month` (monthly subscription) is supported.

**SeatNumber** `number` `Required`  |  Seat quantity

`Seats.SeatNumber`

The number of seats to create for this tier. The value must be greater than 0. For the first Team plan purchase, the number of seats in `Active` state under the account plus the total number of newly purchased seats must be at least 5.

**Min**: `1`

**AutoRenew** `boolean` `Default false`  |  Auto\-renewal

`Seats.AutoRenew`

Specifies whether to enable auto\-renewal when the plan expires. If set to `true`, the plan is automatically renewed from your cloud account balance before it expires. Only one auto\-renewal is supported.

**DryRun** `boolean`  |  Dry run

Specifies whether to perform only a parameter dry run without actually placing an order. If set to `true`, the full parameter and quota validation is performed without occupying seats or deducting fees. This is suitable for pre\-release validation.

**ProjectName** `string` `Default default`  |  Project name

The name of the project to which the seats belong. If you do not specify this parameter, the default project is used. If you specify a project that does not exist, a `NotFound.ProjectName` error is returned.

## Response parameters

**Seats** `object[]`  |  Seat result list

The list of allocated seat results, returned aggregated by *PlanType*. All seat IDs generated for the same tier are returned in the *SeatIds* array.

**PlanType** `string`  |  Plan tier

`Seats.PlanType`

The plan tier that corresponds to this result item.

**SeatIds** `string[]`  |  Seat ID list

`Seats.SeatIds`

The list of seat IDs generated for this tier. The list length equals *SeatNumber* for this tier. You can use the IDs as input for subsequent seat assignment, renewal, and termination API calls.
