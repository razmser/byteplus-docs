# Quick start

This topic describes how to purchase Agent Plan Team, select and configure AI tools, and use Claude Code to complete development tasks.

## 1. Purchase Agent Plan Team

Enterprise account administrators can sign in to the console with an IAM user, go to [Agent Plan Team](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/subscription), and purchase a plan as needed. For plan details, see [Subscription overview](agent-plan-enterprise-plan-overview.md).

## 2. Assign seats

If the account to use has not received a seat, contact the enterprise account administrator to assign one. Enterprise account administrators can follow these steps:

1. Go to the **Seat management** page of [Agent Plan Team](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/seat).

2. Follow the on\-screen instructions to assign a seat to the account and confirm the plan information for the seat.

Upon completion, the account receives the corresponding seat. After configuring an AI tool, you can use Agent Plan Team.

## 3. Select an AI tool

Agent Plan Team supports a variety of mainstream AI tools. Seat users can select one as needed.

**Claude Code**

AI terminal coding assistant for natural\-language programming

**TraeCode**

Intelligent IDE for next\-generation coding workflows

**Roo Code**

Lightweight coding assistant

**Codex CLI**

Command\-line coding tool from OpenAI

**Pi**

Open\-source terminal coding agent

**OpenCode**

Open\-source AI coding agent tool

**Cline**

VSCode extension for code completion and debugging

**Kilo Code**

Lightweight, high\-performance coding tool

**WorkBuddy**

Tencent desktop AI agent client

**ZCode**

AI coding assistant from Zhipu AI

**OpenClaw**

Open\-source, self\-hosted personal AI assistant

**Cursor**

AI\-native code editor

**Hermes Agent**

Open\-source autonomous AI agent

**DeepSeek Harness**

Agent framework where everything is a plugin

## 4. Configure AI tools

### Install Claude Code

Prerequisites:

* Install [Node.js 18 or later](https://nodejs.org/en/download/).

* Windows users must install [Git for Windows](https://git-scm.com/download/win).

Run the following command in the command\-line interface to install Claude Code.

```Bash
npm install -g @anthropic-ai/claude-code
```

After installation, run the following command. If the command returns a version number, installation was successful.

```Bash
claude --version
```

### Configure Claude Code

#### Required configuration information

If you are familiar with Claude Code, use configuration tools such as CC Switch, or already have local configuration, you can configure it directly with the following information.

* **ANTHROPIC_BASE_URL**: `https://ark.ap-southeast.bytepluses.com/api/plan`.

* **ANTHROPIC_AUTH_TOKEN**: [Obtain exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine).

* **ANTHROPIC_MODEL**: Configure a text generation model. You can configure `ark-code-latest` or a model name. For model information, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

> **warning**

> If you already have local Claude Code configuration, such as terminal startup commands in `.bashrc` or `.zshrc`, configuration conflicts may prevent Claude Code from working. Try backing up and clearing the local configuration, then run it again.

#### Method 1: One\-click configuration (recommended)

Ark CLI is the official command\-line tool for ModelArk. Ark CLI Helper can quickly configure Claude Code.

1. Install Ark CLI.

   Environment requirements: Node.js 18 or later. macOS, Linux, and Windows are supported.

   ```Bash
   npm install -g @byteplus/ark-cli@latest
   arkcli --version
   ```

2. Sign in to Ark CLI.

   Run the following command, then complete sign\-in authorization as prompted.

   ```Bash
   arkcli auth login
   ```

3. Run ArkCLI Helper.

   ```Bash
   arkcli helper
   ```

Complete the following configuration as prompted:

* **Select Profile**: `agent-plan-team_ap-southeast-1_team`.

* **Select the default model**: Select as needed.

* **Select AI Agent**: `Claude Code`.

   Harness capabilities can be configured. Select `Configure` or `Skip` as prompted.

After configuration, Claude Code automatically recognizes the configured model. Start Claude Code to use it.

#### Method 2: Manual configuration

1. Create `~/.claude/settings.json`. The Windows path is `%USERPROFILE%\.claude\settings.json`.

2. Add the following configuration to the file and replace the placeholders:

   *  **`<ARK_API_KEY>`** : Replace with your [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine).

   *  **`<MODEL_NAME>`** : Replace with the name of the text generation model to use, such as `ark-code-latest`. For model information, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

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

   * > For instructions on enabling the 1M context window, see [Extended context](agent-plan-enterprise-claude-code.md#extended-context).

   * > **Model configuration recommendation**: Use the full model configuration, and select Haiku (lightweight), Sonnet (daily tasks), or Opus (complex tasks) based on task complexity.

   * > We recommend keeping `CLAUDE_CODE_SUBAGENT_MODEL` consistent with the primary model.

   * > `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` disables Claude Code background anonymous telemetry requests. Set it to `"1"` to prevent silent consumption of plan quota.

3. Edit or create `~/.claude.json` (Windows path: `%USERPROFILE%\.claude.json`), set `hasCompletedOnboarding` to `true`, and skip official sign\-in verification.

   ```JSON
   {
       "hasCompletedOnboarding": true
   }
   ```

After saving the configuration files, open a new terminal window, then start Claude Code.

### Use Claude Code

1. Go to the project directory and run the `claude` command to start Claude Code.

   ```Bash
   cd my-project
   claude
   ```

2. Choose to trust the current folder and allow Claude Code to access files in the folder.

3. Enter `/status` to confirm the model status.

4. Enter a question in Claude Code to start a conversation.

For more usage instructions, see the [Claude Code official quickstart](https://code.claude.com/docs/zh-CN/quickstart).

### Other operations

#### Add image and video generation capabilities

Agent Plan Team Small and higher plans provide image generation model quota, and Large and higher plans provide video generation model quota. For configuration instructions, see [Video and image generation models](agent-plan-enterprise-visual-models.md).

> **warning**

* > Different plans support different visual models. For details, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

* > Visual models cannot be switched through Auto mode or the console.

#### Switch models

You can switch text generation models through the console, command parameters, or configuration files.

|Configuration method |Through the console |Through command parameters |Modify the configuration file |
|---|---|---|---|
|Model switching steps |1. Set the model name to `ark-code-latest` in the configuration file.<br><br>2. On the [Agent Plan Team console](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine) page, click the **Usage configuration** tab, select a model, and wait 3 to 5 minutes for the switch to take effect.<br><br>3. After starting Claude Code, enter `/status` to confirm the model status. |* At startup: Run `claude --model <MODEL_NAME>` to specify the model for this conversation.<br><br>* During a conversation: Run `/model <MODEL_NAME>` to switch models. |In the configuration file, change the model name to the model you want to use. |
