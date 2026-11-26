# Grok

This topic describes how to configure and use ModelArk Agent Plan Team in Grok CLI.

# Prerequisites

* An administrator must complete the following actions before subaccounts can configure and use Grok CLI:

   1. Purchase a ModelArk Agent Plan Team plan: [1. Purchase Agent Plan Team](agent-plan-enterprise-get-started.md#purchase-plan).

   2. Assign seats to subaccounts: [2. Assign seats](agent-plan-enterprise-get-started.md#assign-seats).

* Install [Node.js 18 or later](https://nodejs.org/en/download/).

# Grok CLI installation

**macOS/Linux**

Run the following command in a command\-line interface to install Grok CLI.

```Bash
curl -fsSL https://x.ai/cli/install.sh | bash
```

After installation, run the following command. Installation is successful if help information is displayed.

```Bash
grok --help
```

**Windows**

Install [Git for Windows](https://gitforwindows.org/), then open Git Bash and run the following command. You can also run it in MSYS2 Bash.

```Bash
curl -fsSL https://x.ai/cli/install.sh | bash
```

The official script installs the native Windows executable `grok.exe`. To launch it from PowerShell or CMD, add `%USERPROFILE%\.grok\\bin` to the user PATH, reopen the terminal, and run `grok`. When running the installation script in WSL, use the Linux version.

After installation, run the following command. Installation is successful if help information is displayed.

```Bash
grok --help
```

# Grok CLI configuration

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

3. Configure Grok CLI with one click

   ```Bash
   arkcli helper
   ```

Run ArkCLI Helper and complete configuration as prompted.

* **Select a profile**: `agent-plan-team_ap-southeast-1_team`.

* **Select the default model**: Select as needed.

* **Select an AI agent**: `Grok`.

After configuration, Grok CLI automatically recognizes the configured model. Start Grok CLI to use it.

> **tip**

* > If ArkCLI reports that `grok` is unsupported, upgrade ArkCLI and configure it again. For more diagnostic commands, see [ArkCLI Helper command reference](tools-and-platform.md#helper-command).

* > If an authentication error is returned, confirm that the profile, API key, and Base URL use the same access method.

## Method 2: Manual configuration

Create and edit `~/.grok/config.toml`. On Windows, use `%USERPROFILE%\.grok\config.toml`. The following configuration items require attention:

* `<ARK_API_KEY>`: Replace it with your own [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine).

* `model`: Configure a text generation model. You can configure `ark-code-latest` or a Model Name. For model information, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

```toml
[model.ark-code-latest]
model = "ark-code-latest"
base_url = "https://ark.ap-southeast.bytepluses.com/api/plan/v3"
api_backend = "chat_completions"
api_key = "<ARK_API_KEY>"
name = "ark-code-latest"

[models]
default = "ark-code-latest"
```

> **tip**

* > To write the configuration to another directory, set the `GROK_HOME` environment variable. The configuration file path is `$GROK_HOME/config.toml`.

* > If the previous model is still used after configuration, exit Grok CLI, confirm that `GROK_HOME` in the launch terminal matches the configuration, and then restart it.

# Grok CLI usage

1. In a terminal, enter the project directory where you want to use Grok CLI and start it.

   ```Bash
   grok
   ```

2. Enter the following request to verify that the model can respond and read the workspace. If it returns the file structure of the current project, Grok CLI is connected to ModelArk.

   ```text
   Briefly describe the file structure of the current directory.
   ```
