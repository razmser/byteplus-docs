# Hermes Agent

[Hermes Agent](https://hermes-agent.nousresearch.com/) is an open\-source autonomous AI agent developed by Nous Research, featuring persistent memory across conversations and self\-learning capabilities. This article describes how to use the ModelArk Coding Plan in Hermes Agent.

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

# Prerequisites

Hermes Agent can be installed on macOS, Linux, and Windows systems.

# Install and configure

## Install

Run the following command based on your operating system to install Hermes Agent. During installation, dependencies such as uv, Python, Node.js, ripgrep, and ffmpeg will be detected and installed. Reload your shell or terminal after installation completes. For more installation instructions, see the [Hermes Agent official documentation](https://hermes-agent.nousresearch.com/docs/getting-started/installation).

**macOS / Linux**

1. Run the following cURL command to install:

   ```Bash
   curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
   ```

2. Reload your shell after installation completes.

   ```Bash
   source ~/.bashrc   # or: source ~/.zshrc
   ```

**Windows**

1. Run the following command in PowerShell to install:

   ```PowerShell
   iex (irm https://hermes-agent.nousresearch.com/install.ps1)
   ```

2. After installation completes, reopen the PowerShell window, or run `. $PROFILE` to reload the environment variables.

## Configure

### Method 1: Automated configuration (recommended)

ArkCLI Helper is a coding tool assistant that helps quickly configure the selected tool to connect to Coding Plan. Install and run it, then follow the on\-screen prompts to complete the tool configuration automatically, reducing manual setup time and the risk of errors.

> **Note**

* > ArkCLI Helper is an upgraded tool configuration service evolved from Ark Helper. Related capabilities will continue to iterate in ArkCLI Helper. We recommend using ArkCLI Helper for tool configuration.

* > ArkCLI Helper supports macOS, Linux, and Windows.

1. Install Ark CLI.

   ```Bash
   # 1. Install Ark CLI through npm
   npm install -g @byteplus/ark-cli
   
   # 2. Verify the installation
   arkcli --version
   ```

2. Sign in to Ark CLI.

   ```Bash
   arkcli auth login
   ```

   Follow the on\-screen prompts to select a sign\-in method. On first sign\-in, complete the following configuration as prompted:

   1. Select a project: Choose one that fits your needs, or use the default.

   2. Select `coding-plan`.

3. Run ArkCLI Helper and follow the on\-screen prompts to complete the Hermes Agent configuration.

   1. Enter the `arkcli helper` command in the terminal to start ArkCLI Helper.

   2. Select the Plan profile to configure: Choose `coding-plan_ap-southeast-1_personal (Coding Plan)`.

   3. Select the model to use.

   4. Select the AI agent to configure: `Hermes Agent`.

After you complete these steps, Hermes Agent is configured automatically. No manual installation is required.

### Method 2: Configure manually

Coding Plan is compatible with the OpenAI interface protocol and supports both Responses API and Chat API. The Responses API configuration is recommended for better reasoning quality.

**Responses API**

> **warning**

> To use Responses API, set `api_mode: codex_responses`.

Run the following commands to configure Hermes Agent. Replace `<ARK_API_KEY>` with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey). For supported models, see [Key configuration](coding-plan-personal-ai-hermes-agent.md#8f1d9f20).

```Bash
hermes config set model.provider custom
hermes config set model.base_url https://ark.ap-southeast.bytepluses.com/api/coding/v3
hermes config set model.api_key <ARK_API_KEY>
hermes config set model.default ark-code-latest
hermes config set model.api_mode codex_responses
```

After configuration, the contents of `~/.hermes/config.yaml` are as follows. You can also edit this file directly.

```YAML
model:
  default: ark-code-latest
  provider: custom
  base_url: https://ark.ap-southeast.bytepluses.com/api/coding/v3
  api_key: <ARK_API_KEY>
  api_mode: codex_responses
```

**Chat API**

> **warning**

> To use Chat API, set `api_mode: chat_completions`.

Run the following commands to configure Hermes Agent. Replace `<ARK_API_KEY>` with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey). For supported models, see [Key configuration](coding-plan-personal-ai-hermes-agent.md#8f1d9f20).

```Bash
hermes config set model.provider custom
hermes config set model.base_url https://ark.ap-southeast.bytepluses.com/api/coding/v3
hermes config set model.api_key <ARK_API_KEY>
hermes config set model.default ark-code-latest
hermes config set model.api_mode chat_completions
```

After configuration, the contents of `~/.hermes/config.yaml` are as follows. You can also edit this file directly.

```YAML
model:
  default: ark-code-latest
  provider: custom
  base_url: https://ark.ap-southeast.bytepluses.com/api/coding/v3
  api_key: <ARK_API_KEY>
  api_mode: chat_completions
```

> **tip**

* > To use a 1M context window, simply select a model that supports 1M context; Hermes Agent adjusts automatically without additional configuration.

* > If a model supports 1M context but Hermes Agent does not detect it correctly, manually add or edit the `context_length` field for the corresponding model in `~/.hermes/config.yaml`: `context_length: 1048576`.

## Get started

1. Run the following command to turn on Hermes Agent.

   ```Bash
   hermes
   ```

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/183992bd59e84921b3cd540e4fc98642~tplv-goo7wpa0wc-image.image)

2. Get started with Hermes Agent. For supported CLI Interface, see [Documentation](https://hermes-agent.nousresearch.com/docs/user-guide/cli).

# Additional operations

## Switch model

You can switch the **text generation model** at any time.

|Configuration method |Via console |By editing configuration file |
|---|---|---|
|Switch model |1. Refer to [Configure](coding-plan-personal-ai-hermes-agent.md#14207659) and set the model name to `ark-code-latest`.<br><br>2. On the [subscription management page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=subscribe), select the model to use. The switch takes effect within 3\-5 minutes. |Refer to [Configure](coding-plan-personal-ai-hermes-agent.md#14207659) and set the model name to the target model. |

# Integrate Feishu bot

> **Note**

> This documentation is based on Hermes Agent v0.8.0 (2026.4.8). The configuration steps may vary as the tool evolves. Refer to the instructions below to configure key information.

1. Click this [link](https://open.larksuite.com/page/openclaw?form=multiAgent) to quickly create a Feishu bot.

2. Run the following command in the terminal to configure the information platform.

   ```Bash
   hermes gateway setup
   ```

* **Select a platform to configure**: select `Feishu / Lark`

* **How would you like to set up Feishu / Lark**: select `Enter existing App ID and App Secret manually`

* Configure the **App ID** and **App Secret** corresponding to the bot you created.

* **Domain**: select `feishu`

* **Connection mode**: select `WebSocket`

* **How should direct messages be authorized**: select `Use DM pairing approval`

* **How should group chats be handled**: select `Respond only when @mentioned in groups`

* **Home chat ID**: do not configure for now

* After you select **done**, **Install the gateway as a launchd service**: select `Y`

* **Start the service now**: select `Y`

3. Open the newly created Feishu bot in Feishu and send any message to obtain the pairing code.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/c2011e63fa4f4be8af7b0a56c3ea1301~tplv-goo7wpa0wc-image.image)

4. Run the following command in the terminal to configure Feishu. Replace `<pairing-code>` with the obtained pairing code.

   ```Bash
   hermes pairing approve feishu <pairing-code>
   ```

5. Send the `/sethome` command in Feishu to set the Feishu conversation as the primary conversation. You can then use Hermes Agent normally in Feishu.
