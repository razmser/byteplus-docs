# Tool use (Responses API)

Responses API supports using ModelArk function calling to extend model capabilities, addressing limitations of large models in real\-time scenarios, complex workflow orchestration, and other issues, and enabling more accurate and efficient interactions and applications.

<span id="07456946"></span>
## Quick start

Using a weather query scenario as an example, this section describes how to use function call.


<Tabs>
<Tab zoneid="QJTs6HfHnm" title="Curl">
<TabTitle>Curl</TabTitle>

**Request of round 1: Trigger tool calling**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "seed-2-0-lite-260228",
    "store": true,
    "input": [
        {
            "type": "message",
            "role": "user",
            "content": "Query the weather in Beijing today"
        }
    ],
    "tools": [
        {
            "type": "function",
            "name": "get_weather",
            "description": "Query weather (including temperature and weather conditions) based on the city name",
            "parameters": {
                "type": "object",
                "properties": {
                    "location": {
                        "type": "string",
                        "description": "City name, e.g., Beijing, Shanghai (only domestic prefecture-level cities are supported)"
                    }
                },
                "required": ["location"]
            }
        }
    ]
  }'
```


**Response of round 1: Return the function_call instruction**

The model returns a tool call instruction. The key parameters are `call_id` (used to associate subsequent results) and `arguments` (call parameters):

```Plain Text
{
  "created_at": 1756980000,
  "id": "resp_02175698000123456789abcdef0123",
  "model": "seed-2-0-lite-260228",
  "object": "response",
  "output": [
    {
      "arguments": "{\"location\":\"Beijing\"}",
      "call_id": "call_abc123def456ghi789jkl0",
      "name": "get_weather",
      "type": "function_call",
      "id": "fc_02175698000abcdef0123456789gh",
      "status": "completed"
    }
  ],
  "status": "completed",
  "store": true,
  "expire_at": 1757239200
}
```


**Execute the tool: Get the weather query result**

The developer calls the actual weather tool (such as a third\-party weather API) based on `arguments`. Assume the returned result is:

```JSON
{
    "city": "Beijing",
    "date": "2025-10-13",
    "temperature": "18~28℃",
    "condition": "Sunny to cloudy",
    "wind": "Northeast wind level 2"
}
```


**Request of the second turn: pass back the results and generate the final response**

Pass in the `response_id` and tool call results of the previous round, and then the model generates a response in natural language:

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "seed-2-0-lite-260228",
    "previous_response_id": "resp_02175698000123456789abcdef0123",
    "input": [
      {
        "type": "function_call_output",
        "call_id": "call_abc123def456ghi789jkl0",
        "output": "{\"city\":\"Beijing\",\"date\":\"2025-10-13\",\"temperature\":\"18~28℃\",\"condition\":\"Sunny to cloudy\",\"wind\":\"Northeast wind level 2\"}"
      }
    ]
  }'
```


**Request of round 2: generate the final answer**

The model returns a natural language response based on the tool call results:

```Plain Text
{
    "created_at": 1762841326,
    "id": "resp_0217628413262947ed3dd5a26da08a73b6b7a2883108092b7ed59",
    "max_output_tokens": 32768,
    "model": "seed-1-6-250915",
    "object": "response",
    "output": [
        {
            "id": "rs_02176284132664900000000000000000000ffffc0a8a872ca6f75",
            "type": "reasoning",
            "summary": [
                {
                    "type": "summary_text",
                    "text": "\nGot it, let's see. The user asked for today's weather in Beijing. I first called the get_weather function with location Beijing, and now the tool returned the data: date 2025-10-13, temp 18~28℃, condition sunny to cloudy, wind northeast level 2. I need to present this clearly to the user. Make sure to mention all the key info in a friendly way. Let me structure that: start with confirming the date, then temperature range, weather condition, and wind. Yeah, that should cover it. Let me write that out."
                }
            ],
            "status": "completed"
        },
        {
            "type": "message",
            "role": "assistant",
            "content": [
                {
                    "type": "output_text",
                    "text": "Here is the weather information for Beijing on October 13, 2025:\n- Temperature range: 18℃ to 28℃\n- Weather condition: Sunny to cloudy\n- Wind condition: Northeast wind at level 2\n\nYou may adjust your outdoor activities and clothing according to the above weather conditions."
                }
            ],
            "status": "completed",
            "id": "msg_02176284132872400000000000000000000ffffc0a8a872597ded"
        }
    ],
    "previous_response_id": "resp_02176284098226874bd01b7bc1bacfcfc78546caab5b5fae6868e",
    "service_tier": "default",
    "status": "completed",
    "usage": {
        "input_tokens": 176,
        "output_tokens": 197,
        "total_tokens": 373,
        "input_tokens_details": {
            "cached_tokens": 0
        },
        "output_tokens_details": {
            "reasoning_tokens": 129
        }
    },
    "caching": {
        "type": "disabled"
    },
    "store": true,
    "expire_at": 1763100526
}
```



