# List of parameters supported by models

This topic introduces the `thinking`, `reasoning_effort`, and `service_tier` parameters that can be configured in ModelArk Managed Agents when defining an agent or creating a session, and lists the current support status of each model for thinking and service tier parameters.

Managed Agents validates `thinking`, `reasoning_effort`, and `service_tier` separately based on the target model version. Before creating an agent, updating an agent, or temporarily overriding session model parameters, you must first confirm whether the target model version supports the corresponding value.

> **Note**

> The model support scope may change as models are upgraded. The actual supported values and default values of each parameter depend on the target model version. During calls, if you pass a value that the current model does not support, Managed Agents returns 400 `InvalidParameter`.

## Model parameters

|Parameter |Valid values |Note |
|---|---|---|
|`model.id` |Model ID or inference endpoint ID |The model or inference endpoint used by the agent. When an inference endpoint ID is used, Managed Agents validates runtime parameters based on the model associated with the endpoint. |
|`model.thinking` |`enabled`, `disabled`, `auto` |Thinking switch override, corresponding to the `thinking.type` parameter in the underlying model call.`auto` means the model determines whether to think; it does not mean inheritance. |
|`model.reasoning_effort` |`none`, `minimal`, `low`, `medium`, `high`, `xhigh`, `max`. |Reasoning effort tier, corresponding to the `reasoning_effort` parameter in the underlying model call. Different models support different tiers. |
|`model.service_tier` |`auto`, `default`, `fast` |Inference service tier override, corresponding to the `service_tier` parameter in the underlying model call. Only some model versions support the `fast` tier. |

> **Tip**

> When creating or updating an agent, `model.id` can be either a model ID or an inference endpoint ID. When creating a session, `agent_with_overrides.model.id` supports only model IDs; the `model` parameter of Dream also supports only model IDs. If the base agent uses an inference endpoint, the model snapshot in the session response still returns the inference endpoint ID.

## Default values and validation rules

* If `thinking`, `reasoning_effort`, or `service_tier` is not set, or if an empty string is passed in the agent definition, Managed Agents preferentially uses the default value in the model metadata.

* If no default value is configured in the model metadata, Managed Agents does not pass the corresponding parameter to the underlying model call. The underlying model service handles it according to its default policy.

> **Note**

> Seedance Managed Agent does not use the model default value of `reasoning_effort`. When you create a Seedance Managed Agent, if `reasoning_effort` is omitted, the server fills it in as `high`; if it is explicitly specified, it can only be set to `high`. Other values return 400 `InvalidParameter`. Select a model from the models displayed on the Seedance Managed Agent creation page in the console, and confirm that the model supports `high`.

## Supported models and parameter list

Values marked with "default" in the table indicate the default values of this model version.

|Model ID |`thinking` |`reasoning_effort` |`service_tier` |
|---|---|---|---|
|`doubao-seed-evolving` (not available for BytePlus currently) |enabled (default), disabled |low, medium, high (default), minimal |auto, default (default) |
|`seed-2-0-pro-260328` |enabled (default), disabled |low, medium, high (default), minimal |auto, default (default) |
|`doubao-seed-2-1-lite-260915` (not available for BytePlus currently) |enabled (default), disabled |low, medium, high (default), minimal |auto, default (default) |
|`doubao-seed-2-1-pro-260628` (not available for BytePlus currently) |enabled (default), disabled |low, medium, high (default), minimal |auto, default (default) |
|`dola-seed-2-1-turbo-260628` |enabled (default), disabled |low, medium, high (default), minimal |auto, default (default), fast |
|`seed-2-0-lite-260428` |enabled (default), disabled |low, medium (default), high, minimal |auto, default (default), fast |
|`deepseek-v4-pro-260425` |enabled (default), disabled |low, high (default), max, minimal |auto, default (default) |
|`deepseek-v4-pro-ga-260813` |enabled (default), disabled |none, low, high (default), max, minimal |auto, default (default) |
|`deepseek-v4-flash-ga-260731` |enabled (default), disabled |none, low, high (default), max, minimal |auto, default (default) |
|`glm-5-2-260617` |enabled (default), disabled |none, minimal, high, max (default) |auto, default (default) |

## Models that support fast service tier

The following model versions support `service_tier: "fast"`:

* `dola-seed-2-1-turbo-260628`

* `seed-2-0-lite-260428`

Other model versions currently support `auto` and `default`, with the default value being `default`.

> **Note**

> `service_tier: "fast"` only means requesting the fast service tier. It does not change the capability boundaries of the model itself. Before configuring the fast tier, you should confirm that your business can accept the capability boundaries and inference parameter limits of the corresponding model version.

## Configuration examples

When creating an agent, you can explicitly configure supported runtime parameters in the `model` object:

```json
{
  "model": {
    "id": "dola-seed-2-1-turbo-260628",
    "thinking": "enabled",
    "reasoning_effort": "high",
    "service_tier": "fast"
  }
}
```

If you only want to use the model's default policy, you can set only `model.id`. The value of `model.id` can be a model ID or an inference endpoint ID. The following example uses a model ID:

```json
{
  "model": {
    "id": "dola-seed-2-1-turbo-260628"
  }
}
```
