# OpenClaw

This document describes how to configure and use ModelArk Agent Plan in OpenClaw.

# Prerequisites

You have subscribed to Agent Plan as needed. If you have not subscribed, visit the [Agent Plan console](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan) to subscribe as needed.

# OpenClaw installation

Prerequisite: Install [Node.js 22 or later](https://nodejs.org/en/download/).

1. Run the following command to install OpenClaw.

   **macOS / Linux**

```Bash
   curl -fsSL https://openclaw.ai/install.sh | bash
   ```

   **Windows**

Run the following installation command in Windows PowerShell:

   ```Bash
   iwr -useb https://openclaw.ai/install.ps1 | iex
   ```

2. Follow the prompts to configure OpenClaw using the settings below.

   > **warning**

   > OpenClaw is continuously updated. If the setup differs from the following configuration, select "Skip" or the default options to finish setup. You can then follow this document to configure and use Agent Plan.

|Prompt |Configuration |
|---|---|
|I understand this is personal\-by\-default and shared/multi\-user use requires lock\-down. Continue? |Select "Yes" |
|Setup mode |Select "QuickStart" |
|Model/auth provider |Select "Skip for now". You can configure it later. |
|Default model |Select "Keep current" |
|Select channel (QuickStart) |Select "Skip for now". You can configure it later. |
|Search provider |Select "Skip for now" |
|Configure skills now? (recommended) |Select "No". You can configure it later. |
|Enable hooks? |Select "Skip for now". Press Space to select an option, then press Enter to continue. |
|How do you want to hatch your bot? |Select "Hatch in Terminal". |

# OpenClaw configuration

## Required configuration

If you are familiar with OpenClaw or already have a local configuration, obtain the following information to configure it directly.

* **Base URL**: `https://ark.ap-southeast.bytepluses.com/api/plan/v3`.

* **API Key**: [Obtain your exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan).

* **Model**: Configure a text generation model using `ark-code-latest` or a Model Name. For model information, see [Supported models and harness](agent-plan-personal-plan-overview.md#supported-models-harness).

## Method 1: Automatic configuration (recommended)

Ark CLI is the official command\-line tool for ModelArk. It lets you authenticate and manage resources from your local terminal and install capabilities in commonly used AI agents, reducing configuration time and the risk of errors.

1. Install Ark CLI

   Requirements: Node.js 18 or later. Supports macOS, Linux, and Windows.

   ```Bash
   npm install -g @byteplus/ark-cli@latest
   arkcli --version
   ```

2. Sign in to Ark CLI

   Follow the prompts to sign in and complete authorization.

   ```Bash
   arkcli auth login
   ```

3. Configure OpenClaw automatically

   ```Bash
   arkcli helper
   ```

Run ArkCLI Helper and follow the prompts to complete configuration.

* **Select a profile**: `agent-plan_ap-southeast-1_personal`.

* **Select the default model**: Select a model as needed.

* **Select an AI Agent**: `OpenClaw`.

   harness configuration is supported. Follow the prompts to select `Configure` or `Skip` as needed.

After configuration, OpenClaw automatically recognizes the configured models. Start OpenClaw to use them.

## Method 2: Manual configuration

Agent Plan is compatible with the OpenAI API protocol and supports the Responses API and Chat API. The Responses API configuration is recommended for better inference results.

The configuration is similar for both APIs. Specify the corresponding API adapter in `models.providers`:

* Responses API configuration: `"api": "openai-responses"`

* Chat API configuration: `"api": "openai-completions"`

**Web UI**

1. Run the following command to open the Web UI.

   ```Bash
   openclaw dashboard
   ```

2. In the left sidebar, select **Configuration** \> **Settings** \> **Advanced**, then click **Open** to view and edit the configuration. Use the following configuration and replace `<ARK_API_KEY>` with your [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan).

> **warning**

> If OpenClaw is already configured, do not overwrite the existing configuration directly. Update the `models`, `agents`, and `gateway` nodes using the configuration provided. The `models` node contains the list of supported models.

The following is a configuration example for the Responses API.

```JSON
{
  "models": {
    "providers": {
      "byteplus-agent-plan": {
        "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/plan/v3",
        "apiKey": "<ARK_API_KEY>",
        "api": "openai-responses",
        "models": [
          {
            "id": "ark-code-latest",
            "name": "ark-code-latest",
            "contextWindow": 256000,
            "maxTokens": 65536,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "seed-2.0-mini",
            "name": "seed-2.0-mini",
            "contextWindow": 256000,
            "maxTokens": 65536,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "seed-2.0-lite",
            "name": "seed-2.0-lite",
            "contextWindow": 256000,
            "maxTokens": 65536,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "dola-seed-2.1-turbo",
            "name": "dola-seed-2.1-turbo",
            "contextWindow": 256000,
            "maxTokens": 65536,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "deepseek-v4-flash",
            "name": "deepseek-v4-flash",
            "contextWindow": 1024000,
            "maxTokens": 65536,
            "input": [
              "text"
            ]
          },
          {
            "id": "deepseek-v4-pro",
            "name": "deepseek-v4-pro",
            "contextWindow": 1024000,
            "maxTokens": 65536,
            "input": [
              "text"
            ]
          },
          {
            "id": "deepseek-v4.1-flash",
            "name": "deepseek-v4.1-flash",
            "contextWindow": 1024000,
            "maxTokens": 65536,
            "input": [
              "text",
              "image"
            ]
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "byteplus-agent-plan/ark-code-latest"
      },
      "models": {
        "byteplus-agent-plan/ark-code-latest": {},
        "byteplus-agent-plan/seed-2.0-mini": {},
        "byteplus-agent-plan/seed-2.0-lite": {},
        "byteplus-agent-plan/dola-seed-2.1-turbo": {},
        "byteplus-agent-plan/deepseek-v4-flash": {},
        "byteplus-agent-plan/deepseek-v4-pro": {},
        "byteplus-agent-plan/deepseek-v4.1-flash": {}
      }
    }
  },
  "gateway": {
    "mode": "local"
  }
}
```

3. Save the configuration file, then click **Update** to update the configuration. After the update, reconnect to the service to access the Web UI.

**Terminal**

1. Run the following command in your terminal to open the OpenClaw configuration file.

   ```Bash
   nano ~/.openclaw/openclaw.json
   ```

2. Edit the configuration to integrate Agent Plan. Replace `<ARK_API_KEY>` with your [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan).

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/agent-plan-personal-api-key.png)

> **warning**

> If OpenClaw is already configured, do not overwrite the existing configuration directly. Update the `models`, `agents`, and `gateway` nodes using the configuration provided. The `models` node contains the list of supported models.

The following is a configuration example for the Responses API.

```JSON
{
  "models": {
    "providers": {
      "byteplus-agent-plan": {
        "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/plan/v3",
        "apiKey": "<ARK_API_KEY>",
        "api": "openai-responses",
        "models": [
          {
            "id": "ark-code-latest",
            "name": "ark-code-latest",
            "contextWindow": 256000,
            "maxTokens": 65536,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "seed-2.0-mini",
            "name": "seed-2.0-mini",
            "contextWindow": 256000,
            "maxTokens": 65536,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "seed-2.0-lite",
            "name": "seed-2.0-lite",
            "contextWindow": 256000,
            "maxTokens": 65536,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "dola-seed-2.1-turbo",
            "name": "dola-seed-2.1-turbo",
            "contextWindow": 256000,
            "maxTokens": 65536,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "deepseek-v4-flash",
            "name": "deepseek-v4-flash",
            "contextWindow": 1024000,
            "maxTokens": 65536,
            "input": [
              "text"
            ]
          },
          {
            "id": "deepseek-v4-pro",
            "name": "deepseek-v4-pro",
            "contextWindow": 1024000,
            "maxTokens": 65536,
            "input": [
              "text"
            ]
          },
          {
            "id": "deepseek-v4.1-flash",
            "name": "deepseek-v4.1-flash",
            "contextWindow": 1024000,
            "maxTokens": 65536,
            "input": [
              "text",
              "image"
            ]
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "byteplus-agent-plan/ark-code-latest"
      },
      "models": {
        "byteplus-agent-plan/ark-code-latest": {},
        "byteplus-agent-plan/seed-2.0-mini": {},
        "byteplus-agent-plan/seed-2.0-lite": {},
        "byteplus-agent-plan/dola-seed-2.1-turbo": {},
        "byteplus-agent-plan/deepseek-v4-flash": {},
        "byteplus-agent-plan/deepseek-v4-pro": {},
        "byteplus-agent-plan/deepseek-v4.1-flash": {}
      }
    }
  },
  "gateway": {
    "mode": "local"
  }
}
```

3. Save the file, then run the following command in your terminal to restart the service and apply the changes.

   ```Bash
   openclaw gateway restart
   ```

# OpenClaw usage

* Open the Web UI and interact on the Chat page.

   ```Bash
   
   openclaw dashboard
   
   ```

* Open the TUI and check the Gateway status.

   ```Bash
   
   openclaw tui
   /status
   
   ```

# Other operations

## Model switching

Select a model switching method based on how you configured the model.

* Model configuration: Model Name. You can switch models in either of the following ways.

   Prerequisite: Complete the file configuration in [OpenClaw configuration](agent-plan-personal-openclaw.md#48c0b1ea). The `agents.defaults.models` node must contain the list of supported models.

   * Method 1: Global configuration (permanent)

      In the configuration file, change `agents.defaults.model.primary` to the target model, then restart the service.

      ```JSON
      {
          "agents": {
              "defaults": {
                  "model": {
                      "primary": "byteplus-agent-plan/dola-seed-2.1-turbo"
                  }
              }
          }
      }
      ```

   * Method 2: Switch models in the current session (temporary)

      Run `openclaw tui` in your terminal, then use `/models` to switch models. The message "model set to <Model_Name\>" indicates that the configuration has taken effect.

* Model configuration: `byteplus-agent-plan/ark-code-latest`. Switch models in the console.

   In the configuration file, `agents.defaults.model.primary` is set to `byteplus-agent-plan/ark-code-latest`.

   To switch models, click the **Use configuration** tab in the [Agent Plan console](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan) and select a model. The change takes effect in 3\-5 minutes.

## Embedding model configuration

The `skylark-embedding-vision` embedding model provided by ModelArk Agent Plan gives OpenClaw semantic embedding capabilities. It converts conversations, documents, and task information into high\-dimensional vectors for efficient retrieval based on semantic similarity.

Compared with traditional keyword matching, embedding retrieval can recall relevant content more accurately when there is a large amount of information, long conversations, or scattered materials, significantly reducing omissions and incorrect matches. Embeddings also provide a shared semantic index for memory retrieval, knowledge base Q&A, and tool calls, helping OpenClaw track context across turns and improve overall results without increasing inference costs for the main model.

Configure the embedding model using either of the following methods.

**Web UI**

1. Run the following command to open the Web UI.

   ```Bash
   openclaw dashboard
   ```

2. In the left sidebar, select **Configuration** \> **Settings** \> **Advanced**, then click **Open** to view and edit the configuration. Add the following `memorySearch` configuration under `agents.defaults`. Replace `<ARK_API_KEY>` with your [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan).

   ```JSON
   {
     ...
     "agents": {
       "defaults": {
         ...
         "memorySearch": {
           "provider": "openai",
           "model": "skylark-embedding-vision",
           "remote": {
             "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/plan/v3",
             "apiKey": "<ARK_API_KEY>"
           }
         }
         ...
       }
     }
     ...
   }
   ```

3. Save the configuration file, then click **Update** to update the configuration. After the update, reconnect to the service to access the Web UI.

**Terminal**

1. Run the following command in your terminal to open the OpenClaw configuration file.

   ```Bash
   nano ~/.openclaw/openclaw.json
   ```

2. Add the following `memorySearch` configuration under `agents.defaults`. Replace `<ARK_API_KEY>` with your [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan).

   ```JSON
   {
     ...
     "agents": {
       "defaults": {
         ...
         "memorySearch": {
           "provider": "openai",
           "model": "skylark-embedding-vision",
           "remote": {
             "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/plan/v3",
             "apiKey": "<ARK_API_KEY>"
           }
         }
         ...
       }
     }
     ...
   }
   ```

3. Restart the gateway to apply the configuration.

   ```Bash
   openclaw gateway restart
   ```

## Image and video generation integration

ModelArk Agent Plan provides image and video generation models that you can integrate through Skills in AI tools. For configuration details, see [Visual model configuration](agent-plan-personal-visual-models.md).

> **warning**

* > Visual model availability varies by plan. For details, see [Supported models and harness](agent-plan-personal-plan-overview.md#supported-models-harness).

* > Visual models cannot be switched using Auto or the console.

## Feishu bot integration

### Prerequisites

Install and configure OpenClaw. See [OpenClaw installation](agent-plan-personal-openclaw.md#b51c87f8) and [OpenClaw configuration](agent-plan-personal-openclaw.md#48c0b1ea).

> **warning**

> The Feishu plugin is being updated rapidly. This document uses plugin version **2026.3.10**. If your experience differs from the steps below, see the [official OpenClaw Feishu plugin documentation](https://bytedance.larkoffice.com/docx/MFK7dDFLFoVlOGxWCv5cTXKmnMh).

### Feishu plugin installation

1. Run the following command to install the Feishu plugin.

   ```Bash
   npx -y @larksuite/openclaw-lark-tools install
   ```

   If the command fails, add `sudo` before the command and run it again.

2. Follow the prompts and select **Create bot**.

3. Scan the QR code using the Feishu client, then select **Create a Feishu bot in one click**.

4. Send `/feishu start` to the bot in Feishu. A response containing the version number indicates a successful installation.

5. Send a message to the bot in Feishu to start a conversation.
