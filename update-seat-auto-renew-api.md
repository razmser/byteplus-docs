# Batch enable or disable auto-renewal for team seats

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=UpdateSeatAutoRenew&Version=2024-01-01`

Allows a team administrator to batch set the auto\-renewal status of team seats. Supports enabling or disabling auto\-renewal for multiple seats at the same time and returns processing results using partial success semantics.

> **Note**

> This API uses partial success semantics: the returned result lists seats that were updated successfully and seats that failed to update in `SuccessIds` and `FailedResults`, respectively. The caller must retry or fix failed items based on `FailedResults`.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Plan** `string` `Required`  |  Product name

The name of the team product for which to set the auto\-renewal status. Value:

* `AgentPlanTeam`: Agent Plan Team

* `CodingPlanTeam`: Coding Plan Team

**SeatIds** `string[]` `Required`  |  Seat ID list

The list of seat IDs for which to set the auto\-renewal status. A single request supports up to 200 seat IDs. Empty strings or duplicate values are not allowed.

**AutoRenew** `boolean` `Required`  |  Auto\-renewal

Whether to enable auto\-renewal. Value:

* `true`: Enable auto\-renewal

* `false`: Disable auto\-renewal

**ProjectName** `string` `Default default`  |  Project name

The name of the project space to which the seat belongs. If no value is passed, the default project is used.

## Response parameters

**SuccessIds** `string[]`  |  Successful seat ID

The list of seat IDs whose auto\-renewal status was successfully updated in this operation.

**FailedResults** `object[]`  |  Failed seat details

A detailed list of seats whose auto\-renewal status failed to update. Only failed seats and their failure reasons are returned.

**SeatId** `string`  |  Failed seat ID

`FailedResults.SeatId`

The seat ID for which the auto\-renewal status failed to update.

**Message** `string`  |  Failure reason

`FailedResults.Message`

The detailed reason why the auto\-renewal status failed to update.
