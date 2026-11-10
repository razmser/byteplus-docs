# GetModelActivation - Query model activation details

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=GetModelActivation&Version=2024-01-01`

Queries activation details for the specified foundation model under the current account, including its status, billing items, resource packs, and subservice statuses.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**FoundationModelName** `string` `Required`  |  Foundation model name

The name of the foundation model whose activation details you want to query.

**WithFreeUsage** `boolean`  |  Whether to include free quota information

Whether to include the model's free inference quota information in the results.

**WithPrice** `boolean`  |  Whether to include pricing information

Whether to include model pricing information in the results.

## Response parameters

**Item** `object`  |  Model activation details

The activation details of the specified foundation model.

**ChargeItems** `object[]`  |  Billing item list

`Item.ChargeItems`

The unit price list. Returned only when the `WithPrice` request parameter is `true`.

**Price** `number`  |  Unit price

`Item.ChargeItems.Price`

The unit price of the billing item.

**Type** `string`  |  Billing item type

`Item.ChargeItems.Type`

The type identifier of the billing item.

**UnitCode** `string`  |  Measurement unit code

`Item.ChargeItems.UnitCode`

The measurement unit code of the billing item.

**DiscountPriceEndTime** `string`  |  Discount end time

`Item.ChargeItems.DiscountPriceEndTime`

The end time of the discounted price.

**DiscountPriceStartTime** `string`  |  Discount start time

`Item.ChargeItems.DiscountPriceStartTime`

The time when the discounted price takes effect.

**OriginalPrice** `number`  |  Original price

`Item.ChargeItems.OriginalPrice`

The original price of the billing item before discounts.

**DisplayName** `string`  |  Display name

`Item.DisplayName`

The display name of the model.

**FoundationModelName** `string`  |  Foundation model name

`Item.FoundationModelName`

The unique name identifying the foundation model.

**FreeResourcePackItems** `object[]`  |  Free resource pack list

`Item.FreeResourcePackItems`

The list of free resource packs. Returned only when the `WithFreeUsage` request parameter is `true`.

**Consumed** `integer`  |  Consumed amount

`Item.FreeResourcePackItems.Consumed`

The amount consumed from the resource pack.

**Reclaimed** `integer`  |  Reclaimed amount

`Item.FreeResourcePackItems.Reclaimed`

The amount reclaimed from the resource pack.

**Total** `integer`  |  Total amount

`Item.FreeResourcePackItems.Total`

The total resource pack amount.

**Type** `string`  |  Resource pack type

`Item.FreeResourcePackItems.Type`

The resource pack type. `DataPermission` indicates data permissions, and `FreeInference` indicates free inference quota.

**SyncTime** `string`  |  Synchronization time

`Item.FreeResourcePackItems.SyncTime`

The most recent synchronization time of the resource pack data.

**MultiChargeItems** `object[]`  |  Tiered billing item list

`Item.MultiChargeItems`

The list of tiered billing items for the model, supporting tiered pricing based on token usage. Returned only when the `WithPrice` request parameter is `true`.

**ChargeItems** `object[]`  |  Billing item list

`Item.MultiChargeItems.ChargeItems`

The list of billing items for this tier.

**Price** `number`  |  Unit price

`Item.MultiChargeItems.ChargeItems.Price`

The unit price of the billing item.

**Type** `string`  |  Billing item type

`Item.MultiChargeItems.ChargeItems.Type`

The type identifier of the billing item.

**UnitCode** `string`  |  Measurement unit code

`Item.MultiChargeItems.ChargeItems.UnitCode`

The measurement unit code of the billing item.

**DiscountPriceEndTime** `string`  |  Discount end time

`Item.MultiChargeItems.ChargeItems.DiscountPriceEndTime`

The end time of the discounted price.

**DiscountPriceStartTime** `string`  |  Discount start time

`Item.MultiChargeItems.ChargeItems.DiscountPriceStartTime`

The time when the discounted price takes effect.

**OriginalPrice** `number`  |  Original price

`Item.MultiChargeItems.ChargeItems.OriginalPrice`

The original price of the billing item before discounts.

**Description** `string`  |  Tier description

`Item.MultiChargeItems.Description`

The description of this billing tier.

**MaxCompletionTokens** `integer`  |  Maximum completion token count

`Item.MultiChargeItems.MaxCompletionTokens`

The maximum number of completion tokens for this tier.

**MaxPromptTokens** `integer`  |  Maximum prompt token count

`Item.MultiChargeItems.MaxPromptTokens`

The maximum number of prompt tokens for this tier.

**Name** `string`  |  Tier name

`Item.MultiChargeItems.Name`

The name of this billing tier.

**State** `string`  |  Activation status

`Item.State`

The activation status of the model. `Available` indicates activated, and `Unavailable` indicates not activated.

**SubServices** `object[]`  |  Subservice status list

`Item.SubServices`

The list of subservices associated with the model and their activation statuses.

**Status** `string`  |  Activation status

`Item.SubServices.Status`

The activation status of the subservice. `Available` indicates activated, and `Unavailable` indicates not activated.

**SubService** `string`  |  Subservice type

`Item.SubServices.SubService`

The type identifier of the subservice.

**VendorName** `string`  |  Vendor name

`Item.VendorName`

The name of the model vendor.

**InitialInferenceFreeUsage** `object`  |  Initial free inference quota

`Item.InitialInferenceFreeUsage`

The free quota before first activation. Returned only when the `WithFreeUsage` request parameter is `true`. If it is not returned, no quota is available.

**Consumed** `number`  |  Used quota

`Item.InitialInferenceFreeUsage.Consumed`

The consumed free inference quota.

**Total** `number`  |  Total quota

`Item.InitialInferenceFreeUsage.Total`

The total free inference quota.

**Instruction** `string`  |  Activation instructions

`Item.Instruction`

The activation instructions or notes for the model.

**IsDeprecated** `boolean`  |  Whether retired

`Item.IsDeprecated`

Whether the model has been retired.

**IsLimitedActivation** `boolean`  |  Whether activation is limited

`Item.IsLimitedActivation`

Whether activation of the model is limited, meaning it is available only to certain users.

**IsOverdue** `boolean`  |  Whether overdue

`Item.IsOverdue`

Whether the model is in an overdue state.
