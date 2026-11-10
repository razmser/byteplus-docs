# Migrate to Responses API

Responses API is the latest API launched by ModelArk. It natively supports efficient context management, simpler input and output formats, and more convenient tool calling. It not only continues the ease of use of Chat API, but also combines stronger AI agent capabilities.

As large model technologies continue to advance, Responses API provides a more flexible foundation for developing applications that take real\-world actions. It also supports multiple extended capabilities through tool calling, making it well suited for scenarios such as building AI assistants and automation tools.

<span id="24944445"></span>
## Core advantages of the Responses API

Compared with Chat API, Responses API has advantages in multiple capabilities.


* Simple input and output formats: The input can be a string or an array, and the output is a response object that contains its own ID and is stored by default.

* Efficient context management: Storage is enabled by default. In multi\-turn conversation mode, it can automatically manage context, avoiding the tedious process of manually maintaining context and improving the intelligent interaction experience.

* Low\-cost context caching: By caching commonly used context information, it reduces the overhead of repeatedly processing and loading the same content for each request, lowering costs.

* Convenient tool calling: Supports multiple tool calling methods, such as cloud\-deployed MCP, improving development and integration efficiency.

* Good scalability: More built\-in tools will be supported in the future, providing developers with richer and more flexible capabilities for developing intelligent applications.



|Capability ||Chat API |Responses API |
|---|---|---|---|
| | | | |
|Text generation ||<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/aba4522e4aab46318574c8c3e460d20b~tplv-goo7wpa0wc-image.image) </span> |<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/aba4522e4aab46318574c8c3e460d20b~tplv-goo7wpa0wc-image.image) </span> |
|Visual understanding ||<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/aba4522e4aab46318574c8c3e460d20b~tplv-goo7wpa0wc-image.image) </span> |<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/aba4522e4aab46318574c8c3e460d20b~tplv-goo7wpa0wc-image.image) </span> |
|Structured output ||beta phase |beta phase |
|Tool calling |Function calling |<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/aba4522e4aab46318574c8c3e460d20b~tplv-goo7wpa0wc-image.image) </span> |<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/aba4522e4aab46318574c8c3e460d20b~tplv-goo7wpa0wc-image.image) </span> |
| | | | |
| | | | |
|Context caching ||<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/96a134db51ea4e8d83b5c9dccff686c3~tplv-goo7wpa0wc-image.image) </span> |<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/aba4522e4aab46318574c8c3e460d20b~tplv-goo7wpa0wc-image.image) </span><br><br>> Model versions after 250615 are supported. |


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">Responses API does not support the following scenarios:</div>



* <div data-tips="true" data-tips-type="tip">Online inference for fine\-tuned models is not supported.</div>


* <div data-tips="true" data-tips-type="tip">Model version switching for online inference services is not supported.</div>



<span id="28dfc4e3"></span>
## Supported models

