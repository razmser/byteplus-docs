# ListModelActivations - List activated models

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=ListModelActivations&Version=2024-01-01`

Queries foundation models visible to the current account and their activation statuses, corresponding to foundation model activation information on the [Model activation page](https://console.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=model). You can use this API to obtain model names for activation, confirm activation results, and query pricing and free quotas as needed. If you do not filter by activation status, the list can include both activated and non\-activated models.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Filter** `object`  |  Filter conditions

The filter conditions for the model activation list. You can filter by status, vendor, domain, and other dimensions.

**DisplayName** `string`  |  Model display name

`Filter.DisplayName`

Filters by model display name.

**FoundationModelDomain** `string`  |  Model domain

`Filter.FoundationModelDomain`

Filters by the domain of the foundation model.

**FoundationModelNames** `string[]`  |  Model name list

`Filter.FoundationModelNames`

Filters by foundation model name. You can specify a model name used by [ActivateModels - Batch activation of foundation models](activate-models-api.md).

**IncludeDeprecatedModels** `boolean`  |  Whether to include retired models

`Filter.IncludeDeprecatedModels`

Whether to include retired models in the results.

**States** `string[]`  |  Status filter

`Filter.States`

Filters by activation status. `Available` indicates activated, and `Unavailable` indicates not activated. If omitted, the results are not filtered by activation status.

**VendorName** `string`  |  Vendor name

`Filter.VendorName`

Filters by model vendor name.

**PageNumber** `integer` `Default 1`  |  Starting page number

The starting page number for pagination.

**PageSize** `integer` `Default 10`  |  Records per page

The number of records returned per page for pagination.

**SortBy** `string` `Default VendorName`  |  Sorting criterion

The name of the field to sort by.

**SortOrder** `string` `Default Desc`  |  Sorting order

The sort order. `Desc` indicates descending order, and `Asc` indicates ascending order.

**WithFreeUsage** `boolean`  |  Whether to include free quota information

Whether to include the model's free inference quota information in the results.

**WithPrice** `boolean`  |  Whether to include pricing information

Whether to include model pricing information in the results.

## Response parameters

**Items** `object[]`  |  Model activation list

The model activation list. Each item contains activation details for one foundation model.

**ChargeItems** `object[]`  |  Billing item list

`Items.ChargeItems`

The list of billing items for the model.

**Price** `number`  |  Unit price

`Items.ChargeItems.Price`

The unit price of the billing item.

**Type** `string`  |  Billing item type

`Items.ChargeItems.Type`

The type identifier of the billing item.

**UnitCode** `string`  |  Measurement unit code

`Items.ChargeItems.UnitCode`

The measurement unit code of the billing item.

**DiscountPriceEndTime** `string`  |  Discount end time

`Items.ChargeItems.DiscountPriceEndTime`

The end time of the discounted price.

**DiscountPriceStartTime** `string`  |  Discount start time

`Items.ChargeItems.DiscountPriceStartTime`

The time when the discounted price takes effect.

**OriginalPrice** `number`  |  Original price

`Items.ChargeItems.OriginalPrice`

The original price of the billing item before discounts.

**DisplayName** `string`  |  Display name

`Items.DisplayName`

The display name of the model.

**FoundationModelName** `string`  |  Foundation model name

`Items.FoundationModelName`

The foundation model name, which can be used by [ActivateModels - Batch activation of foundation models](activate-models-api.md).

**FreeResourcePackItems** `object[]`  |  Free resource pack list

`Items.FreeResourcePackItems`

The list of free resource packs associated with the model.

**Consumed** `integer`  |  Consumed amount

`Items.FreeResourcePackItems.Consumed`

The amount consumed from the resource pack.

**Reclaimed** `integer`  |  Reclaimed amount

`Items.FreeResourcePackItems.Reclaimed`

The amount reclaimed from the resource pack.

**Total** `integer`  |  Total amount

`Items.FreeResourcePackItems.Total`

The total amount in the resource pack.

**Type** `string`  |  Resource pack type

`Items.FreeResourcePackItems.Type`

The resource pack type. `DataPermission` indicates data permissions, and `FreeInference` indicates free inference quota.

**SyncTime** `string`  |  Synchronization time

`Items.FreeResourcePackItems.SyncTime`

The most recent synchronization time of the resource pack data.

**MultiChargeItems** `object[]`  |  Tiered billing item list

`Items.MultiChargeItems`

The list of tiered billing items for the model, supporting tiered pricing based on token usage.

**ChargeItems** `object[]`  |  Billing item list

`Items.MultiChargeItems.ChargeItems`

The list of billing items for this tier.

**Price** `number`  |  Unit price

`Items.MultiChargeItems.ChargeItems.Price`

The unit price of the billing item.

**Type** `string`  |  Billing item type

`Items.MultiChargeItems.ChargeItems.Type`

The type identifier of the billing item.

**UnitCode** `string`  |  Measurement unit code

`Items.MultiChargeItems.ChargeItems.UnitCode`

The measurement unit code of the billing item.

**DiscountPriceEndTime** `string`  |  Discount end time

`Items.MultiChargeItems.ChargeItems.DiscountPriceEndTime`

The end time of the discounted price.

**DiscountPriceStartTime** `string`  |  Discount start time

`Items.MultiChargeItems.ChargeItems.DiscountPriceStartTime`

The time when the discounted price takes effect.

**OriginalPrice** `number`  |  Original price

`Items.MultiChargeItems.ChargeItems.OriginalPrice`

The original price of the billing item before discounts.

**Description** `string`  |  Tier description

`Items.MultiChargeItems.Description`

The description of this billing tier.

**MaxCompletionTokens** `integer`  |  Maximum completion token count

`Items.MultiChargeItems.MaxCompletionTokens`

The maximum number of completion tokens for this tier.

**MaxPromptTokens** `integer`  |  Maximum prompt token count

`Items.MultiChargeItems.MaxPromptTokens`

The maximum number of prompt tokens for this tier.

**Name** `string`  |  Tier name

`Items.MultiChargeItems.Name`

The name of this billing tier.

**State** `string`  |  Activation status

`Items.State`

The activation status. `Available` indicates activated, and `Unavailable` indicates not activated. An activated model is not necessarily available for normal calls. Also consider whether the account is in arrears and other business statuses.

**SubServices** `object[]`  |  Subservice status list

`Items.SubServices`

The list of subservices associated with the model and their activation statuses.

**Status** `string`  |  Activation status

`Items.SubServices.Status`

The activation status of the subservice. `Available` indicates activated, and `Unavailable` indicates not activated.

**SubService** `string`  |  Subservice type

`Items.SubServices.SubService`

The type identifier of the subservice.

**VendorName** `string`  |  Vendor name

`Items.VendorName`

The name of the model vendor.

**InitialInferenceFreeUsage** `object`  |  Initial free inference quota

`Items.InitialInferenceFreeUsage`

The initial free inference quota granted when the model is activated.

**Consumed** `number`  |  Used quota

`Items.InitialInferenceFreeUsage.Consumed`

The consumed free inference quota.

**Total** `number`  |  Total quota

`Items.InitialInferenceFreeUsage.Total`

The total free inference quota.

**Instruction** `string`  |  Activation instructions

`Items.Instruction`

The activation instructions or notes for the model.

**IsDeprecated** `boolean`  |  Whether retired

`Items.IsDeprecated`

Whether the model has been retired.

**IsLimitedActivation** `boolean`  |  Whether activation is restricted

`Items.IsLimitedActivation`

Whether activation is restricted. This is not equivalent to whether the model is activated.

**IsOverdue** `boolean`  |  Whether the account is in arrears

`Items.IsOverdue`

Whether the account is in arrears. Even when `State` is `Available`, this field can be `true`.

**PageNumber** `integer`  |  Current page number

The page number of the current results.

**PageSize** `integer`  |  Records per page

The number of records per page in the current results.

**TotalCount** `integer`  |  Total count

The total number of model activation records that match the filter conditions.
