# Integrate with third-party tools

ModelArk API is compatible with the OpenAI and Anthropic API protocols and can be used in third\-party tools. You can see this topic for configuration and usage.

# Ecosystem compatibility

To meet developers' needs for using the OpenAI API and Anthropic API ecosystems, ModelArk API now supports adaptation to both interface formats and is fully compatible with mainstream large model API specifications and related tool ecosystems. Developers do not need to modify core code. They only need to switch the base URL and API key to make cross\-platform model calls and integrate tools.

When configuring third\-party tools, use the base URL information in the table below.

> **Tip**

> Before configuring tools, you need to [activate the required model services](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement).

ModelArk API includes built\-in tools such as Web Search (not available for BytePlus). After activation, you can use them directly in third\-party tools such as Claude Code and Codex CLI. We recommend using them first. If your use case is not covered by the built\-in tools, you can also integrate second\-party and third\-party MCP servers and skills into third\-party tools yourself to extend the capabilities available to the model. For each tool's integration method, see the corresponding sections below.

## Get an MCP server

* **Third\-party**: Integrate MCP servers provided by other MCP marketplaces, or deploy open\-source MCP servers yourself.

> **Note**

> Obtain MCP servers from trusted sources. Their security and stability are the responsibility of the provider. Before integration, confirm that their permission scope meets your expectations.

# Integrate Chatbox

## Installation steps

