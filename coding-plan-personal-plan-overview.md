# Subscription overview

The ModelArk Coding Plan is a subscription service for AI coding scenarios tailored for developers. Key benefits of this service are as follows:

* **Support for multiple coding models**: Mainline coding models include Dola Seed 2.0, GLM, and DeepSeek. The plan also supports embedding models such as Skylark\-Embedding\-Vision.

* **Compatible with mainstream coding tools**: Supports Claude Code, OpenCode, OpenClaw, TraeCode, Cline, Cursor, Codex, Roo Code, and Kilo Code; quota shared across tools.

* **Plans for different workloads**: Choose between Lite and Pro to match both everyday and high\-intensity coding scenarios.

* **Stable performance at peak**: TPM fits day\-to\-day development workloads.

> **Tip**

> See the [Quick start guide](coding-plan-personal-get-started.md) to enable and set up the service in a few steps.

# Scenarios and requirements

|Scenarios and quotas ||Description |
|---|---|---|
|**Applicable scenarios**: ||**Individual development** \- Helps developers complete coding tasks such as personal projects, learning and practice, and tool building.<br><br>> **tip**<br><br><br>> For enterprise\-grade needs, use ModelArk's model services via APIs.<br> |
|**Quota** |Rules |Only available in **AI coding tools**. Quotas are **shared within the plan**. |
||Limitation |**Not available for API calls.** <br><br>> **tip**<br><br><br>> Using the Coding Plan–provided **Base URL** and **API key** outside AI coding tools may be identified as abuse or a policy violation, which could result in subscription deactivation or account suspension.<br> |

# Special offers

[Time-limited referral campaign](coding-plan-personal-invitation-rewards.md): Invite friends to subscribe—your friends get an extra 10% off and you receive 10% voucher rewards with no upper limit.

# Subscription details

## Subscription options

|Plan |Scenario |Limits |
|---|---|---|
|Lite |Moderate\-intensity development tasks, suitable for most developers. |* Every 5 hours: Up to approximately 1,900 requests.<br><br>* Per week: Up to approximately 12,000 requests.<br><br>* Per subscription month: Up to approximately 24,000 requests. |
|Pro |Complex project development, suitable for developers with high\-intensity workloads. |> 5× that of the Lite plan.<br><br><br>* Every 5 hours: Up to approximately 9,500 requests.<br><br>* Per week: Up to approximately 60,000 requests.<br><br>* Per subscription month: Up to approximately 120,000 requests. |

> **Note**

> As with other models, the Embedding model consumes plan quota based on the number of model calls.

The estimated request count refers to the expected number of model invocations. In practice, a single user prompt often triggers multiple model invocations, and each invocation consumes quota as one request. As a result, the actual number of requests consumed is typically higher than the number of user prompts.

* Simple Q&A/code generation: 5–15 calls per prompt

* Refactor/complex task: 15–30+ calls per prompt

Actual usage depends on complexity, repository size, auto\-accept, and thinking. Check usage on the [Activation Management](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=model) page.

> **Note**

* > The Lite plan provides a usable total quota equivalent to 3× that of the Claude Pro plan, while the Pro plan offers 5× the quota of the Lite plan.

* > Each user operates independently on the platform, ensuring no noticeable slowdowns during peak usage. The available TPM is sufficient for normal development needs.

   > The Pro plan offers higher TPM limits, while the Lite plan still fully supports individual developer workflows.

## Plan validity period

Plan validity is calculated in calendar months, starting from the date of successful purchase. For example, a 1\-month plan is valid as follows:

|Purchase Date |Plan Period |End Date |
|---|---|---|
|01.04 |1 Month |02.04 23:59 |
|01.31 |1 Month |02.28 23:59 |
|02.01 |1 Month |03.01 23:59 |

## Quota refresh rules

After the quota is exhausted within the time cycle, you need to wait for the next cycle for the quota to automatically recover. Exceeding the quota will not consume other packages or your account balance.

* 5\-hour limit: Quotas are refreshed every 5 hours, calculated from the time of the first request.

* Weekly limit: The weekly quota resets every Monday at 00:00:00.

* Monthly limit: The monthly quota resets at 00:00:00 on the first day of each subscription month.

## Notes

* The same subscription can be used across all supported tools, and quota is shared.

* The quota is only applicable to supported programming tools and cannot be used for API calls. Using the Base URL and API Key associated with ModelArk Coding Plan entitlements in non\-AI programming products may be considered abuse or a violation, which can result in subscription deactivation or account suspension.

# Integration

> **Note**

> Be sure to use the supported models and Base URL: https://ark.ap\-southeast.bytepluses.com/api/coding/v3 (OpenAI compatible) or https://ark.ap\-southeast.bytepluses.com/api/coding (Anthropic compatible).

* > Other models cannot be used in the Coding Plan.

* > If the specified Base URL is not in use, you will not be able to use the Coding Plan quota and may incur additional API request charges.

## Supported models

Mainstream coding models are supported. The models available in each region are subject to what's shown in the console.

