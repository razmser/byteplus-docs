# Get shared package subscription information

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=GetSharedPackageInfo&Version=2024-01-01`

Queries the subscription status, purchase quantity, and subscription start and expiration times of an Agent Plan Team shared package using its shared package ID or a bound seat ID.

> **Usage notes**

* > Specify at least one of `SharedPackageID` and `SeatID`. If both are specified, the query uses `SharedPackageID`.

* > `CurrentQuota` and `NextPeriodQuota` indicate the shared package purchase quantity, rather than AFP consumption or remaining quota. This API does not return AFP usage details.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**SeatID** `string`  |  Seat ID

The ID of a seat bound to the shared package, used to query the subscription information of that shared package.

**SharedPackageID** `string`  |  Shared package ID

The ID of the shared package whose subscription information you want to query.

## Response parameters

**ArrivalTime** `integer`  |  Query time

The time when the query response was generated, as a Unix timestamp in seconds.

**Data** `object`  |  Shared package subscription information

The shared package subscription information, including subscription status, purchase quantities for the current and next billing periods, and subscription start and expiration times.

**AccountID** `string`  |  Account ID

`Data.AccountID`

The ID of the account to which the shared package belongs, returned as a decimal string.

**CurrentQuota** `number`  |  Current\-period purchase quantity

`Data.CurrentQuota`

The shared package purchase quantity in effect for the current billing period.

**ExpiredTime** `integer`  |  Subscription expiration time

`Data.ExpiredTime`

The expiration time of the shared package subscription, as a Unix timestamp in seconds. Returns `0` if it has not been initialized.

**NextPeriodQuota** `number`  |  Next\-period purchase quantity

`Data.NextPeriodQuota`

The shared package purchase quantity for the next billing period.

**OrderTime** `integer`  |  Subscription start time

`Data.OrderTime`

The start time of the shared package subscription, as a Unix timestamp in seconds. Returns `0` if it has not been initialized.

**SharedPackageID** `string`  |  Shared package ID

`Data.SharedPackageID`

The ID of the shared package to which the subscription information belongs.

**Status** `integer`  |  Subscription status

`Data.Status`

The subscription status of the shared package.

* **`1`**: Pending activation.

* **`2`**: Active.

* **`3`**: Expired.

* **`4`**: Reclaimed.

**InstanceID** `string`  |  Subscription instance ID

`Data.InstanceID`

The subscription instance ID associated with the shared package, used to renew the shared package.

> **tip**

> This field may not be returned if no transaction subscription instance is associated with the shared package.
