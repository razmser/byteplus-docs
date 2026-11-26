# Claude Code

This topic describes how to configure and use ModelArk Agent Plan Team in Claude Code.

# Prerequisites

An administrator must complete the following prerequisites before subaccounts can configure and use Claude Code:

1. Purchase a ModelArk Agent Plan Team plan: [1. Purchase Agent Plan Team](agent-plan-enterprise-get-started.md#purchase-plan).

2. Assign seats to subaccounts: [2. Assign seats](agent-plan-enterprise-get-started.md#assign-seats).

# Install Claude Code

Prerequisites:

* Install [Node.js 18 or later](https://nodejs.org/en/download/).

* Windows users must install [Git for Windows](https://git-scm.com/download/win).

Run the following command in a command\-line interface to install Claude Code.

```Bash
npm install -g @anthropic-ai/claude-code
```

After installation, run the following command to check the result. Installation is successful if a version number is displayed.

```Bash
claude --version
```

# Configure Claude Code

## Required configuration

If you are familiar with Claude Code, use a configuration tool such as CC Switch, or already have a local configuration, obtain the following information and configure it directly.

* **ANTHROPIC_BASE_URL**: `https://ark.ap-southeast.bytepluses.com/api/plan`.

* **ANTHROPIC_AUTH_TOKEN**: [Get exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine).

* **ANTHROPIC_MODEL**: Configure a text\-generation model. You can configure `ark-code-latest` or a model name. For model information, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

> **warning**

> Before configuring, clear the following environment variables to avoid conflicts with existing configuration:

> ```Bash
> unset ANTHROPIC_AUTH_TOKEN
> unset ANTHROPIC_BASE_URL
> ```

> If these variables are in `~/.bashrc` or `~/.zshrc`, back up the file and remove the relevant variables.

## Method 1: One\-click configuration (recommended)

Ark CLI is the official command\-line tool for the ModelArk platform. It supports fast authentication and resource management in a local terminal, and can install capabilities in commonly used AI agents, reducing manual configuration time and errors.

1. Install Ark CLI.

   Environment requirement: Node.js 18 or later. macOS, Linux, and Windows are supported.

   ```Bash
   npm install -g @byteplus/ark-cli@latest
   arkcli --version
   ```

2. Sign in to Ark CLI.

   Complete sign\-in authorization as prompted.

   ```Bash
   arkcli auth login
   ```

3. Configure Claude Code with one click.

   ```Bash
   arkcli helper
   ```

Run ArkCLI Helper and complete configuration as prompted.

* **Select a profile**: `agent-plan-team_ap-southeast-1_team`.

* **Select a default model**: Select as needed.

* **Select an AI agent**: `Claude Code`.

   harness capabilities are supported. Select `Configure` or `Skip` as needed.

After configuration is complete, Claude Code automatically recognizes the configured model. Start Claude Code to use it.

## Method 2: Manual configuration

1. Create `~/.claude/settings.json` (Windows path: `%USERPROFILE%\.claude\settings.json`), then configure the following:

   * `<ARK_API_KEY>`: Replace with your [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine).

   * `<MODEL_NAME>`: Update the text\-generation model name based on your plan and requirements, for example `ark-code-latest`. For model information, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

   ```JSON
   {
       "env": {
           "ANTHROPIC_AUTH_TOKEN": "<ARK_API_KEY>",
           "ANTHROPIC_BASE_URL": "https://ark.ap-southeast.bytepluses.com/api/plan",
           "ANTHROPIC_MODEL": "<MODEL_NAME>",
           "ANTHROPIC_DEFAULT_HAIKU_MODEL": "<MODEL_NAME>",
           "ANTHROPIC_DEFAULT_SONNET_MODEL": "<MODEL_NAME>",
           "ANTHROPIC_DEFAULT_OPUS_MODEL": "<MODEL_NAME>",
           "CLAUDE_CODE_SUBAGENT_MODEL": "<MODEL_NAME>",
           "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": "1"
       }
   }
   ```

   > **tip**

   * > To enable the 1M context window, see [Extended context](agent-plan-enterprise-claude-code.md#extended-context).

   * > We recommend using the full model configuration and selecting a model based on task complexity: Haiku (lightweight), Sonnet (everyday), and Opus (complex).

   * > We recommend keeping `CLAUDE_CODE_SUBAGENT_MODEL` consistent with the primary model.

   * > We recommend setting `ANTHROPIC_DEFAULT_HAIKU_MODEL` to a small model, which typically does not affect overall usage.

   * > `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` disables Claude Code background anonymous telemetry requests. Setting it to `"1"` prevents quota from being consumed silently.

2. Edit or create `~/.claude.json` (Windows path: `%USERPROFILE%\.claude.json`), then set `hasCompletedOnboarding` to `true` to skip official sign\-in verification.

   ```JSON
   {
       "hasCompletedOnboarding": true
   }
   ```

After saving the configuration file, run subsequent commands in a new terminal window.

# Use Claude Code

1. Go to the project directory and run `claude` to start Claude Code.

   ```Bash
   cd my-project
   claude
   ```

2. After starting Claude Code, choose to trust the folder and allow Claude Code to access its files.

3. Enter `/status` to confirm model status.

4. Start a conversation in Claude Code.

> For more tutorials, see the [Claude Code website](https://code.claude.com/docs/zh-CN/quickstart).

# Other operations

## Add image and video generation

ModelArk Agent Plan Team Small and higher plans provide image generation model quotas, and Large and higher plans provide video generation model quotas. ModelArk provides Skills to help AI tools integrate and use these capabilities. For configuration instructions, see [Video and image generation models](agent-plan-enterprise-visual-models.md).

> **warning**

* > Different plans support different visual models. For details, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

* > Visual models cannot be used through Auto or console switching.

## Switch models

You can switch text\-generation models through the console, command parameters, or configuration files.

|Configuration method |Through the console |Through command parameters |Modify the configuration file |
|---|---|---|---|
|Switch model |1. Set the model name to `ark-code-latest` in the configuration file.<br><br>2. On the [Agent Plan Team console](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine) page, click the **Use configuration** tab, select a model, then wait 3 to 5 minutes for the change to take effect.<br><br>3. After starting Claude Code, enter `/status` to confirm model status. |* At startup: Run `claude --model <MODEL_NAME>` to specify the model for this conversation.<br><br>* During a session: Run `/model <MODEL_NAME>` to switch models. |Update the model name to the model you want to use in the configuration file. |

## Extended context

Agent Plan Team supports a 1M context window for some models, for use in long sessions involving large codebases. For supported models, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness). The following uses `deepseek-v4.1-flash` as an example:

```JSON
{
    "env": {
        "ANTHROPIC_AUTH_TOKEN": "<ARK_API_KEY>",
        "ANTHROPIC_BASE_URL": "https://ark.ap-southeast.bytepluses.com/api/plan",
        "CLAUDE_CODE_AUTO_COMPACT_WINDOW": "1000000",
        "ANTHROPIC_MODEL": "deepseek-v4.1-flash[1m]",
        "ANTHROPIC_DEFAULT_HAIKU_MODEL": "deepseek-v4.1-flash[1m]",
        "ANTHROPIC_DEFAULT_SONNET_MODEL": "deepseek-v4.1-flash[1m]",
        "ANTHROPIC_DEFAULT_OPUS_MODEL": "deepseek-v4.1-flash[1m]"
    }
}
```

> **warning**

* > To enable 1M context, add the `[1m]` suffix to the model name, such as `deepseek-v4.1-flash[1m]`, and configure the compaction window parameter `"CLAUDE_CODE_AUTO_COMPACT_WINDOW": "1000000"`.

* > If Claude Code cannot identify the model after the `[1m]` suffix is added, upgrade Claude Code to the latest version and try again.

## Install and use the Claude Code IDE plugin

The Claude Code IDE plugin depends on the Claude Code CLI. First complete Claude Code installation and configuration.

> As the IDE plugin continues to evolve, the following content is for reference only. For specific installation and usage information, see [Visual Studio Code](https://code.claude.com/docs/en/vs-code) and [JetBrains IDEs](https://code.claude.com/docs/en/jetbrains).

**Claude Code VSCode plugin**

The Claude Code VSCode plugin can be used in VSCode and VSCode\-based IDEs, such as Cursor and TraeCode.

**Install the plugin**

Open VSCode and search for `claude code` in the extension marketplace to install the plugin.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/VS-plugin-claude.png)

**Configure environment variables**

1. Open Settings in the lower\-left corner of VS Code, then open **Extensions** \- **Claude Code**.

2. In **Claude Code: Environment Variables**, click **Edit in settings.json** and modify `claudeCode.environmentVariables` and `claudeCode.selectedModel`.

   \* ``: Replace with your [exclusive API Key](https://ai.byteplus.com/ark/region:ap\-southeast\-1/subscription/agent\-plan\-enterprise/mine). \* ``: Replace with the model to use. \* If you enter `/config` in the Claude Code dialog, the plugin only returns usage information for configurable items and does not provide an interactive editing interface. Use the Settings entry instead.

   ```JSON
   ...
   "claudeCode.environmentVariables": [
       {
           "name": "ANTHROPIC_BASE_URL",
           "value": "https://ark.ap-southeast.bytepluses.com/api/plan"
       },
       {
           "name": "ANTHROPIC_AUTH_TOKEN",
           "value": "<ARK_API_KEY>"
       },
       {
           "name": "ANTHROPIC_MODEL",
           "value": "<MODEL_NAME>"
       }
   ],
   "claudeCode.selectedModel": "<MODEL_NAME>",
   ...
   ```

After saving the configuration, open a new window to use Claude Code.

**Claude Code JetBrains plugin**

The Claude Code JetBrains plugin supports JetBrains IDEs such as IntelliJ IDEA, PyCharm, and WebStorm.

**Install the plugin**

Open the JetBrains IDE and search for `claude code` in the plugin marketplace to install the plugin.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/jetbrain-plugin-market.png)

**Start using the plugin**

After installation, restart the IDE, then click the Claude Code icon to open the Claude Code page.
