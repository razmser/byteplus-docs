# DeepSeek Harness

This topic describes how to configure and use ModelArk Agent Plan in DeepSeek harness.

> **warning**

> DeepSeek harness is evolving rapidly. Future changes might break compatibility.

# Prerequisites

* Subscribe to the Agent Plan plan as needed. If you have not subscribed, visit the [Agent Plan console](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan) to subscribe to a plan.

* Install Node.js. We recommend using an LTS release.

# Install DeepSeek harness

Choose one of the following methods to use DeepSeek harness:

* **Desktop app**: Visit the [DeepSeek Harness website](https://www.deepseek.com/harness/) to download and install the version for your operating system.

* **Web UI**: Run the following command in a terminal to start the DeepSeek harness Web UI, then open the access URL in the command output in a browser. The default address is `http://127.0.0.1:3080/`.

   ```Bash
   npx @deepseek-ai/dsh web
   ```

# Configure DeepSeek harness

You can connect ModelArk Agent Plan to DeepSeek harness using any of the following methods:

* **Automated helper**: Use ArkCLI Helper to guide tool configuration.

* **Manual configuration**: Configure each setting in the DeepSeek harness desktop app or Web UI.

* **Configuration through the Ark plugin**: Use the official Ark plugin to automatically add a provider; you only need to enter an API key.

## Method 1: One\-click configuration (recommended)

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

3. Configure DeepSeek harness with one click

   ```Bash
   arkcli helper
   ```

Run ArkCLI Helper and complete configuration as prompted.

* **Select Profile**: `agent-plan_ap-southeast-1_personal`.

* **Select the default model**: Select as needed.

* **Select AI Agent**: `DeepSeek Harness`.

   Harness capabilities can be configured. Select `Configure` or `Skip` as prompted.

After configuration, DeepSeek harness automatically recognizes the configured model. Start DeepSeek harness to use it.

## Method 2: Manual configuration

1. In the DeepSeek harness desktop app or Web UI, click **Settings** in the lower\-left corner.

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/deepseek-settings.png)

2. Click **Models** in the left navigation and configure it according to the interface.

   * Method one: Configure the DeepSeek card

      On the DeepSeek card, select **Customized settings**, enter the following information, and save.

      * **API key**: Enter [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan).

      * **Base URL**: `https://ark.ap-southeast.bytepluses.com/api/plan/v3`

      * **Add model**: Add a **text generation model** supported by the plan as needed. For the model list, see [Supported models and harness](agent-plan-personal-plan-overview.md#supported-models-harness).

      ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/deepseek-model-config.png)

   * Method two: Add a custom provider

      Click **Custom Model API**, then enter the following information to create the provider.

      * **Provider ID**: Set a custom value. To rename it later, add a new provider and delete the old provider.

      * **Display name**: Set a custom value.

      * **Base URL** and **API protocol**: The supported API addresses and protocols are mapped as follows. Ensure that they are configured correctly.

         |API protocol |Base URL |
         |---|---|
         |openai\-completions |`https://ark.ap-southeast.bytepluses.com/api/plan/v3` |
         |openai\-responses |`https://ark.ap-southeast.bytepluses.com/api/plan/v3` |
         |anthropic\-messages |`https://ark.ap-southeast.bytepluses.com/api/plan` |

      * **API key**: Enter [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan).

      * **Add model**: Add a **text generation model** supported by the plan as needed. For the model list, see [Supported models and harness](agent-plan-personal-plan-overview.md#supported-models-harness).

3. (Optional) To configure a model that supports image input, configure it in the configuration file.

   1. On the model configuration page, click **Open configuration file**. The path is `$DSH_HOME/settings.yaml`.

   2. Add `input: [text, image]` to the supported model configuration section. The following is an example.

      ```Yaml
      - id: llm-pi-ai
          config:
          providers:
              ark-model:
              displayName: ark-model
              apiKeyEnv: ARK_MODEL_API_KEY
              api: openai-responses
              baseURL: https://ark.ap-southeast.bytepluses.com/api/plan/v3
              models:
                  - id: dola-seed-2.1-turbo
                  name: dola-seed-2.1-turbo
                  input: [text, image]
      ```

# Use DeepSeek harness

1. Click **Select workspace** on the page and add the project directory in which you started `dsh`. The chat input box is available only after a workspace is selected.

2. Select the configured model from the model dropdown menu in the lower\-right corner of the input box to start using it. For the list of available models, see [Supported models and harness](agent-plan-personal-plan-overview.md#supported-models-harness).

3. Enter your request in the input box to start using DeepSeek harness.

   An agent can read and edit workspace files, run commands, delegate work, and maintain a plan. When an action requires approval under the current permission policy, the Web UI asks you first.
