# DeepSeek Harness

DeepSeek Harness is an application framework developed by DeepSeek AI for building, running, and extending agents, using an **everything\-is\-a\-plugin** architecture. This document describes how to configure ModelArk Coding Plan Personal Edition in DeepSeek Harness.

> **warning**

> DeepSeek Harness is currently in **developer preview** and iterates rapidly. Compatibility\-breaking changes may occur.

> **tip**

> Go to the [ModelArk Coding Plan event](https://www.byteplus.com/en/activity/codingplan) and subscribe to a plan before configuring your coding tool.

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

# Configure DeepSeek Harness

Prerequisites: You have installed Node.js (LTS version recommended).

1. Start DeepSeek Harness.

   1. In your terminal, run the following command to start the DeepSeek Harness Web UI.

      ```bash
      npx @deepseek-ai/dsh web
      ```

   2. Open the URL printed in the command output in your browser. The default is `http://127.0.0.1:3080/`.

2. In the DeepSeek Harness Web UI, click **Settings** in the bottom\-left corner.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/harness1.png)

3. In the left navigation, click **Models**, and configure according to the on\-screen information.

* Option 1: Configure the DeepSeek card

On the DeepSeek card, select **Custom settings**, fill in the following information, and save.

* **API key**: Enter the Coding Plan [API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

* **API endpoint**: `https://ark.ap-southeast.bytepluses.com/api/coding/v3`

* **Add model**: For available models, see [Model configuration](coding-plan-personal-ai-deepseek-harness.md#8f1d9f20).

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/harness2.png)

* Option 2: Add a custom provider

Click **Add custom provider** and fill in the following information to create a provider.

* **Provider ID**: Custom. If you need to rename it later, add a new provider and delete the old one.

* **Display name**: Custom.

* **API endpoint**, **API protocol**: The supported API endpoints, API protocols, and their correspondence are as follows. Do not misconfigure.

|API protocol |API endpoint |
|---|---|
|openai\-completions |`https://ark.ap-southeast.bytepluses.com/api/coding/v3` |
|openai\-responses |`https://ark.ap-southeast.bytepluses.com/api/coding/v3` |
|anthropic\-messages |`https://ark.ap-southeast.bytepluses.com/api/coding` |

* **API key**: Enter the Coding Plan [API key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

* **Add model**: For available models, see [Model configuration](coding-plan-personal-ai-deepseek-harness.md#8f1d9f20).

4. (Optional) To enable image input for a model, configure it in the configuration file.

   1. On the model configuration page, click **Open configuration file**, path `$DSH_HOME/settings.yaml`.

   2. Add `input: [text, image]` in the supported model configuration section. Configuration example:

      ```Yaml
      llm-pi-ai:
        providers:
          ark-plan:
            displayName: ark-plan
            apiKeyEnv: CODING_PLAN_API_KEY
            api: openai-responses
            baseURL: https://ark.ap-southeast.bytepluses.com/api/coding/v3
            models:
              - id: ark-code-latest
                name: ark-code-latest
                input: [text, image]
      ```

# Use DeepSeek Harness

1. On the page, click **Select workspace** and add the project directory in which you started `dsh`. The conversation input box is only available after a workspace is selected.

2. In the bottom\-right corner of the input box, select the model you want to use.

3. In the input box, enter your request to start using DeepSeek Harness.

   The agent can read and edit workspace files, run commands, delegate work, and maintain plans. When an action requires approval under the current permission policy, the Web UI asks you first.
