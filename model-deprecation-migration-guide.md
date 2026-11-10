# Retiring model migration guide

As ModelArk models are upgraded, older versions are gradually retired. After a model reaches end of service (EOS), inference endpoints that have not been migrated are automatically replaced with the recommended model or shut down. For the retirement schedule and model lists, see [Model deprecations](model-deprecation-notice.md). Retiring models and their recommended replacements can support different API parameters. Some models do not support parameters such as `thinking`, `reasoning_effort`, `detail`, or thinking summary. During an automatic migration, only the `thinking` setting is applied automatically. You must update `reasoning_effort`, `detail`, and other parameters in your request code after migration.

This guide compares API parameter behavior before and after migration for the models in the retirement notice. Review and update your requests before migration to avoid request errors or unexpected behavior.

> **Note**

> Model capabilities, API parameters, pricing, and real\-world performance vary by model and version. Before migrating, review the model documentation and evaluate the replacement model against your workloads.

> During an automatic migration, only the `thinking` setting is applied automatically. You must update `reasoning_effort`, `detail`, thinking summary, and other parameters in your request code after migration. Otherwise, requests may fail.

## Control deep reasoning with `thinking`

The `thinking` field controls whether deep reasoning is enabled. Valid values are `enabled` (default) and `disabled`. Some models also support `auto`. For details, see [Deep reasoning](deep-thinking.md).

|Retiring model ID |`thinking` support |Migration consideration |
|---|---|---|
|`kimi-k2-5-260127` |Not supported |`thinking` is set to `disabled`. |
|`glm-4-7-251222` |`enabled` (default) / `disabled` |Keep the original `thinking` value. |
|`glm-5-1-260408` |||
|`deepseek-v3-2-251201` |||
|`seed-1-8-251228` |`enabled` (default) / `disabled` / `auto` |Keep the original `thinking` value. |
|`seed-1-6-250915` |||
|`seed-1-6-250615` |||
|`seed-1-6-flash-250615` |||
|`seed-1-6-flash-250715` |||
|`seed-1-6-sc-preview` |||
|`seed-1-6-code-preview` |||
|`skylark-pro-sc-preview` |||
|`dola-seed-2-0-lite-260228` |||
|`dola-seed-2-0-mini-260215` |||

**Migration recommendations:**

* If the original model does not support `thinking`, the automatically migrated endpoint uses `disabled`. No request code changes are required to preserve the original behavior. To enable deep reasoning on the replacement model, explicitly pass `"thinking": {"type": "enabled"}`.

* If the original model supports `thinking`, an automatic migration preserves the original value. No request code changes are required. When switching models manually, keep the original value and confirm that the replacement model supports it.

* If the replacement model does not support `thinking`, such as `dreamina-seedance-2-0-mini-260615`, remove the parameter from the request.

## Adjust reasoning length with `reasoning_effort`

Use `reasoning_effort` with the Chat API or `reasoning.effort` with the Responses API to adjust chain\-of\-thought length. Valid values are `none`, `minimal`, `low`, `medium`, `high`, `xhigh`, and `max`. For details, see [Deep reasoning](deep-thinking.md).

|Retiring model ID |`reasoning_effort` support |Default |Compatibility mapping |`thinking` support |Migration consideration |
|---|---|---|---|---|---|
|`deepseek-v3-2-251201` |Not supported |\- |\- |`enabled` (default) / `disabled` |`reasoning_effort` is not applied automatically. |
|`gpt-oss-120b-250805` |Supported |`medium` |`minimal` turns off reasoning; `none` maps to `minimal`; `xhigh` and `max` map to `high`. |`enabled` (default) |`reasoning_effort` is not applied automatically. Set it manually after migration. |
|`kimi-k2-5-260127` |Not supported |\- |\- |Not supported |`thinking` is set to `disabled`. `reasoning_effort` is not applied automatically. |
|`glm-5-1-260408` |Not supported |\- |\- |`enabled` (default) / `disabled` |`reasoning_effort` is not applied automatically. |
|`glm-4-7-251222` ||||||
|`seed-1-6-code-preview` ||||||
|`seed-1-6-sc-preview` ||||||
|`skylark-pro-sc-preview` ||||||
|`seed-1-6-flash-250715` ||||||
|`seed-1-6-flash-250615` ||||||
|`seed-1-6-250615` ||||||
|`seed-1-6-250915` |Not supported |\- |\- |`enabled` (default) / `disabled` |`reasoning_effort` is not applied automatically. |
|`seed-1-8-251228` |Supported |`medium` |`minimal` turns off reasoning; `none` maps to `minimal`; `xhigh` and `max` map to `high`. |`enabled` (default) / `disabled` |`reasoning_effort` is not applied automatically. Set it manually after migration. |

**Migration recommendations:**

* During an automatic migration, `reasoning_effort` is not applied automatically. If the replacement model does not support this parameter, remove it from the request to avoid errors.

* If the replacement model supports `reasoning_effort`, set the value manually according to the replacement model's default value and compatibility mapping.

## Handle thinking summaries

When thinking summary is enabled, the model returns a summary in `reasoning_content` and encrypted original reasoning content in `encrypted_content` instead of returning the original reasoning content in plaintext. For details, see [Deep reasoning](deep-thinking.md).

> **Note**

> When thinking summary is enabled, return `encrypted_content` to the model as specified for agent scenarios such as multi\-turn tool calls. Omitting the encrypted content can reduce reasoning quality. For the correct request and passback pattern, see [Best practices of model calling for agents](agent-model-invocation-practice.md).

The retiring models do not support thinking summary. Support after migration depends on the recommended replacement:

