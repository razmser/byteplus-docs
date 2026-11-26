# TRAE

This topic describes how to configure and use ModelArk Agent Plan in TRAE.

# Prerequisites

You have subscribed to Agent Plan as needed. If you have not subscribed, visit the [Agent Plan console](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan) to subscribe as needed.

# Install TRAE

Visit the [TRAE website](https://www.trae.ai/) to download and install the version for your operating system.

# Configure TRAE

1. After signing in through the personal user entry, select **Settings** \> **Models**, then click **Add Model**.

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/image-20261116-142946-710.png)

2. Configure the model in the **Add Model** window.

   The following table maps API Format to URL.

|API Format |Base URL |Full URL |
|---|---|---|
|Anthropic Messages |`https://ark.ap-southeast.bytepluses.com/api/plan` |`https://ark.ap-southeast.bytepluses.com/api/plan/v1/messages` |
|OpenAI Chat Completions |`https://ark.ap-southeast.bytepluses.com/api/plan/v3` |`https://ark.ap-southeast.bytepluses.com/api/plan/v3/chat/completions` |
|OpenAI Responses API |`https://ark.ap-southeast.bytepluses.com/api/plan/v3` |`https://ark.ap-southeast.bytepluses.com/api/plan/v3/responses` |

The following configuration uses OpenAI Chat Completions as the API Format example.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/image-20261116-143154-095.png)

1. Select the provider: **Custom Model**.

2. API Format: **OpenAI Chat Completions**.

3. Custom Request URL: `https://ark.ap-southeast.bytepluses.com/api/plan/v3`. If Full URL is enabled, enter the corresponding Full URL.

4. Model ID: For **Model ID**, use the name of a **text generation model**. For details, see [Supported models and harness](agent-plan-personal-plan-overview.md#supported-models-harness).

5. API Key: [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan)

6. Click **Add Model** to complete model configuration.

# Use TRAE

Select the configured model in the lower\-right corner of the AI chat input box, send a simple task, and check the actual response.

# Other operations

## Add image and video generation capabilities

ModelArk Agent Plan Small and higher plans provide image generation model quotas, and Large and higher plans provide video generation model quotas. ModelArk provides Skills to help AI tools integrate and use these capabilities. For configuration instructions, see [Video and image generation models](agent-plan-personal-visual-models.md).

> **warning**

* > Supported visual models vary by plan. For details, see [Supported models and harness](agent-plan-personal-plan-overview.md#supported-models-harness).

* > Visual models cannot be switched through Auto or the console.

## Switch models

You can switch **text generation models** at any time as needed.

|Configuration method |Through the console |Through TRAE |
|---|---|---|
|Switch model |1. See [Configure TRAE](agent-plan-personal-trae.md#configure-trae) and configure the model name as `ark-code-latest`. |1. See [Configure TRAE](agent-plan-personal-trae.md#configure-trae) and add model information. |
| |2. On the [Agent Plan console](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan) page, click the **Use configuration** tab, select a model, and wait 3 to 5 minutes for the change to take effect. |2. In the lower\-right corner of the AI chat input box, click the current model name and select a configured model from the list. |
