# Models API

`GET https://ark.ap-southeast.bytepluses.com/api/v3/models`

Queries the base model versions visible to the current account, including their capabilities, modalities, token limits, reasoning effort, and supported APIs. Returns all visible model versions, sorted by creation time in descending order.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Response parameters

**data** `object[]`  |  Model version list

List of model versions visible to the current account. Returns an empty array when there are no visible model versions.

**created** `integer`  |  Creation time

`data.created`

Creation time of the model version, as a Unix timestamp in seconds.

**domain** `string`  |  Model domain

`data.domain`

Domain that the model belongs to, including `LLM`, `VLM`, `ImageGeneration`, `VideoGeneration`, `3DGeneration`, `Audio`, and `Embedding`.

Returns an empty string when the model does not support domain information.

**features** `object`  |  Model capabilities

`data.features`

Capabilities supported by the model.

**batch** `object`  |  Batch inference capability

`data.features.batch`

Batch inference capability.

**batch_chat** `boolean`  |  Batch Chat

`data.features.batch.batch_chat`

Whether batch chat is supported. Returns `false` when the model does not support batch chat.

**batch_job** `boolean`  |  Batch Job

`data.features.batch.batch_job`

Whether batch jobs are supported. Returns `false` when the model does not support batch jobs.

**cache** `object`  |  Cache capability

`data.features.cache`

Cache capability.

**prefix_cache** `boolean`  |  Prefix cache

`data.features.cache.prefix_cache`

Whether prefix cache is supported. Returns `false` when the model does not support prefix cache.

**session_cache** `boolean`  |  Session cache

`data.features.cache.session_cache`

Whether session cache is supported. Returns `false` when the model does not support session cache.

**structured_outputs** `object`  |  Structured output capability

`data.features.structured_outputs`

Structured output capability.

**json_object** `boolean`  |  JSON object output

`data.features.structured_outputs.json_object`

Whether JSON object output is supported. Returns `false` when the model does not support JSON object output.

**json_schema** `boolean`  |  JSON Schema output

`data.features.structured_outputs.json_schema`

Whether JSON Schema output is supported. Returns `false` when the model does not support JSON Schema output.

**thinking** `object`  |  Thinking capability

`data.features.thinking`

Thinking capability.

**type** `string`  |  Thinking type

`data.features.thinking.type`

Whether deep reasoning is supported.`enabled` indicates support, and `disabled` indicates no support.

**tools** `object`  |  Tool capabilities

`data.features.tools`

Tool use capabilities.

**function_calling** `boolean`  |  Function calling

`data.features.tools.function_calling`

Whether function calls are supported. Returns `false` when the model does not support function calls.

**web_search** `boolean`  |  Web search

`data.features.tools.web_search`

Whether web search is supported. Returns `false` when the model does not support web search.

**id** `string`  |  Model version ID

`data.id`

**name** `string`  |  Base model name

`data.name`

Base model name.

**object** `string`  |  Object type

`data.object`

Object type, fixed as `model`.

**version** `string`  |  Version

`data.version`

Version.

**api_supported** `object`  |  Supported APIs

`data.api_supported`

APIs supported by the model and their enabled status.

**context_window** `integer`  |  Context window

`data.context_window`

Maximum context length, in tokens.

**Min**: `0`

**effort** `object`  |  Reasoning effort

`data.effort`

Reasoning effort levels supported by the model and the default level.

**supported_levels** `string[]`  |  Supported reasoning effort levels

`data.effort.supported_levels`

Supported reasoning effort levels.

**default_level** `string`  |  Default reasoning effort level

`data.effort.default_level`

Default reasoning effort level used by the model.

**max_output_tokens** `integer`  |  Maximum number of output tokens

`data.max_output_tokens`

Maximum output length, in tokens.

**Min**: `0`

**modalities** `object`  |  Modality

`data.modalities`

Input and output modalities supported by the model.

**input_modalities** `string[]`  |  Input modalities

`data.modalities.input_modalities`

Input modalities supported by the model, including `text`, `image`, `video`, and `audio`.

**output_modalities** `string[]`  |  Output modalities

`data.modalities.output_modalities`

Output modalities supported by the model, including `text`, `image`, `video`, `audio`, `3D`, and `embedding`.

**status** `string`  |  Lifecycle status

`data.status`

Lifecycle status of the model version.`Published` indicates that the model is online and can be called normally; `Retiring` indicates that the model is scheduled for retirement, can still be called currently, and will stop serving later.

**task_type** `string[]`  |  Task type

`data.task_type`

Task type supported by the model.

**token_limits** `object`  |  Token limit

`data.token_limits`

Model token limits.

**context_window** `integer`  |  Context window

`data.token_limits.context_window`

Maximum context length, in tokens.

**Min**: `0`

**max_input_token_length** `integer`  |  Maximum number of input tokens

`data.token_limits.max_input_token_length`

Maximum input length, in tokens.

**Min**: `0`

**max_output_token_length** `integer`  |  Maximum number of output tokens

`data.token_limits.max_output_token_length`

Maximum output length, in tokens.

**Min**: `0`

**max_reasoning_token_length** `integer`  |  Maximum number of reasoning tokens

`data.token_limits.max_reasoning_token_length`

Maximum reasoning length, in tokens.

**Min**: `0`

**object** `string`  |  Object type

Response object type, fixed as `list`.
