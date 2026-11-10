# Claude Code

Claude Code is a terminal\-based AI coding assistant designed to help developers write, debug, and manage code more efficiently through natural language interaction.

The ModelArk Coding Plan supports integration with Claude Code. Refer to this article for configuration and usage instructions.

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

# Install Claude Code

**Prerequisites**:

* Install [Node.js 18 or later environment](https://nodejs.org/en/download/).

* Windows users need to install [Git for Windows](https://git-scm.com/download/win).

In the command line interface, execute the following command to install Claude Code.

```Bash
npm install -g @anthropic-ai/claude-code
```

After installation, execute the following command to view the installation result. If the version number is displayed, the installation is successful.

```Bash
claude --version
```

# Configure

## Method 1: Automated configuration (recommended)

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

## Method 2: Configure manually

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

# Use Claude Code

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

> [How to enable thinking mode in Claude Code?](coding-plan-personal-faq.md#5e379f69)

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/631742d8a5814d68a5cbc62988ba8cab~tplv-goo7wpa0wc-image.image)

# Switch models

Choose the model switching option that matches your configuration method.

|Configuration method |`ark-code-latest` |Configured the **Model Name** directly |
|---|---|---|
|How to switch |1. On the [enablement/management](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=subscribe) page, select the model you want to use. The change takes effect in **3–5 minutes**.<br><br>2. After launching **Claude Code** in a terminal, run `/status` to confirm the model status. |* **At startup**: Run `claude --model <Model_Name>` to specify the model.<br><br>* **During a session**: Run `/model <Model_Name>` to switch models. |

## Extended context

`glm-5.2`, `glm-5.3-flash`, `deepseek-v4.1-flash`, `deepseek-v4-flash`, and `deepseek-v4-pro` support a 1M context window, which can be used for long sessions involving large codebases. Taking `glm-5.2` as an example, the configuration is as follows:

```JSON
{
    "env": {
        "ANTHROPIC_AUTH_TOKEN": "<ARK_API_KEY>",
        "ANTHROPIC_BASE_URL": "https://ark.ap-southeast.bytepluses.com/api/coding",
        "CLAUDE_CODE_AUTO_COMPACT_WINDOW": "1000000",
        "ANTHROPIC_MODEL": "glm-5.2[1m]",
        "ANTHROPIC_DEFAULT_HAIKU_MODEL": "glm-5.2[1m]",
        "ANTHROPIC_DEFAULT_SONNET_MODEL": "glm-5.2[1m]",
        "ANTHROPIC_DEFAULT_OPUS_MODEL": "glm-5.2[1m]"
    }
}
```

> **warning**

* > To enable the 1M context, append the `[1m]` suffix to the model name (for example, `glm-5.2[1m]`), and set the compaction window parameter `"CLAUDE_CODE_AUTO_COMPACT_WINDOW": "1000000"`.

* > If Claude Code reports that the model does not exist after appending the `[1m]` suffix, upgrade Claude Code to the latest version and try again.

## Use CC Switch

CC Switch is a cross\-platform desktop application designed for developers using AI coding tools. It helps you centrally manage configurations for supported apps such as Claude Code, Claude Desktop, Codex, Gemini CLI, OpenCode, OpenClaw, and Hermes.

### Supported platforms

* Windows 10 and later

* macOS 12 (Monterey) and later

* Linux Ubuntu 22.04+ / Debian 11+ / Fedora 34+ (x64 / ARM64)

### Install

> For more installation methods and details, see the [CC Switch official documentation](https://github.com/farion1231/cc-switch).

**macOS**

* **Homebrew (recommended)** :

   ```Bash
   brew tap farion1231/ccswitch
   brew install --cask cc-switch
   ```

* **Manual download**: download the `.dmg` from the [Releases page](https://github.com/farion1231/cc-switch/releases).

**Linux**

* **Arch\-based distributions**:

   ```Bash
   paru -S cc-switch-bin
   ```

* **Other distributions**: download `.deb` / `.rpm` from the [Releases page](https://github.com/farion1231/cc-switch/releases) based on your architecture (Debian / Ubuntu), or download the `.AppImage` (universal).

**Windows**

* Download the `.msi` installer or `.zip` portable package from the [Releases page](https://github.com/farion1231/cc-switch/releases).

### Add a provider

1. Add a provider in CC Switch:

   1. In the top icon bar of the main screen, select the Claude Code icon, then click the  **+**  in the upper right corner to add a new provider.

   2. Select **Custom configuration** and fill in the following:

      * **API Key**: replace with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

      * **Request URL**: `https://ark.ap-southeast.bytepluses.com/api/coding`

   3. Expand the advanced options and configure the Sonnet, Opus, Fable, and Haiku models according to the model range supported by your current plan. See [Key configuration](coding-plan-personal-ai-claude-code.md#92b07719) for the available models.

      > **warning**

      > We recommend setting Haiku to a small\-size model.

   4. Click **Add** in the lower right corner to complete the provider configuration.

2. Return to the home page, click **Enable** on the right, and open a new Claude Code session for the configuration to take effect.

# Use Claude Code IDE plugin

1. Install Claude Code and configure the environment variables following [Install Claude Code](coding-plan-personal-ai-claude-code.md#a4a94900).

2. Install and use the IDE plugin.

> As IDE plugins continue to evolve, the following information is provided for reference only. For specific installation and usage guides, see [Visual Studio Code](https://code.claude.com/docs/en/vs-code) and [JetBrains IDEs](https://code.claude.com/docs/en/jetbrains).

**Claude Code VSCode plugin**

> **tip**

> The Claude Code VSCode plugin supports use in VSCode and VSCode\-based IDEs (such as Cursor, TraeCode, etc).

### Install the plugin

Open VSCode and search for `claude code` in the extension marketplace to install.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/VS-plugin-claude.png)

### Configure environment variables

1. Open Settings from the bottom\-left in VS Code, then go to **Extensions** \- **Claude Code**.

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/claude-code-VS.png)

2. In the **Claude Code: Environment Variables** section, click **Edit in settings.json** to modify `claudeCode.environmentVariables` and `claudeCode.selectedModel`.

> **tip**

> The configuration information that needs to be modified is as follows:

* > `<ARK_API_KEY>`: Replace with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* > `<Model_Name>`: Update with the model information you need to use, such as `ark-code-latest`. For supported models, see [Key configuration](coding-plan-personal-ai-claude-code.md#92b07719).

> If you type `/config` in the Claude Code dialog box, the plugin only returns usage descriptions of configurable items and does not provide an interactive edit interface. Use the Settings entry in step 1 instead.

```JSON
...
"claudeCode.environmentVariables": [
    {
        "name": "ANTHROPIC_BASE_URL",
        "value": "https://ark.ap-southeast.bytepluses.com/api/coding"
    },
    {
        "name": "ANTHROPIC_AUTH_TOKEN",
        "value": "<ARK_API_KEY>"
    },
    {
        "name": "ANTHROPIC_MODEL",
        "value": "<Model_Name>"
    }
],
"claudeCode.selectedModel": "<Model_Name>",
...
```

After saving the configuration, open a new window to get started with Claude Code.

**Claude Code JetBrains plugin**

> **tip**

> The Claude Code JetBrains plugin supports JetBrains IDEs such as IntelliJ IDEA, PyCharm, WebStorm, and many others.

### Install the plugin

Open a JetBrains IDE and search for `claude code` in the plugin marketplace to install.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/jetbrain-plugin-market.png)

### Get started

After installation, restart the IDE, then click the Claude Code icon to enter the Claude Code page and get started.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/jetbrain-claude.png)

# FAQs

[How to enable thinking mode in Claude Code?](coding-plan-personal-faq.md#5e379f69)

# Error code

When using the Coding Plan package in programming tools, you may encounter errors. You can view the corresponding error scenarios based on the error information and locate the issue. For details, see [Error codes](error-codes.md).
