# Renew team seats

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=RenewTeamSeats&Version=2024-01-01`

Renew Team seats. You can set a different renewal duration for different seats in a single request.

> **Service Terms**

> Before purchasing or using the Service through the API, please carefully read and understand the [General Terms for AI Services](https://docs.byteplus.com/en/docs/legal/AI-Services-terms), [Specific Terms for Coding Plan and Agent Plan](https://console.byteplus.com/ark-stg/region:ap-southeast-1/docs/2278469), [Specific Terms for the Searchinfinity Service](https://docs.byteplus.com/en/docs/searchinfinity/Specific_Terms_for_the_Searchinfinity_Service), [Byteplus Searchinfinity Service Level Agreement](https://docs.byteplus.com/en/docs/searchinfinity/Byteplus_Searchinfinity_Service_Level_Agreement), and other applicable platform rules. If you do not agree to the applicable terms, please discontinue use of the relevant Service.

> **Note**

> The API runs asynchronously and deducts fees automatically. Make sure your cloud account has sufficient balance before placing an order.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Plan** `string` `Required`  |  Product name

The Team product to renew. Valid values: `AgentPlanTeam` and `CodingPlanTeam`.

**Seats** `object[]` `Required`  |  Renewal details

The list of renewal details. This parameter cannot be empty. You can set a different renewal duration for each seat group in a single request.

**Duration** `number` `Required`  |  Renewal duration

`Seats.Duration`

The renewal duration for this group. The unit is specified by *PricingCycle*.

**Range**: `[1, 12]`

**PricingCycle** `string` `Required`  |  Billing cycle

`Seats.PricingCycle`

The billing cycle. Only `Month` (monthly subscription) is supported.

**SeatIds** `string[]` `Required`  |  Seat ID list

`Seats.SeatIds`

The list of seat IDs to renew in this group. This field cannot be empty.

**DryRun** `boolean` `Default false`  |  Dry run

Specifies whether to perform only a parameter dry run without actually placing an order. If set to `true`, only the request is validated and no fees are deducted.

**ProjectName** `string` `Default default`  |  Project name

The name of the project to which the resource belongs. If the resource is not in the default project, enter the actual project name.

## Response parameters

**SeatIds** `string[]`  |  Successful seat IDs

The aggregated list of all seat IDs that were renewed in this request.