Download and install the appropriate version from the [Chatbox official website](https://chatboxai.app/en), or **launch the web version** directly.

## Configure tools

Open Chatbox and go to the Settings page.

1. On the Model Provider tab, click to add a provider, and set **API Mode** to `OpenAI API Compatible`.

2. After the provider is added, configure the following information.

   * **API key**: [Get API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

   * **API Host**: `https://ark.ap-southeast.bytepluses.com/api/v3`

   * **API Path**: `/chat/completions`

   * **Model**: [Select a model and get the model ID](model-list.md)

After the configuration is complete, you can enter your requirements in the input box and interact with the model.

# Integrate Cherry Studio

## Installation steps

Download and install the Cherry Studio client from the [Cherry Studio official website](https://www.cherry-ai.com/).

## Configure tools

Open the Cherry Studio client and go to the Settings page.

1. In model services, click to add a provider, and set **Provider type** to `OpenAI`.

2. After the provider is added, configure the following information.

   * **API key**: [Get API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

   * **API host**: `https://ark.ap-southeast.bytepluses.com/api/v3`

   * **Model**: Click to add a model and enter the [model ID](model-list.md) you want to use.

After the configuration is complete, you can enter your requirements in the input box and interact with the model.

# Integrate Codex CLI

## Installation steps

> **Tip**

> Coding Plan supports the Responses API, so you can use the latest version of Codex CLI.

Prerequisite: Install [Node.js 18 or later](https://nodejs.org/en/download/).

In the command\-line interface, run the following command to install Codex CLI.

```Bash
npm i -g @openai/codex
```

After installation is complete, run the following command to check the version.

```Bash
codex --version
```

## Configure tools

1. Create and open the Codex configuration file. The file path varies by system. Use the following steps:

**macOS/Linux**

macOS/Linux Codex configuration file path: `~/.codex/config.toml`.

1. If the `.codex` directory does not exist in the home directory, run the following command to create it.

   ```Bash
   mkdir -p ~/.codex
   ```

2. Create and open the configuration file.

   ```Bash
   nano ~/.codex/config.toml
   ```

**Windows**

Windows Codex configuration file path: `%USERPROFILE%\.codex\config.toml`. The following uses the CMD command line as an example.

1. If the `.codex` directory does not exist in the current user directory, run the following command to create it.

   ```Bash
   if not exist "%USERPROFILE%\.codex" mkdir "%USERPROFILE%\.codex"
   ```

2. Create and open the configuration file.

   ```Bash
   notepad "%USERPROFILE%\.codex\config.toml"
   ```

2. Edit `config.toml`. The configuration information to focus on is as follows:

   * `<Model ID>`: Select a model as needed and obtain the [model ID](model-list.md).

   * `env_key`: This sets the environment variable name. Do not directly modify `ARK_API_KEY`; you need to set the value of this environment variable in the next step.

   ```plaintext
   model = "<Model ID>"
   model_provider = "volcengine"
   
   [model_providers.volcengine]
   name = "volcengine"
   base_url = "https://ark.ap-southeast.bytepluses.com/api/v3"
   env_key = "ARK_API_KEY"
   wire_api = "responses"
   ```

   > **Note**

   * > `model_supports_reasoning_summaries = true`: Enable reasoning capabilities.

   * > `model_reasoning_effort`: Controls the thinking effort. You can set it to `low`, `medium`, or `high`.

   * > minimax\-m2.7, kimi\-k2.6, and kimi\-k2.7\-code do not support setting `model_supports_reasoning_summaries = true`.

3. Configure the environment variable. Set the `ARK_API_KEY` environment variable to the [API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

   **macOS / Linux**

1. Check the shell type.

         ```Bash
         echo $SHELL
         ```

      2. Write the environment variable to the shell configuration file so that it automatically takes effect when a new terminal is opened.

         * Zsh

            ```Bash
            # Replace YOUR_API_KEY with the API key.
            echo 'export ARK_API_KEY="YOUR_API_KEY"' >> ~/.zshrc
            source ~/.zshrc
            ```

         * Bash

            ```Bash
            # Replace YOUR_API_KEY with the API key.
            echo 'export ARK_API_KEY="YOUR_API_KEY"' >> ~/.bashrc
            source ~/.bashrc
            ```

   **Windows**

* CMD: After the environment variable is set, it takes effect in a newly opened CMD window.

         ```Bash
         # Replace YOUR_API_KEY with the API key.
         setx ARK_API_KEY "YOUR_API_KEY"
         # Open a new window and check whether the environment variable has taken effect
         echo %ARK_API_KEY%
         ```

      * PowerShell: After the environment variable is set, it takes effect in a newly opened PowerShell window.

         ```Bash
         # Replace YOUR_API_KEY with the API key.
         [Environment]::SetEnvironmentVariable("ARK_API_KEY", "YOUR_API_KEY", [EnvironmentVariableTarget]::User)
         # Open a new window and check whether the environment variable has taken effect
         echo $env:ARK_API_KEY
         ```

## Get started

Run the following command to start Codex CLI.

```Bash
codex
```

## Integrate second\-party/third\-party MCP servers

If the built\-in tools do not cover your use case, you can integrate second\-party or third\-party MCP servers in Codex CLI. For how to obtain them, see [Get an MCP server](integrate-third-party-tools.md#c5f28d13).

Edit `~/.codex/config.toml` (`%USERPROFILE%\.codex\config.toml` on Windows) and add the MCP server configuration.

```plaintext
[mcp_servers.<MCP_name>]
command = "<launch_command>"
args = ["<launch_arguments>"]
```

Restart Codex CLI for the configuration to take effect.

# Integrate Claude Code

## Installation steps

Prerequisites:

* Install [Node.js 18 or later](https://nodejs.org/en/download/).

* Windows users must install [Git for Windows](https://git-scm.com/download/win).

In the command\-line interface, run the following command to install Claude Code.

```Bash
npm install -g @anthropic-ai/claude-code
```

After installation is complete, run the following command to check the installation result. If a version number is displayed, the installation is successful.

```Bash
claude --version
```

## Configure tools

After installing Claude Code, configure the following information.

* **ANTHROPIC_BASE_URL**: `https://ark.ap-southeast.bytepluses.com/api/compatible`

* **ANTHROPIC_AUTH_TOKEN**: [Get API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* **ANTHROPIC_MODEL**: [Select a model and get the model ID](model-list.md)

   The configuration steps are as follows:

1. Edit or add the `settings.json` file, and replace the following information in the configuration:

   * `<ARK_API_KEY>`: Replace it with the [API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

   * `<Model_Name>`: Replace it with the [model ID](model-list.md) you want to use.

> **Tip**

> The configuration file paths differ by system, as follows:

* > macOS & Linux: `~/.claude/settings.json`

* > Windows: `C:\Users\<username>\.claude\settings.json`

The following uses `dola-seed-2-1-turbo-260628` as an example. The configuration is as follows:

```JSON
{
    "env": {
        "ANTHROPIC_AUTH_TOKEN": "<ARK_API_KEY>",
        "ANTHROPIC_BASE_URL": "https://ark.ap-southeast.bytepluses.com/api/compatible",
        "ANTHROPIC_MODEL": "dola-seed-2-1-turbo-260628",
        "ANTHROPIC_DEFAULT_HAIKU_MODEL": "seed-2-0-lite-260428",
        "ANTHROPIC_DEFAULT_SONNET_MODEL": "dola-seed-2-1-turbo-260628",
        "ANTHROPIC_DEFAULT_OPUS_MODEL": "dola-seed-2-1-turbo-260628",
        "CLAUDE_CODE_SUBAGENT_MODEL": "dola-seed-2-1-turbo-260628"
    }
}
```

> **Note**

* > We recommend using the complete model configuration and selecting models based on task complexity: Haiku (lightweight), Sonnet (daily use), and Opus (complex).

* > We recommend keeping `CLAUDE_CODE_SUBAGENT_MODEL` consistent with the main model.

* > We recommend setting `ANTHROPIC_DEFAULT_HAIKU_MODEL` to a small model, such as `seed-2-0-lite-260428`. This usually does not affect the overall usage experience.

2. Edit or add the `.claude.json` file, and modify or add the `hasCompletedOnboarding` parameter with the value `true`.

> **Tip**

> The configuration file paths differ by system, as follows:

* > macOS and Linux: `~/.claude.json`

* > Windows: `C:\Users\<username>\.claude.json`

```JSON
{
  "hasCompletedOnboarding": true
}
```

After saving the configuration file, run the subsequent commands in a new terminal window.

## Use CC Switch

CC Switch is a cross\-platform desktop application designed for developers who use AI coding tools. It helps you centrally manage configurations for managed applications such as Claude Code, Claude Desktop, Codex, Gemini CLI, OpenCode, OpenClaw, and Hermes.

### Supported platforms

* Windows 10 and later

* macOS 12 (Monterey) and later

* Linux Ubuntu 22.04+ / Debian 11+ / Fedora 34+ (x64 / ARM64)

### Installation

> For more installation methods and detailed instructions, see [CC Switch official documentation](https://github.com/farion1231/cc-switch).

**macOS**

* **Homebrew (recommended)** :

   ```Bash
   brew tap farion1231/ccswitch
   brew install --cask cc-switch
   ```

* **Manual download**: Download the `.dmg` from the [Releases page](https://github.com/farion1231/cc-switch/releases).

**Linux**

* **Arch distributions**:

   ```Bash
   paru -S cc-switch-bin
   ```

* **Other distributions**: Download the `.deb` / `.rpm` from the [Releases page](https://github.com/farion1231/cc-switch/releases) based on architecture (Debian / Ubuntu), or download the `.AppImage` (universal).

**Windows**

* Download the `.msi` installer or the portable `.zip` version from the [Releases page](https://github.com/farion1231/cc-switch/releases).

### Add a provider

1. Add a provider in CC Switch:

   1. In the icon bar at the top of the main interface, select the Claude Code icon, and click  **+**  in the upper\-right corner to add a new provider.

   2. Select **Custom configuration** and fill in the following information:

      * **API key**: Replace it with your [API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

      * **Request URL**: `https://ark.ap-southeast.bytepluses.com/api/compatible`

   3. Expand advanced options and configure the Sonnet, Opus, Fable, and Haiku models separately based on the currently supported model range. For configurable models, see [Select a model and get the model ID](model-list.md).

      > **Note**

      > We recommend setting Haiku to a small model.

   4. Select **Add** in the lower\-right corner to complete provider configuration.

2. Return to the home page, click the **Enable** button on the right, and start a new Claude Code session for the configuration to take effect.

## Use Claude Code

* Start Claude Code: Go to the project directory and run the `claude` command to start using Claude Code.

   ```Bash
   cd my-project
   claude
   ```

* Verify model status: Enter `/status` to confirm the model status.

## Use the Claude Code IDE plugin

1. Install Claude Code and configure the environment variables. For details, see [Integrate Claude Code](integrate-third-party-tools.md#adcc555a).

The Claude Code IDE plugin depends on the Claude Code CLI tool. You must complete Claude Code installation and configuration first.

2. Install and use the IDE plugin.

> Because the IDE plugin is continuously evolving, the following content is for reference only. For installation and usage details, see [Visual Studio Code](https://code.claude.com/docs/en/vs-code) and [JetBrains IDEs](https://code.claude.com/docs/en/jetbrains).

<Tabs>
<Tab zoneid="p4OoZRBXt9" title="Claude Code VS Code plugin">

> **Tip**

> The Claude Code VS Code plugin supports VS Code and VS Code\-based IDEs, such as Cursor and Trae.

### Install the plugin

Open VS Code and search for `claude code` in the extension marketplace to install it.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/6c39c8cb4ec84f03bb0ca2444804eac7~tplv-goo7wpa0wc-image.image)

### Configure environment variables

1. After installation, click the Claude Code icon in the upper\-right corner of VS Code to go to the Claude Code page.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/388665c0f9ed47379faa5291e180a6b2~tplv-goo7wpa0wc-image.image)

2. In the dialog box, enter `/config` and press Enter to go to the settings configuration page.

3. In the **Claude Code: Environment Variables** section, click **Edit in settings.json** and modify `claudeCode.environmentVariables` and `claudeCode.selectedModel`.

> **Tip**

> You need to replace the following information in the configuration:

* > `<ARK_API_KEY>`: Replace it with the [API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

* > `<Model_Name>`: Replace it with one of the supported model names above, such as dola\-seed\-2\-1\-turbo\-260628.

```Plain Text
```JSON
...
"claudeCode.environmentVariables": [
    {
        "name": "ANTHROPIC_BASE_URL",
        "value": "https://ark.ap-southeast.bytepluses.com/api/compatible"
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
```


After saving the configuration information, you can start using Claude Code.


</Tab>
<Tab zoneid="muH2lkd7FU" title="Claude Code JetBrains plugin">
<TabTitle>Claude Code JetBrains plugin</TabTitle>

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">The Claude Code JetBrains plugin supports JetBrains IDEs such as IntelliJ IDEA, PyCharm, and WebStorm.</div>


<span id="install-the-plugin"></span>
### Install the plugin

Open a JetBrains IDE and search for `claude code` in the plugin marketplace to install it.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/5859916985a34036bb41f5097ba58f6c~tplv-goo7wpa0wc-image.image) </span>

<span id="get-started"></span>
### Get started

After installation, restart the IDE, then click the Claude Code icon to go to the Claude Code page and start using it.


</Tab>
</Tabs>


<span id="d6a39e24"></span>
## Integrate with second\-party/third\-party MCP servers and skills

If the built\-in tools do not cover your use case, you can integrate second\-party and third\-party MCP servers and skills in Claude Code (for how to obtain MCP servers, see [Get an MCP server](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/integrate-third-party-tools#c5f28d13)).


1. Integrate an MCP server: run the following command to add one (using the stdio type as an example).

   ```Bash
   claude mcp add <MCP_name> -- <launch_command> <launch_arguments>
   ```

   After adding it, you can run `claude mcp list` to view the integrated MCP servers. For more information (such as HTTP/SSE types and scope management), see the [Claude Code official documentation](https://code.claude.com/docs/en/mcp).

2. Integrate skills: place the skill directory in `~/.claude/skills/` (personal global) or in the `.claude/skills/` directory under the project. For details, see [Claude Code Skills](https://code.claude.com/docs/en/skills).

# Integrate OpenCode

## Installation steps

In the command\-line interface, run the following command to install OpenCode.

```Bash
npm install -g opencode-ai
```

After installation is complete, run the following command to check the installation result. If a version number is displayed, the installation is successful.

```Bash
opencode --version
```

## Configure tools

1. Edit the OpenCode configuration file at the following path:

* macOS / Linux: ~/.config/opencode/opencode.json

* Windows: C:\Users<your_username\>.config\opencode\opencode.json

Using the model `seed-1-8-251228` as an example, the configuration is as follows.

> **Tip**

* > Replace [<ARK_API_KEY\>](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey) in the configuration.

* > Select a model as needed and obtain the [model ID](model-list.md).

```JSON
    {
      "$schema": "https://opencode.ai/config.json",
      "provider": {
        "myprovider": {
          "npm": "@ai-sdk/openai-compatible",
          "name": "volcengine",
          "options": {
            "baseURL": "https://ark.ap-southeast.bytepluses.com/api/v3",
            "apiKey": "<ARK_API_KEY>"
          },
          "models": {
        "seed-1-8-251228": { 
          "name": "seed-1-8-251228"
        }
          }
        }
      }
    }
```

## Get started

1. Start OpenCode:

   ```Bash
   opencode
   ```

2. Enter `/models`, select the configured `seed-1-8-251228` model, and use it in OpenCode.

## Integrate second\-party/third\-party MCP servers

If the built\-in tools do not cover your use case, OpenCode supports configuring second\-party and third\-party MCP servers in settings (for how to obtain MCP servers, see [Get an MCP server](integrate-third-party-tools.md#c5f28d13)). For specific operations, see the OpenCode official documentation.

# Integrate with OpenClaw (formerly Clawdbot)

## Installation steps

1. Run the following command to install OpenClaw.

**macOS**

```Bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

**Windows**

The installation command in a Windows PowerShell environment is as follows:

```Bash
iwr -useb https://openclaw.ai/install.ps1 | iex
```

2. Follow the prompts to complete the OpenClaw configuration. The configuration information is as follows.

   > **Note**

   > OpenClaw is continuously evolving. If actual usage differs from the following configuration, you can choose "Skip" or the default option to complete the configuration process. You can see the documentation to configure and use Agent Plan later.

   |Prompts |Configuration |
   |---|---|
   |I understand this is personal\-by\-default and shared/multi\-user use requires lock\-down. Continue? |Select "Yes" |
   |Setup mode |Select "QuickStart" |
   |Model/auth provider |Select "Skip for now"; you can configure it later. |
   |Default model |Select "Keep current" |
   |Select channel (QuickStart) |Select "Skip for now"; you can configure it later. |
   |Search provider |Select "Skip for now" |
   |Configure skills now? (recommended) |Select "No"; you can configure it later. |
   |Enable hooks? |Select "Skip for now". Selection method: press the Space key to select an option, and then press Enter to go to the next step. |
   |How do you want to hatch your bot? |Select "Hatch in Terminal". |

## Configure tools

> **Note**

> `glm-5.2`, `deepseek-v4-flash`, and `deepseek-v4-pro` support a 1M context window for long sessions that include large codebases. You can explicitly specify the window size using the `contextWindow` parameter.

**Web UI**

1. Run the following command to open the Web UI.

```Bash
openclaw dashboard
```

2. In the left menu bar, select **Configuration** \- **Settings** \- **Advanced**, then click **Open** to view and modify the configuration information. The detailed configuration information is as follows. The key configuration items that need to be modified are:

* baseUrl: https://ark.ap\-southeast.bytepluses.com/api/v3

* apiKey: [Get an API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* models: [Select a model and obtain the model ID](model-list.md)

> **Note**

* > If OpenClaw has already been configured, do not directly overwrite the existing configuration. We recommend updating the `models`, `agents`, and `gateway` node information based on the provided configuration. The `models` node contains the list of supported models.

```Plain Text
```JSON
{
  "models": {
    "providers": {
      "byteplus": {
        "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/v3",
        "apiKey": "<ARK_API_KEY>",
        "api": "openai-completions",
        "models": [
          {
            "id": "seed-1-8-251228",
            "name": "seed-1-8-251228"
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "byteplus/seed-1-8-251228"
      },
      "models": {
        "byteplus/seed-1-8-251228": {}
      }
    }
  },
  "gateway": {
    "mode": "local"
  }
}
```
```



3. After completing the configuration, save the configuration file first, and then click **Update** to update the configuration. After the configuration update is complete, you need to reconnect to the service to access the Web UI.

   <span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/be24b57cff314b749016abd2fd3ada7a~tplv-goo7wpa0wc-image.image) </span>


</Tab>
<Tab zoneid="l8B4p92jFi" title="Terminal">
<TabTitle>Terminal</TabTitle>

1. Run the following command in the terminal to open the OpenClaw configuration file.


```Bash
nano ~/.openclaw/openclaw.json
```

2. Modify the configuration information. The core configuration information that needs to be modified is as follows:

* baseUrl: https://ark.ap\-southeast.bytepluses.com/api/v3

* apiKey: [Get an API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* models: [Select a model and obtain the model ID](model-list.md)

> **Note**

* > If OpenClaw has already been configured, do not directly overwrite the existing configuration. We recommend updating the `models`, `agents`, and `gateway` node information based on the provided configuration. The `models` node contains the list of supported models.

```Plain Text
```JSON
{
  "models": {
    "providers": {
      "byteplus": {
        "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/v3",
        "apiKey": "<ARK_API_KEY>",
        "api": "openai-completions",
        "models": [
          {
            "id": "seed-1-8-251228",
            "name": "seed-1-8-251228"
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "byteplus/seed-1-8-251228"
      },
      "models": {
        "byteplus/seed-1-8-251228": {}
      }
    }
  },
  "gateway": {
    "mode": "local"
  }
}
```
```



3. After completing the configuration, save the file, and run the following command in the terminal to restart the service for the changes to take effect.

   ```Bash
   openclaw gateway restart
   ```

## Get started

* Open the TUI and check the gateway status.

```JSON
openclaw tui
/status
```

* Open the Web UI and interact on the Chat page.

```JSON
openclaw dashboard
```

## Integrate skills

If the built\-in tools do not cover your use case, OpenClaw supports skill configuration: select **Configure skills now** in the installation wizard, or configure skills later in settings. For detailed instructions, see the OpenClaw official documentation.

# Integrate TRAE

## Install TRAE

Visit the [TRAE official website](https://www.trae.ai/) to download and install the version for your operating system.

## Configure tools

1. After logging in through the **Individual user** entry point, click the **Settings** icon in the upper\-right corner of the interface to go to the settings center.

2. In the left navigation bar, select **Models** and configure settings on the model management page.

3. Click  **+ Add model**. The **Add model** window is displayed. Configure the following information in the window.

* **Provider**: **BytePlus**

* **Model**:

   * Select one of the TRAE preset models directly from the list. These are all default versions.

   * To use another model, click **Use another model**, and then enter the **model ID** configuration information in the input box.

* **API key**: [Get API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

## Switch models

In the lower\-right corner of the AI chat input box, click the current model name, and then select the configured model from the model list.

After selecting the model, you can use TRAE for development tasks.

## Integrate second\-party/third\-party MCP servers

If the built\-in tools do not cover your use case, TRAE supports configuring second\-party and third\-party MCP servers in settings (for how to obtain them, see [Get an MCP server](integrate-third-party-tools.md#c5f28d13)). For detailed instructions, see the TRAE official documentation.

# Integrate DeepSeek Harness

## Installation steps

Visit the [DeepSeek Harness official website](https://www.deepseek.com/en/harness/) and install the version for your operating system.

## Configure tools

### Method 1: Manually configure through the Web UI

1. Run the following command in the terminal to start the DeepSeek Harness Web UI. Open the access URL output by the command in a browser. The default URL is `http://127.0.0.1:3080/`.

   ```bash
   npx @deepseek-ai/dsh web
   ```

2. In the DeepSeek Harness Web UI, click the **account avatar** in the lower\-left corner, and then click **Settings**.

3. In the left navigation bar, click **Models** and configure the settings based on the information on the page.

You can integrate DeepSeek Harness with the ModelArk platform in either of the following ways.

**Method 1: Configure the DeepSeek card**

On the DeepSeek card, select **Custom settings**, enter the following information, and save the configuration.

* **API key**: Get an [API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* **API host**: `https://ark.ap-southeast.bytepluses.com/api/v3`

* **Add model**: Add models as needed. For the model list, see [Model list](model-list.md), and obtain the [model ID](model-list.md).

**Method 2: Add a custom provider**

Click **Add model provider \-\> Custom model API** and enter the following information to create the provider.

* **Provider ID**: Customize this value. If you need to rename it later, add a new provider and delete the old provider.

* **Display name**: Customize this value.

* **API address** and **API protocol**: The supported API addresses, API protocols, and their mappings are as follows. Make sure you configure them correctly.

|API protocol |API address |
|---|---|
|openai\-completions |`https://ark.ap-southeast.bytepluses.com/api/v3` |
|openai\-responses |`https://ark.ap-southeast.bytepluses.com/api/v3` |
|anthropic\-messages |`https://ark.ap-southeast.bytepluses.com/api/compatible` |

* **API key**: Get an [API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* **Add model**: Add models as needed. For the model list, see [Model list](model-list.md), and obtain the [model ID](model-list.md).

4. (Optional) To configure image input support for the model, configure it in the configuration file. On the **model configuration page**, click **Open configuration file** (path: `$DSH_HOME/settings.yaml`), and add `input: [text, image]` to the configuration section for the supported model.

Configuration example:

```Yaml
- id: llm-pi-ai
  config:
    providers:
      ark-model:
        displayName: ark-model
        apiKeyEnv: ARK_MODEL_API_KEY
        api: openai-responses
        baseURL: https://ark.ap-southeast.bytepluses.com/api/v3
        models:
          - id: dola-seed-2-1-turbo-260628
            name: dola-seed-2.1-turbo
            input: [text, image]
```

### Method 2: Configure through the ModelArk plugin

ModelArk provides the official DeepSeek Harness plugin `dsh-volcengine-plugin`. After you install and enable the plugin, you can configure how to access the model in the **ModelArk** plugin. For pay\-as\-you\-go, select **PayGo** as the access method.

1. **Launch DeepSeek Harness**.

   Run the following command in the terminal to start the DeepSeek Harness Web UI. Open the access URL output by the command in a browser. The default URL is `http://127.0.0.1:3080/`.

   ```Bash
   npx @deepseek-ai/dsh web
   ```

2. Install and enable the ModelArk plugin.

   1. In the DeepSeek Harness Web UI, click **Plugins** in the left navigation pane to open the plugin add page.

   2. Enter the plugin package name `dsh-volcengine-plugin`, and click **Install**.

      ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/image-20261029-163642-351.png)

   3. After the plugin is installed, click **Enable now**.

3. Configure the ModelArk plugin.

   1. Click the **ModelArk** plugin card to go to the plugin configuration page.

   2. In the connection configuration area, select **PayGO** as the access method.

   3. Enter the [API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey), and click **Save**.

   4. (Optional) To adjust the invocation protocol or service address, expand **Custom configuration**, and modify the invocation protocol and base URL based on your actual access environment.

4. Configure the model.

   1. Select a model from the model catalog.

   2. Click **Save model configuration**.

After you complete the preceding steps, return to the conversation page, select the configured model from the model drop\-down menu in the lower\-right corner of the input box, and then start using it.

# Integrate Cline

## Installation steps

Open VS Code, search for `Cline` in the extension marketplace, and install it.

## Configure tools

After the Cline extension is installed, you need to configure the following information.

* **API Provider**: `OpenAI Compatible` (The Agent Plan API is compatible with the OpenAI standard.)

* **Base URL**: `https://ark.ap-southeast.bytepluses.com/api/v3`

* **API key**: [Get API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* **Model ID**: Select a model as needed and obtain its [model ID](model-list.md).

After the configuration is complete, you can enter your requirements in the input box and interact with the model.

## Integrate second\-party/third\-party MCP servers

If the built\-in tools do not cover your use case, Cline supports configuring second\-party and third\-party MCP servers in settings. For how to obtain them, see [Get an MCP server](integrate-third-party-tools.md#c5f28d13). For detailed steps, see the Cline official documentation.

# Integrate Cursor

## Installation steps

Download the installer from the official website: Download and install Cursor from the [Cursor official website](https://cursor.com/features).

## Configure tools

> **Tip**

> Due to Cursor limitations, only users subscribed to Cursor Pro or a higher plan can customize model configurations.

After Cursor is installed, configure the Models module as follows:

* OpenAI API Key: [Get API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* Override OpenAI base URL: `https://ark.ap-southeast.bytepluses.com/api/v3`

* Add Custom Model: Select a model as needed and obtain its [model ID](model-list.md).

After the configuration is complete, you can select the configured model in the chat panel for interaction.

# Integrate Roo Code

## Installation steps

Open VS Code, search for `Roo Code` in the extension marketplace and install it. After installation is complete, select Trust Publisher.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/dd47c5150044469cb4e95b8de067d817~tplv-goo7wpa0wc-image.image)

## Configure tools

After installation is complete, configure the following information.

* **API Provider**: `OpenAI Compatible` (The Agent Plan API is compatible with the OpenAI standard.)

* **Base URL**: `https://ark.ap-southeast.bytepluses.com/api/v3`

* **API key**: [Get API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* **Model**: Select a model as needed and obtain the [model ID](model-list.md).

After the configuration is complete, you can enter your requirements in the input box and interact with the model.

## Integrate second\-party/third\-party MCP servers

If the built\-in tools do not cover your use case, Roo Code supports configuring second\-party and third\-party MCP servers in settings. For how to obtain them, see [Get an MCP server](integrate-third-party-tools.md#c5f28d13). For detailed steps, see the Roo Code official documentation.

# Integrate Kilo Code

## Installation steps

Open VS code, search for `kilo code` in the extension marketplace and install it. After installation is complete, select Trust Publisher.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/3e991c168c0c49bc9449f6f6efc29bd7~tplv-goo7wpa0wc-image.image)

## Configure tools

Select Use your own API key, and then configure the following information.

* **API Provider**: `OpenAI Compatible` (The Agent Plan API is compatible with the OpenAI standard.)

* **Base URL**: `https://ark.ap-southeast.bytepluses.com/api/v3`

* **API key**: [Get API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

* **Model**: Select a model as needed and obtain the [model ID](model-list.md).

After the configuration is complete, you can enter your requirements in the input box and interact with the model.

## Integrate second\-party/third\-party MCP servers

If the built\-in tools do not cover your use case, Kilo Code supports configuring second\-party and third\-party MCP servers in settings (for how to obtain them, see [Get an MCP server](integrate-third-party-tools.md#c5f28d13)); for specific steps, see the official Kilo Code documentation.
