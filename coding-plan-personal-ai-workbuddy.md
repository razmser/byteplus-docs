# WorkBuddy

WorkBuddy is a desktop AI agent client from Tencent Cloud, designed for the QQ and WeCom ecosystems. This document describes how to configure ModelArk Coding Plan in WorkBuddy.

# Prerequisite steps

1. Go to the [ModelArk Coding Plan event](https://www.byteplus.com/en/activity/codingplan) and subscribe to a plan before configuring your coding tool.

2. Download and install WorkBuddy from the [official website](https://www.workbuddy.ai/) for your operating system. After installation, sign in as prompted.

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

# Configure WorkBuddy

1. Start WorkBuddy, click your account avatar in the bottom\-left corner, and select **Settings**.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/workbuddy1.png)

2. In the left navigation, select **Models**, and in the custom models area click **Add model**.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/workbuddy2.png)

3. Under Provider, select **Custom**, fill in the following parameters, and save:

   * **API endpoint**: `https://ark.ap-southeast.bytepluses.com/api/coding/v3`

   * **API key**: Use the **API key** from the [Coding Plan page](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/coding-plan).

   * **Model name**: Enter a supported Model Name from your subscribed plan. See model information in [Supported models](coding-plan-personal-plan-overview.md#9f209b6d). A text generation model is required.

   * **Advanced tools**: Optional. Enable **Tool call**, **Image input**, or **Thinking mode** as needed.

   * **Custom protocol**: When the model service uses a non\-standard URL path (for example, wrapped behind a gateway or proxy layer), you can enable the custom protocol toggle in advanced configuration. When enabled, Tencent WorkBuddy sends requests directly to the URL you provide, skipping path validation and auto\-completion. When disabled (default): uses the standard `/chat/completions` path with auto\-validation and auto\-completion of the API endpoint. When enabled: uses the API endpoint you provide directly, skipping path validation and auto\-completion.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/workbuddy3.png)

In addition to adding a custom model via the client UI, you can also directly edit the local configuration file `~/.workbuddy/models.json` to connect to Coding Plan. After saving, WorkBuddy automatically watches for and hot\-reloads the new configuration (**no restart required**). The `name` field is what appears in the model dropdown on the main screen.

`~/.workbuddy/models.json` example

```JSON
[
  {
    "id": "ark-code-latest",
    "name": "ark-code-latest (Auto)",
    "vendor": "Custom",
    "url": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
    "apiKey": "<YOUR_API_KEY>",
    "supportsToolCall": true,
    "supportsImages": false,
    "supportsReasoning": false,
    "useCustomProtocol": false
  }
]
```

4. After saving, return to the WorkBuddy main screen.

# Use WorkBuddy

1. In the model dropdown at the top of the main screen, select the model you just added.

2. In the input box, describe your task in one sentence (for example, generating a weekly report, data analysis, organizing files, code generation / refactoring / bug fixing) and press Enter. WorkBuddy plans and executes autonomously, producing documents, spreadsheets, code files, and other outputs. You can view the generated content and file changes in the results panel on the right.

> For more usage tips and scenarios, see the [WorkBuddy official documentation](https://www.workbuddy.ai/docs/en/workbuddy/Quickstart).
