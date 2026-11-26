# ZCode

This topic describes how to configure and use ModelArk Agent Plan Team in ZCode.

# Prerequisites

An administrator must complete the following actions before subaccounts can configure and use ZCode:

1. Purchase a ModelArk Agent Plan Team plan: [1. Purchase Agent Plan Team](agent-plan-enterprise-get-started.md#purchase-plan).

2. Assign a seat to the subaccount: [2. Assign seats](agent-plan-enterprise-get-started.md#assign-seats).

# Install ZCode

Visit the [ZCode website](https://zcode.z.ai/cn#all-downloads) to download and install the version for your operating system. For installation steps, see the [official ZCode installation guide](https://zcode.net.cn/en/docs/install#setup-guide). After installation, sign in as prompted.

# Configure ZCode

Agent Plan Team supports both the OpenAI and Anthropic API protocols, and ZCode supports both. The following example uses the **OpenAI protocol**. To use the Anthropic protocol, change the API format to **Anthropic** and set the Base URL to `https://ark.ap-southeast.bytepluses.com/api/plan`.

## Method 1: One\-click configuration (recommended)

Ark CLI is the official command\-line tool for the ModelArk platform. It lets you quickly authenticate and manage resources in a local terminal, and install capabilities into commonly used AI agents to reduce manual configuration time and errors.

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

3. Configure ZCode with one click

   ```Bash
   arkcli helper
   ```

Run ArkCLI Helper and complete configuration as prompted.

* **Select a profile**: `agent-plan-team_ap-southeast-1_team`.

* **Select the default model**: Select as needed.

* **Select an AI agent**: `ZCode`.

   You can configure harness capabilities. Select `Configure` or `Skip` as needed.

After configuration, ZCode automatically recognizes the configured models. Start ZCode to use them.

## Method 2: Manual configuration

1. Open ZCode, click the **Settings** icon in the lower\-left corner, go to **Model settings**, and click **Add provider**.

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/zcode-config.png)

2. Complete the following fields in the form and save the provider.

   * **Name**: Enter a recognizable name, such as `Ark Agent Plan Team`.

   * **Base URL**: Enter `https://ark.ap-southeast.bytepluses.com/api/plan/v3` for the OpenAI protocol, or `https://ark.ap-southeast.bytepluses.com/api/plan` for the Anthropic protocol.

   * **API Key**: Enter your [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine).

      Do not use a regular ModelArk platform API Key interchangeably with this key.

      ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/agent-plan-enterprise-api-key.png)

   * **API format**: Select the format that corresponds to the chosen Base URL.

      |API format |Base URL |
      |---|---|
      |Anthropic Messages |`https://ark.ap-southeast.bytepluses.com/api/plan` |
      |Chat Completions |`https://ark.ap-southeast.bytepluses.com/api/plan/v3` |
      |Responses |`https://ark.ap-southeast.bytepluses.com/api/plan/v3` |

3. Click **Add model**, complete the following fields, and save the model. You can add multiple models to one provider.

   * **Model ID**: Enter a Model Name supported by your plan. See [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness). Configure a text generation model.

   * **Context window**: Enter the selected model's maximum context length. For values, see [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

   * **Maximum output tokens** (optional): Set as needed. Leave this field empty to use the model's default maximum.

You can also add a provider by editing the local configuration file `~/.zcode/v2/config.json` (on Windows, `%USERPROFILE%\.zcode\v2\config.json`). The file is generated automatically after you first configure anything in the UI. If it already contains settings, add an entry to the `provider` object.

Example `~/.zcode/v2/config.json` with the API format set to **Anthropic Messages**

```JSON
{
  "provider": {
    ...
    "byteplus-enterprise-agent-plan": {
      "name": "Ark Agent Plan Team",
      "kind": "anthropic",
      "options": {
        "apiKey": "<ARK_API_KEY>",
        "baseURL": "https://ark.ap-southeast.bytepluses.com/api/plan",
        "apiKeyRequired": true
      },
      "enabled": true,
      "source": "custom",
      "models": {
        "ark-code-latest": {
          "limit": { "context": 256000, "output": 128000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "seed-2.0-mini": {
          "limit": { "context": 256000, "output": 128000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "seed-2.0-lite": {
          "limit": { "context": 256000, "output": 128000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "deepseek-v4-flash": {
          "limit": { "context": 1024000, "output": 384000 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "dola-seed-2.1-turbo": {
          "limit": { "context": 256000, "output": 256000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "deepseek-v4-pro": {
          "limit": { "context": 1024000, "output": 384000 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "deepseek-v4.1-flash": {
          "limit": { "context": 1024000, "output": 384000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        }
      }
    }
    ...
  }
}
```

Example `~/.zcode/v2/config.json` with the API format set to **Chat Completions**

```JSON
{
  "provider": {
    ...
    "byteplus-enterprise-agent-plan": {
      "name": "Ark Agent Plan Team",
      "kind": "openai-compatible",
      "options": {
        "apiKey": "<ARK_API_KEY>",
        "baseURL": "https://ark.ap-southeast.bytepluses.com/api/plan/v3",
        "apiKeyRequired": true
      },
      "enabled": true,
      "source": "custom",
      "models": {
        "ark-code-latest": {
          "limit": { "context": 256000, "output": 128000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "seed-2.0-mini": {
          "limit": { "context": 256000, "output": 128000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "seed-2.0-lite": {
          "limit": { "context": 256000, "output": 128000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "deepseek-v4-flash": {
          "limit": { "context": 1024000, "output": 384000 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "dola-seed-2.1-turbo": {
          "limit": { "context": 256000, "output": 256000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "deepseek-v4-pro": {
          "limit": { "context": 1024000, "output": 384000 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "deepseek-v4.1-flash": {
          "limit": { "context": 1024000, "output": 384000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        }
      }
    }
    ...
  }
}
```

Example `~/.zcode/v2/config.json` with the API format set to **Responses**

```JSON
{
  "provider": {
    ...
    "byteplus-enterprise-agent-plan": {
      "name": "Ark Agent Plan Team",
      "kind": "openai",
      "options": {
        "apiKey": "<ARK_API_KEY>",
        "baseURL": "https://ark.ap-southeast.bytepluses.com/api/plan/v3",
        "apiKeyRequired": true
      },
      "enabled": true,
      "source": "custom",
      "models": {
        "ark-code-latest": {
          "limit": { "context": 256000, "output": 128000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "seed-2.0-mini": {
          "limit": { "context": 256000, "output": 128000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "seed-2.0-lite": {
          "limit": { "context": 256000, "output": 128000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "deepseek-v4-flash": {
          "limit": { "context": 1024000, "output": 384000 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "dola-seed-2.1-turbo": {
          "limit": { "context": 256000, "output": 256000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "deepseek-v4-pro": {
          "limit": { "context": 1024000, "output": 384000 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "deepseek-v4.1-flash": {
          "limit": { "context": 1024000, "output": 384000 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        }
      }
    }
    ...
  }
}
```

> **warning**

* > Replace `<ARK_API_KEY>` with your [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine).

* > Do not use a regular ModelArk platform API Key interchangeably with this key.

* > The examples list all text generation models currently offered by Agent Plan Team. Model support differs between Small, Medium, Large, and Max plans. Follow the support matrix in [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness) and remove entries unsupported by your selected plan to avoid model not found errors.

4. After saving, return to the ZCode main interface.

# Use ZCode

1. Select the model you added from the model dropdown at the top of the ZCode main interface.

2. Enter your request in the chat box, such as generating code, fixing a bug, refactoring, or writing unit tests, then press Enter to start developing through conversation. ZCode supports the full development lifecycle, including planning, coding, review, and deployment.

> For more tips and advanced features, see the [official ZCode documentation](https://zcode.net.cn/en/docs).
