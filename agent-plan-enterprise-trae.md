# TRAE

This topic describes how to configure and use ModelArk Agent Plan Team in TRAE.

# Prerequisites

An administrator must complete the following prerequisite actions before subaccounts can configure and use TRAE:

1. Purchase a ModelArk Agent Plan Team plan: [1. Purchase Agent Plan Team](agent-plan-enterprise-get-started.md#purchase-plan).

2. Assign seats to subaccounts: [2. Assign seats](agent-plan-enterprise-get-started.md#assign-seats).

# Install TRAE

Visit the [TRAE website](https://www.trae.ai/) to download and install the version for your operating system.

# Configure TRAE

1. Add a model in the TRAE Team console.

   1. Sign in to the [TRAE Team console](https://console.enterprise.trae.cn/).

   2. In the left menu, click **Enterprise configuration** \> **Models**. In the upper\-right corner of **Enterprise built\-in models**, click **Add Model**.

2. Configure the following information in the **Add Model** window.

   1. Model provider: **Custom**.

   2. Model family: **Other**.

   3. Model name: Configure the name of a **text generation model**. For details, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

   4. URL: `https://ark.ap-southeast.bytepluses.com/api/plan/v3`

   5. API Key: [Get a dedicated API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine)

      ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/agent-plan-enterprise-api-key.png)

# Use TRAE

Select the configured model in the lower\-right corner of the AI chat input box, send a simple task, and check the actual response.

# Other operations

## Add image and video generation capabilities

ModelArk Agent Plan Team Small and higher plans provide image generation model quotas, and Large and higher plans provide video generation model quotas. ModelArk provides Skills to help AI tools integrate and use these capabilities. For configuration instructions, see [Video and image generation models](agent-plan-enterprise-visual-models.md).

> **warning**

* > Supported visual models vary by plan. For details, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

* > Visual models cannot be switched through Auto or the console.

## Switch models

You can switch **text generation models** at any time as needed.

|Configuration method |Through the console |Through TRAE |
|---|---|---|
|Switch model |1. See [Configure TRAE](agent-plan-enterprise-trae.md#configure-trae) and configure the model name as `ark-code-latest`. |1. See [Configure TRAE](agent-plan-enterprise-trae.md#configure-trae) and add model information. |
| |2. On the [Agent Plan Team console](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine) page, click the **Use configuration** tab, select a model, and wait 3 to 5 minutes for the change to take effect. |2. In the lower\-right corner of the AI chat input box, click the current model name and select a configured model from the list. |
