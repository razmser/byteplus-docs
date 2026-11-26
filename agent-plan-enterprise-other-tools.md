# Other tools

ModelArk Agent Plan Team supports the OpenAI and Anthropic API protocols. Configure the Base URL, API key, and model for the applicable protocol to use it in compatible tools.

# Prerequisites

An administrator must complete the following actions before subaccounts can configure and use third\-party tools:

1. **Purchase a plan**: Purchase a ModelArk Agent Plan Team plan. For details, see [1. Purchase Agent Plan Team](agent-plan-enterprise-get-started.md#purchase-plan).

2. **Assign seats**: Assign seats to subaccounts. For details, see [2. Assign seats](agent-plan-enterprise-get-started.md#assign-seats).

> **warning**

> The Agent Plan Team exclusive API Key is different from the API key for the ModelArk platform. Do not use them interchangeably. Use [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine) when configuring a tool.

> ![](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/agent-plan-enterprise-api-key.png)

# Applicable tools

|API protocol |Base URL |Applicable tools |
|---|---|---|
|Anthropic\-compatible API protocol |`https://ark.ap-southeast.bytepluses.com/api/plan` |Claude Code and more |
|OpenAI\-compatible API protocol |`https://ark.ap-southeast.bytepluses.com/api/plan/v3` |Codex CLI, OpenCode, OpenClaw, TraeCode, Hermes Agent, WorkBuddy, ZCode, DeepSeek harness, Pi, Cline, Cursor, Roo Code, Kilo Code, and more |

# Cline

## Install Cline

Open VSCode, search for `Cline` in the Extensions marketplace, and install it.

## Configure Cline

After Cline is installed, configure the following information.

* **API Provider**: `OpenAI Compatible`

* **Base URL**: `https://ark.ap-southeast.bytepluses.com/api/plan/v3`

* **API Key**: [Get exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine)

* **Model ID**: Configure `ark-code-latest` or the Model Name of a text generation model. For model information, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

After configuration, enter a request in the input box to interact with the model.

# Cursor

## Install Cursor

Visit the [Cursor website](https://cursor.com/) to download and install Cursor.

## Configure Cursor

> **tip**

> Due to Cursor limitations, only users subscribed to Cursor Pro or a higher plan can configure custom models.

After Cursor is installed, configure the following information in **Models**.

* **OpenAI API Key**: [Get exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine)

* **Override OpenAI Base URL**: `https://ark.ap-southeast.bytepluses.com/api/plan/v3`

* **Add Custom Model**: Configure `ark-code-latest` or the Model Name of a text generation model. For model information, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

> **warning**

> If you encounter a name conflict when configuring a model in Cursor, we recommend using the model name instead. For example, change `deepseek-v4.1-flash` to `deepseek-v4-1-flash`.

After configuration, select the configured model in the chat panel to interact with it.

# Roo Code

## Install Roo Code

> **tip**

> If a model performs unexpectedly, update Roo Code to version 3.43.0 or later.

Open VSCode, search for `Roo Code` in the Extensions marketplace, install it, and trust the publisher after installation.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/dd47c5150044469cb4e95b8de067d817~tplv-goo7wpa0wc-image.image)

## Configure Roo Code

After Roo Code is installed, configure the following information.

* **API Provider**: `OpenAI Compatible`

* **Base URL**: `https://ark.ap-southeast.bytepluses.com/api/plan/v3`

* **API Key**: [Get exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine)

* **Model**: Configure `ark-code-latest` or the Model Name of a text generation model. For model information, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

After configuration, enter a request in the input box to interact with the model.

# Kilo Code

## Install Kilo Code

Open VSCode, search for `Kilo Code` in the Extensions marketplace, install it, and trust the publisher after installation.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/3e991c168c0c49bc9449f6f6efc29bd7~tplv-goo7wpa0wc-image.image)

## Configure Kilo Code

Select **Settings** \- **Providers** \- **Custom provider**, then configure the following information.

* **Provider ID**: Enter a unique identifier for the provider.

* **Display Name**: Enter the provider display name.

* **API Provider**: `OpenAI Compatible`

* **Base URL**: `https://ark.ap-southeast.bytepluses.com/api/plan/v3`

* **API Key**: [Get exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine)

* **Model ID**: Configure `ark-code-latest` or the Model Name of a text generation model. For model information, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

* **Model Name**: Enter the model display name.

> **warning**

> The Kilo Code client UI changes frequently. Field names, order, or quantity might vary by version. If this topic differs from the client interface, follow the actual client prompts.

After configuration, enter a request in the input box to interact with the model.
