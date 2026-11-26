# Codex

This topic describes how to configure and use ModelArk Agent Plan in Codex.

# Prerequisites

Subscribe to the Agent Plan plan as needed. If you have not subscribed, visit the [Agent Plan console](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan) to subscribe to a plan.

# Codex CLI

## Install Codex CLI

> **tip**

> Agent Plan supports the Responses API and can use the latest Codex CLI.

**Prerequisite**: Install [Node.js 18 or later](https://nodejs.org/en/download/).

Run the following command in the command\-line interface to install Codex CLI.

```Bash
npm i -g @openai/codex
```

After installation, run the following command to check the version.

```Bash
codex --version
```

## Configure Codex CLI

### Method 1: One\-click configuration (recommended)

Ark CLI is the official command\-line tool for the ModelArk platform. It supports quickly completing authentication and resource management in a local terminal, and installing capabilities in commonly used AI agents to reduce the time and error risk of manual configuration.

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

3. Configure Codex with one click

   ```Bash
   arkcli helper
   ```

Run ArkCLI Helper and complete configuration as prompted.

* **Select Profile**: `agent-plan_ap-southeast-1_personal`.

* **Select the default model**: Select as needed.

* **Select AI Agent**: `Codex`.

   harness capabilities can be configured. Select `Configure` or `Skip` as prompted.

After configuration, Codex automatically recognizes the configured model. Start Codex to use it.

### Method 2: Manual configuration

1. Create or edit `~/.codex/config.toml` (Windows path: `%USERPROFILE%\.codex\config.toml`) and configure the following information:

   * `model`: Configure a text generation model. You can configure `ark-code-latest` or a model name. For model information, see [Supported models and harness](agent-plan-personal-plan-overview.md#supported-models-harness).

   ```PlainText
   model = "<MODEL_NAME>"
   model_provider = "byteplus-agent-plan"
   cli_auth_credentials_store = "file"
   
   [model_providers.byteplus-agent-plan]
   name = "byteplus-agent-plan"
   base_url = "https://ark.ap-southeast.bytepluses.com/api/plan/v3"
   requires_openai_auth = true
   wire_api = "responses"
   ```

   > **warning**

   * > `model_reasoning_effort`: Controls the reasoning length. You can set it to `low`, `medium`, or `high`.

2. Create or edit `~/.codex/auth.json` (Windows path: `%USERPROFILE%\.codex\auth.json`), then enter your [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan) in the **OPENAI_API_KEY** field.

   ```JSON
   {
       "OPENAI_API_KEY": "<ARK_API_KEY>"
   }
   ```

**OPENAI_API_KEY** is a fixed field name in the Codex authentication file. Enter your [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan) here; you do not need to set an environment variable or sign in to OpenAI. `auth.json` contains a plaintext key. Do not commit it to a code repository or share it.

After configuration, you can use Codex CLI.

## Use Codex CLI

Run the following command to start Codex CLI.

```Bash
codex
```

# Codex desktop client

After an OpenAI update, the standalone Codex application was retired and integrated as a mode in the ChatGPT desktop app.

## Install the ChatGPT desktop app

Download and install the ChatGPT desktop app from the [OpenAI website](https://openai.com/chatgpt/download/).

## Configure the desktop client

After signing in to the ChatGPT desktop app, switch between ChatGPT Work and Codex from the upper\-left menu. After switching to Codex mode, complete the following configuration steps.

1. Click **Settings** in the lower\-left corner.

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/codex-desktop-settings-menu.png)

2. On the **Configuration** page, click **Open config.toml**.

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/codex-desktop-settings-config.png)

3. Edit `config.toml` and configure the following information.

   * `model`: Configure a text generation model. You can configure `ark-code-latest` or a model name. For model information, see [Supported models and harness](agent-plan-personal-plan-overview.md#supported-models-harness).

   ```PlainText
   model = "<MODEL_NAME>"
   model_provider = "byteplus-agent-plan"
   cli_auth_credentials_store = "file"
   
   [model_providers.byteplus-agent-plan]
   name = "byteplus-agent-plan"
   base_url = "https://ark.ap-southeast.bytepluses.com/api/plan/v3"
   requires_openai_auth = true
   wire_api = "responses"
   ```

   > **warning**

   * > `model_reasoning_effort`: Controls the reasoning length. You can set it to `low`, `medium`, or `high`.

4. Create or edit `~/.codex/auth.json` (Windows path: `%USERPROFILE%\.codex\auth.json`), then enter your [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan) in the **OPENAI_API_KEY** field.

   ```JSON
   {
       "OPENAI_API_KEY": "<ARK_API_KEY>"
   }
   ```

**OPENAI_API_KEY** is a fixed field name in the Codex authentication file. Enter your [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan) here; you do not need to set an environment variable or sign in to OpenAI. `auth.json` contains a plaintext key. Do not commit it to a code repository or share it.

## Use the desktop client

After configuration, quit and reopen the ChatGPT desktop app for the configuration to take effect. After restarting, you can use it in the chat interface.
