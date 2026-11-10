# Codex

This topic describes how to configure ModelArk Coding Plan in Codex CLI.

> **tip**

> First, visit the [ModelArk Coding Plan page](https://www.byteplus.com/en/activity/codingplan), subscribe to a plan that fits your needs, and then configure it in your coding tool.

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

# Codex CLI

## Install Codex CLI

> **tip**

> Coding Plan supports the Responses API, so you can use it with the latest version of Codex CLI.

Prerequisites: Install [Node.js 18 or later environment](https://nodejs.org/en/download/).

In your terminal, run the following command to install Codex CLI.

```Bash
npm i -g @openai/codex
```

After the installation is complete, run the following command to check the version.

```Bash
codex --version
```

## Configure Codex CLI

### Option 1: Automated configuration (recommended)

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

3. Run ArkCLI Helper and follow the on\-screen prompts to complete the Codex configuration.

   1. Enter the `arkcli helper` command in the terminal to start ArkCLI Helper.

   2. Select the Plan profile to configure: Choose `coding-plan_ap-southeast-1_personal (Coding Plan)`.

   3. Select the model to use.

   4. Select the AI agent to configure: `Codex`.

After you complete these steps, Codex is configured automatically. No manual installation is required.

### Option 2: Manual configuration

1. Create and open the Codex configuration file. The file path varies by operating system. Locate it as follows:

   **macOS/Linux**

Codex configuration file path on macOS/Linux: `~/.codex/config.toml`.

      1. If the `.codex` directory does not exist in your home directory, run the following command to create it:

         ```Bash
         mkdir -p ~/.codex
         ```

      2. Create and open the configuration file.

         ```Bash
         nano ~/.codex/config.toml
         ```

   **Windows**

Codex configuration file path on Windows: `%USERPROFILE%\.codex\config.toml`. The following example uses Command Prompt:

      1. If the `.codex` directory does not exist in your home directory, run the following command to create it:

         ```Bash
         if not exist "%USERPROFILE%\.codex" mkdir "%USERPROFILE%\.codex"
         ```

      2. Create and open the configuration file.

         ```Bash
         notepad "%USERPROFILE%\.codex\config.toml"
         ```

2. Edit `config.toml`. The configuration information to note is as follows:

   * `model`: For the configuration method, see [Model configuration](coding-plan-personal-ai-codex.md#model-configuration).

   * `env_key`: The name of the environment variable. Do not modify `ARK_API_KEY` directly — you'll set its value in the next step.

      ```plaintext
      model = "<Model_Name>"
      model_provider = "byteplus-coding-plan"
      model_supports_reasoning_summaries = true
      
      [model_providers.byteplus-coding-plan]
      name = "byteplus-coding-plan"
      base_url = "https://ark.ap-southeast.bytepluses.com/api/coding/v3"
      env_key = "ARK_API_KEY"
      wire_api = "responses"
      ```

      > **warning**

      * > `model_supports_reasoning_summaries = true`: Enables reasoning.

      * > `model_reasoning_effort`: Controls the reasoning length. Set to `low`, `medium`, or `high`.

3. Configure the environment variable: set `ARK_API_KEY` to your [API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

   **macOS / Linux**

1. View the Shell type.

         ```Bash
         echo $SHELL
         ```

      2. Add the environment variable to your shell configuration file so it takes effect automatically in new terminal sessions.

         * Zsh

            ```Bash
            # Replace YOUR_API_KEY with your API key.
            echo 'export ARK_API_KEY="YOUR_API_KEY"' >> ~/.zshrc
            source ~/.zshrc
            ```

         * Bash

            ```Bash
            # Replace YOUR_API_KEY with your API key.
            echo 'export ARK_API_KEY="YOUR_API_KEY"' >> ~/.bashrc
            source ~/.bashrc
            ```

   **Windows**

* Command Prompt: The environment variable takes effect in new Command Prompt windows after it is set.

         ```Bash
         # Replace YOUR_API_KEY with your API Key.
         setx ARK_API_KEY "YOUR_API_KEY"
         # Open a new window and check whether the environment variable takes effect.
         echo %ARK_API_KEY%
         ```

      * PowerShell: The environment variable takes effect in new PowerShell windows after it is set.

         ```Bash
         # Replace YOUR_API_KEY with your API Key.
         [Environment]::SetEnvironmentVariable("ARK_API_KEY", "YOUR_API_KEY", [EnvironmentVariableTarget]::User)
         # Open a new window and check whether the environment variable takes effect.
         echo $env:ARK_API_KEY
         ```

## Get started

Run the following command to start Codex CLI.

```Bash
codex
```

# ChatGPT desktop app

## Install the ChatGPT desktop app

After an update, OpenAI has removed the standalone Codex app and integrated it into the ChatGPT desktop app as one of its built\-in modes.

Download and install the [ChatGPT desktop app](https://openai.com/chatgpt/download/).

## Configure the ChatGPT desktop app

1. Click the toggle in the top\-left corner to switch from **ChatGPT Work** mode to **Codex** mode.

2. Click your account in the bottom\-left corner, then click **Settings**.

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/Codex_example-1.png)

3. On the **Configuration** page, in the **Custom config.toml settings** section, click **Open config.toml**.

4. Edit `config.toml` with the following key configuration.

   * `model`: For the configuration method, see [Model configuration](coding-plan-personal-ai-codex.md#model-configuration).

   * `env_key`: The name of the environment variable. Do not modify `ARK_API_KEY` directly — you'll set its value in the next step.

      ```plaintext
      model = "<Model_Name>"
      model_provider = "byteplus-coding-plan"
      model_supports_reasoning_summaries = true
      
      [model_providers.byteplus-coding-plan]
      name = "byteplus-coding-plan"
      base_url = "https://ark.ap-southeast.bytepluses.com/api/coding/v3"
      env_key = "ARK_API_KEY"
      wire_api = "responses"
      ```

      > **warning**

      * > `model_supports_reasoning_summaries = true`: Enables reasoning.

      * > `model_reasoning_effort`: Controls the reasoning length. Set to `low`, `medium`, or `high`.

5. Configure the environment variable: set `ARK_API_KEY` to your [API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey). For details, see [Configure Codex CLI](coding-plan-personal-ai-codex.md#method-1-auto-helper).

## Get started

After configuration, we recommend that you quit and restart the ChatGPT desktop app to apply the changes. After restarting, you can use it in the chat interface.

# Error code

When using Coding Plan in coding tools, you may encounter errors. Use the error message to identify the error scenario and diagnose the issue. For details, see [Error codes](error-codes.md).
