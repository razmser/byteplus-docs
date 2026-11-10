# Quick start guide

This article describes how to quickly use the models supported by the ModelArk Coding Plan in programming tools for code development.

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

## Step 1: Subscribe to a plan

Go to [ModelArk Coding Plan event](https://www.byteplus.com/en/activity/codingplan) and subscribe to a plan. For plan details, see [Subscription overview](coding-plan-personal-plan-overview.md).

## Step 2: Configure programming tools

Coding Plan supports a range of popular programming tools \- you can choose what best fits your workflow:

[Claude Code](coding-plan-personal-ai-claude-code.md)

AI terminal coding assistant for natural\-language programming

[TraeCode](coding-plan-personal-ai-trae.md)

Intelligent IDE for next\-generation development workflows

[Roo Code](coding-plan-personal-ai-other-tools.md)

Lightweight coding assistant

[Codex](coding-plan-personal-ai-codex.md)

OpenAI's coding tool (CLI and ChatGPT desktop app)

[OpenCode](coding-plan-personal-ai-opencode.md)

Open\-source AI coding agent tool

[Cline](coding-plan-personal-ai-other-tools.md)

VS Code extension for code completion and debugging

[Kilo Code](coding-plan-personal-ai-other-tools.md)

Lightweight, high\-performance coding tool

[OpenClaw](coding-plan-personal-ai-openclaw.md)

Open\-source, self\-hosted personal AI assistant

[Cursor](coding-plan-personal-ai-other-tools.md)

AI\-native code editor

[Hermes Agent](coding-plan-personal-ai-hermes-agent.md)

Open\-source, self\-hosted AI agent

The following uses Claude Code as an example to show how to use the Coding Plan.

### Install Claude Code

Prerequisites:

* Install [Node.js 18 or later](https://nodejs.org/en/download/).

* Windows users need to install [Git for Windows](https://git-scm.com/download/win).

In the command line interface, execute the following command to install Claude Code.

```Bash
npm install -g @anthropic-ai/claude-code
```

After installation, execute the following command to view the installation result. If the version number is displayed, the installation is successful.

```Bash
claude --version
```

### Configure Claude Code

#### Method 1: Automated configuration (recommended)

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

3. Run ArkCLI Helper and follow the on\-screen prompts to complete the Claude Code configuration.

   1. Enter the `arkcli helper` command in the terminal to start ArkCLI Helper.

   2. Select the Plan profile to configure: Choose `coding-plan_ap-southeast-1_personal (Coding Plan)`.

   3. Select the model to use.

   4. Select the AI agent to configure: `Claude Code`.

After you complete these steps, Claude Code is configured automatically. No manual installation is required.

#### Method 2: Configure manually

After completing Claude Code installation, configure the following information.

* **ANTHROPIC_BASE_URL**: `https://ark.ap-southeast.bytepluses.com/api/coding`

* **ANTHROPIC_AUTH_TOKEN**: [Get API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* **ANTHROPIC_MODEL**: `ark-code-latest`or the name of a specific model

> **warning**

> Before configuring, clear the following environment variables to avoid conflicts with old configurations:

> ```Bash
> unset ANTHROPIC_AUTH_TOKEN
> unset ANTHROPIC_BASE_URL
> ```

> If the variables are set in `~/.bashrc` / `~/.zshrc`, back up the file and remove the corresponding lines.

The configuration steps are as follows:

1. Create and open the `settings.json` configuration file. The file path varies by system, as follows:

   **macOS/Linux**

macOS/Linux Claude Code configuration file path: `~/.claude/settings.json`.

      1. If the `.claude` directory does not exist in your home directory, run the following command to create it.

         ```Bash
         mkdir -p ~/.claude
         ```

      2. Create and open the configuration file.

         ```Bash
         nano ~/.claude/settings.json
         ```

   **Windows**

Windows Claude Code configuration file path: `C:\Users\<username>\.claude\settings.json`. Using CMD as an example:

      1. If the `.claude` directory does not exist under the current user directory, run the following command to create it.

         ```Bash
         if not exist "%USERPROFILE%\.claude" mkdir "%USERPROFILE%\.claude"
         ```

      2. Create and open the configuration file.

         ```Bash
         notepad "%USERPROFILE%\.claude\settings.json"
         ```

2. Edit the configuration file. The configuration information that needs to be modified is as follows:

   * `<ARK_API_KEY>`: Replace with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

   * `<Model_Name>`: Update to the model information you need to use, such as `ark-code-latest`. For supported model information, see [Key configuration](coding-plan-personal-ai-claude-code.md#92b07719).

   ```JSON
   {
       "env": {
           "ANTHROPIC_AUTH_TOKEN": "<ARK_API_KEY>",
           "ANTHROPIC_BASE_URL": "https://ark.ap-southeast.bytepluses.com/api/coding",
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

* > To enable the 1M context window, see [Extended context](coding-plan-personal-ai-claude-code.md#7a3e5c1b).

* > We recommend using the full model configuration and choosing models by task complexity: Haiku (lightweight), Sonnet (daily), Opus (complex).

* > `CLAUDE_CODE_SUBAGENT_MODEL` is recommended to match the main model.

* > `ANTHROPIC_DEFAULT_HAIKU_MODEL` is recommended to be set to a small\-size model, which usually does not affect the overall experience.

* > `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` disables Claude Code's background anonymous telemetry requests. By default these requests go through `ANTHROPIC_BASE_URL` and consume Coding Plan quota; setting it to `"1"` prevents silent quota consumption (Method 1 with ArkCLI Helper configures this automatically).

* > To disable thinking mode, add `"CLAUDE_CODE_EXTRA_BODY": "{\"thinking\":{\"type\":\"disabled\"}}"` under `env`.

3. Edit or add the `.claude.json` file, modify or add the `hasCompletedOnboarding` field and set its value to true.

> **Tip**

> The configuration file path varies by system, as follows:

* > macOS & Linux: `~/.claude.json`

* > Windows: `C:\Users\<username>\.claude.json`

```JSON
{
  "hasCompletedOnboarding": true
}
```

After saving the configuration file, execute subsequent commands in a new terminal window.

### Use Claude Code

1. Start Claude Code: Enter the project directory and execute the `claude` command to get started with Claude Code.

   ```Bash
   cd my-project
   claude
   ```

2. Model status verification: Input `/status` to confirm the model status.

If the following prompt appears, it may be because you have previously logged in to Claude Code. You can input `/logout` to log out, and then start Claude Code again.

```JSON
{
    "error": {
        "code": "AuthenticationError",
        "message": "The API key format is incorrect. Request id:0217xxxxxxx",
        "param": "",
        "type": "Unauthorized"
    }
}
```

3. Start the conversation in Claude Code.

> **Note**

> [How to enable the deep\-reasoning mode in Claude Code?](coding-plan-personal-faq.md#5e379f69)

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/631742d8a5814d68a5cbc62988ba8cab~tplv-goo7wpa0wc-image.image)

### Switch models

Choose the model switching option that matches your configuration method.

|Configuration method |`ark-code-latest` |Configured the **Model Name** directly |
|---|---|---|
|How to switch |1. On the [enablement/management](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=subscribe) page, select the model you want to use. The change takes effect in **3–5 minutes**.<br><br>2. After launching **Claude Code** in a terminal, run `/status` to confirm the model status. |* **At startup**: Run `claude --model <Model_Name>` to specify the model.<br><br>* **During a session**: Run `/model <Model_Name>` to switch models. |

### FAQs

[FAQs](coding-plan-personal-faq.md)

### Error code

If an error occurs, refer to [Error codes](error-codes.md) to locate and resolve the issue.
