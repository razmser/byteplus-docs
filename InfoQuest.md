# Enable real-time online search via Info Quest MCP integration

If you need to equip your AI model with real\-time internet search capabilities, integrating the Info Quest MCP service is an efficient solution. This document explains in detail how to configure and invoke Info Quest MCP through ModelArk's large model service, helping businesses quickly obtain accurate real\-time information.

<span id="19ac0961"></span>
## **Prerequisites**

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">If you're new to ModelArk, see <a href="https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1399008">Quick start</a> to get up and running quickly.</div>



* [Install and upgrade SDK](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1541595)

* Visit the [Info Quest console](https://console.byteplus.com/infoquest/infoquests?tab=mcp) to obtain MCP information:

   * `server_url`: defaults to `https://mcp.infoquest.bytepluses.com/mcp`

   * `Authorization` authorization information: as shown in the figure below.

   <span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/e36f997ff1f84c1bbd05e6108f54a4e4~tplv-goo7wpa0wc-image.image) </span>


<span id="9fe4d840"></span>
## **Procedure**

<span id="68204830"></span>
### **Configure** client information

When initializing the ModelArk client using the Python SDK, you need to configure the following key parameters:


* `base_url`: https://ark.ap\-southeast.bytepluses.com/api/v3

* `api_key`: see [Obtain and configure an API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1541594)


**Code sample**

```Python
client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,  # Replace with your API Key
)
```


<span id="e96ef07e"></span>
### **MCP invocation implementation**

When initiating a request through the `client.responses.create` method, you need to accurately configure model parameters, MCP tool information, and user input:


* `model`: select an available model on the platform (such as seed\-2\-0\-lite\-260228). For more models, see [Tool calling](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1330310#f44ceef7).

* `tools`: MCP tool configuration parameters, including:

   * `type`: fixed value `mcp`.

   * `server_label`: custom service identifier for easy identification and management.

   * `server_url`: MCP service address provided by Info Quest, defaults to `https://mcp.infoquest.bytepluses.com/mcp`.

   * `require_approval`: invocation approval policy (example: `never` means no approval required).

   * `headers`: the obtained Info Quest authentication information (such as the `Authorization` parameter), see [Prerequisites](InfoQuest.md#19ac0961).

* `input`: user input content (supports multimodal input such as text, the example is a query for the weather in London today).

* `extra_headers`: must contain `{"ark-beta-mcp": "true"}` to enable MCP invocation permission.


**Code sample**

```Python
# Initiate MCP invocation request
response = client.responses.create(
    model="seed-2-0-lite-260228",  # Replace with an actual available model
    tools=[
        {
            "type": "mcp",
            "server_label": "info-quest-mcp",  # Custom service identifier
            "server_url": "https://mcp.infoquest.bytepluses.com/mcp",  # MCP service address provided by Info Quest
            "require_approval": "never",  # Call directly without approval
            "headers": {
                "Authorization": "******"  # Replace with authentication information obtained from Info Quest
            }
        }
    ],
    input=[
        {
            "role": "user",
            "content": [{"type": "input_text", "text": "What is the weather like in London today? "}]  # User input
        }
    ],
    extra_headers={"ark-beta-mcp": "true"}
)
```


<span id="bd6f6709"></span>
### Complete code sample

```Python
from arkruntime import Ark
import os

# Retrieve the API key from environment variables (See https://docs.byteplus.com/en/docs/ModelArk/1399008 for details).
api_key = os.getenv('ARK_API_KEY')

# Initialize the client and enable the MCP
client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key
)

# Send a basic MCP call request
response = client.responses.create(
    model="seed-2-0-lite-260228",
    tools=[
        {
            "type": "mcp",
            "server_label": "info-quest-mcp",
            "server_url": "https://mcp.infoquest.bytepluses.com/mcp",
            "require_approval": "never",
            "headers": {
                "Authorization": "******" # Replace with authentication information provided by Info Quest
            }
        }
    ],
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_text",
                    "text": "What is the weather like in London today? "
                }
            ]
        }
    ],
    extra_headers={"ark-beta-mcp": "true"},
    stream=True  # Stream results
)

# Print the response
for chunk in response:
    if hasattr(chunk, 'delta'):
        print(chunk.delta, end="", flush=True)
```


<span id="9e0aacec"></span>
### Response

After the invocation is successful, you can directly extract the search results returned by Info Quest MCP (such as real\-time weather information) from the `response` object.

```text
... (Thinking process)...

Here is the weather information for London today (November 25, 2025) compiled from multiple reliable meteorological sources:
- Temperature range: Most sources indicate a low of 1°C to 3°C (34°F to 38°F) and a high of 6°C to 8°C (43°F to 46°F)
- Weather conditions: There are slight discrepancies among sources—some report sunny intervals, while others mention light rain showers, partly cloudy (morning), cloudy (afternoon) and clear sky (evening)
- Wind and other details: The wind speed is about 22 km/h (13.7 mph) with a southwest direction and a gust of up to 33 km/h (20.5 mph); the humidity is around 76%; sunrise is at 7:31 AM and sunset is at 4:02 PM
- Note: Some outdated posts (1 week to several months ago) mention torrential rain, snow or wind warnings, which do not apply to today's weather.
```


<span id="6166cea3"></span>
## **Summary**

Through the above steps, you can quickly integrate ModelArk with Info Quest MCP to equip your AI model with powerful real\-time internet search capabilities, and meet diverse and dynamic business needs.

If you encounter problems during configuration or invocation, you can submit a ticket through the platform at any time, or contact the Info Quest team for technical support.
