# WorkBuddy

This topic describes how to configure and use ModelArk Agent Plan Team in WorkBuddy.

# Prerequisites

An administrator must complete the following actions before subaccounts can configure and use WorkBuddy:

1. Purchase a ModelArk Agent Plan Team plan: [1. Purchase Agent Plan Team](agent-plan-enterprise-get-started.md#purchase-plan).

2. Assign seats to subaccounts: [2. Assign seats](agent-plan-enterprise-get-started.md#assign-seats).

# Install WorkBuddy

Visit the [WorkBuddy website](https://www.workbuddy.ai/) to download and install the version for your operating system. After installation, sign in as prompted.

# Configure WorkBuddy

## Method 1: One\-click configuration (recommended)

Ark CLI is the official command\-line tool for the ModelArk platform. It lets you quickly complete authentication and resource management in a local terminal, and install capabilities into commonly used AI agents to reduce manual configuration time and errors.

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

3. Configure WorkBuddy with one click

   ```Bash
   arkcli helper
   ```

Run ArkCLI Helper and complete configuration as prompted.

* **Select a profile**: `agent-plan-team_ap-southeast-1_team`.

* **Select the default model**: Select as needed.

* **Select an AI agent**: `WorkBuddy`.

   You can configure harness capabilities. Select `Configure` or `Skip` as prompted.

After configuration, WorkBuddy automatically recognizes the configured model. Start WorkBuddy to use it.

## Method 2: Manual configuration

1. Start WorkBuddy, click the account avatar in the lower\-left corner, and select **Settings**.

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/workbuddy-settings-v2.png)

2. Select **Models** in the left navigation, then click **Add Model** in the Custom Models section.

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/workbuddy-add-model-menu.png)

3. Select **Custom** as the provider, complete the following fields, and save:

   * **API endpoint**: `https://ark.ap-southeast.bytepluses.com/api/plan/v3`

   * **API Key**: exclusive API Key ([get it here](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine)).

      ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/agent-plan-enterprise-api-key.png)

   * **Model name**: Enter a Model Name supported by the selected plan. For model information, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness). Configure a text generation model.

   * **Advanced settings**: Optional. Enable **Tool calling**, **Image input**, and **Reasoning** as needed.

   * **Custom protocol**: When a model service uses a nonstandard URL path, such as one wrapped by a gateway or proxy, you can enable the Custom Protocol switch in advanced settings. When enabled, Tencent WorkBuddy sends requests directly to the URL you enter and skips path validation and automatic completion. Disabled (default): Uses the standard `/chat/completions` path, and automatically validates and completes the API endpoint. Enabled: Sends requests directly to the API endpoint entered by the user, and skips path validation and automatic completion.

      ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/workbuddy-custom-protocol-v2.png)

   In addition to adding a custom model in the client UI, you can directly edit the local configuration file `~/.workbuddy/models.json` to connect Agent Plan Team. After saving, WorkBuddy automatically watches for and hot\-loads the new configuration (**no restart required**). The value displayed in the model dropdown on the main page is the `name` field.

   `~/.workbuddy/models.json` example

   ```JSON
   [
     {
       "id": "ark-code-latest",
       "name": "ark-code-latest",
       "vendor": "Custom",
       "url": "https://ark.ap-southeast.bytepluses.com/api/plan/v3",
       "apiKey": "<YOUR_API_KEY>",
       "supportsToolCall": true,
       "supportsImages": false,
       "supportsReasoning": false,
       "useCustomProtocol": false
     }
   ]
   ```

4. After saving, return to the WorkBuddy main page.

# Use WorkBuddy

1. In the model dropdown at the top of the main page, select the model you just added.

2. Describe your task in one sentence in the input box, such as generating a weekly report, analyzing data, organizing files, or generating, refactoring, or debugging code. Press Enter to start. WorkBuddy autonomously plans and executes the task, then produces documents, spreadsheets, code files, and other results. You can view generated content and file changes in the results pane on the right.

> For more usage tips and scenarios, see the [official WorkBuddy documentation](https://www.workbuddy.ai/docs/zh/workbuddy/Quickstart).
