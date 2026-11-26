# Kimi Code

This topic describes how to configure and use ModelArk Agent Plan in Kimi Code.

# Prerequisites

* You have subscribed to Agent Plan as needed. If you have not subscribed, visit the [Agent Plan console](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan) to subscribe to a plan.

* Install [Node.js 18 or later](https://nodejs.org/en/download/).

# Kimi Code installation

**macOS/Linux**

Run the following command in a command\-line interface to install Kimi Code.

```Bash
curl -fsSL https://code.kimi.com/kimi-code/install.sh | bash
```

After installation, run the following command. Installation is successful if a version number is displayed.

```Bash
kimi --version
```

**Windows**

Install [Git for Windows](https://gitforwindows.org/) before the first launch. Kimi Code uses the provided Git Bash to run shell commands. Run the following installation command in PowerShell.

```PowerShell
irm https://code.kimi.com/kimi-code/install.ps1 | iex
```

After installation, run the following command. Installation is successful if a version number is displayed.

```Bash
kimi --version
```

# Kimi Code configuration

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

3. Configure Kimi Code with one click

   ```Bash
   arkcli helper
   ```

Run ArkCLI Helper and complete configuration as prompted.

* **Select a profile**: `agent-plan_ap-southeast-1_personal`.

* **Select the default model**: Select as needed.

* **Select an AI agent**: `KimiCode`.

   You can configure harness capabilities. Select `Configure` or `Skip` as prompted.

After configuration, Kimi Code automatically recognizes the configured model. Start Kimi Code to use it.

> **tip**

* > If ArkCLI reports that `kimi-code` is unsupported, upgrade ArkCLI and configure it again. For more diagnostic commands, see [ArkCLI Helper command reference](tools-and-platform.md#helper-command).

* > If an authentication error is returned, confirm that the profile, API key, and Base URL use the same access method.

## Method 2: Manual configuration

Create and edit `~/.kimi-code/config.toml`. On Windows, use `%USERPROFILE%\.kimi-code\config.toml`. The following configuration items require attention:

* `<ARK_API_KEY>`: Replace it with your own [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan).

* `model`: Configure a text generation model. You can configure `ark-code-latest` or a Model Name. For model information, see [Supported models and harness](agent-plan-personal-plan-overview.md#supported-models-harness).

```toml
default_model = "byteplus-agent-plan/ark-code-latest"

[providers.byteplus-agent-plan]
type = "openai"
base_url = "https://ark.ap-southeast.bytepluses.com/api/plan/v3"
api_key = "<ARK_API_KEY>"

[models."byteplus-agent-plan/ark-code-latest"]
provider = "byteplus-agent-plan"
model = "ark-code-latest"
max_context_size = 256000
```

> **tip**

* > To write the configuration to another directory, set the `KIMI_CODE_HOME` environment variable. The configuration file path is `$KIMI_CODE_HOME/config.toml`.

* > If the previous model is still used after configuration, exit Kimi Code, confirm that `KIMI_CODE_HOME` in the launch terminal matches the configuration, and then restart it.

# Kimi Code usage

1. In a terminal, enter the project directory where you want to use Kimi Code and start it.

   ```Bash
   kimi
   ```

2. Enter the following request to verify that the model can respond and read the workspace. If it returns the file structure of the current project, Kimi Code is connected to ModelArk.

   ```text
   Briefly describe the file structure of the current directory.
   ```
