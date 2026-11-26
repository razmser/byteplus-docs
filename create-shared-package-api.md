# Create shared package

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=CreateSharedPackage&Version=2024-01-01`

Creates a shared package for Agent Plan Team and submits a subscription order. The shared package is shared by its bound seats.

> **Service Terms**

> Before purchasing or using the Service through the API, please carefully read and understand the [General Terms for AI Services](https://docs.byteplus.com/en/docs/legal/AI-Services-terms), [Specific Terms for Coding Plan and Agent Plan](https://console.byteplus.com/ark-stg/region:ap-southeast-1/docs/2278469), [Specific Terms for the Searchinfinity Service](https://docs.byteplus.com/en/docs/searchinfinity/Specific_Terms_for_the_Searchinfinity_Service), [Byteplus Searchinfinity Service Level Agreement](https://docs.byteplus.com/en/docs/searchinfinity/Byteplus_Searchinfinity_Service_Level_Agreement), and other applicable platform rules. If you do not agree to the applicable terms, please discontinue use of the relevant Service.

> **Usage notes**

* > For shared package benefits, subscription rules, and usage instructions, see the [Shared package usage guide](agent-plan-enterprise-shared-package-guide.md).

* > After placing an order, use [Get shared package subscription information](get-shared-package-info-api.md) to confirm the subscription status of the shared package.

* > Avoid submitting duplicate purchase requests to prevent duplicate orders.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Duration** `integer` `Required`  |  Subscription duration

The subscription duration of the shared package, in months. Only a one\-month subscription is currently supported.

Value is fixed to `1`.

**PricingCycle** `string` `Required`  |  Billing cycle

The billing cycle of the shared package. Only `Month` is currently supported.

**Quantity** `number` `Required`  |  Purchase quantity

The number of shared packages to purchase in this request. Only integer quantities are currently supported.

**Min**: `1`

**AutoPay** `boolean` `Default false`  |  Automatic payment

Specifies whether to pay the shared package order automatically.

* **`true`**: Pays the order automatically.

* **`false`**: Creates the order and waits for manual payment.

**AutoRenew** `boolean` `Default false`  |  Auto\-renewal

Specifies whether to enable auto\-renewal for the shared package subscription.

* **`true`**: Enables auto\-renewal.

* **`false`**: Does not enable auto\-renewal.

**DryRun** `boolean` `Default false`  |  Dry run

Specifies whether to perform only resource and transaction pre\-validation.

* **`true`**: Performs validation only, without creating a shared package, subscription instance, or order.

* **`false`**: Creates a shared package and submits a subscription order.

**ProjectName** `string` `Default default`  |  Project name

The name of the project to which the resource belongs. If the resource is not in the default project, specify the actual project name.

> **tip**

> The server trims leading and trailing whitespace. If the field is omitted or is empty after trimming, the field's default value is used.

## Response parameters

**InstanceID** `string`  |  Subscription instance ID

The subscription instance ID associated with the shared package, used to renew the shared package. An empty string is returned when `DryRun` is set to `true`.

**NeedPay** `boolean`  |  Payment required

Indicates whether the caller still needs to complete payment.

* **`true`**: Payment is still required.

* **`false`**: No further payment is required.

**Quantity** `number`  |  Purchase quantity

The shared package purchase quantity actually used for billing.

**SharedPackageID** `string`  |  Shared package ID

The ID of the created shared package, used to query its subscription information. This ID also serves as the subscription instance name.

An empty string is returned when `DryRun` is set to `true`.

**OrderNumber** `string`  |  Order number

The final order number of the subscription order. Returned when `NeedPay` is `false` and an order number is available.

This field is not returned when `DryRun` is set to `true`.

**PreOrderNumbers** `string`  |  Pre\-order number

The pre\-order number of the subscription order. Returned when `NeedPay` is `true` and a pre\-order number is available.

This field is not returned when `DryRun` is set to `true`.
