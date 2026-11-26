# Renew personal plan

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=RenewPersonalPlan&Version=2024-01-01`

Renew a Personal plan.

> **Important Notice**

1. > **Service Terms**

> Before purchasing or using the Service through the API, please carefully read and understand the [General Terms for AI Services](https://docs.byteplus.com/en/docs/legal/AI-Services-terms), [Specific Terms for Coding Plan and Agent Plan](https://console.byteplus.com/ark-stg/region:ap-southeast-1/docs/2278469), [Specific Terms for the Searchinfinity Service](https://docs.byteplus.com/en/docs/searchinfinity/Specific_Terms_for_the_Searchinfinity_Service), [Byteplus Searchinfinity Service Level Agreement](https://docs.byteplus.com/en/docs/searchinfinity/Byteplus_Searchinfinity_Service_Level_Agreement), and other applicable platform rules. If you do not agree to the applicable terms, please discontinue use of the relevant Service.

2. > **Data Authorization and Purchase Options**

> Purchasing an Individual Plan through this API requires you to read and agree to the [Data Authorization Agreement](https://docs.byteplus.com/en/docs/legal/data_authorization_agreement_modelark) and explicitly confirm your consent to data authorization in the API request.

> If you do not wish to provide data authorization, you can purchase a subscription through the [BytePlus Console](https://console.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan). When purchasing through the console, data authorization is optional and does not affect your ability to use the Plan.

> After purchase, you may request to withdraw your data authorization at any time through BytePlus's official support channels. Withdrawing authorization will not affect your ability to use your Individual Plan. For details on data processing, please refer to the [Data Authorization Agreement](https://docs.byteplus.com/en/docs/legal/data_authorization_agreement_modelark).

> **Note**

* > The API executes asynchronously and deducts fees automatically. Make sure your cloud account has sufficient balance before placing an order.

* > After you call `RenewPersonalPlan` to submit a renewal request, the order may be processed asynchronously or its status may be delayed. After calling `RenewPersonalPlan` to renew a Personal Edition plan, call `GetPersonalPlan` to verify the final renewal result.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**AgreementAccepted** `boolean` `Required`  |  Agreement and Data Authorization Consent

Indicates whether you have read and agreed to the applicable Plan terms and the [Data Authorization Agreement](https://docs.byteplus.com/en/docs/legal/data_authorization_agreement_modelark).

Purchasing a subscription through this API requires explicit acceptance of the applicable terms and consent to data authorization. This parameter only accepts `true`. If the parameter is omitted or set to `false`, the purchase request will be rejected.

If you do not wish to provide data authorization, you can purchase a subscription through the [BytePlus Console](https://console.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan), where data authorization is optional.

**Duration** `number` `Required`  |  Renewal duration

The renewal duration. The unit is specified by *PricingCycle*.

**Range**: `[1, 12]`

**Plan** `string` `Required`  |  Product name

The product to renew. Valid values: `AgentPlan` and `CodingPlan`.

**PricingCycle** `string` `Required`  |  Billing cycle

The billing cycle. The only valid value is `Month`.

**DryRun** `boolean` `Default false`  |  Dry run

Specifies whether to perform only a dry run without placing an order. Valid values:

* `true`: Validates the request only.

* `false` (default): Executes the request.

## Response parameters

This API has no response\-specific parameters. On success the HTTP status is `200` and the response `Result` is an empty object `{}`.
