# OpenClaw (Clawdbot)

[OpenClaw](https://clawd.bot/) (formerly known as Clawdbot) is an open\-source, self\-hosted personal AI assistant that runs locally on your computer. It is compatible with macOS, Windows, and Linux, and supports integration with popular chat tools. In addition to a rich set of built\-in agent tools, OpenClaw can be further extended through plugins and skills.

The ModelArk Coding Plan supports integration with OpenClaw. Refer to this article for configuration and usage instructions.

> **Tip**

> You must first access the [ModelArk Coding Plan campaign page](https://www.byteplus.com/en/activity/codingplan) and subscribe to a plan as required, and then configure your programming tools accordingly.

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

# Install and configure OpenClaw

## Install

**Prerequisite**: Install [Node.js 22 or later](https://nodejs.org/en/download/).

1. Run the following command to install OpenClaw.

**macOS**

```Bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

**Windows**

PowerShell:

```PowerShell
iwr -useb https://openclaw.ai/install.ps1 | iex
```

2. Complete the OpenClaw configuration following the prompts. Refer to the configuration example below.

   1. Basic configuration

   |Prompt |Configuration |
   |---|---|
   |I understand this is powerful and inherently risky. Continue? |Select "Yes" |
   |Onboarding mode |Select "QuickStart" |

   2. Model configuration

   |Prompt |Configuration |
   |---|---|
   |Model/auth provider |Select "**BytePlus**" |
   |How do you want to provide this API key? |"Paste API key now" |
   |Enter BytePlus API key |[API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey) |
   |Default model |Select a model supported by the Coding Plan (`byteplus-plan`). In this document, we use `byteplus-plan/ark-code-latest`.<br><br>If you want to use a model that is not listed under `byteplus-plan`, complete the setup first, then update the model setting following steps described below. |

   3. Functionality configuration

   |Prompt |Configuration |
   |---|---|
   |Select channel (QuickStart) |Select "Skip for now"; you can configure this later. |
   |Configure skills now? (recommended) |Select "No"; you can configure this later. |
   |Enable hooks? |Press the Spacebar to select an option, then press Enter to proceed to the next step. |
   |How do you want to hatch your bot? |Select "Hatch in TUI". |

# Configure

After selecting the appropriate method based on the operating system to complete the configuration, the configuration file will include the complete list of models supported by Coding Plan, making it easy to quickly switch between target models.

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

3. Run ArkCLI Helper and follow the on\-screen prompts to complete the OpenClaw configuration.

   1. Enter the `arkcli helper` command in the terminal to start ArkCLI Helper.

   2. Select the Plan profile to configure: Choose `coding-plan_ap-southeast-1_personal (Coding Plan)`.

   3. Select the model to use.

   4. Select the AI agent to configure: `OpenClaw`.

After you complete these steps, OpenClaw is configured automatically. No manual installation is required.

## Method 2: Configure manually

Coding Plan is compatible with the OpenAI interface protocol and supports both Responses API and Chat API. The Responses API configuration is recommended for better reasoning quality.

The two API types share the same configuration structure; only the API adapter under `models.providers` differs:

* Responses API configuration: `"api": "openai-responses"`

* Chat API configuration: `"api": "openai-completions"`

> **warning**

> `glm-5.2`, `glm-5.3-flash`, `deepseek-v4.1-flash`, `deepseek-v4-flash`, and `deepseek-v4-pro` support a 1M context window for long sessions involving large codebases. Specify the window size explicitly via the `contextWindow` field.

**Web UI**

1. Run the following command to open the Web UI.

```Bash
openclaw dashboard
```

2. In the left menu bar, select configuration \> **All Settings** \> **Raw** to view and modify the configuration. The specific configuration information is as follows. You need to replace `<ARK_API_KEY>` with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

> **warning**

* > If you have already configured OpenClaw, do not overwrite the existing configuration directly.

   > It is recommended to update the `models`, `agents`, and `gateway` node information according to the provided configuration. `models` contains the list of supported models.

```JSON
{
  "models": {
    "providers": {
      "byteplus-plan": {
        "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
        "apiKey": "<ARK_API_KEY>",
        "api": "openai-responses",
        "models": [
          {
            "id": "ark-code-latest",
            "name": "ark-code-latest",
            "contextWindow": 256000,
            "maxTokens": 32000,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "dola-seed-2.0-pro",
            "name": "dola-seed-2.0-pro",
            "contextWindow": 256000,
            "maxTokens": 32000,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "dola-seed-2.0-lite",
            "name": "dola-seed-2.0-lite",
            "contextWindow": 256000,
            "maxTokens": 32000,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "dola-seed-2.0-code",
            "name": "dola-seed-2.0-code",
            "contextWindow": 256000,
            "maxTokens": 32000,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "glm-5.2",
            "name": "glm-5.2",
            "contextWindow": 1024000,
            "maxTokens": 65536,
            "input": [
              "text"
            ]
          },
          {
            "id": "glm-5.3-flash",
            "name": "glm-5.3-flash",
            "contextWindow": 1024000,
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
        "primary": "byteplus-plan/ark-code-latest"
      },
      "models": {
        "byteplus-plan/ark-code-latest": {},
        "byteplus-plan/dola-seed-2.0-pro": {},
        "byteplus-plan/dola-seed-2.0-lite": {},
        "byteplus-plan/dola-seed-2.0-code": {},
        "byteplus-plan/glm-5.2": {},
        "byteplus-plan/glm-5.3-flash": {},
        "byteplus-plan/deepseek-v4-flash": {},
        "byteplus-plan/deepseek-v4-pro": {},
        "byteplus-plan/deepseek-v4.1-flash": {}
      }
    }
  },
  "gateway": {
    "mode": "local"
  }
}
```

3. After completing the configuration, first click the **Save** button in the upper right corner to save. Once saved, click **Update** to update the configuration.

**Terminal**

1. Run the following command in the terminal to open the OpenClaw configuration file.

```Bash
nano ~/.openclaw/openclaw.json
```

2. Modify the configuration information to complete the integration with Coding Plan. Replace `<ARK_API_KEY>` with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

> **warning**

* > If you have already configured OpenClaw, do not overwrite the existing configuration directly. It is recommended to update the `models`, `agents`, and `gateway` node information according to the provided configuration. The `models` node contains the list of supported models.

```JSON
{
  "models": {
    "providers": {
      "byteplus-plan": {
        "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
        "apiKey": "<ARK_API_KEY>",
        "api": "openai-responses",
        "models": [
          {
            "id": "ark-code-latest",
            "name": "ark-code-latest",
            "contextWindow": 256000,
            "maxTokens": 32000,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "dola-seed-2.0-pro",
            "name": "dola-seed-2.0-pro",
            "contextWindow": 256000,
            "maxTokens": 32000,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "dola-seed-2.0-lite",
            "name": "dola-seed-2.0-lite",
            "contextWindow": 256000,
            "maxTokens": 32000,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "dola-seed-2.0-code",
            "name": "dola-seed-2.0-code",
            "contextWindow": 256000,
            "maxTokens": 32000,
            "input": [
              "text",
              "image"
            ]
          },
          {
            "id": "glm-5.2",
            "name": "glm-5.2",
            "contextWindow": 1024000,
            "maxTokens": 65536,
            "input": [
              "text"
            ]
          },
          {
            "id": "glm-5.3-flash",
            "name": "glm-5.3-flash",
            "contextWindow": 1024000,
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
        "primary": "byteplus-plan/ark-code-latest"
      },
      "models": {
        "byteplus-plan/ark-code-latest": {},
        "byteplus-plan/dola-seed-2.0-pro": {},
        "byteplus-plan/dola-seed-2.0-lite": {},
        "byteplus-plan/dola-seed-2.0-code": {},
        "byteplus-plan/glm-5.2": {},
        "byteplus-plan/glm-5.3-flash": {},
        "byteplus-plan/deepseek-v4-flash": {},
        "byteplus-plan/deepseek-v4-pro": {},
        "byteplus-plan/deepseek-v4.1-flash": {}
      }
    }
  },
  "gateway": {
    "mode": "local"
  }
}
```

3. After completing the configuration, save the file, and run the following command in the terminal to restart the service for the changes to take effect.

   ```Bash
   openclaw gateway restart
   ```

## Get started

* Open TUI and view the Gateway status.

```Bash
openclaw tui
/status
```

* Open the Web UI and interact on the Chat page.

```Bash
openclaw dashboard
```

## Switch model

Please select the corresponding model switching solution according to the model configuration method.

* Model configuration: Configure the Model Name. The following two methods are supported for switching models.

   Prerequisite: Complete [Configure](coding-plan-personal-ai-openclaw.md#48c0b1ea), where `agents.defaults.models` contains the list of supported models.

   * Method 1: Global configuration (permanent)

      Set `agents.defaults.model.primary` in the configuration file to the target model. After the change, you need to restart the service.

      ```JSON
      {
          "agents": {
              "defaults": {
                  "model": {
                      "primary": "byteplus-plan/dola-seed-2.0-code"
                  }
              }
          }
      }
      ```

   * Method 2: Switch model in the current session (temporary)

      After executing `openclaw tui` in the terminal, switch models via `/models`. When the interface returns "model set to <Model_Name\>", the configuration takes effect.

* Model configuration: `byteplus-plan/ark-code-latest`, switch models via the console.

   In the configuration file, `agents.defaults.model.primary` is set to `byteplus-plan/ark-code-latest`.

   Model switching actions: On the [activation management page](https://www.byteplus.com/en/activity/codingplan), select the model to use. The switch will take effect within 3–5 minutes.

# Integrate Feishu bot

## Prerequisites

Install and configure OpenClaw.

> **Note**

> The Feishu plugin is in rapid iteration. The plugin version used in this document is **2026.3.10**. If there are differences between actual usage and the following steps, refer to [OpenClaw Feishu official plugin](https://bytedance.larkoffice.com/docx/MFK7dDFLFoVlOGxWCv5cTXKmnMh).

## Install the Feishu plugin

1. Run the following command to install the Feishu plugin.

   ```Bash
   npx -y @larksuite/openclaw-lark-tools install
   ```

   If an error occurs when running the command, add `sudo` before the command and run it again.

2. According to the interface prompts, select **create new bot**.

3. Scan the QR code using the Feishu app and select one\-click create Feishu bot.

4. Send `/feishu start` to the bot in Feishu. If version information is returned, the installation is successful.

5. Send a message to the bot in Feishu to start a conversation.

# Advanced guide: Configure embedding models

The ModelArk Coding Plan provides the `skylark-embedding-vision` model, which enables OpenClaw to generate semantic vector representations, converting conversations, documentation, and task information into high\-dimensional vectors for efficient retrieval based on semantic similarity.

Compared to traditional keyword matching, embedding\-based retrieval can more accurately recall relevant content in scenarios with large volumes of information, lengthy conversations, and dispersed materials, significantly reducing omissions and misjudgments. Additionally, embedding provides a unified semantic indexing layer for memory retrieval, knowledge base Q&A, and tool invocation, helping OpenClaw improve cross\-turn context tracking and overall effectiveness without increasing the main model's inference cost.

Select any of the following methods to configure the embedding model.

**Web UI**

1. Execute the following command to open the Web UI.

```Bash
openclaw dashboard
```

2. In the left menu, select **configuration** \- **All Settings** \- **Raw**, then add `memorySearch` configuration information under the `agents.defaults` node. The configuration to be added is as follows. You need to replace `<ARK_API_KEY>` with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

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
             "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
             "apiKey": "<ARK_API_KEY>"
           }
         }
         ...
       }
     }
     ...
    }
   ```

3. After completing the configuration, first click **Save** in the upper right corner to save, then click **Update** to update the configuration.

**Terminal**

1. Execute the following command in the terminal to open the OpenClaw configuration file.

```Bash
nano ~/.openclaw/openclaw.json
```

2. Add `memorySearch` configuration information under the `agents.defaults` node. The configuration to be added is as follows. You need to replace `<ARK_API_KEY>` with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

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
             "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
             "apiKey": "<ARK_API_KEY>"
           }
         }
         ...
       }
     }
     ...
   }
   ```

3. After completing the configuration, restart the gateway to make the configuration take effect.

   ```Bash
   openclaw gateway restart
   ```

# FAQs

## OpenClaw configuration issues

* [How to enable the deep reasoning mode in OpenClaw?](coding-plan-personal-faq.md#8463b9f0)

* [Error: The developer role is not supported when using OpenClaw](coding-plan-personal-faq.md#b93c211a)

* [How to handle image understanding failure in OpenClaw?](coding-plan-personal-faq.md#d6e54a95)

* [How to handle the error 'gateway connect failed: Error: pairing required' when installing OpenClaw?](coding-plan-personal-faq.md#d992e222)

* [How to handle the error 'run error: 404 The model or endpoint xxx does not exist or you do not have access to it' when configuring OpenClaw?](coding-plan-personal-faq.md#1031e91a)

## Feishu plugin usage issues

* [How to upgrade the Feishu plugin?](coding-plan-personal-faq.md#9ea2656a)

* [What actions should be taken when encountering issues while using the Feishu plugin?](coding-plan-personal-faq.md#b79427a5)

* [How to configure the Feishu bot to enable streaming output, time display, and status presentation capabilities?](coding-plan-personal-faq.md#b4add6a7)

* [How to modify the reply mode of the Feishu bot in the group?](coding-plan-personal-faq.md#bf4d91a0)

## Ark Helper usage issues

* [How to upgrade Ark Helper?](coding-plan-personal-faq.md#ef5aba2a)

* [How to uninstall Ark Helper?](coding-plan-personal-faq.md#d1dcd27a)

# Error code

When using the Coding Plan package in programming tools, you may encounter errors. You can identify the corresponding error scenario based on the error message and troubleshoot the issue. For details, see [Error codes](error-codes.md).
