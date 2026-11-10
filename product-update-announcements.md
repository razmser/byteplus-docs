# Product updates

This document describes the key feature updates of the ModelArk platform.

> For more information, see [Model releases - 2025](model-release-announcement-2025.md).

> For previously released features, see [Previous releases.](product-update-announcement-2025.md)

# Release note \- 202610

|Module |Description |
|---|---|
|Tools |The [DeepSeek Harness plugin](coding-plan-personal-ai-deepseek-harness.md#configure-deepseek-harness) supports connecting Agent Plan, Coding Plan, or postpaid models through the official ModelArk plugin. |
|API |The validity period of [Draft](video-generation-tutorial.md#draft-mode) video task IDs is extended to 14 days. After expiration, the Draft video cannot be used to generate a final video. |
|Playground |Added experience with the Kimi K3.1 Pro, DeepSeek\-V4.1\-Flash, and DeepSeek\-V4.1\-Flash\-Economy models in the [Experience Center](https://ai.byteplus.com/ark/region:ap-southeast-1/experience). |
|Model Finetune |The `lora_alpha` hyperparameter is no longer available for LoRA training tasks. Use `lora_scaling=1.0` instead. See [Model fine-tuning overview](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/fine-tuning-overview). |
|Usage |* Supports querying usage by request and asynchronous task. See [Query single request usage](get-inference-request-usage-api.md) and [Query asynchronous task usage](get-inference-task-usage-api.md).<br><br>* Call data can be split by model, inference endpoint, and API Key, with further usage detail available. See [IAM API Key best practices](iam-api-key-best-practices.md). |
|Managed Agents |* Supports [Webhook notifications](receive-webhook-events.md), which can be subscribed to as needed for key events such as session lifecycle, Agent, and resource changes.<br><br>* Supports [asynchronous tool execution](background-tool-tasks.md).<br><br>* When a task has ended, supports [archiving the Session](manage-session.md) to release runtime resources while retaining the configuration and history for querying.<br><br>* The Session API supports returning [usage information](manage-session.md).<br><br>* Supports configuring [automatic context compaction](context-compaction.md).<br><br>* Adds `session.usage` usage statistics. See [Manage Session](manage-session.md). |

# Release note \- 202609

|Module |Description |
|---|---|
|Console |The homepage supports displaying the [account balance](https://ai.byteplus.com/ark/region:ap-southeast-1/overview). |
|Model Inference |* Added [Online inference (Flex)](online-inference-flex.md), which lets you use low\-priority resources for inference by specifying `service_tier=flex` in your request. Flex has an independent TPM quota and a more favorable price, making it suitable for latency\-insensitive scenarios. Requests in Flex mode are queued and retried on the server side. You can use `X-Ark-Max-Wait-Timeout-Ms` to specify the server\-side timeout, up to a maximum of 20 minutes.<br><br>* Added model\-level and endpoint\-level [usage limits](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement). You can configure usage controls by model or endpoint. When the limit is reached, the platform automatically suspends the corresponding model service or endpoint service. |
|Managed Agents<br><br>Model finetune |* Added support for project\-level permission management across all Managed Agents modules.<br><br>* The Session detail page adds a file resource system that supports previewing and uploading file resources, making it easy to view resource status, mount paths, and other related information.<br><br>   Model fine\-tuning [performance metrics](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/fine-tuning-overview) now support multi\-task comparison, allowing you to compare training metric curves across tasks in one view. |
|Playground |* Responses API supports parsing external link readers, with no cloud service activation required. The platform provides online browsing and non\-PDF parsing for a seamless experience.<br><br>* [Dreamina-Seedance-2.5](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/gen_video?model=dreamina-seedance-2-5-260628) supports sample generation mode. Try it in the Experience Center. |
|Gallery |[Gallery](https://ai.byteplus.com/ark/region:ap-southeast-1/gallery) is now available, allowing you to browse high\-quality Seedance film and TV works, as well as information on select open\-source productions. |
|API |[API compatibility enhancements](api-schema-upgrade-notice.md). |

# Release note \- 202608

|Module |Description |
|---|---|
|Playground |The [Voice Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/voice?model=dola-seed-audio-1-0) now supports experience with the `dola-seed-audio-1-0` model. |
|Managed Agents |* Added support for [custom skill](https://console.byteplus.com/ark/region:ap-southeast-1/managed-agents/skills) downloads.<br><br>* [Credentials Optimization](https://console.byteplus.com/ark/region:ap-southeast-1/managed-agents/credentials-vault): supports custom header authentication and re\-editing of custom MCP configurations.<br><br>* Environments [introduces reusable TOS artifact storage configurations](create-environment-api.md), allowing Agent artifacts to be uniformly saved to specified TOS directories by default according to the environment. Session\-level override is also supported, maintaining flexibility for special scenarios.<br><br>* Added a user message queueing mechanism, allowing users to continue sending messages while an Agent is busy. Queued messages are consumed automatically between runs to improve conversation continuity and correction efficiency.<br><br>* Added a [Quickstart onboarding flow](https://console.byteplus.com/ark/region:ap-southeast-1/managed-agents/quickstart), enabling new users to initialize Agents, configure environments, run trials, and integrate APIs in 3 minutes using templates or natural language.<br><br>* Users can more conveniently [view, configure, and manage skills](https://console.byteplus.com/ark/region:ap-southeast-1/managed-agents/skills) in Managed Agents, and directly select and orchestrate available skills when building Agents.<br><br>* Users can configure model thinking mode and reasoning intensity parameters as needed in [Managed Agents](https://console.byteplus.com/ark/region:ap-southeast-1/managed-agents/agents/create), allowing more flexible control over model behavior during Agent setup and runtime. |
|Activation |Added support for [deactivating the Managed Agents service](https://console.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=manageAgent). |
|API |* Added support for downloading Managed Agents [session artifacts](search-files-api.md) via the Files API.<br><br>* [Error Code](error-codes.md) optimization for Managed Agents: errors are now distinguished between the HTTP phase and the SSE phase based on the delivery channel.<br><br>* The [Session Query](get-session-api.md) API endpoint now returns the configuration snapshot of each sub\-agent invoked within a session.<br><br>* The Chat and Responses APIs support the [reasoning_effort parameter with compatibility handling](deep-thinking.md#fc5eac89). Values outside the supported range of the current model or API are processed according to compatibility logic. |

# Release note \- 202607

|Module |Description |
|---|---|
|Managed Agents |* Added [Managed Agents Product Demonstration Cookbook](managed-agent-custom-tool-tutorial.md).<br><br>* Supports custom scripts configuration for [Environment](configure-cloud-environment.md).<br><br>* Added streaming output capability for Agent thinking and message.<br><br>* Supports overriding Agent and Environment configurations during session creation.<br><br>* Supports injecting credentials via environment variables (ENV), covering managed credentials and credential\-switching scenarios.<br><br>* Added custom tool capability for end\-user [Custom Tool](managed-agent-custom-tool-tutorial.md).<br><br>* Supports custom [skills](skills.md). |
|API |* Released model activation APIs, including [Batch activation of foundation models](activate-models-api.md), [Enable automatic activation of new models](enable-auto-model-activation-api.md), [Turn off automatic activation of new models](disable-auto-model-activation-api.md), [Query model activation details](get-model-activation-api.md), and [List activated models](list-model-activations-api.md).<br><br>* Released `GetArkOfficialResult` \- [Obtain ModelArk artifact verification result](get-ark-official-result-api.md) API. |
|Model Generations Quality Diagnostics |Launched [Model Generations Quality Diagnostics](model-diagnostic-service-acknowledgement.md). Once users enable this feature, the platform will retain input and output data to track and diagnose issues related to generation quality. Task data is retained for 3 days by default, and data retention will cease immediately after the feature is disabled. |
|Copyright Commercialization |Enabled copyright library capability. |

# Release note \- 202606

|Module |Description |
|---|---|
|API |Added support for 4K resolution in [Dreamina-Seedance-2.0](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=dreamina-seedance-2-0&_vtm_=a78999.b69280.0_0.0_0.auth_login_signup-42.41_7652373917182445061). |

# Release note \- 202605

|Module |Description |
|---|---|
|Model Playground |* Added [audio understanding](audio-understanding.md) support for the Dola\-Seed\-2.0\-lite and Dola\-Seed\-2.0\-mini models.<br><br>* [Dola-Seedream-5.0](image-generation-tutorial.md) supports output 4k images now. |
|Coding Plan |* Supports the DeepSeek\-V4 model family.<br><br>* Supports the [Responses API](create-model-responses-api.md).<br><br>* Supports [integration with the latest Codex CLI](coding-plan-personal-ai-codex.md). |
|API |* The [Video generation API](create-video-generation-task-api.md) now includes a `priority` field, which lets you set request\-level task priority within the same inference endpoint.<br><br>* [Seedance 2.0 series models](create-video-generation-task-api.md) now support `heic` and `heif` input image formats. |

# Release note \- 202604

|Module |Description |Document/link |
|---|---|---|
|Model Playground |Dreamina\-Seedance\-2.0 now supports 1080p output. |[Model Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience/chat?csid=excs-202605081959-%5BnhuY1R_p4nHfgw2FO4L3U%5D&modelId=seed-2-0-code-preview-260328) |
|Model Inference |* The custom endpoint details page now shows the default value of the thinking parameter and supports editing it. If either thinking.type or reasoning_effort is explicitly provided in an API request, the API request value takes precedence.<br><br>* The default thinking mode configuration is preserved when switching model versions.<br><br>* Ark now trusts face\-containing outputs generated by the following models. You can use face\-containing source outputs generated by these models under your account within the last 30 days as input materials for further creation with the Seedance 2.0 series models. |[Online Inference](manage-inference-endpoints.md#86e82076)<br><br>[Batch Inference](batch-inference-via-console.md#c34d231e) |
|Token calculator |* Dreamina\-Seedance\-2.0 and Dreamina\-Seedance\-2.0\-fast are now supported. |[Token calculator](https://ai.byteplus.com/ark/region:ap-southeast-1/tokenCalculator) |
|API |* The input image size limit for the Image Generation API has been increased from 10MB to 30MB. |[Image generation API](image-generation-api.md) |
|API Key Management |* Support billing at the API Key level. |[Monitoring](1159199.md) |

# Release note \- 202603

|Module |Description |Document/link |
|---|---|---|
|Starter apps |Support long Video Understanding, with models including Dola\-Seed\-2.0\-lite and Dola\-Seed\-2.0\-mini. |[Starter apps](https://ai.byteplus.com/ark/region:ap-southeast-1/application) |
|API |* Responses API Prefill mode now supports context caching<br><br>* The thinking summary is enabled by default for the Seed pro and lite models of version 260328 and later. When it is enabled, the summary of the model's thinking content (choices.message.reasoning_content) and the encrypted thinking content (choices.message.encrypted_content) will be returned instead of the thinking content. |[Prefill-based response](responses-api-text-generation.md#a1384090)<br><br>[Output-thinking-summary](deep-thinking.md#8cfd447b) |

# Release note \- 202602

|Module |Description |Document/link |
|---|---|---|
|Playground |Playground supports trial access to ByteDance\-Seedream\-5.0\-lite/260128 |[Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience) |
|Model Inference |* SNS integration is available, supporting user subscription to model unit notifications and batch inference alerts.<br><br>* Video generation models support monitoring alert configuration via VMP. |[Online Inference](manage-inference-endpoints.md#86e82076)<br><br>[Batch Inference](batch-inference-via-console.md#75759432) |
|API |* Seed\-2.0\-Mini model error code updates: when requests are sent to the ModelArk platform excessively fast, the error code will be updated from **ServerOverloaded** to **RequestBurstTooFast**. The new models up from 2.0 will follow this rule. Please note that in scenarios such as the platform having reached an excessively high load percentage or accessing an endpoint that has not been used for a long time, the error code will still return **ServerOverloaded**. For models before Seed\-2.0, the error code will remain **ServerOverloaded** as before.<br><br>* Seed\-2.0 upgraded image/video understanding capabilities<br><br>* For security moderation scenarios, new error codes have been added to vision models.<br><br>* ByteDance\-Seedream\-5.0\-lite/260128 API has been fully released. |[API](api-key.md) |

# Release note \- 202601

|Module |Description |Document/link |
|---|---|---|
|API |Bytedance\-Seedance\-1.5\-pro model supports generating 1080p videos.<br><br><br>* Bytedance\-Seedance\-1.5\-pro model supports the Draft Sample Mode. |[Video Generation API](create-video-generation-task-api.md) |
|Coding Plan |* Coding Plan has been upgraded to support auto mode and multiple models. |[Coding Plan](coding-plan-personal-plan-overview.md) |
|Playground |* Download supported for storybooks generated by Seedream 4.0. |[Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience) |
|Usage |* Usage viewing period is extended to 6 months |[Usage](https://ai.byteplus.com/ark/region:ap-southeast-1/usageTrackin) |
|Foundation models |* Support model comparison and card view on the foundation model page |[Foundation models](https://ai.byteplus.com/ark/region:ap-southeast-1/model) |
