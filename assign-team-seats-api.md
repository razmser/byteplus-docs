# Batch assign team seats

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=AssignTeamSeats&Version=2024-01-01`

Batch assign team seats to specified sub\-users. You can assign corresponding users to multiple seats in a single call. This API uses partial success semantics: the returned result lists seats that were assigned successfully and seats that failed to be assigned separately, so the caller can retry failed items accordingly. Seats that have already been assigned to other users are not overwritten and are returned as failed items.

> **Note**

> This API uses partial success semantics: the returned result lists seats that were assigned successfully and seats that failed to update in `SuccessIds` and `FailedResults`, respectively. The caller must retry or fix based on `FailedResults`. Seats that have already been assigned to other users are not overwritten and are included in FailedResult.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Data** `object[]` `Required`  |  Seat assignment list

The list of seat assignments to be made. Each item specifies a seat ID and the corresponding target sub\-user.

**SeatId** `string` `Required`  |  Seat ID

`Data.SeatId`

The seat ID to assign.

**UserId** `string` `Required`  |  Sub\-user ID

`Data.UserId`

The target sub\-user ID to assign the seat to.

**UserName** `string` `Required`  |  Sub\-user name

`Data.UserName`

The display name of the sub\-user.

**ProjectName** `string` `Default default`  |  Project name

The name of the project space to which the seat belongs. If no value is passed, the default project is used. If you specify a project that does not exist, a `NotFound.ProjectName` error is returned.

## Response parameters

**FailedResult** `object[]`  |  Failed seat list

The list of seats that failed to be assigned, including the failed *SeatId* and the failure reason.

**Message** `string`  |  Failure reason

`FailedResult.Message`

The detailed reason why the assignment failed.

**SeatId** `string`  |  Failed seat ID

`FailedResult.SeatId`

The seat ID that failed to be assigned.

**SuccessIds** `string[]`  |  Successful seat ID

The list of seat IDs that were assigned successfully.
