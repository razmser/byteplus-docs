# Other tools

ModelArk Coding Plan is compatible with Anthropic and OpenAI protocols, and can be integrated with the corresponding tool ecosystem.

Developers can quickly integrate and use related tools by configuring the dedicated Coding Plan Base URL and specifying supported models.

**Configure model name (via config file)**

# Key configuration

The key configurations for integrating tools are as follows:

## **Model selection**Specify the name of the model to be used in the configuration file of the tool.

The following models are supported:

* \`dola-seed-2.0-pro\`

* \`dola-seed-2.0-lite\`

* \`dola-seed-2.0-code\`

* \`glm-5.3-flash\`

* \`glm-5.2\`

* \`deepseek-v4.1-flash\`

* \`deepseek-v4-flash\`

* \`deepseek-v4-pro\`

> **Tip**

* > Due to regional regulatory requirements and other applicable considerations, the availability and features of the Model Service may vary by country or region. The final offering shall be subject to BytePlus’s actual provision.

* > The DeepSeek models are provided in compliance with applicable local laws and regulations.

**Configure ark-code-latest (in the console)**

1. Specify the model as \`ark-code-latest\` in the configuration file.

2. Select or switch the target model via the [management page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=subscribe).

Auto mode is supported, and the model is automatically selected using an intelligent algorithm based on both "effect" and "speed" dimensions.

## **Base URL**

The Base URL differs by tool depending on the protocol it is compatible with:

* Tools compatible with the Anthropic protocol: https://ark.ap\-southeast.bytepluses.com/api/coding

* Tools compatible with the OpenAI protocol: https://ark.ap\-southeast.bytepluses.com/api/coding/v3

> **Note**

> Do not use the Base model URL (https://ark.ap\-southeast.bytepluses.com/api/v3). Requests sent to this Base URL do not consume your Coding Plan quota and will instead incur additional charges.

> **Tip**

> You can create an API key in the [console](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D).

# Available tools

|Interface Protocol |Base URL |Compatible Tools |
|---|---|---|
|Compatible with Anthropic interface protocol |`https://ark.ap-southeast.bytepluses.com/api/coding` |Claude Code |
|Compatible with OpenAI interface protocol |`https://ark.ap-southeast.bytepluses.com/api/coding/v3` |OpenCode, OpenClaw, TraeCode, Hermes Agent, Codex CLI, Cline, Cursor, Kilo Code, Roo Code, and more |

# Integrate with Cline

## Install

Open VSCode and search for `Cline` in the extension marketplace to install.

## Configure

After the Cline plugin is installed, you need to configure the following information.

* **API Provider**: `OpenAI Compatible` (Coding Plan is compatible with the OpenAI standard)

* **Base URL**: `https://ark.ap-southeast.bytepluses.com/api/coding/v3`

* **API Key**: [Get API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* **Model ID**: Specify either `ark-code-latest` or a supported model here. See [Key configuration](coding-plan-personal-ai-other-tools.md#59b5ff11) for details.

After configuration is complete, you can input requirements in the input box and interact with the model.

# Integrate with Cursor

## Install

Official website installation package: Download and install Cursor from the [Cursor official website](https://cursor.com/features).

## Configure

> **Tip**

> Due to Cursor limitations, only users subscribed to Cursor Pro or higher plans can customize model configurations.

After Cursor is installed, you must log in to a paid account to configure Models. The specific configuration for the Models module is as follows:

* OpenAI API Key: [Get API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* Override OpenAI Base URL: `https://ark.ap-southeast.bytepluses.com/api/coding/v3`

* Add Custom Model: Specify either `ark-code-latest` or a supported model here. See [Key configuration](coding-plan-personal-ai-other-tools.md#59b5ff11) for details.

> **Note**

> If `glm-5.2` or `glm-5.3-flash` cannot be used because of a naming conflict, use `glm-5-2` or `glm-5-3-flash`, respectively.

Once configuration is complete, you can select the configured model in the chat panel to interact with the model.

# Integrate with Kilo Code

## Install

Open VSCode, search for `kilo code` in the extension marketplace to install. After installation, select "Trust Publisher".

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/3e991c168c0c49bc9449f6f6efc29bd7~tplv-goo7wpa0wc-image.image)

## Configure

Select **Settings** → **Providers** → **Custom provider**, then configure the following information.

* **Provider ID**: Unique identifier for the provider. Fill in as needed.

* **Display Name**: Display name of the provider. Fill in as needed.

* **API Provider**: `OpenAI Compatible` (Coding Plan is compatible with the OpenAI standard).

* **Base URL**: `https://ark.ap-southeast.bytepluses.com/api/coding/v3`

* **API Key**: [Get API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* **Model ID**: The model name to configure, for example `ark-code-latest`. For supported models, see [Key configuration](coding-plan-personal-ai-other-tools.md#59b5ff11).

* **Model Name**: Display name of the model. Fill in as needed.

> **warning**

> The Kilo Code client UI is updated frequently. Field names, order, and count may change across versions. This document reflects a recent version and is for reference only. If your local UI differs, follow the actual prompts shown in the client.

Once configuration is complete, you can enter your requirements in the input box and interact with the model.

# Integrate with Roo Code

> **tip**

> If model performance is abnormal, we recommend updating the plugin to the latest version (3.43.0 or later).

## Install

Open VSCode, search for `Roo Code` in the extension marketplace to install. After installation, select "Trust Publisher".

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/dd47c5150044469cb4e95b8de067d817~tplv-goo7wpa0wc-image.image)

## Configure

After installation is complete, configure the following information.

* **API Provider**: `OpenAI Compatible`

* **Base URL**: `https://ark.ap-southeast.bytepluses.com/api/coding/v3`

* **API Key**: [Get API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* **Model**: Specify either `ark-code-latest` or a supported model here. See [Key configuration](coding-plan-personal-ai-other-tools.md#59b5ff11) for details.

Once configuration is complete, you can enter your requirements in the input box and interact with the model.

# Error codes

When using the Coding Plan in coding tools, you may encounter errors. You can identify the corresponding error scenario based on the error message and troubleshoot the issue. For details, see [Error codes](error-codes.md).