</Tab>
<Tab zoneid="rTktvQhEQ3" title="Python">
<TabTitle>Python</TabTitle>

```Python
import os
from arkruntime import Ark
import json

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')
client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# Define the tool (weather query tool)
weather_tool = [
    {
        "type": "function",
        "name": "get_weather",
        "description": "Query weather (including temperature and weather conditions) based on the city name",
        "parameters": {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string",
                    "description": "City name, e.g., Beijing, Shanghai (only domestic prefecture-level cities are supported)"
                }
            },
            "required": ["location"]
        }
    }
]

# ------------------- First Round Request: Trigger Tool Call -------------------
print("=== First Round Request: Trigger Tool Call ===")
first_response = client.responses.create(
    model="seed-2-0-lite-260228",
    store=True,
    input=[
        {
            "type": "message",
            "role": "user",
            "content": "Query the weather in Beijing today"
        }
    ],
    tools=weather_tool
)
# Extract key information (previous_response_id, call_id, arguments)
previous_response_id = first_response.id
function_call = next(
    item for item in first_response.output if item.type == "function_call"
)
call_id = function_call.call_id
call_arguments = function_call.arguments
print(f"First round response ID: {previous_response_id}")
print(f"Tool call ID: {call_id}")
print(f"Call parameters: {call_arguments}")

# ------------------- Simulate Tool Execution: Get Weather Results -------------------
print("=== Simulate Tool Execution: Get Weather Results ===")
# In real scenarios, call a third-party weather API here; we use mock data instead.
tool_output = {
    "city": "Beijing",
    "date": "2025-10-13",
    "temperature": "18~28℃",
    "condition": "Sunny to Cloudy",
    "wind": "Northeast wind level 2"
}
print(f"Tool return result: {tool_output}")

# ------------------- Second Round Request: Return Result and Generate Final Response -------------------
print("=== Second Round Request: Return Result and Generate Final Response ===")
second_response = client.responses.create(
    model="seed-2-0-lite-260228",
    previous_response_id=previous_response_id,
    input=[
        {
            "type": "function_call_output",
            "call_id": call_id,
            "output": json.dumps(tool_output, ensure_ascii=False)
        }
    ]
)
# Extract and print the final answer
final_answer = next(
    item for item in second_response.output if item.type == "message"
)
print("=== Final Answer  ===")
print(final_answer.content[0].text)
```



</Tab>
<Tab zoneid="OTY6MeumxY" title="Go">
<TabTitle>Go</TabTitle>