You can configure the model in either of the following ways (see [Key configuration](coding-plan-personal-get-started.md#key-configuration) for details):

* Set **Model Name** to switch models in real time.

* Set it to `ark-code-latest` and switch the underlying model in the console.

> **Tip**

> You can view the model's default thinking behavior in the console. In actual use, you can override this behavior by specifying the relevant parameters.

> **Note**

> `glm-5.2`, `glm-5.3-flash`, `deepseek-v4.1-flash`, `deepseek-v4-flash`, and `deepseek-v4-pro` support 1M context windows for long sessions that involve large codebases. To enable a 1M context window in tools, see [Claude Code](coding-plan-personal-ai-claude-code.md#92b07719), [OpenCode](coding-plan-personal-ai-opencode.md#31ccb411), and [OpenClaw](coding-plan-personal-ai-openclaw.md#48c0b1ea).

|Model |Description |
|---|---|
|Auto |Default option. ModelArk will select the optimal model for you based on performance and speed, prioritizing your access to the latest models. |
|Dola\-Seed\-2.0\-Pro |Focused on long\-chain reasoning and stability in complex task execution, designed for complex real\-world business scenarios. |
|Dola\-Seed\-2.0\-Lite |Balances generation quality and response speed, making it a strong general\-purpose production model. |
|Dola\-Seed\-2.0\-Code |Enhanced coding capabilities leveraging Seed 2.0 Agent and visual understanding capabilities, with outstanding front\-end development performance and multi\-language support. (Multimodal visual understanding) |
|GLM\-5.2 |Zhipu's flagship model. It supports a 1M context window, performs strongly on long\-running tasks, and ranks highly on multiple authoritative benchmarks.<br><br>> **Tip**<br><br><br>> GLM\-5.2 length limits are as follows:<br><br><br><br>* > Context window: 1024k<br><br><br>* > Maximum output length: 128k<br> |
|GLM\-5.3\-Flash |Zhipu's first native multimodal model uses a cost\-efficient architecture to deliver stronger intelligence than GLM\-5.2.<br><br>> **Note**<br><br><br>> `glm-5.3-flash` is a native multimodal model and supports image input.<br><br><br>> **Tip**<br><br><br>> GLM\-5.3\-Flash length limits are as follows:<br><br><br><br>* > Context window: 1024k<br><br><br>* > Maximum output length: 128k<br> |
|DeepSeek\-V4\-Flash |DeepSeek\-V4\-Flash provides faster and more cost\-effective API services. Deep reasoning is enabled by default and can be turned off manually.<br><br>> **Note**<br><br><br>> The DeepSeek models are provided in compliance with applicable local laws and regulations.<br><br><br>> **tip**<br><br><br>> DeepSeek\-V4\-Flash length limits are as follows:<br><br><br><br>* > Context window: 1024k<br><br><br>* > Maximum output length: 384k<br> |
|DeepSeek\-V4\-Pro |DeepSeek\-V4\-Pro offers significantly enhanced agent capabilities and extensive world knowledge. Deep reasoning is enabled by default and can be turned off manually.<br><br>> **warning**<br><br><br><br>* > The official DeepSeek\-V4\-Pro model is now available, with a comprehensive leap in Agent capabilities, and can be selected by model name or in the console.<br><br><br>* > DeepSeek\-V4\-Pro has a relatively high quota deduction coefficient and consumes quota quickly. It is recommended for difficult and complex problems; for everyday use, switching to another model is recommended.<br><br><br>* > The DeepSeek models are provided in compliance with applicable local laws and regulations.<br><br><br><br>> **tip**<br><br><br>> DeepSeek\-V4\-Pro length limits are as follows:<br><br><br><br>* > Context window: 1024k<br><br><br>* > Maximum output length: 384k<br> |
|DeepSeek\-V4.1\-Flash |DeepSeek\-V4.1\-Flash is a lightweight flagship model in DeepSeek's new architecture series. With 552B total MoE parameters, it delivers intelligence beyond its class and natively supports multimodal visual understanding. Deep reasoning (thinking) is enabled by default and can be disabled manually.<br><br>> **Note**<br><br><br>> The DeepSeek models are provided in compliance with applicable local laws and regulations.<br><br><br>> **tip**<br><br><br>> DeepSeek\-V4.1\-Flash length limits are as follows:<br><br><br><br>* > Context window: 1024k<br><br><br>* > Maximum output length: 384k<br> |

## Supported coding tools

If you encounter an error while using the Coding Plan, check the corresponding error scenario to help diagnose the issue. For details, see [Error codes](error-codes.md).

[Claude Code](coding-plan-personal-ai-claude-code.md)

AI terminal coding assistant for natural\-language programming

[TraeCode](coding-plan-personal-ai-trae.md)

Intelligent IDE for next\-generation development workflows

[Roo Code](coding-plan-personal-ai-other-tools.md)

Lightweight coding assistant

[Codex](coding-plan-personal-ai-codex.md)

OpenAI's coding tool (CLI and ChatGPT desktop app)

[Hermes Agent](coding-plan-personal-ai-hermes-agent.md)

Open\-source, self\-hosted AI agent

[OpenCode](coding-plan-personal-ai-opencode.md)

Open\-source AI coding agent tool

[Cline](coding-plan-personal-ai-other-tools.md)

VS Code extension for code completion and debugging

[OpenClaw](coding-plan-personal-ai-openclaw.md)

Open\-source, self\-hosted personal AI assistant

[Cursor](coding-plan-personal-ai-other-tools.md)

AI\-native code editor

[Kilo Code](coding-plan-personal-ai-other-tools.md)

Lightweight, high\-performance coding tool

# FAQ

See [FAQs](coding-plan-personal-faq.md).
