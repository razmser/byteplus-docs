# Context editing (Responses API)

Context editing can manage chain\-of\-thought content and tool call content in the context, with the following benefits:


* Better tool use: Enables the model to use thinking content from historical context, improving tool triggering and usage.

* Context window management: Automatically stays within the model's context window limit.

* Smart caching: Works with the caching feature to optimize performance.


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">This capability is still in beta stage. Please use it with caution in production environments.</div>


<span id="d6c6c029"></span>
# Supported models


* dola\-seed\-2\-1\-turbo\-260628

* seed\-2\-0\-lite\-260428

* seed\-2\-0\-mini\-260428

* seed\-2\-0\-pro\-260328

* seed\-2\-0\-lite\-260228

* seed\-2\-0\-mini\-260215

* seed\-2\-0\-code\-preview\-260328

* seed\-1\-8\-251228


<span id="b8d655bb"></span>
# API documentation

[Responses API](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1569618)

<span id="53274bbd"></span>
# Feature policies


<span aceTableMode="list" aceTableWidth="1,1,3"></span>
|Feature policy |Identifier |Description |
|---|---|---|
|Clearing chain\-of\-thought content |`clear_thinking` |Manages chain\-of\-thought content when thinking is enabled.<br><br>This policy automatically clears older chain\-of\-thought content from previous turns. |
|Clearing tool call content |`clear_tool_uses` |Clears tool call content when the conversation context grows beyond the configured threshold.<br><br>This policy automatically clears older tool call content from previous turns. |


<span id="d44abb15"></span>
# Clear chain\-of\-thought content

<span id="c4a97c35"></span>
## Configuration options

The `clear_thinking` policy supports the following configuration:


<span aceTableMode="list" aceTableWidth="1,1,2,1"></span>
|Configuration option |Type |Default value |Description |
|---|---|---|---|
|keep |Object / String |`{type: "thinking_turns", value: 1}` |Defines chain\-of\-thought retention policy. |


> The retained turns cover both tool\-call and non\-tool\-call scenarios, and are determined only by the number of turns.


Configuration examples:


* Retain chain\-of\-thought content from the last N turns: (N must be \> 0)

   ```JSON
   // Retain chain-of-thought content from the last 3 turns
   {
       "type": "clear_thinking",
       "keep": {
           "type": "thinking_turns",
           "value": 3
       }
   }
   ```
   

* Retain all chain\-of\-thought content:

   ```Bash
   {
       "type": "clear_thinking",
       "keep": "all"
   }
   ```
   


<span id="26061490"></span>
## Usage examples


<Tabs>
<Tab zoneid="pkXnLt2sYR" title="Curl">
<TabTitle>Curl</TabTitle>

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d '{
        "model": "seed-2-0-lite-260228",
        "input": "Differences between reasoning and non-reasoning models",
        "thinking":{"type": "enabled"},
        "context_management": {
            "edits": [
                {
                    "type": "clear_thinking",
                    "keep": {
                        "type": "thinking_turns",
                        "value": 3
                    }
                }
            ]
        }
    }'
```



</Tab>
</Tabs>


<span id="f46ff0c8"></span>
# Clear tool call content

<span id="3ec28f05"></span>
## Configuration options

The `clear_tool_uses` policy supports the following configuration:


<span aceTableMode="list" aceTableWidth="1,1,1,2"></span>
|Configuration option |Type |Default value |Description |
|---|---|---|---|
|trigger |Object ||Threshold that triggers the tool call content clearance policy. |
|keep |object |3 tool call turns |Retain the last N rounds of tool call content. |
|exclude_tools |Array ||List of tool names that will not be cleared, used to retain important context. |
|clear_tool_inputs |Bool |false |Whether to clear tool call parameters. |


Configuration examples:

```JSON
{
    "type": "clear_tool_uses",
    "trigger": {
        "type": "tool_uses",
        "value": 5
    },
    "keep": {
        "type": "tool_uses",
        "value": 3
    },
    "exclude_tools": [
        "web_search"
    ]
}
```


<span id="22e63008"></span>
## Usage examples


<Tabs>
<Tab zoneid="XT80VIjgPY" title="Curl">
<TabTitle>Curl</TabTitle>

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d '{
        "model": "seed-2-0-lite-260228",
        "input": [
            {
                "role": "system",
                "content": "You are an AI assistant."                
            },
            {
                "role": "user",
                "content": "What is the weather like in New York today?"  
            }
        ],
        "thinking":{"type": "enabled"},
        "tools": [
            {
                "type": "function",
                "name": "get_weather",
                "description": "Search for the weather (including temperature and weather conditions) of a city for the current day based on its name.",
                "parameters": {
                    "type": "object",
                    "properties": {
                        "location": {
                            "type": "string",
                            "description": "City names, such as Beijing and Shanghai."
                        }
                    },
                    "required": ["location"]
                }
            }
        ],
        "context_management": {
            "edits": [
                {
                    "type": "clear_tool_uses",
                    "trigger": {
                        "type": "tool_uses",
                        "value": 5
                    }
                }
            ]
        }
    }'
```



</Tab>
</Tabs>


<span id="f11b5c06"></span>
# Combined policies

You can use chain\-of\-thought content clearing and tool call content clearing at the same time. When using multiple policies:


* Definition order: `clear_thinking` must come before `clear_tool_uses`.

* Processing order: clear chain\-of\-thought content first, and then clear tool call content.



<Tabs>
<Tab zoneid="oAdiAhKNuk" title="Curl">
<TabTitle>Curl</TabTitle>

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d '{
        "model": "seed-2-0-lite-260228",
        "input": [
            {
                "role": "system",
                "content": "You are an AI assistant."  
            },
            {
                "role": "user",
                "content": "What is the weather like in New York today?"  
            }
        ],
        "thinking":{"type": "enabled"},
        "tools": [
            {
                "type": "function",
                "name": "get_weather",
                "description": "Search for the weather (including temperature and weather conditions) of a city for the current day based on its name.",
                "parameters": {
                    "type": "object",
                    "properties": {
                        "location": {
                            "type": "string",
                            "description": "City names, such as Beijing and Shanghai."
                        }
                    },
                    "required": ["location"]
                }
            }
        ],
        "context_management": {
            "edits": [
                {
                    "type": "clear_thinking",
                    "keep": {
                        "type": "thinking_turns",
                        "value": 3
                    }
                },
                {
                    "type": "clear_tool_uses",
                    "trigger": {
                        "type": "tool_uses",
                        "value": 5
                    }
                }
            ]
        }
    }'
```



</Tab>
</Tabs>