```Go
package main

import (
    "context"
    "encoding/json"
    "fmt"
    "os"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/responses"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    tool := weatherTool()
    response, err := client.CreateResponses(context.Background(), &responses.ResponsesRequest{
        Model: "seed-2-0-lite-260228",
        Input: responses.NewStringResponsesInput("What is the weather in Beijing today?"),
        Store: responses.NewOptBool(true),
        Tools: []responses.Tool{tool},
    })
    if err != nil {
        fmt.Printf("response error: %v\n", err)
        return
    }

    call, ok := functionCall(response.Output)
    if !ok {
        fmt.Println(response)
        return
    }
    fmt.Printf(
        "response_id=%s call_id=%s arguments=%s\n",
        response.ID,
        call.CallID.Or(""),
        call.Arguments.Or(""),
    )

    toolOutput, err := json.Marshal(map[string]string{
        "city": "Beijing", "condition": "Sunny to cloudy", "temperature": "18-28 C",
    })
    if err != nil {
        panic(err)
    }
    second, err := client.CreateResponses(context.Background(), &responses.ResponsesRequest{
        Model:              "seed-2-0-lite-260228",
        PreviousResponseID: responses.NewOptString(response.ID),
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            {
                OneOf: responses.NewItemFunctionToolCallOutputInputItemSum(
                    responses.ItemFunctionToolCallOutput{
                        Type:   responses.ItemFunctionToolCallOutputTypeFunctionCallOutput,
                        CallID: responses.NewOptString(call.CallID.Or("")),
                        Output: responses.NewOptMessageContent(
                            responses.NewStringMessageContent(string(toolOutput)),
                        ),
                    },
                ),
            },
        }),
    })
    if err != nil {
        fmt.Printf("response error: %v\n", err)
        return
    }
    fmt.Println(second)
}

func weatherTool() responses.Tool {
    var parameters responses.FunctionToolParameters
    schema := `{"type":"object","properties":{"location":{"type":"string","description":"City name, for example Beijing"}},"required":["location"]}`
    if err := json.Unmarshal([]byte(schema), &parameters); err != nil {
        panic(err)
    }
    return responses.Tool{
        OneOf: responses.NewFunctionToolToolSum(responses.FunctionTool{
            Name: "get_weather",
            Type: responses.FunctionToolTypeFunction,
            Description: responses.NewOptString("Get current weather information for a city"),
            Parameters: responses.NewOptFunctionToolParameters(parameters),
        }),
    }
}

func functionCall(items []responses.OutputItem) (responses.ItemFunctionToolCall, bool) {
    for _, item := range items {
        if call, ok := item.OneOf.GetItemFunctionToolCall(); ok {
            return call, true
        }
    }
    return responses.ItemFunctionToolCall{}, false
}
```



</Tab>
<Tab zoneid="SyibZ6dMYE" title="Java">
<TabTitle>Java</TabTitle>

