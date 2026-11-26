# Renew shared package

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=RenewSharedPackage&Version=2024-01-01`

Renews a shared package for Agent Plan Team. Only a one\-month renewal of the entire shared package is currently supported per request, with the purchase quantity unchanged.

> **Service Terms**

> Before purchasing or using the Service through the API, please carefully read and understand the [General Terms for AI Services](https://docs.byteplus.com/en/docs/legal/AI-Services-terms), [Specific Terms for Coding Plan and Agent Plan](https://console.byteplus.com/ark-stg/region:ap-southeast-1/docs/2278469), [Specific Terms for the Searchinfinity Service](https://docs.byteplus.com/en/docs/searchinfinity/Specific_Terms_for_the_Searchinfinity_Service), [Byteplus Searchinfinity Service Level Agreement](https://docs.byteplus.com/en/docs/searchinfinity/Byteplus_Searchinfinity_Service_Level_Agreement), and other applicable platform rules. If you do not agree to the applicable terms, please discontinue use of the relevant Service.

> **Usage notes**

* > To renew a shared package, use the `InstanceID` returned by [Create shared package](create-shared-package-api.md) or [Get shared package subscription information](get-shared-package-info-api.md).

* > Only renewal of the entire shared package at its existing purchase quantity is currently supported. Changing the purchase quantity and partial renewal are not supported.

* > Renewal only extends the subscription validity period. It does not increase the AFP quota for the current active period.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Duration** `integer` `Required`  |  Renewal duration

The subscription duration added by this renewal, in months. Only a one\-month renewal is currently supported per request.

Value is fixed to `1`.

**InstanceID** `string` `Required`  |  Subscription instance ID

The subscription instance ID of the shared package to renew, rather than the shared package ID.

> **warning**

> An empty string or a string containing only whitespace is not allowed.

**PricingCycle** `string` `Required`  |  Billing cycle

The billing cycle for renewing the shared package. Only `Month` is currently supported.

**AutoPay** `boolean` `Default false`  |  Automatic payment

Specifies whether to pay the renewal order automatically.

* **`true`**: Pays the renewal order automatically.

* **`false`**: Submits the renewal order and waits for manual payment.

**DryRun** `boolean` `Default false`  |  Dry run

Specifies whether to perform only transaction pre\-validation.

* **`true`**: Performs validation only, without generating a renewal order.

* **`false`**: Submits a renewal order.

## Response parameters

**InstanceID** `string`  |  Subscription instance ID

The subscription instance ID of the shared package associated with this renewal request.

**NeedPay** `boolean`  |  Payment required

Indicates whether the caller still needs to complete payment.

* **`true`**: Payment for the renewal order is still required.

* **`false`**: No further payment is required.

**OrderNumber** `string`  |  Order number

The final order number of the renewal order. Returned when `NeedPay` is `false` and an order number is available.

This field is not returned when `DryRun` is set to `true`.

**PreOrderNumbers** `string`  |  Pre\-order number

The pre\-order number of the renewal order. Returned when `NeedPay` is `true` and a pre\-order number is available.

This field is not returned when `DryRun` is set to `true`.
