# Pi

This topic describes how to configure and use ModelArk Agent Plan Team in Pi.

# Prerequisites

* An administrator must complete the following steps before subaccounts can configure and use Pi:

   1. Purchase a ModelArk Agent Plan Team plan: [1. Purchase Agent Plan Team](agent-plan-enterprise-get-started.md#purchase-plan).

   2. Assign seats to subaccounts: [2. Assign seats](agent-plan-enterprise-get-started.md#assign-seats).

* Install [Node.js 20 or later](https://nodejs.org/en/download/).

# Install Pi

**macOS/Linux**

Run the following commands in a terminal to install Pi.

```Bash
# Method 1: Global installation with npm
npm install -g @earendil-works/pi-coding-agent

# Method 2: Official one-click installation script
curl -fsSL https://pi.dev/install.sh | sh
```

After installation, run the following command to check the result. If a version number is displayed, installation was successful.

```Bash
pi --version
```

**Windows**

First install [Git for Windows](https://git-scm.com/download/win). Pi uses the provided `bash.exe` to run `!` / `!!` shell commands. The official one\-click curl script is not supported on Windows.

Install globally with npm:

```Bash
npm install -g @earendil-works/pi-coding-agent
```

After installation, run the following command to check the result. If a version number is displayed, installation was successful.

```Bash
pi --version
```

# Configure Pi

## Method 1: One\-click configuration (recommended)

Ark CLI is the official command\-line tool for ModelArk. It lets you quickly complete authentication and resource management in a local terminal and install capabilities in commonly used AI agents, reducing manual configuration time and errors.

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

3. Configure Pi with one click

   ```Bash
   arkcli helper
   ```

Run Ark CLI Helper and complete the configuration as prompted.

* **Select a profile**: `agent-plan-team_ap-southeast-1_team`.

* **Select the default model**: Select as needed.

* **Select an AI agent**: `Pi`.

   You can configure harness capabilities. Select `Configure` or `Skip` as prompted.

After configuration, Pi automatically recognizes the configured model. Start Pi to use it.

## Method 2: Manual configuration

Create and edit `~/.pi/agent/models.json`. Configure the following:

* `<ARK_API_KEY>`: Replace with your own [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine).

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/agent-plan-enterprise-api-key.png)

* `model`: Configure a text generation model. See [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

   ```JSON
   {
     "providers": {
       "agent-plan": {
         "api": "openai-completions",
         "apiKey": "<ARK_API_KEY>",
         "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/plan/v3",
         "models": [
           {
             "id": "ark-code-latest"
           },
           {
             "contextWindow": 256000,
             "id": "seed-2.0-mini",
             "input": ["text", "image"],
             "maxTokens": 128000
           },
           {
             "contextWindow": 256000,
             "id": "seed-2.0-lite",
             "input": ["text", "image"],
             "maxTokens": 128000
           },
           {
             "contextWindow": 1024000,
             "id": "deepseek-v4-flash",
             "input": ["text"],
             "maxTokens": 384000
           },
           {
             "contextWindow": 256000,
             "id": "dola-seed-2.1-turbo",
             "input": ["text", "image"],
             "maxTokens": 256000
           },
           {
             "contextWindow": 1024000,
             "id": "deepseek-v4-pro",
             "input": ["text"],
             "maxTokens": 384000
           },
           {
             "contextWindow": 1024000,
             "id": "deepseek-v4.1-flash",
             "input": ["text", "image"],
             "maxTokens": 384000
           }
         ]
       }
     }
   }
   ```

> **tip**

> You do not need to restart Pi after editing `models.json`. Run `/model` in a conversation to reload it automatically.

# Use Pi

1. Go to your project directory and start Pi:

   ```Bash
   pi
   ```

2. Run `/model` in the Pi interactive interface to select and switch to a provider and model you just configured.

   For supported models, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

3. Send a question to check connectivity, for example:

   ```Plain Text
   Briefly describe the file structure of the current directory.
   ```

   If Pi returns a normal response, Agent Plan Team has been integrated successfully.

> For more Pi tutorials, see the [official Pi documentation](https://pi.dev/docs/latest).

# Other operations

## Switch models

Pi supports two ways to switch models:

|Configuration method |Steps |
|---|---|
|Interactive switching |Run `/model` in the Pi conversation interface and select the target model from the drop\-down list. |
|Edit the configuration |Edit `~/.pi/agent/models.json` to add or update model entries in the corresponding provider's `models` array. Save the file, then run `/model` to reload it. |

## Add image and video generation capabilities

ModelArk Agent Plan Team Small and higher plans provide image generation model quotas, and Large and higher plans provide video generation model quotas. ModelArk provides Skills to help AI tools integrate and use these capabilities. For configuration instructions, see [Video and image generation models](agent-plan-enterprise-visual-models.md).

Pi's `models[].input` declares only **input** modalities (`text` / `image`). Image and video generation require tool calls or MCP integration.

> **warning**

* > Different plans support different visual models. For details, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

* > Visual models cannot be switched through Auto mode or the console.