```Java
package com.ark.example;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.volcengine.ark.runtime.models.responses.*;
import com.volcengine.ark.runtime.service.ArkService;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.JsonNode;
import java.util.*;

public class demo {
    public static ObjectMapper om = new ObjectMapper();
    public static List<Tool> buildTools() {
        JsonNode params = om.createObjectNode()
                .put("type", "object")
                .set("properties", om.createObjectNode()
                        .set("location", om.createObjectNode()
                                .put("type", "string")

                                .put("description", "City name, e.g., Beijing, Shanghai (only domestic prefecture-level cities are supported)")));

        FunctionTool t = FunctionTool.builder()
                .name("get_weather")

                .description("Query weather (including temperature and weather conditions) based on the city name")
                .parameters(om.convertValue(params, Map.class))
                .build();

        return Arrays.asList(t);
    }
    public static void main(String[] args) throws JsonProcessingException {
        String apiKey = System.getenv("ARK_API_KEY");

        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();
        System.out.println("=== First Round Request: Trigger Tool Call ===");
        ResponsesRequest req = ResponsesRequest.builder()
                .model("seed-2-0-lite-260228")
                .store(true)
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.USER).content(
                                MessageContent.ofList(java.util.Arrays.asList(ContentItemText.builder().text("Query the weather in Beijing today").build()))
                        ).build())))
                .tools(buildTools())
                .build();
        Response resp = arkService.createResponse(req);
        System.out.println(resp);
// # Extract key information (previous_response_id, call_id, arguments)
        String previousId = resp.getId();
        OutputItem targetCall = null;
        for(OutputItem item : resp.getOutput()) {
            if (item.getType() == ItemType.FUNCTION_CALL) {
                targetCall = item;
                break;
            }
        }
        ObjectMapper objectMapper = new ObjectMapper();
        String jsonStr = objectMapper.writeValueAsString(targetCall);
        ItemFunctionToolCall functionCall = objectMapper.readValue(jsonStr, ItemFunctionToolCall.class);
        String callId = functionCall.getCallId();
        String callArguments = functionCall.getArguments();
        System.out.println("First Round Response ID:" + previousId);
        System.out.println("Tool Call ID:" + callId);
        System.out.println("Call Parameters:" + callArguments);

        System.out.println("=== Simulate Tool Execution: Get Weather Results  ===");
        Map<String, String> toolOutput = new HashMap<String, String>() {{

            put("date", "2025-10-13");
            put("temperature", "18~28℃");

            put("condition", "Sunny to Cloudy");

            put("wind", "Northeast wind level 2");
        }};

        System.out.println(toolOutput);

        System.out.println("=== Second Round Request: Return Result and Generate Final Response ===");
        ResponsesRequest secondReq = ResponsesRequest.builder()
                .model("seed-2-0-lite-260228")
                .previousResponseId(previousId)
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemFunctionToolCallOutput.builder().callId(callId).output(MessageContent.ofString(objectMapper.writeValueAsString(toolOutput))).build())))
                .tools(buildTools())
                .build();
        Response secondResp = arkService.createResponse(secondReq);
        System.out.println("=== Final Answer ===");
        System.out.println(secondResp);
        arkService.shutdownExecutor();
    }
}
```



</Tab>
<Tab zoneid="hDiM2TxeEk" title="OpenAI SDK">
<TabTitle>OpenAI SDK</TabTitle>

```Python
import os
from openai import OpenAI
import json

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')
client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# Define the tool (weather query tool)
weather_tool = [
    {
        "type": "function",
        "name": "get_weather",
        "description": "Query weather (including temperature and weather conditions) based on the city name",
        "parameters": {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string",
                    "description": "City name, e.g., Beijing, Shanghai (only domestic prefecture-level cities are supported)"
                }
            },
            "required": ["location"]
        }
    }
]

# ------------------- First Round Request: Trigger Tool Call -------------------
print("=== First Round Request: Trigger Tool Call ===")
first_response = client.responses.create(
    model="seed-2-0-lite-260228",
    store=True,
    input=[
        {
            "type": "message",
            "role": "user",
            "content": "Query the weather in Beijing today"
        }
    ],
    tools=weather_tool
)
# Extract key information (previous_response_id, call_id, arguments)
previous_response_id = first_response.id
function_call = next(
    item for item in first_response.output if item.type == "function_call"
)
call_id = function_call.call_id
call_arguments = function_call.arguments
print(f"First round response ID: {previous_response_id}")
print(f"Tool call ID: {call_id}")
print(f"Call parameters: {call_arguments}")

# ------------------- Simulate Tool Execution: Get Weather Results -------------------
print("=== Simulate Tool Execution: Get Weather Results ===")
tool_output = {
    "city": "Beijing",
    "date": "2025-10-13",
    "temperature": "18~28℃",
    "condition": "Sunny to Cloudy",
    "wind": "Northeast wind level 2"
}
print(f"Tool return result: {tool_output}")

# ------------------- Second Round Request: Return Result and Generate Final Response-------------------
print("=== Second Round Request: Return Result and Generate Final Response ===")
second_response = client.responses.create(
    model="seed-2-0-lite-260228",
    previous_response_id=previous_response_id,
    input=[
        {
            "type": "function_call_output",
            "call_id": call_id,
            "output": json.dumps(tool_output, ensure_ascii=False)
        }
    ]
)
# Extract and print the final answer
final_answer = next(
    item for item in second_response.output if item.type == "message"
)
print("=== Final Answer ===")
print(final_answer.content[0].text)
```



</Tab>
</Tabs>