|Retiring model ID |Thinking summary support |Recommended replacement |Support after migration |
|---|---|---|---|
|`seed-1-6-flash-250715` |Not supported |`dola-seed-2-0-mini-260428` |Supported |
|`seed-1-6-flash-250615` ||`dola-seed-2-0-mini-260428` ||
|`seed-1-6-250615` ||`dola-seed-2-0-lite-260428` ||
|`seed-1-6-250915` ||`dola-seed-2-0-lite-260428` ||
|`seed-1-8-251228` ||`dola-seed-2-0-lite-260428` ||
|`dola-seed-2-0-mini-260215` ||`dola-seed-2-0-mini-260428` ||
|`dola-seed-2-0-lite-260228` ||`dola-seed-2-0-lite-260428` ||
|`gpt-oss-120b-250805` ||`dola-seed-2-0-mini-260428` ||
|`kimi-k2-5-260127` ||`dola-seed-evolving` ||
|`glm-5-1-260408` ||`dola-seed-evolving` ||
|`glm-4-7-251222` ||`dola-seed-evolving` ||
|`seed-1-6-code-preview` ||`dola-seed-evolving` ||
|`seed-1-6-sc-preview` ||`dola-seed-sc-260628` |Not supported |
|`skylark-pro-sc-preview` ||`dola-seed-sc-260628` ||
|`deepseek-v3-2-251201` ||`deepseek-v4-flash-ga-260731` ||

**Migration recommendations:**

* Thinking summary is not applied automatically. After migrating to a model that supports thinking summary, enable and integrate it as described in the model documentation. The response then includes the summary in `reasoning_content` and the encrypted original reasoning content in `encrypted_content`. Increase the request `timeout` because inter\-chunk latency may increase.

* After migrating to a model that does not support thinking summary, the response does not include these summary\-related fields.

* To avoid receiving thinking summaries after migration, review the replacement model documentation for instructions on disabling them.

## Configure image understanding detail

The `detail` parameter controls how precisely a model understands an image. Pass it in the `image_url` object. For details, see [Image understanding](image-understanding.md). The `detail` parameter is not applied automatically during migration. Update your request code according to the following rules:

* If the original model does not support image understanding, set `detail` to `low` after migration.

* If the original model predates `seed-1-8` and supports image understanding, set `detail` to `low` when switching to a Dola Seed 2.0 model.

* If the original model is `seed-1-8` or later, preserve the original `detail` value after switching models.

**Migration recommendation:**  If your workload requires a specific image understanding precision, such as `high` or above, check the `detail` value in each `image_url` object after migration and explicitly set a value supported by the replacement model.

## Recommended model parameter support

|Model ID |`thinking` support |`reasoning_effort` support |Default |Compatibility mapping |Thinking summary support |
|---|---|---|---|---|---|
|`dola-seed-evolving` |`enabled` (default) / `disabled` |Supported |`high` |`minimal` turns off reasoning; `none` maps to `minimal`; `xhigh` and `max` map to `high`. |Supported |
|`deepseek-v4-flash-ga-260731` |`enabled` (default) / `disabled` |Supported |`high` |`none` and `minimal` turn off reasoning; `medium` maps to `low`; `xhigh` maps to `high`. |Not supported |
|`dola-seed-2-0-lite-260428` |`enabled` (default) / `disabled` |Supported |`medium` |`minimal` turns off reasoning; `none` maps to `minimal`; `xhigh` and `max` map to `high`. |Supported |
|`dola-seed-2-0-mini-260428` ||||||
|`dola-seed-sc-260628` |||||Not supported |

## Migration recommendations

1. Review [Model deprecations](model-deprecation-notice.md) to find the retirement batch, EOS date, and recommended replacement for your model.

2. Review the request parameters. During an automatic migration, only the `thinking` setting is applied automatically. Update the remaining parameters in your request code:

   * `thinking`: Remove it if the replacement model does not support it. Otherwise, confirm the value.

   * `reasoning_effort` or `reasoning.effort` for the Responses API: Remove it if the replacement model does not support it. Otherwise, confirm the value against the compatibility mapping.

   * `detail` in each `image_url` object: Confirm the value against the `detail` migration rules.

   * Thinking summary fields, including `reasoning_content` and `encrypted_content`: Update response parsing and timeout settings based on replacement model support.

3. Switch the inference endpoint to the replacement model before EOS and evaluate it against your workloads. For switching instructions, see [Model switching instructions](model-deprecation-notice.md#979a2e6e).

4. Plan for automatic migration. On the EOS date, the system automatically replaces or shuts down remaining inference endpoints. The replacement model may have different pricing, so review the pricing before migration.

The following example migrates from `seed-1-8-251228` to `dola-seed-2-0-lite-260428`. Both models use compatible reasoning settings, so the parameter values remain unchanged.

|Scenario |Request example |
|---|---|
|Before migration |```json```<br>```{```<br>```    "model": "seed-1-8-251228",```<br>```    "messages": [```<br>```        {"role": "user", "content": "Explain deep reasoning."}```<br>```    ],```<br>```    "thinking": {"type": "enabled"},```<br>```    "reasoning_effort": "medium"```<br>```}```<br> |
|After migration |```json```<br>```{```<br>```    "model": "dola-seed-2-0-lite-260428",```<br>```    "messages": [```<br>```        {"role": "user", "content": "Explain deep reasoning."}```<br>```    ],```<br>```    "thinking": {"type": "enabled"},```<br>```    "reasoning_effort": "medium"```<br>```}```<br> |

For other migration questions, submit a [support ticket](https://console.byteplus.com/workorder/create).
