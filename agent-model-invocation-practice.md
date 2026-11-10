# Best practices of model calling for agents

> **Important**

> In agent scenarios, if the model performance is not as expected, the task pass rate drops, the output is truncated, or the cache hit rate is abnormally low, we recommend first checking the model calling method. The [recommended practices](agent-model-invocation-practice.md#recommended-practice) are as follows:

* > **Pass reasoning content back**: Select a passback method supported by your model. For models that require the encrypted reasoning content to be passed back, you **must pass back the encrypted reasoning content as is**.

* > **Configure key parameters**: Configure recommended values for the output length, thinking, reasoning_effort, temperature, and top_p parameters.

* > **Follow cache hit principles**: Keep sampling parameters, thinking configuration, tool definitions, and the system prompt stable within the same session to avoid resetting prefix cache.

This topic summarizes possible causes based on common issues in agent scenarios and provides recommended practices to help troubleshoot.

|FAQs |Possible causes |[Recommended practices](agent-model-invocation-practice.md#recommended-practice) |
|---|---|---|
|The performance of multi\-turn tool calling decreases as the number of turns increases |The reasoning content is not passed back, causing reasoning content to be lost after the tool returns in each turn. |Pass thinking content back |
|The task pass rate differs significantly across different implementations with the same model and the same prompt. |Parameter configuration does not follow the recommended values; context passback is incomplete. |Configure key parameters<br><br>Pass thinking content back |
|The response is truncated, tool parameter JSON is incomplete, or `finish_reason` / `stop_reason` is `length`. |`max_tokens` is not explicitly passed in or its value is too small. |Adjust the output length parameter |
|The prompt cache hit rate is abnormally low. |The system prompt or tool definitions change between turns; parameters are changed within the session. |Follow cache hit principles |

# Recommended practices

The three APIs differ in parameter names, hierarchy, and default return policies. Configure them according to the API you actually use and the following table.

|Dimension |Chat API (OpenAI compatible)<br><br>`/api/v3/chat/completions` |Responses API<br><br>`/api/v3/responses` |Anthropic\-compatible<br><br>`/api/compatible/v1/messages` |
|---|---|---|---|
|Models that support passing back the original reasoning content (plaintext) |Seed models: seed\-2\-0\-pro\-260328, seed\-2\-0\-lite\-260228, seed\-2\-0\-mini\-260215<br><br>Open\-source models: deepseek\-v4\-pro\-260425, deepseek\-v4\-flash\-ga\-260731, deepseek\-v4\-1\-flash\-261017, deepseek\-v4\-1\-flash\-260910, glm\-5\-2\-260617 |||
|Parameter for passing back the original reasoning content<br><br>> The original reasoning content needs to be appended back to the next request. |`choices[].message.reasoning_content` |* Manual passback: `output[reasoning].summary[].text`<br><br>* Use `previous_response_id`: Configure `store: true` to automatically obtain the original reasoning content. |`content[thinking].thinking` |
|**Models that support passing back encrypted reasoning content** |Seed models: dola\-seed\-2\-1\-turbo\-260628, seed\-2\-0\-lite\-260428<br><br>> **Note**<br><br><br><br>* > When using supported models, you need to **pass back the encrypted reasoning content**. For key rules and usage examples for passback, see [key rules](agent-model-invocation-practice.md#key-rules) and [example of using encrypted reasoning content blocks](agent-model-invocation-practice.md#encrypted-thinking-block-examples). The content returned by the model call includes the encrypted reasoning content parameter and the plaintext summary parameter, not the original reasoning content. The complete reasoning content that the model actually uses for reasoning is stored in the **encrypted parameter**. Passing back only summary parameters such as `reasoning_content` is not the same as passing back the complete reasoning content, and will directly degrade the model's reasoning performance.<br><br><br>* > Different versions of the same model may belong to different categories: `seed-2-0-lite-260228` passes back the original reasoning content (plaintext), while `seed-2-0-lite-260428` and later versions pass back encrypted reasoning content blocks. When upgrading the model version, make sure to update the calling code accordingly. Otherwise, the passback method may become invalid and no API error will be triggered.<br> |||
|**Parameter for passing back the encrypted reasoning content**<br><br>> The encrypted reasoning content needs to be appended back to the next request. |`choices[].message.encrypted_content` |* Manual passback: `output[reasoning].encrypted_content`<br><br>* Use `previous_response_id`: Configure `store: true` to automatically obtain the original reasoning content. |`content[thinking].signature` |
|Whether the encrypted reasoning content parameter is returned by default |Yes |Yes |Yes |
|Parameter for passing back the plaintext summary of reasoning content<br><br>> Do not pass back only the plaintext summary of reasoning content. This will degrade model performance. |`choices[].message.reasoning_content` |`output[reasoning].summary[].text` |`content[thinking].thinking` |
|Configure the output length parameter |`max_tokens` / `max_completion_tokens`<br><br>**Recommended**: Must be explicitly passed in. For agents, a value ≥ `128000` is recommended. |`max_output_tokens`<br><br>**Recommended**: Must be explicitly passed in. For agents, a value ≥ `128000` is recommended. |`max_tokens`<br><br>**Recommended**: Must be explicitly passed in. For agents, a value ≥ `128000` is recommended. |
|Configure the thinking parameter |`{"type": "enabled"}`<br><br>**Recommended**: Explicitly enable it for agentic use. |`{"type": "enabled"}`<br><br>**Recommended**: Explicitly enable it for agentic use. |`{"type": "enabled"}`<br><br>**Recommended**: Explicitly enable it for agentic use. |
|Configure the reasoning_effort parameter |`"reasoning_effort": "high"`<br><br>**Recommended**: `high` or above is recommended for agents. |`"reasoning": {"effort": "high"}`<br><br>**Recommended**: `high` or above is recommended for agents. |`"output_config": {"effort": "high"}`<br><br>**Recommended**: `high` or above is recommended for agents. |
|Configure the temperature / top_p parameters |**Recommended**: `temperature=1`, `top_p=0.95` |**Recommended**: `temperature=1`, `top_p=0.95` |**Recommended**: `temperature=1`, `top_p=0.95` |

> **Note**

> Make sure to keep sampling parameters, thinking configuration, tool definitions, and the system prompt stable within the same session. Any change during the session will reset prefix cache.

# Core mechanism: Passing reasoning content back

## Why reasoning content needs to be passed back

In agent scenarios, a task usually goes through multiple turns of "reasoning \-\> tool calling \-\> waiting for results \-\> continuing reasoning \-\> tool calling". After tool results are returned, the model needs to continue completing the subsequent steps on that reasoning path.

If reasoning content is not passed back, the following impacts may occur:

* **Reasoning continuity is interrupted**: After tool results are returned, the model can only reconstruct the context based on the visible conversation text. For complex tasks, these implicit intermediate judgments cannot be fully restored, which can easily cause unsmooth reasoning transitions or logical gaps.

* **Multistep execution stability decreases**: Because the intermediate reasoning state is missing, the model may repeat derivations that have already been done, or use assumptions in the next step that are inconsistent with the previous step. This can cause task execution to go off track, make steps unstable, and affect result consistency and reproducibility.

* **Overall cost increases**: The reasoning content that is passed back participates in caching together with the tool result, making it more likely to hit the prompt cache. For scenarios that include multiple tool calls, cache hits mean less repeated token consumption, reducing the overall cost.

## Passback methods and supported models

ModelArk supports two types of reasoning content passback methods:

* **Pass back the original reasoning content (plaintext)** : Append the original reasoning content in the response directly back to the next request.

* **Pass back the encrypted reasoning content**: Append the encrypted content in the response back to the next request as is.

The models supported by the two passback methods are different. For details, see [recommended practices](agent-model-invocation-practice.md#recommended-practice).

## Key rules

* The encrypted reasoning content parameter **must be passed back as is and in full**. Any tampering or truncation returns an `Invalid signature` error.

* Not passing back the encrypted reasoning content **does not trigger an error, but affects reasoning performance**.

* The encrypted reasoning content parameter **has a higher priority than the reasoning content summary**: When both are passed in, the plaintext summary is ignored.

* Billing for the output reasoning content part is calculated based on the original reasoning content tokens. If the encrypted reasoning content is not passed back, the paid reasoning content not only fails to take effect, but may also degrade reasoning performance.

---

# Other key parameter configuration recommendations

|Key configuration |Recommended configuration |Impact of incorrect configuration |Self\-check method |
|---|---|---|---|
|thinking |Explicitly set it to `enabled` for agentic use. |The ability to plan multi\-step tasks decreases. |Check whether the response contains a reasoning content block. |
|reasoning_effort |Set it to `high` or above in agent and multi\-step tool calling scenarios. |With lower levels of reasoning effort, reasoning is insufficient, and the error rate of tool selection and tool parameter construction increases. |\- |
|Temperature and top_p |`temperature=1`, `top_p=0.95` |It will affect tool call stability and reasoning quality.<br><br>> It is not recommended to adjust `temperature` and `top_p` significantly at the same time, because their combined effects are hard to pinpoint. |\- |
|max_tokens |**Must be explicitly passed in**. For agents, a value ≥ `128000` is recommended. The actual value must be determined based on the model's `max_output_tokens` limit and the business requirements. |Responses are truncated, tool parameter JSON is incomplete, or the agent loop is interrupted. |Check whether `finish_reason` / `stop_reason` is `length`. |
|Cache hit rate |Place the system prompt and tool definitions at the beginning of the request, and keep them stable within the same session.<br><br>> **Note**<br><br><br>> Common operations that invalidate the cache: changing the `thinking` configuration, changing `reasoning_effort`, changing sampling parameters (`temperature`, `top_p`, etc.), changing the system prompt, and changing tool definitions.<br> |Cache hit rate decreases, and overall costs increase. |Check the number of cache hit tokens in the response. |

# Examples of passing back encrypted reasoning content blocks

**Chat API (OpenAI-compatible)**

### Non\-streaming response

For a non\-streaming response, the encrypted block is in the **flat parameter** `encrypted_content` of the assistant message, at the same level as `reasoning_content` and `tool_calls`.`encrypted_content` is returned by default and does not need to be declared separately.

**Correct usage**: Add the entire assistant message object from the response to `messages` in the next round, and avoid selecting parameters to rebuild it.

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
    "thinking": {"type": "enabled"},
    "temperature": 1,
    "top_p": 0.95,
    "max_tokens": 128000,
    "messages": [
      {"role": "system", "content": "You are an AI assistant."},
      {"role": "user", "content": "Today is August 10, 2026 (Monday). What is the weather like in Beijing tomorrow?"},
      {
        "role": "assistant",
        "reasoning_content": "The user asks about the weather in Beijing tomorrow. Tomorrow is August 11, 2026. Call the weather tool.",
        "encrypted_content": "<complete encrypted block, passed back as is>",
        "tool_calls": [
          {
            "id": "call_wiezxeyae8jzxl3jx8nhfgb5",
            "type": "function",
            "function": {"name": "get_weather", "arguments": "{\"location\":\"Beijing\",\"date\":\"2026-08-11\"}"}
          }
        ]
      },
      {
        "role": "tool",
        "tool_call_id": "call_wiezxeyae8jzxl3jx8nhfgb5",
        "content": "2026-08-11 Beijing: overcast, 11–16°C, north wind, moderate breeze."
      }
    ],
    "tools": [
      {
        "type": "function",
        "function": {
          "name": "get_weather",
          "description": "Query the weather for a specified city on a specified date.",
          "parameters": {
            "type": "object",
            "properties": {
              "location": {"type": "string", "description": "City name, such as Beijing or Shanghai."},
              "date": {"type": "string", "description": "Date in YYYY-MM-DD format."}
            },
            "required": ["location", "date"]
          }
        }
      }
    ]
  }'
```

### Streaming response

In a streaming response, the encrypted reasoning content block is delivered as an **independent chunk** after reasoning content output is complete and before the final answer starts. The `content` and `reasoning_content` of this chunk **may both be empty**.

**Correct usage**: Make sure to handle the `encrypted_content` parameter in your streaming merge logic. If you only handle `delta.content` or `delta.reasoning_content`, the encrypted reasoning content block will be lost directly, which affects reasoning quality.

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
    "stream": true,
    "thinking": {"type": "enabled"},
    "max_tokens": 128000,
    "messages": [
      {"role": "user", "content": "Today is August 10, 2026. What is the weather like in Beijing tomorrow?"}
    ]
  }'
```

Response snippet (example):

```text
data: {"choices":[{"delta":{"reasoning_content":"The user asks about tomorrow"}}]}
data: {"choices":[{"delta":{"reasoning_content":"weather in Beijing…"}}]}
data: {"choices":[{"delta":{"content":"","reasoning_content":"","encrypted_content":"<complete encrypted block>"}}]}
data: {"choices":[{"delta":{"content":"Tomorrow in Beijing"}}]}
data: [DONE]
```

**Responses API**

### Non\-streaming response

For a non\-streaming response, the encrypted block is in the item with `type == "reasoning"` in the `output` array.

Pass back the encrypted reasoning content block as follows:

* **previous_response_id**: **Note that you must explicitly configure ** **`store: true`**. The server hosts the session state. The client does not need to carry historical items itself. The encrypted reasoning content block is automatically obtained and passed back to the model for reasoning.

* **Manually passing back encrypted reasoning content blocks**: Get the reasoning item from the response and pass back `encrypted_content` as is in subsequent requests.

Notes for manually passing back encrypted reasoning content blocks:

* The **`id`** of the reasoning item must be passed back as is and cannot be generated by yourself.

* When passed back, `content` can be an empty array `[]`, but `encrypted_content` **cannot be empty**.

* For multiple consecutive tool calls, all items (`reasoning` / `function_call` / `function_call_output`) after the last user message must be passed back in their original order. You cannot pass back only the last one.

An example of manually passing back the encrypted reasoning content block is as follows:

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
    "store": false,
    "reasoning": {"effort": "high", "summary": "detailed"},
    "max_output_tokens": 128000,
    "input": [
      {"type": "message", "role": "user", "content": "Today is August 10, 2026 (Monday). What will the weather be like tomorrow?"},
      {
        "id": "rs_0c2e0389c1bdc80f016a79c44c1fd08198bbdcb852ca75789b",
        "type": "reasoning",
        "content": [],
        "encrypted_content": "<complete encrypted block, passed back as is>",
        "summary": [
          {"type": "summary_text", "text": "First get the user's city, then check the weather for 2026-08-11."}
        ]
      },
      {
        "id": "fc_0c2e0389c1bdc80f016a79c44d9fac81988abe3597d1fbba88",
        "type": "function_call",
        "status": "completed",
        "call_id": "call_tjOpXT7tq4KbtdnpQYdBNTXz",
        "name": "get_location",
        "arguments": "{\"location_level\":\"city\"}"
      },
      {
        "type": "function_call_output",
        "call_id": "call_tjOpXT7tq4KbtdnpQYdBNTXz",
        "output": "Beijing"
      },
      {
        "id": "fc_0c2e0389c1bdc80f016a79c451d4348198a1c4eb858f8a858b",
        "type": "function_call",
        "status": "completed",
        "call_id": "call_cf3Sz3cE2fZkZBPdez5P9Ecb",
        "name": "get_weather",
        "arguments": "{\"location\":\"Beijing\",\"date\":\"2026-08-11\"}"
      },
      {
        "type": "function_call_output",
        "call_id": "call_cf3Sz3cE2fZkZBPdez5P9Ecb",
        "output": "2026-08-11 Beijing: overcast, 11–16°C, north wind, moderate breeze."
      }
    ],
    "tools": [
      {
        "type": "function",
        "name": "get_location",
        "description": "Get the user's current location",
        "parameters": {
          "type": "object",
          "properties": {
            "location_level": {"type": "string", "description": "Required granularity: country / province / city / street"}
          },
          "required": ["location_level"],
          "additionalProperties": false
        },
        "strict": true
      },
      {
        "type": "function",
        "name": "get_weather",
        "description": "Query the weather for a specified city on a specified date.",
        "parameters": {
          "type": "object",
          "properties": {
            "location": {"type": "string", "description": "City name"},
            "date": {"type": "string", "description": "Date, in YYYY-MM-DD format"}
          },
          "required": ["location", "date"],
          "additionalProperties": false
        },
        "strict": true
      }
    ]
  }'
```

### Streaming response

For streaming responses, reasoning content encrypted blocks are delivered through related events such as `response.output_item.added` / `response.output_item.done` / `response.completed`. You need to filter for events where the **encrypted_content** parameter is not empty, and obtain the encrypted reasoning content.

After obtaining the reasoning content encrypted block, see the example above for how to pass it back.

**Anthropic-compatible API**

The encrypted content is in the **`signature`** parameter of the block with `type == "thinking"` in the assistant content array (the native name in the Anthropic protocol).

**Correct usage**:

* The thinking block must be passed back **as a whole block** (the `thinking` text together with `signature`), and must remain in the original order **before** the `tool_use` block.

* **Do not reconstruct the content array after allowlist filtering by ** **`type == "thinking"`** . Changing the order or blocks will cause verification to fail.

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/compatible/v1/messages \
  -H "Content-Type: application/json" \
  -H "x-api-key: $ARK_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
    "max_tokens": 128000,
    "thinking": {"type": "enabled"},
    "messages": [
      {"role": "user", "content": "Today is August 10, 2026 (Monday). What will the weather be like tomorrow?"},
      {
        "role": "assistant",
        "content": [
          {
            "type": "thinking",
            "thinking": "The user asks about tomorrow's weather. Tomorrow is 2026-08-11. Need to get the city first, then check the weather.",
            "signature": "<complete encrypted block, passed back as is>"
          },
          {
            "type": "tool_use",
            "id": "tool_4U01J2huMeLhTlTKyrU5rhI5",
            "name": "get_location",
            "input": {"location_level": "city"}
          }
        ]
      },
      {
        "role": "user",
        "content": [
          {
            "type": "tool_result",
            "tool_use_id": "tool_4U01J2huMeLhTlTKyrU5rhI5",
            "content": "Beijing"
          }
        ]
      }
    ],
    "tools": [
      {
        "name": "get_location",
        "description": "Get the user's current location",
        "input_schema": {
          "type": "object",
          "properties": {
            "location_level": {"type": "string", "description": "Required granularity: country / province / city / street"}
          },
          "required": ["location_level"]
        }
      },
      {
        "name": "get_weather",
        "description": "Query the weather for a specified city on a specified date.",
        "input_schema": {
          "type": "object",
          "properties": {
            "location": {"type": "string", "description": "City name"},
            "date": {"type": "string", "description": "Date, in YYYY-MM-DD format"}
          },
          "required": ["location", "date"]
        }
      }
    ]
  }'
```
