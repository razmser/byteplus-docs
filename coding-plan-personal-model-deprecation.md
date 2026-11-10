# Model deprecation announcement

As the ModelArk Coding Plan continues to evolve, newer model versions significantly outperform older versions in output quality and inference latency. Therefore, we will gradually deprecate older models.

When a model has a confirmed deprecation plan, we will notify you through SMS, in\-product messages, and other channels. Please stay tuned.

Model availability is subject to what is shown in the console. For the full list, see [Supported models](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1925114#9f209b6d). To switch models in the tool, see [Switch models](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/2578687#steps-to-switch-models).

<span id="deprecated-models"></span>
# Deprecated models


<span aceTableMode="list" aceTableWidth="3,1"></span>
|Model |Deprecation date |
|---|---|
|GLM\-5.1 |09.23 |
|Kimi\-K2.5 |09.23 |
|GPT\-OSS\-120B |09.23 |
|ByteDance\-Seed\-Code |09.23 |


<span id="steps-to-switch-models"></span>
# Steps to switch models

The following methods apply to text generation models.

<span id="use-the-latest-alias"></span>
## Method 1: Use the latest alias (recommended)

When configuring the model name, we recommend using the latest alias to avoid the impact of old model deprecation:


* ark\-code\-latest: You can select or switch the target model in the console without modifying the tool configuration file.

* glm\-latest, minimax\-latest, kimi\-latest, deepseek\-latest: These aliases use the latest supported model version by default, so you can start using the latest version immediately.



<span aceTableMode="list" aceTableWidth="3,1"></span>
|Latest model |Mapped model |
|---|---|
|ark\-code\-latest |Auto\-selected by console configuration (recommended for BP endpoint) |


<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">warning</div>


<div data-tips="true" data-tips-type="warning">On the BytePlus endpoint, only <code>ark-code-latest</code> is routed as a latest alias. Other <code>*-latest</code> variants (<code>glm-latest</code>, <code>minimax-latest</code>, <code>kimi-latest</code>, <code>deepseek-latest</code>) are not accepted at the coding\-plan endpoint.</div>


<span id="update-the-configuration-file"></span>
## Method 2: Update the configuration file

If your tool is configured with a specific Model Name, update the model information in the configuration file to a currently supported model name before the old model is deprecated. For configuration entry points in different tools, see the following documents:


* Claude Code: [Configure Claude Code](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1928262#92b07719)

* OpenClaw: [Configure OpenClaw](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/2183190#48c0b1ea)

* Codex: [Configure Codex](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/2556056#6d2d0676)

* OpenCode: [Configure OpenCode](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/2188958#a17f2e1a)

* TraeCode: [Configure TraeCode](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/2205646#e0525cf3)

* Hermes Agent: [Configure Hermes Agent](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/2318283#14207659)

* Other tools: [Other tools](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/2188959)
