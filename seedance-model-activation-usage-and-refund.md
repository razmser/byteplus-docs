# Activate, use, and cancel Dreamina Seedance 2.5 and 2.0 series models

This topic describes the activation requirements, billing methods, deduction order, and cancellation rules for Dreamina Seedance 2.5 and Dreamina Seedance 2.0 series models (hereafter referred to as Seedance models).

<span id="activate-models"></span>
## Activate models

<span id="meet-the-activation-requirements"></span>
### Meet the activation requirements

You can activate Seedance 2.5, Seedance 2.0, Seedance 2.0 mini, and Seedance 2.0 fast by meeting any of the following requirements.


<span aceTableMode="list" aceTableWidth="1.5,2.5,2,3,2"></span>
|Activation method |Requirement |Validity period |Scope |Top\-up or purchase entry |
|---|---|---|---|---|
|Credits (Recommended) |If you are a verified Credits user, you can activate the model when your Credits reach **USD 30**. |No expiration |Credits can offset charges for BytePlus products. |[Billing overview](https://console.byteplus.com/finance/overview)<br><br>Credits rules: [Billing account](https://docs.byteplus.com/en/docs/byteplus-platform/billing_account) |
|AI Savings Plan (Recommended) |Purchase a dedicated decreasing AI Savings Plan of **USD 30** or more and maintain a remaining balance. |\* The dedicated USD 30 decreasing AI Savings Plan is valid for one month.<br><br>\* The dedicated USD 60 and USD 100 decreasing AI Savings Plans are valid for three months. |Offsets charges for all BytePlus ModelArk models, including all Seedance models and language, image, and speech models. |\* [Purchase an AI Savings Plan](https://console.byteplus.com/common-buy/AI-SavingsPlans%7C%7Cd9urs77og65q382arfog)<br><br>\* [Purchase a higher-value AI Savings Plan](https://www.byteplus.com/en/product/ai-savingsplans) |
|Resource pack |Purchase model resource packs worth **USD 30** or more and maintain a remaining quota. |90 days |Offsets charges only for the corresponding model. |[Resource pack rules](seedance-2-0-model-resource-pack-rules.md) |


<span id="activate-models"></span>
### Activate models


1. Go to [Model activation](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=model) and activate the corresponding models.

2. Go to [API key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D) to obtain an API key and make online inference requests.


<span id="billing-and-usage"></span>
## Billing and usage

After activation, token\-based billing is enabled for the models. Each request is billed based on the model called and its actual token usage, and charges are automatically deducted in a fixed order.

<span id="deduction-order"></span>
### Deduction order

Charges incurred by model calls are automatically deducted in the following order:


1. Resource pack: If a resource pack for the model has remaining token quota, the quota is deducted first.

2. AI Savings Plan: If you have purchased a plan and it has a remaining balance, the plan balance can offset pay\-as\-you\-go charges.

3. Account balance (pay\-as\-you\-go): Charges are calculated from actual token usage and deducted from the account balance in real time.


<span id="compare-billing-methods"></span>
### Compare billing methods


<span aceTableMode="list" aceTableWidth="1.5,2.5,3"></span>
|Billing method |Token usage statistics |Balance management and monitoring |
|---|---|---|
|Resource pack |Go to [Usage statistics](https://ai.byteplus.com/ark/region:ap-southeast-1/usageTracking?usageTrackingTab=model) to view token usage for the current model. |Go to [Resource pack management](https://console.byteplus.com/finance/resource-package) to view the remaining quota, validity period, and other status information for resource pack instances. |
|AI Savings Plan ||Go to [AI Savings Plan](https://console.byteplus.com/finance/saving-plan) to view the plan balance, validity period, and other status information. |
|Available balance ||Go to [Billing overview](https://console.byteplus.com/finance/overview) to view the current account balance. For rules on available balance, see [Available Balance components](https://docs.byteplus.com/en/docs/byteplus-platform/Available_Balance_components). |


<span id="related-model-documentation"></span>
### Related model documentation

For more information about using the models, see the following documents:


* [Model pricing](model-pricing.md#8f25f772): Learn about token billing and estimation methods to understand video generation costs for different specifications.

* [Video Generation API](create-video-generation-task-api.md): Learn how to integrate and use the API.

* [Dreamina Seedance 2.5 tutorial](seedance-2-5.md): Learn about Seedance 2.5 capabilities and how to use the Video Generation API.

* [Dreamina Seedance 2.5 prompt guide](seedance-2-5-prompt-guide.md): Learn how to write prompts for Seedance 2.5 to generate high\-quality videos that meet your requirements.

* [Dreamina Seedance 2.0 series tutorial](seedance-2-0.md): Learn about the capabilities of Seedance 2.0 series models and how to use the Video Generation API.

* [Dreamina Seedance 2.0 series prompt guide](seedance-2-0-prompt-guide.md): Learn how to write prompts for Seedance 2.0 to generate high\-quality videos that meet your requirements.


<span id="cancellation-and-withdrawal"></span>
## Cancellation and withdrawal


<span aceTableMode="list" aceTableWidth="1.5,8.5"></span>
|Activation threshold type |Cancellation or withdrawal rules |
|---|---|
|Credits |Credits cannot be withdrawn after purchase. For details, see [Billing account](https://docs.byteplus.com/en/docs/byteplus-platform/billing_account#5QFRSz9f). |
|AI Savings Plan |Cannot be canceled. |
|Resource pack |Cannot be canceled. |
