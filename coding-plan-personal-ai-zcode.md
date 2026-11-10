# ZCode

ZCode is a desktop AI coding client from Zhipu (available for macOS / Windows / Linux) that deeply integrates the GLM series and is also compatible with custom OpenAI / Anthropic protocol providers. This document describes how to configure ModelArk Coding Plan in ZCode.

# Prerequisite steps

1. Go to the [ModelArk Coding Plan event](https://www.byteplus.com/en/activity/codingplan) and subscribe to a plan before configuring your coding tool.

2. Go to the [ZCode website](https://zcode.z.ai/cn#all-downloads) and download and install the version that matches your operating system. For installation steps, see the [ZCode installation guide](https://zcode.net.cn/en/docs/install#setup-guide). After installation, sign in as prompted.

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

# Configure ZCode

Coding Plan is compatible with both OpenAI and Anthropic API protocols, and ZCode supports both. The following example uses the **OpenAI protocol**; to use the Anthropic protocol, change the API format to **Anthropic** and set the Base URL to `https://ark.ap-southeast.bytepluses.com/api/coding`.

1. Start the ZCode client, click the **Settings** icon in the bottom\-left corner, go to **Model settings**, and click **Add provider**.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/zcode1.png)

2. In the form that appears, fill in the following parameters and save the provider.

   * **Name**: Custom, for easy identification, for example `Ark Coding Plan`.

   * **Base URL**: `https://ark.ap-southeast.bytepluses.com/api/coding/v3` (OpenAI protocol); to use the Anthropic protocol, enter `https://ark.ap-southeast.bytepluses.com/api/coding`.

   * **API key**: Enter your [ModelArk API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

   * **API format**: Depends on the Base URL you selected.

      |API format |Base URL |
      |---|---|
      |Anthropic Messages |`https://ark.ap-southeast.bytepluses.com/api/coding` |
      |Chat Completions |`https://ark.ap-southeast.bytepluses.com/api/coding/v3` |
      |Responses |`https://ark.ap-southeast.bytepluses.com/api/coding/v3` |

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/zcode2.png)

3. Click **Add model**, fill in the following parameters in the form, and save the model. You can click **Add model** multiple times to add multiple models under one provider.

   * **Model ID**: Enter a supported Model Name (see **Core configuration** above), or use `ark-code-latest`.

   * **Context window**: Enter the maximum context length of the selected model.

   * **Max output tokens** (optional): Fill in as needed; leave empty to follow the model's default upper limit.

In addition to adding a provider through the client UI, you can also directly edit the local configuration file `~/.zcode/v2/config.json` (on Windows, `%USERPROFILE%\.zcode\v2\config.json`). This file is generated automatically the first time you complete any configuration through the UI; if the file already contains entries, just append a new entry to the `provider` object.

Example `~/.zcode/v2/config.json` when API format is **Anthropic Messages**

```JSON
{
  "provider": {
    ...
    "ark-coding-plan": {
      "name": "Ark Coding Plan",
      "kind": "anthropic",
      "options": {
        "apiKey": "<ARK_API_KEY>",
        "baseURL": "https://ark.ap-southeast.bytepluses.com/api/coding",
        "apiKeyRequired": true
      },
      "enabled": true,
      "source": "custom",
      "models": {
        "ark-code-latest": {
          "limit": { "context": 256000, "output": 32768 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "dola-seed-2.0-pro": {
          "limit": { "context": 256000, "output": 32768 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "dola-seed-2.0-lite": {
          "limit": { "context": 256000, "output": 32768 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "dola-seed-2.0-code": {
          "limit": { "context": 256000, "output": 32768 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "glm-5.2": {
          "limit": { "context": 1024000, "output": 131072 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "glm-5.3-flash": {
          "limit": { "context": 1024000, "output": 131072 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "deepseek-v4-flash": {
          "limit": { "context": 1024000, "output": 393216 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "deepseek-v4-pro": {
          "limit": { "context": 1024000, "output": 393216 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "deepseek-v4.1-flash": {
          "limit": { "context": 1024000, "output": 393216 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        }
      }
    }
    ...
  }
}
```

Example `~/.zcode/v2/config.json` when API format is **Chat Completions**

```JSON
{
  "provider": {
    ...
    "ark-coding-plan": {
      "name": "Ark Coding Plan",
      "kind": "openai-compatible",
      "options": {
        "apiKey": "<ARK_API_KEY>",
        "baseURL": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
        "apiKeyRequired": true
      },
      "enabled": true,
      "source": "custom",
      "models": {
        "ark-code-latest": {
          "limit": { "context": 256000, "output": 32768 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "dola-seed-2.0-pro": {
          "limit": { "context": 256000, "output": 32768 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "dola-seed-2.0-lite": {
          "limit": { "context": 256000, "output": 32768 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "dola-seed-2.0-code": {
          "limit": { "context": 256000, "output": 32768 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "glm-5.2": {
          "limit": { "context": 1024000, "output": 131072 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "glm-5.3-flash": {
          "limit": { "context": 1024000, "output": 131072 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "deepseek-v4-flash": {
          "limit": { "context": 1024000, "output": 393216 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "deepseek-v4-pro": {
          "limit": { "context": 1024000, "output": 393216 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "deepseek-v4.1-flash": {
          "limit": { "context": 1024000, "output": 393216 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        }
      }
    }
    ...
  }
}
```

Example `~/.zcode/v2/config.json` when API format is **Responses**

```JSON
{
  "provider": {
    ...
    "ark-coding-plan": {
      "name": "Ark Coding Plan",
      "kind": "openai",
      "options": {
        "apiKey": "<ARK_API_KEY>",
        "baseURL": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
        "apiKeyRequired": true
      },
      "enabled": true,
      "source": "custom",
      "models": {
        "ark-code-latest": {
          "limit": { "context": 256000, "output": 32768 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "dola-seed-2.0-pro": {
          "limit": { "context": 256000, "output": 32768 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "dola-seed-2.0-lite": {
          "limit": { "context": 256000, "output": 32768 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "dola-seed-2.0-code": {
          "limit": { "context": 256000, "output": 32768 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "glm-5.2": {
          "limit": { "context": 1024000, "output": 131072 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "glm-5.3-flash": {
          "limit": { "context": 1024000, "output": 131072 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "deepseek-v4-flash": {
          "limit": { "context": 1024000, "output": 393216 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "deepseek-v4-pro": {
          "limit": { "context": 1024000, "output": 393216 },
          "modalities": { "input": ["text"], "output": ["text"] }
        },
        "deepseek-v4.1-flash": {
          "limit": { "context": 1024000, "output": 393216 },
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        }
      }
    }
    ...
  }
}
```

> **warning**

* > Replace `<ARK_API_KEY>` with your [ModelArk API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

* > The examples above list only the text generation models currently offered by Coding Plan. The actual supported Model Name list is authoritative in **Core configuration** above; delete any entries you don't need or that your subscribed plan does not support, to avoid `model not found` errors when calling.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/zcode3.png)

1. After saving, return to the ZCode main screen.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/zcode4.png)

# Use ZCode

1. In the model dropdown at the top of the ZCode main screen, select the model you just added.

2. In the input box, describe your task (for example, generating code, fixing bugs, refactoring, or writing unit tests) and press Enter to start a conversational development session. ZCode supports the full development lifecycle, including planning, coding, review, and release.

> For more usage tips and advanced features, see the [ZCode official documentation](https://zcode.net.cn/en/docs).