For large language models of version 250615 and later, the Responses API is supported by default unless otherwise specified. For more information, see [Text generation](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1330310#32e9e053).

<span id="fd5fe98e"></span>
## Basic differences

Chat API (`/chat/completions`) and Responses API (`/responses`) use slightly different input and output formats.


* Input: Chat API requires a message (messages) array, while Responses API accepts input in string or array format. In addition, in Responses API, you can use the **instructions** parameter to add a system prompt in a specific turn. For usage, see [Supplement system prompts](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1958520#6855e23d).

* Output: Chat API returns a message, while Responses API returns a response object that contains its own ID.


Use the following examples to quickly experience the usage differences between the two APIs.


<span aceTableMode="list" aceTableWidth="1,1"></span>
|Chat API |Responses API |
|---|---|
|Input example<br><br>```Python```<br>```import os```<br>```from arkruntime import Ark```<br>``````<br>```client = Ark(```<br>```    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",```<br>```    api_key=os.getenv('ARK_API_KEY'),```<br>```)```<br>``````<br>```completion = client.chat.completions.create(```<br>```    model = "seed-2-0-lite-260228",```<br>```    messages=[```<br>```        {"role": "user", "content": "Hello."},```<br>```    ],```<br>```)```<br>```print(completion)```<br> |Input example<br><br>```Python```<br>```import os```<br>```from arkruntime import Ark```<br>``````<br>```client = Ark(```<br>```    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',```<br>```    api_key=os.getenv('ARK_API_KEY'),```<br>```)```<br>``````<br>```response = client.responses.create(```<br>```    model="seed-2-0-lite-260228",```<br>```    input="Hello.",```<br>```)```<br>``````<br>```print(response)```<br> |
|Output example<br><br>```JSON```<br>```{```<br>```    "choices": [```<br>```        {```<br>```            "finish_reason": "stop",```<br>```            "index": 0,```<br>```            "logprobs": null,```<br>```            "message": {```<br>```                "content": "Hello! How can I assist you today? Whether you have a question you'd like answered, want to chat about something that's on your mind, or need help with a specific task, feel free to share—I'm here to help. 😊",```<br>```                "reasoning_content": "\nGot it, let's see. The user just said \"Hello.\" I need to respond in a friendly way. Since the system prompt mentions I'm an AI assistant who can answer questions, chat, and provide information, I should keep it open-ended to encourage them to share what they need help with. Maybe something like \"Hello! How can I assist you today? Whether you have a question, want to chat about something, or need help with a task, feel free to let me know.\" That sounds natural and covers the points from the system prompt.",```<br>```                "role": "assistant"```<br>```            }```<br>```        }```<br>```    ],```<br>```    "created": 1765193367,```<br>```    "id": "0217651933631536335e3dfd75940b9979797202ce7ea2a894823",```<br>```    "model": "seed-2-0-lite-260228",```<br>```    "service_tier": "default",```<br>```    "object": "chat.completion",```<br>```    "usage": {```<br>```        "completion_tokens": 164,```<br>```        "prompt_tokens": 35,```<br>```        "total_tokens": 199,```<br>```        "prompt_tokens_details": {```<br>```            "cached_tokens": 0```<br>```        },```<br>```        "completion_tokens_details": {```<br>```            "reasoning_tokens": 114```<br>```        }```<br>```    }```<br>```}```<br> |Output example<br><br>```JSON```<br>```{```<br>```    "created_at": 1765193461,```<br>```    "id": "resp_0217651934613099e1bacc68b98f823c2af95ea68bff0aec36f83",```<br>```    "max_output_tokens": 32768,```<br>```    "model": "seed-2-0-lite-260228",```<br>```    "object": "response",```<br>```    "output": [```<br>```        {```<br>```            "id": "rs_02176519346192100000000000000000000ffffac15322033925d",```<br>```            "type": "reasoning",```<br>```            "summary": [```<br>```                {```<br>```                    "type": "summary_text",```<br>```                    "text": "\nGot it, let's see. The user said \"hello.\" First, I need to respond in a friendly way. Since the system prompt mentions being a helpful assistant, I should greet back and maybe invite them to ask whatever they need help with. Let me make it natural. Like, \"Hello! How can I assist you today?\" That's simple and open-ended. Yeah, that works."```<br>```                }```<br>```            ],```<br>```            "status": "completed"```<br>```        },```<br>```        {```<br>```            "type": "message",```<br>```            "role": "assistant",```<br>```            "content": [```<br>```                {```<br>```                    "type": "output_text",```<br>```                    "text": "Hello! How can I assist you today? Whether you have a question, need help with a task, or just want to chat, feel free to let me know. 😊"```<br>```                }```<br>```            ],```<br>```            "status": "completed",```<br>```            "id": "msg_02176519346378200000000000000000000ffffac153220cfab51"```<br>```        }```<br>```    ],```<br>```    "service_tier": "default",```<br>```    "status": "completed",```<br>```    "usage": {```<br>```        "input_tokens": 35,```<br>```        "output_tokens": 118,```<br>```        "total_tokens": 153,```<br>```        "input_tokens_details": {```<br>```            "cached_tokens": 0```<br>```        },```<br>```        "output_tokens_details": {```<br>```            "reasoning_tokens": 82```<br>```        }```<br>```    },```<br>```    "caching": {```<br>```        "type": "disabled"```<br>```    },```<br>```    "store": true,```<br>```    "expire_at": 1765452661```<br>```}```<br> |


<span id="10e45e52"></span>
## Advanced capability adaptation

<span id="44b336d8"></span>
### Update multi\-turn conversations

In multi\-turn conversation scenarios, the Responses API can manage context more efficiently, avoiding the tedious process of manually maintaining context.


* The Chat API is stateless. Each request must include the history in **messages** and set it through the **role** parameter, so the conversation can continue on the related topic. For instructions, see [Multi-turn conversation](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1399009#f6222fec).

* The Responses API enables storage by default to simplify context management. It uses **previous_response_id** to include the input and response of the corresponding request, enabling an intelligent interaction experience. For instructions, see [Context management](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1958520#6d3df616).



<span aceTableMode="list" aceTableWidth="1,1"></span>
|Chat API |Responses API |
|---|---|
|```Python```<br>```import os```<br>```from arkruntime import Ark```<br>``````<br>```client = Ark(```<br>```    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",```<br>```    api_key=os.getenv('ARK_API_KEY'),```<br>```)```<br>``````<br>```completion = client.chat.completions.create(```<br>```    # Replace with Model ID```<br>```    model = "seed-2-0-lite-260228",```<br>```    messages=[```<br>``````<br>```        {"role": "user", "content": "Hi, tell a joke."},```<br>``````<br>```        {"role": "assistant","content":"I squeezed facial cleanser onto my toothbrush by mistake and didn’t notice the weird taste until I finished brushing."},```<br>``````<br>```        {"role": "user", "content": "What's the punchline of this joke?"}```<br>```    ]```<br>```)```<br>```print(completion.choices[0].message.content)```<br> |```Python```<br>```import os```<br>```from arkruntime import Ark```<br>``````<br>```client = Ark(```<br>```    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',```<br>```    api_key=os.getenv('ARK_API_KEY'),```<br>```)```<br>``````<br>```# Create the first-round conversation request```<br>```response = client.responses.create(```<br>```    model="seed-2-0-lite-260228",```<br>``````<br>```    input=[{"role": "user", "content": "Hi, tell a joke."}],```<br>```)```<br>```print(response)```<br>``````<br>```# Create the second-round conversation request```<br>```second_response = client.responses.create(```<br>```    model="seed-2-0-lite-260228",```<br>```    previous_response_id=response.id,```<br>``````<br>```    input=[{"role": "user", "content": "What's the punchline of this joke?"}],```<br>```)```<br>```print(second_response)```<br> |


<span id="7c3903e2"></span>
### Update structured output definitions

Methods for defining structured output:


* Chat API: **response_format**. For instructions, see [Structured output (beta)](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1568221).

* Responses API: **text.format**. For instructions, see [Structured output (Responses API)](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1958523).



<span aceTableMode="list" aceTableWidth="1,1"></span>
|Chat API |Responses API |
|---|---|
|```Python```<br>```import os```<br>```from arkruntime import Ark```<br>``````<br>```client = Ark(```<br>```    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",```<br>```    api_key=os.getenv('ARK_API_KEY'),```<br>```)```<br>``````<br>```completion = client.chat.completions.create(```<br>```    model = "dola-seed-2-1-turbo-260628",```<br>```    messages=[```<br>``````<br>```        {"role": "user", "content": "What are the common cruciferous plants? Output in JSON format."}```<br>```    ],```<br>```    response_format={"type":"json_object"},```<br>```    thinking={"type": "disabled"},# Do not use deep reasoning capabilities.```<br>```)```<br>```print(completion.choices[0].message.content)```<br> |```Python```<br>```import os```<br>```from arkruntime import Ark```<br>``````<br>```client = Ark(```<br>```    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',```<br>```    api_key=os.getenv('ARK_API_KEY'),```<br>```)```<br>``````<br>```response = client.responses.create(```<br>```    model="dola-seed-2-1-turbo-260628",```<br>```    input=[```<br>``````<br>```        {"role": "user", "content": "What are the common cruciferous plants? Output in JSON format."}```<br>```    ],```<br>```    text={"format":{"type": "json_object"}},```<br>```    thinking={"type": "disabled"},# Do not use deep reasoning capabilities.```<br>```)```<br>```print(response)```<br> |


<span id="a9c4810b"></span>
### Update the maximum output length parameter


* Chat API: Use the **max_completion_tokens** parameter to control the model's maximum output length. For the detailed tutorial, see [Set maximum output length](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1449737#cfc7c5a8).

* Responses API: Use the **max_output_tokens** parameter to control the model's maximum output length. For the detailed tutorial, see [Set maximum output length](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1956279#1460ba95).



<span aceTableMode="list" aceTableWidth="1,1"></span>
|Chat API |Responses API |
|---|---|
|```Python```<br>```import os```<br>```from arkruntime import Ark```<br>``````<br>```client = Ark(```<br>```    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",```<br>```    api_key=os.getenv('ARK_API_KEY'),```<br>```)```<br>``````<br>```completion = client.chat.completions.create(```<br>```    model = "seed-2-0-lite-260228",```<br>```    messages=[```<br>``````<br>``````<br>```        {"role": "system", "content": "You are an AI assistant"},```<br>```        {"role": "user", "content": "What are the common cruciferous plants?"},```<br>```    ],```<br>```    max_completion_tokens = 1024,```<br>```)```<br>```print(completion.choices[0].message.content)```<br> |```Python```<br>```import os```<br>```from arkruntime import Ark```<br>``````<br>```client = Ark(```<br>```    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',```<br>```    api_key=os.getenv('ARK_API_KEY'),```<br>```)```<br>``````<br>```response = client.responses.create(```<br>```    model="seed-2-0-lite-260228",```<br>```    input=[```<br>``````<br>``````<br>```        {"role": "system", "content": "You are an AI assistant"},```<br>```        {"role": "user", "content": "What are the common cruciferous plants?"},```<br>```    ],```<br>```    max_output_tokens = 1024,```<br>```)```<br>```print(response)```<br> |


<span id="f61fe601"></span>
### Use context caching

Context API supports context caching, while Responses API is more flexible in cache operations and supports ID\-level usage and changes. For the two usage methods, see [Context cache](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1398933).

Replace `<YOUR_ENDPOINT_ID>` with your inference endpoint ID.


|Context API |Responses API |
|---|---|
|```Python```<br>```import datetime```<br>```import os```<br>```from arkruntime import Ark```<br>``````<br>```client = Ark(```<br>```    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',```<br>```    api_key=os.environ.get("ARK_API_KEY"),```<br>```)```<br>``````<br>```response = client.context.create(```<br>```    model="<YOUR_ENDPOINT_ID>",```<br>```    mode="session",```<br>```    messages=[```<br>``````<br>```        {"role": "system", "content": "You are Li Lei."},```<br>```    ],```<br>```    ttl=datetime.timedelta(minutes=60),```<br>```)```<br>```print(response)```<br>``````<br>```print("----- chat round 1 -----")```<br>```first_response = client.context.completions.create(```<br>```    context_id=response.id,```<br>```    model="<YOUR_ENDPOINT_ID>",```<br>```    messages=[```<br>``````<br>```        {"role": "user", "content": "I am FangFang."},```<br>```    ]```<br>```)```<br>```print(first_response.choices[0].message.content)```<br>``````<br>```print("----- chat round 2  -----")```<br>```second_response = client.context.completions.create(```<br>```    context_id=response.id,```<br>```    model="<YOUR_ENDPOINT_ID>",```<br>```    messages=[```<br>``````<br>```        {"role": "user", "content": "Who are you, and who am I?"},```<br>```    ]```<br>```)```<br>```print(second_response.choices[0].message.content)```<br> |```Python```<br>```import os```<br>```from arkruntime import Ark```<br>``````<br>```client = Ark(```<br>```    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',```<br>```    api_key=os.getenv('ARK_API_KEY'),```<br>```)```<br>``````<br>```response = client.responses.create(```<br>```    model="seed-2-0-lite-260228",```<br>```    input=[```<br>``````<br>```        {"role": "system", "content": "You are Li Lei."},```<br>```    ],```<br>```    caching={"type": "enabled"},```<br>```    thinking={"type": "disabled"},```<br>```)```<br>```print(response.output[0].content[0].text)```<br>```print("----- chat round 1 -----")```<br>```first_response = client.responses.create(```<br>```    model="seed-1-6-250915",```<br>```    previous_response_id=response.id,```<br>``````<br>```    input=[{"role": "user", "content": "I am FangFang."}],```<br>```    caching={"type": "enabled"},```<br>```    thinking={"type": "disabled"},```<br>```)```<br>```print(first_response.output[0].content[0].text)```<br>```print("----- chat round 2 -----")```<br>```second_response = client.responses.create(```<br>```    model="seed-1-6-250915",```<br>```    previous_response_id=first_response.id,```<br>``````<br>```    input=[{"role": "user", "content": "Who are you, and who am I?"}],```<br>```    caching={"type": "enabled"},```<br>```    thinking={"type": "disabled"},```<br>```)```<br>```print(second_response.output[0].content[0].text)```<br> |


<span id="aa301c33"></span>
### Use tool calls

<span id="b76ccb7e"></span>
#### Update function definitions

The Responses API and Chat API have minor differences in how they define functions. For detailed instructions, see [Function call](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1262342).


<span aceTableMode="list" aceTableWidth="1,1"></span>
|Chat API |Responses API |
|---|---|
|```JSON```<br>```[```<br>```    {```<br>```        "type": "function",```<br>```        "function": {```<br>```            "name": "get_weather",```<br>``````<br>```            "description": "Query weather (including temperature and weather conditions) based on the city name",```<br>```            "parameters": {```<br>```                "type": "object",```<br>```                "properties": {```<br>```                    "location": {```<br>```                        "type": "string",```<br>``````<br>```                        "description": "City name, e.g., Beijing, Shanghai (only domestic prefecture-level cities are supported)"```<br>```                    }```<br>```                },```<br>```                "required": [```<br>```                    "location"```<br>```                ]```<br>```            }```<br>```        }```<br>```    }```<br>```]```<br> |```JSON```<br>```[```<br>```    {```<br>```        "type": "function",```<br>```        "name": "get_weather",```<br>``````<br>```        "description": "Query weather (including temperature and weather conditions) based on the city name",```<br>```        "parameters": {```<br>```            "type": "object",```<br>```            "properties": {```<br>```                "location": {```<br>```                    "type": "string",```<br>``````<br>```                    "description": "City name, e.g., Beijing, Shanghai (only domestic prefecture-level cities are supported)"```<br>```                }```<br>```            },```<br>```            "required": [```<br>```                "location"```<br>```            ]```<br>```        }```<br>```    }```<br>```]```<br> |


<span id="dc188312"></span>
#### Use the built\-in tools

The Chat API currently does not support ModelArk large model capabilities such as cloud\-deployed MCP. You can use them through the Responses API. For the tutorial, see [Cloud-deployed MCP / remote MCP](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1827534).

```Python
from arkruntime import Ark
import os
# Initialize the client and enable MCP
client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=os.getenv('ARK_API_KEY')
)
# Send a basic MCP call request
response = client.responses.create(
    model="seed-2-0-lite-260228",
    tools=[
        {
            "type": "mcp",
            "server_label": "deepwiki",
            "server_url": "https://mcp.deepwiki.com/mcp",
            "require_approval": "never"
        }
    ],
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_text",
                    "text": "Check the documentation for the byteplus/ai-app-lab repository."
                }
            ]
        }
    ],
    extra_headers={"ark-beta-mcp": "true"},
    stream=True  # Stream results
)

# Print the streaming response results.
for chunk in response:
    if hasattr(chunk, 'delta'):
        print(chunk.delta, end="", flush=True)
```


<span id="8c903ca1"></span>
## Progressive migration

New models are currently adapted for both the Chat API and the Responses API, so you do not need to worry about future maintenance. We recommend gradually adopting the Responses API based on your needs. You can start by using it in some business scenarios such as tool calling and caching, and then fully replace the Chat API after it runs stably to achieve a smooth transition.
