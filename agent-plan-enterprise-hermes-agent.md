# Hermes Agent

This topic describes how to configure and use ModelArk Agent Plan Team in Hermes Agent.

# Prerequisites

* An administrator must complete the following prerequisites before subaccounts can configure and use Hermes Agent:

   1. Purchase a ModelArk Agent Plan Team plan: [1. Purchase Agent Plan Team](agent-plan-enterprise-get-started.md#purchase-plan).

   2. Assign seats to subaccounts: [2. Assign seats](agent-plan-enterprise-get-started.md#assign-seats).

* Hermes Agent can be installed on macOS, Linux, and Windows.

# Install Hermes Agent

Run the following command for your operating system to install Hermes Agent. The installation detects and installs dependencies including uv, Python, Node.js, ripgrep, and ffmpeg. Reload your shell or terminal after installation. For more information, see the [Hermes Agent documentation](https://hermes-agent.nousresearch.com/docs/getting-started/installation).

**macOS / Linux**

1. Run the following cURL command:

   ```Bash
   curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
   ```

2. Reload your shell after installation.

   ```Bash
   source ~/.bashrc   # or: source ~/.zshrc
   ```

**Windows**

1. Run the following command in PowerShell:

   ```PowerShell
   iex (irm https://hermes-agent.nousresearch.com/install.ps1)
   ```

2. Reopen PowerShell after installation, or run `. $PROFILE` to refresh environment variables.

# Configure Hermes Agent

## Configuration information

If you are familiar with Hermes Agent or already have a local configuration, obtain the following information to configure it directly.

* **Base URL**: `https://ark.ap-southeast.bytepluses.com/api/plan/v3`.

* **API Key**: [Obtain exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine).

* **Model**: Configure a text generation model. You can configure `ark-code-latest` or a Model Name. For model information, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

## Method 1: One\-click configuration (recommended)

Ark CLI is the official command\-line tool for the ModelArk platform. It supports fast authentication and resource management in a local terminal, and can install capabilities in commonly used AI agents, reducing the time and errors associated with manual configuration.

1. Install Ark CLI

   Environment requirements: Node.js 18 or later. macOS, Linux, and Windows are supported.

   ```Bash
   npm install -g @byteplus/ark-cli@latest
   arkcli --version
   ```

2. Sign in to Ark CLI

   Complete sign\-in authorization as prompted.

   ```Bash
   arkcli auth login
   ```

3. Configure Hermes Agent with one click

   ```Bash
   arkcli helper
   ```

Run ArkCLI Helper and complete the configuration as prompted.

* **Select a profile**: `agent-plan-team_ap-southeast-1_team`.

* **Select the default model**: Select as needed.

* **Select an AI agent**: `Hermes Agent`.

   You can configure harness capabilities. Select `Configure` or `Skip` as needed.

After the configuration is complete, Hermes Agent automatically recognizes the configured model. Start Hermes Agent to begin using it.

## Method 2: Manual configuration

Agent Plan Team is compatible with the OpenAI API protocol and supports the Responses API and Chat API. We recommend the Responses API configuration for better inference performance.

**Responses API**

> To use the Responses API, configure `api_mode: codex_responses`.

Run the following commands to configure Hermes Agent. Replace `<ARK_API_KEY>` with your own [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine). For available models, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/agent-plan-enterprise-api-key.png)

```Bash
hermes config set model.provider custom
hermes config set model.base_url https://ark.ap-southeast.bytepluses.com/api/plan/v3
hermes config set model.api_key <ARK_API_KEY>
hermes config set model.default ark-code-latest
hermes config set model.api_mode codex_responses
```

After configuration, the `~/.hermes/config.yaml` configuration file is as follows. You can also edit this file directly.

```YAML
model:
  default: ark-code-latest
  provider: custom
  base_url: https://ark.ap-southeast.bytepluses.com/api/plan/v3
  api_key: <ARK_API_KEY>
  api_mode: codex_responses
```

**Chat API**

> To use the Chat API, configure `api_mode: chat_completions`.

Run the following commands to configure Hermes Agent. Replace `<ARK_API_KEY>` with your own [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine). For available models, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/agent-plan-enterprise-api-key.png)

```Bash
hermes config set model.provider custom
hermes config set model.base_url https://ark.ap-southeast.bytepluses.com/api/plan/v3
hermes config set model.api_key <ARK_API_KEY>
hermes config set model.default ark-code-latest
hermes config set model.api_mode chat_completions
```

After configuration, the `~/.hermes/config.yaml` configuration file is as follows. You can also edit this file directly.

```YAML
model:
  default: ark-code-latest
  provider: custom
  base_url: https://ark.ap-southeast.bytepluses.com/api/plan/v3
  api_key: <ARK_API_KEY>
  api_mode: chat_completions
```

> **tip**

* > To use a 1M context window, select a model that supports a 1M context window. Hermes Agent adjusts automatically, with no manual configuration required.

* > If the model supports a 1M context window but Hermes Agent does not recognize it correctly, manually add or modify the corresponding model's `context_length` field in `~/.hermes/config.yaml`: `context_length: 1048576`.

# Use Hermes Agent

1. Run the following command to start Hermes Agent.

   ```Bash
   hermes
   ```

2. Start using Hermes Agent. For supported CLI interfaces, see the [official documentation](https://hermes-agent.nousresearch.com/docs/user-guide/cli).

# Other operations

## Add image and video generation capabilities

ModelArk Agent Plan Team Small and higher plans provide image generation model quotas, and Large and higher plans provide video generation model quotas. ModelArk provides Skills to help AI tools integrate and use these capabilities. For configuration instructions, see [Video and image generation models](agent-plan-enterprise-visual-models.md).

> **warning**

* > Visual models supported by each plan vary. For details, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

* > Visual models cannot be used through Auto or console switching.

## Switch models

You can switch **text generation models** at any time as needed.

|Configuration method |Through the console |Modify the configuration file |
|---|---|---|
|Model switching |1. Refer to [Configure Hermes Agent](agent-plan-enterprise-hermes-agent.md#14207659) and configure `model.default` as `ark-code-latest`.<br><br>2. On the [Agent Plan Team console](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine) page, click the **Use configuration** tab, select a model, and wait 3 to 5 minutes for the change to take effect. |1. Refer to [Configure Hermes Agent](agent-plan-enterprise-hermes-agent.md#14207659) and configure `model.default` as the Model Name to use.<br><br>2. Restart Hermes Agent for the change to take effect. |

## Connect a Feishu bot

> **warning**

> This document is based on Hermes Agent v0.8.0 (2026.4.8). Configuration steps can differ as the tool evolves. Use the following information to configure the key settings.

1. Click this [link](https://open.larkoffice.com/page/openclaw?form=multiAgent) to quickly create a Feishu bot.

2. Run the following command in a terminal to configure the messaging platform.

   ```Bash
   hermes gateway setup
   ```

   * **Select a platform to configure**: Select `Feishu / Lark`

   * **How would you like to set up Feishu / Lark**: Select `Scan QR code to create a new bot automatically (recommended)`, then scan the QR code with the Feishu client to create a Feishu bot.

   * **How should direct messages be authorized**: Select `Use DM pairing approval`

   * **How should group chats be handled**: Select `Respond only when @mentioned in groups`

   * **Home chat ID**: Do not configure this now.

   * After selecting **done**, select `Y` for **Install the gateway as a launchd service**.

   * Select `Y` for **Start the service now**.

3. Open the Feishu bot you just created in Feishu and send any message to obtain a pairing code.

4. Run the following command in a terminal to configure Feishu. Replace `<pairing-code>` with the pairing code you obtained.

   ```Bash
   hermes pairing approve feishu <pairing-code>
   ```

5. Send the `/sethome` command in Feishu to set the Feishu conversation as the primary conversation. You can then use Hermes Agent in Feishu.
