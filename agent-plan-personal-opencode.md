# OpenCode

This topic describes how to configure and use ModelArk Agent Plan in OpenCode.

# Prerequisites

You have subscribed to Agent Plan as needed. If you have not subscribed, visit the [Agent Plan console](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan) to subscribe as needed.

# Install OpenCode

Prerequisite: Install [Node.js 18 or later](https://nodejs.org/en/download/).

Run the following command in a command\-line interface to install OpenCode.

```Bash
npm install -g opencode-ai
```

After installation, run the following command to check the result. Installation is successful if the version number is displayed.

```Bash
opencode --version
```

# Configure OpenCode

## Required configuration

If you are familiar with OpenCode or already have a local configuration, obtain the following information to configure it directly.

* **Base URL**: `https://ark.ap-southeast.bytepluses.com/api/plan/v3`.

* **API Key**: [Get exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan).

* **Model**: Configure a text generation model. You can use `ark-code-latest` or a Model Name. For model information, see [Supported models and harness](agent-plan-personal-plan-overview.md#supported-models-harness).

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

3. Configure OpenCode with one click

   ```Bash
   arkcli helper
   ```

Run ArkCLI Helper and complete configuration as prompted.

* **Select a profile**: `agent-plan_ap-southeast-1_personal`.

* **Select the default model**: Select as needed.

* **Select an AI agent**: `OpenCode`.

   You can configure harness capabilities. Select `Configure` or `Skip` as needed.

After configuration, OpenCode automatically recognizes the configured models. Start OpenCode to use them.

## Method 2: Manual configuration

Create `~/.config/opencode/opencode.json` (on Windows, `%USERPROFILE%\.config\opencode\opencode.json`) with the following configuration:

* `<ARK_API_KEY>`: Replace it with your [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan).

* `model`: Configure a text generation model. See [Supported models and harness](agent-plan-personal-plan-overview.md#supported-models-harness).

Agent Plan is compatible with the OpenAI API protocol and supports the Responses API and Chat API. The Responses API configuration is recommended for better inference results.

The configurations for the two supported APIs differ only in the npm package:

* Responses API configuration: `"npm": "@ai-sdk/openai"`

* Chat API configuration: `"npm": "@ai-sdk/openai-compatible"`

The following example configures `ark-code-latest`.

> **warning**

* > Some models support a 1M context window for long sessions involving large codebases. Specify the window size explicitly through `limit.context`.

* > At `provider.byteplus-agent-plan.models`, **two values** (the object key and the `name` field) must be **replaced with the same Model Name**. Update both.

* > To enable image understanding, add the following setting to the model configuration: `"modalities": {"input": ["text", "image"], "output": ["text"]}`

**Responses API**

> **tip**

> Set npm to `@ai-sdk/openai` to use the Responses API. `/responses` is appended automatically to `baseURL`.

```JSON
{
  "$schema": "https://opencode.ai/config.json",
  "model": "byteplus-agent-plan/ark-code-latest",
  "provider": {
    "byteplus-agent-plan": {
      "npm": "@ai-sdk/openai",
      "name": "BytePlus (Responses API)",
      "options": {
        "baseURL": "https://ark.ap-southeast.bytepluses.com/api/plan/v3",
        "apiKey": "<ARK_API_KEY>"
      },
      "models": {
        "ark-code-latest": {
          "name": "ark-code-latest",
          "limit": {
            "context": 256000,
            "output": 65536
          },
          "modalities": {
            "input": ["text", "image"],
            "output": ["text"]
          }
        },
        "seed-2.0-mini": {
          "name": "seed-2.0-mini",
          "limit": {
            "context": 256000,
            "output": 65536
          },
          "modalities": {
            "input": ["text", "image"],
            "output": ["text"]
          }
        },
        "seed-2.0-lite": {
          "name": "seed-2.0-lite",
          "limit": {
            "context": 256000,
            "output": 65536
          },
          "modalities": {
            "input": ["text", "image"],
            "output": ["text"]
          }
        },
        "dola-seed-2.1-turbo": {
          "name": "dola-seed-2.1-turbo",
          "limit": {
            "context": 256000,
            "output": 65536
          },
          "modalities": {
            "input": ["text", "image"],
            "output": ["text"]
          }
        },
        "deepseek-v4-flash": {
          "name": "deepseek-v4-flash",
          "limit": {
            "context": 1024000,
            "output": 65536
          },
          "modalities": {
            "input": ["text"],
            "output": ["text"]
          }
        },
        "deepseek-v4-pro": {
          "name": "deepseek-v4-pro",
          "limit": {
            "context": 1024000,
            "output": 65536
          },
          "modalities": {
            "input": ["text"],
            "output": ["text"]
          }
        },
        "deepseek-v4.1-flash": {
          "name": "deepseek-v4.1-flash",
          "limit": {
            "context": 1024000,
            "output": 65536
          },
          "modalities": {
            "input": ["text", "image"],
            "output": ["text"]
          }
        }
      }
    }
  }
}
```

**Chat API**

> **tip**

> Set npm to `@ai-sdk/openai-compatible` to use the Chat API. `/chat/completions` is appended automatically to `baseURL`.

```JSON
{
  "$schema": "https://opencode.ai/config.json",
  "model": "byteplus-agent-plan/ark-code-latest",
  "provider": {
    "byteplus-agent-plan": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "BytePlus (Chat API)",
      "options": {
        "baseURL": "https://ark.ap-southeast.bytepluses.com/api/plan/v3",
        "apiKey": "<ARK_API_KEY>"
      },
      "models": {
        "ark-code-latest": {
          "name": "ark-code-latest",
          "limit": {
            "context": 256000,
            "output": 65536
          },
          "modalities": {
            "input": ["text", "image"],
            "output": ["text"]
          }
        },
        "seed-2.0-mini": {
          "name": "seed-2.0-mini",
          "limit": {
            "context": 256000,
            "output": 65536
          },
          "modalities": {
            "input": ["text", "image"],
            "output": ["text"]
          }
        },
        "seed-2.0-lite": {
          "name": "seed-2.0-lite",
          "limit": {
            "context": 256000,
            "output": 65536
          },
          "modalities": {
            "input": ["text", "image"],
            "output": ["text"]
          }
        },
        "dola-seed-2.1-turbo": {
          "name": "dola-seed-2.1-turbo",
          "limit": {
            "context": 256000,
            "output": 65536
          },
          "modalities": {
            "input": ["text", "image"],
            "output": ["text"]
          }
        },
        "deepseek-v4-flash": {
          "name": "deepseek-v4-flash",
          "limit": {
            "context": 1024000,
            "output": 65536
          },
          "modalities": {
            "input": ["text"],
            "output": ["text"]
          }
        },
        "deepseek-v4-pro": {
          "name": "deepseek-v4-pro",
          "limit": {
            "context": 1024000,
            "output": 65536
          },
          "modalities": {
            "input": ["text"],
            "output": ["text"]
          }
        },
        "deepseek-v4.1-flash": {
          "name": "deepseek-v4.1-flash",
          "limit": {
            "context": 1024000,
            "output": 65536
          },
          "modalities": {
            "input": ["text", "image"],
            "output": ["text"]
          }
        }
      }
    }
  }
}
```

# Use OpenCode

1. Start OpenCode:

   ```Bash
   opencode
   ```

2. Enter `/models`, select the configured model, and use it in OpenCode.

# Other operations

## Add image and video generation capabilities

ModelArk Agent Plan Small and higher plans provide image generation model quotas, and Large and higher plans provide video generation model quotas. ModelArk provides Skills to help AI tools integrate and use these capabilities. For configuration instructions, see [Video and image generation models](agent-plan-personal-visual-models.md).

> **warning**

* > Visual model support differs by plan. For details, see [Supported models and harness](agent-plan-personal-plan-overview.md#supported-models-harness).

* > Visual models cannot be switched through Auto mode or the console.

## Switch models

You can switch **text generation models** at any time as needed. ModelArk supports several methods for switching models.

|Configuration method |Console |Command |Configuration file |
|---|---|---|---|
|Model switching |1. See [Configure OpenCode](agent-plan-personal-opencode.md#configure-opencode) and set the model name to `ark-code-latest`.<br><br>2. On the [Agent Plan console](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan), click **Use configuration** and select a model. The change takes effect in 3\-5 minutes. |After starting OpenCode, enter `/models` to switch models. |See [Configure OpenCode](agent-plan-personal-opencode.md#configure-opencode) and set the model name to the model you need. |
