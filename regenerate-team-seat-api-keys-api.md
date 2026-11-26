# Batch update team seat API keys

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=RegenerateTeamSeatApiKeys&Version=2024-01-01`

A team administrator rotates (regenerates) API keys in bulk for the specified seats. After rotation, the original API keys become invalid immediately. The system generates a new key for each seat, and you must promptly update the service\-side call configuration. This API uses partial success semantics: the returned result lists the seats for which rotation succeeded and failed separately.

> **Note**

> The rotation operation is irreversible. The old API keys for the rotated seats become invalid immediately after the operation is complete. Before calling this API, confirm that you have prepared the required service\-side coordination for key replacement, and securely store the newly generated API keys to prevent leaks.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Plan** `string` `Required`  |  Product name

The product name of the target package. Valid values: `AgentPlanTeam` (Agent Plan Team edition), `CodingPlanTeam` (Coding Plan Team edition).

**SeatIds** `string[]` `Required`  |  Seat ID list

The list of seat IDs to rotate. Batch operations are supported.

**ProjectName** `string`  |  Project name

The name of the project space to which the seat belongs.

## Response parameters

**FailedResults** `object[]`  |  Failed seat details

The list of seat details for which rotation failed in this operation. Each item contains the failed *SeatId* and the specific reason.

**Message** `string`  |  Failure reason

`FailedResults.Message`

The specific reason why rotation failed.

**SeatId** `string`  |  Failed seat ID

`FailedResults.SeatId`

The seat ID for which rotation failed.

**SuccessIds** `string[]`  |  Successful seat ID

The list of seat IDs for which rotation succeeded in this operation.
