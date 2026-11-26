# Query personal edition packages

`POST https://ark.ap-southeast-1.byteplusapi.com/? Action=GetPersonalPlan&Version=2024-01-01`

Queries information about personal edition packages purchased by the current account, including the package tier, effective status, start and end times, and auto\-renewal settings.

> **Note**

> This API only returns information about personal edition packages purchased by the current account. If no package has been purchased or the package has been reclaimed, a ResourceNotFound.Plan error is returned.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Plan** `string` `Required`  |  Product name

The product name to query. Valid values: `AgentPlan` and `CodingPlan`.

## Response parameters

**AutoRenew** `boolean`  |  Auto\-renewal

Whether automatic renewal upon expiration is enabled.

**EndTime** `string`  |  Expiration time

The current expiration time of the package, in ISO 8601 format.

**PlanType** `string`  |  Package tier

The package tier subscribed to by the current account. When *Plan* is `AgentPlan`, valid values are `Small`, `Medium`, `Large`, and `Max`; when *Plan* is `CodingPlan`, valid values are `Lite` and `Pro`.

**StartTime** `string`  |  Earliest generation time of trusted outputs

The first effective time of the package, in ISO 8601 format.

**Status** `string`  |  Package status

The current effective status of the package.`Running` indicates that the package is in effect, and `Expired` indicates that the package has expired.
