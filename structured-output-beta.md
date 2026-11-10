# Structured output (beta)

When you need the model to output in standard formats (mainly JSON format) like a program instead of natural language, you can enable structured output to support standardized processing or display.

By configuring the **response_format** object, you can demand the model to output in JSON format, and you can also define the JSON structure to limit the model's output parameters.

Compared with controlling the model to output in JSON format through prompts, using the structured output capability has the following advantages:

* Reliable output: The output structure always conforms to the expected data type, including parameter hierarchy, name, type, order, etc., avoiding missing necessary parameters or generating hallucinated valid values.

* Easy to use: You can define the output format via API parameters, so you can use simpler prompts and you don't have to repeatedly emphasize or use strong wording in prompts.

> **Tip**

> This capability is still in the beta phase. Proceed with caution when using it in the production environment.

# Supported models

See [Structured output (beta)](model-list.md#86588b72).

# API documentation

* [Chat API](chat-api.md): parameter explanation for structured output API. The examples in this document are Chat API examples.

* [Responses API](create-model-responses-api.md): new API for calling models. See [Structured output (beta)](responses-api-structured-output.md) for usage examples.

# Quick start

## json_schema mode

This code sample shows how to use the Chat API to implement JSON structured output that conforms to the schema parameter definition.

> For examples of implementing JSON structured output that conforms to the schema parameter definition using the Responses API, see [json_schema mode](responses-api-structured-output.md#f4619f55).

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "model": "dola-seed-2-1-turbo-260628",
  "messages": [
    {
      "role": "system",
      "content": "You are a math tutor."
    },
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "Solve the problem: 8x + 9 = 32 and x + y = 1"
        }
      ]
    }
  ],
  "response_format": {
    "type": "json_schema",
    "json_schema": {
      "name": "math_reasoning",
      "schema": {
        "type": "object",
        "properties": {
          "steps": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "explanation": {
                  "type": "string"
                },
                "output": {
                  "type": "string"
                }
              },
              "required": [
                "explanation",
                "output"
              ],
              "additionalProperties": false
            }
          },
          "final_answer": {
            "type": "string"
          }
        },
        "required": [
          "steps",
          "final_answer"
        ],
        "additionalProperties": false
      },
      "strict": true
    }
  },
  "thinking": {
    "type": "disabled"
  }
}'
```

You can control whether the model enables deep reasoning through the **thinking** parameter.

* `"disabled"`: Do not use deep reasoning.

* `"enabled"`: Force the use of deep reasoning.

**Python**

```Python
import os

from arkruntime import Ark
from pydantic import BaseModel  # Used to define the response parsing model

# Initialize the ModelArk SDK client
client = Ark(
    # Get ModelArk API Key from environment variables (you need to set the environment variables in advance)
    api_key=os.environ.get("ARK_API_KEY"),
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3"
)

# Define the step-by-step parsing model (corresponds to the structured response for business scenarios)
class Step(BaseModel):
    explanation: str  # Step description
    output: str       # Step calculation result

# Define the final response model (includes step-by-step process and final answer)
class MathResponse(BaseModel):
    steps: list[Step]       # List of problem-solving steps
    final_answer: str       # Final answer

# Call ModelArk model to generate response (automatically parsed to the specified model)
completion = client.beta.chat.completions.parse(
    model="dola-seed-2-1-turbo-260628",  # Replace the model with a real model
    messages=[
        {"role": "system", "content": "You are a math tutor, you need to show the problem-solving steps in detail"},
        {"role": "user", "content": "Solve the system of equations: 8x + 9 = 32 and x + y = 1"}
    ],
    response_format=MathResponse,  # Specify the response parsing model
    extra_body={
         "thinking": {
             "type": "disabled" # Do not use deep reasoning
            # "type": "enabled" # Use deep reasoning
         }
     }
)

# Extract the parsed structured response
resp = completion.choices[0].message.parsed

# Print the formatted JSON result
print(resp.model_dump_json(indent=2))
```

**Go**

```Go
package main

import (
    "context"
    "encoding/json"
    "fmt"
    "os"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/chat"
)

type Step struct {
    Explanation string `json:"explanation" jsonschema_description:"Step description"`
    Output      string `json:"output" jsonschema_description:"Step result"`
}

type MathResponse struct {
    Steps       []Step `json:"steps" jsonschema_description:"Solution steps"`
    FinalAnswer string `json:"final_answer" jsonschema_description:"Final answer"`
}

func mathResponseSchema() []byte {
    return []byte(`{
        "type": "object",
        "properties": {
            "steps": {
                "type": "array",
                "items": {
                    "type": "object",
                    "properties": {
                        "explanation": {"type": "string"},
                        "output": {"type": "string"}
                    },
                    "required": ["explanation", "output"],
                    "additionalProperties": false
                }
            },
            "final_answer": {"type": "string"}
        },
        "required": ["steps", "final_answer"],
        "additionalProperties": false
    }`)
}

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    response, err := client.CreateChatCompletion(
        context.Background(),
        &chat.ChatCompletionRequest{
            Model: "dola-seed-2-1-turbo-260628",
            Messages: []chat.ChatCompletionRequestMessage{
                systemMessage("You are a math tutor and need to show detailed solution steps."),
                userMessage("Solve the system of equations: 8x + 9 = 32 and x + y = 1"),
            },
            ResponseFormat: chat.NewOptChatCompletionResponseFormat(
                chat.ChatCompletionResponseFormat{
                    Type: chat.NewOptResponseFormatType(chat.ResponseFormatTypeJSONSchema),
                    JSONSchema: chat.NewOptChatCompletionResponseFormatJsonSchema(
                        chat.ChatCompletionResponseFormatJsonSchema{
                            Name:        "math_response",
                            Description: chat.NewOptString("Structured response for solving a math problem"),
                            Schema: mathResponseSchema(),
                            Strict: chat.NewOptBool(true),
                        },
                    ),
                },
            ),
            Thinking: chat.NewOptThinking(chat.Thinking{
                Type: chat.NewOptThinkingMode(chat.ThinkingModeEnabled),
            }),
        },
    )
    if err != nil {
        fmt.Printf("structured output chat error: %v\n", err)
        return
    }

    var result MathResponse
    if err := json.Unmarshal([]byte(response.Choices[0].Message.Content), &result); err != nil {
        panic(err)
    }
    pretty, _ := json.MarshalIndent(result, "", "  ")
    fmt.Println(string(pretty))
}

func systemMessage(content string) chat.ChatCompletionRequestMessage {
    return chat.ChatCompletionRequestMessage{
        OneOf: chat.NewChatCompletionRequestSystemMessageChatCompletionRequestMessageSum(
            chat.ChatCompletionRequestSystemMessage{
                Role:    chat.ChatCompletionRequestSystemMessageRoleSystem,
                Content: chat.NewStringChatCompletionMessageContent(content),
            },
        ),
    }
}

func userMessage(content string) chat.ChatCompletionRequestMessage {
    return chat.ChatCompletionRequestMessage{
        OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(
            chat.ChatCompletionRequestUserMessage{
                Role:    chat.ChatCompletionRequestUserMessageRoleUser,
                Content: chat.NewOptNilChatCompletionMessageContent(chat.NewStringChatCompletionMessageContent(content)),
            },
        ),
    }
}
```

**Java**

```Java
package com.example;

import com.volcengine.ark.runtime.models.chat.*;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;

public class ChatCompletionsStructuredOutputsExamplev4 {
    static String apiKey = System.getenv("ARK_API_KEY");
    static ArkService service = ArkService.builder()
            .connectionPool(new ConnectionPool(5, 1, TimeUnit.SECONDS))
            .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
            .dispatcher(new Dispatcher())
            .apiKey(apiKey)
            .build();

    public static void main(String[] args) throws JsonProcessingException {
        ObjectMapper mapper = new ObjectMapper();

        // Construct message list (includes system and user roles)
        List<ChatCompletionRequestMessage> messages = new ArrayList<>();
        messages.add(ChatCompletionRequestSystemMessage.builder()
                .role(ChatCompletionRequestMessageType.SYSTEM)
                .content(ChatCompletionMessageContent.ofString("You are a math tutor, you need to show the problem-solving steps in detail"))
                .build());
        messages.add(ChatCompletionRequestUserMessage.builder()
                .role(ChatCompletionRequestMessageType.USER)
                .content(ChatCompletionMessageContent.ofString("Solve the system of equations in Chinese: 8x + 9 = 32 and x + y = 1"))
                .build());

        // Build the JSON Schema with Java collections.
        java.util.Map<String, Object> stepProperties = new java.util.HashMap<>();
        stepProperties.put("explanation", java.util.Collections.singletonMap("type", "string"));
        stepProperties.put("output", java.util.Collections.singletonMap("type", "string"));
        java.util.Map<String, Object> stepSchema = new java.util.HashMap<>();
        stepSchema.put("type", "object");
        stepSchema.put("properties", stepProperties);
        java.util.Map<String, Object> properties = new java.util.HashMap<>();
        java.util.Map<String, Object> stepsSchema = new java.util.HashMap<>();
        stepsSchema.put("type", "array");
        stepsSchema.put("items", stepSchema);
        properties.put("steps", stepsSchema);
        properties.put("finalAnswer", java.util.Collections.singletonMap("type", "string"));
        java.util.Map<String, Object> schema = new java.util.HashMap<>();
        schema.put("type", "object");
        schema.put("properties", properties);

        ChatCompletionResponseFormat responseFormat = ChatCompletionResponseFormat.builder()
                .type(ResponseFormatType.JSON_SCHEMA)
                .jsonSchema(ChatCompletionResponseFormatJsonSchema.builder()
                        .name("math_response")
                        .description("Structured response for math problem solutions")
                        .schema(schema)
                        .strict(true)
                        .build())
                .build();

        // Construct request (includes thinking configuration)
        ChatCompletionRequest request = ChatCompletionRequest.builder()
                .model("dola-seed-2-1-turbo-260628") // Replace with the model actually used
                .messages(messages)
                .responseFormat(responseFormat)
                .thinking(Thinking.builder().type(ThinkingMode.DISABLED).build()) // turn off model deep reasoning capability
                .build();

        // Call the API and parse the response
        ChatCompletionResponse response = service.createChatCompletion(request);
        if (!response.getChoices().isEmpty()) {
            String content = String.valueOf(response.getChoices().get(0).getMessage().getContent());
            JsonNode jsonNode = mapper.readTree(content);
            // Print the formatted result
            System.out.println(mapper.writerWithDefaultPrettyPrinter().writeValueAsString(jsonNode));
        }

        service.shutdownExecutor();
    }
}
```

**OpenAI SDK**

```Python
from openai import OpenAI
import os
from pydantic import BaseModel

client = OpenAI(
    # Obtain the ModelArk API Key from environment variables
    api_key=os.environ.get("ARK_API_KEY"),
    base_url = "https://ark.ap-southeast.bytepluses.com/api/v3"
)

class Step(BaseModel):
    explanation: str
    output: str
class MathResponse(BaseModel):
    steps: list[Step]
    final_answer: str

completion = client.beta.chat.completions.parse(
    model = "dola-seed-2-1-turbo-260628",  # Replace with the model you need to use
    messages = [
        {"role": "system", "content": "You are a math tutor."},
        {"role": "user", "content": "Solve the system of equations in Chinese: 8x + 9 = 32 and x + y = 1"},
    ],
    response_format=MathResponse,
    extra_body={
         "thinking": {
             "type": "disabled" # Do not use deep reasoning ability
             # "type": "enabled" # Use deep reasoning ability
         }
     }
)
resp = completion.choices[0].message.parsed
# Print results in JSON format
print(resp.model_dump_json(indent=2))
```

Response preview

```JSON
{
  "steps": [
    {
      "explanation": "Solve the first equation 8x + 9 = 32, first subtract 9 from both sides of the equation to get 8x = 32 - 9",
      "output": "8x = 23"
    },
    {
      "explanation": "Then divide both sides of the equation by 8 to find the value of x",
      "output": "x = 23/8"
    },
    {
      "explanation": "Substitute x = 23/8 into the second equation x + y = 1, solve for y, that is y = 1 - x",
      "output": "y = 1 - 23/8"
    },
    {
      "explanation": "Calculate 1 - 23/8, get (8 - 23)/8 after finding a common denominator",
      "output": "y = -15/8"
    }
  ],
  "final_answer": "x = 23/8, y = -15/8"
}
```

## json_object mode

You need to include the string JSON in the input message, and configure the `"response_format":{"type": "json_object"}` parameter.

> For examples of implementing JSON Object structured output that conforms to the schema parameter definition using the Responses API, see [json_object mode](responses-api-structured-output.md#607e37db).

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "model": "dola-seed-2-1-turbo-260628",
  "messages": [
    {"role": "user", "content": "What are common cruciferous plants? Output in json"}
  ],
  "thinking": {
    "type": "disabled"
  },
  "response_format":{
    "type": "json_object"
  }
}'
```

You can control whether the model enables deep reasoning through the **thinking** parameter.

* `"disabled"`: Do not use deep reasoning.

* `"enabled"`: Force the use of deep reasoning.

**Python**

```Python
import os
# Install SDK: python -m pip install --upgrade arkruntime
from arkruntime import Ark

client = Ark(
    # The base URL for model invocation
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.getenv('ARK_API_KEY'),
)

completion = client.chat.completions.create(
    # Replace with Model ID
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {"role": "user", "content": "What are common cruciferous plants? Output in JSON"}
    ],
    response_format={"type":"json_object"},
    thinking={"type": "disabled"},# Do not use deep reasoning capabilities.
)

# Print original response content
print(completion.choices[0].message.content)
```

**Go**

```Go
package main

import (
    "context"
    "fmt"
    "os"
    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/chat"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
        )
    ctx := context.Background()

    // Construct request message
    messages := []chat.ChatCompletionRequestMessage{
userMessage("What are some common cruciferous plants? Output in JSON"),
    }

    // Construct request (includes thinking configuration)
    req := &chat.ChatCompletionRequest{
        Model:    "dola-seed-2-1-turbo-260628", //Replace with Model ID
        Messages: messages,
        ResponseFormat: chat.NewOptChatCompletionResponseFormat(chat.ChatCompletionResponseFormat{Type: chat.NewOptResponseFormatType(chat.ResponseFormatTypeJSONObject)}),
        Thinking: chat.NewOptThinking(chat.Thinking{Type: chat.NewOptThinkingMode(chat.ThinkingModeDisabled)}),
    }

    // Call API
    resp, err := client.CreateChatCompletion(ctx, req)
    if err != nil {
        fmt.Printf("chat error: %v\n", err)
        return
    }

    fmt.Println(resp.Choices[0].Message.Content)
}

func userMessage(content string) chat.ChatCompletionRequestMessage {
    return chat.ChatCompletionRequestMessage{
        OneOf: chat.NewChatCompletionRequestUserMessageChatCompletionRequestMessageSum(
            chat.ChatCompletionRequestUserMessage{
                Role: chat.ChatCompletionRequestUserMessageRoleUser,
                Content: chat.NewOptNilChatCompletionMessageContent(chat.NewStringChatCompletionMessageContent(content)),
            },
        ),
    }
}
```

**Java**

```Java
package com.example;

import com.volcengine.ark.runtime.models.chat.*;
import com.volcengine.ark.runtime.service.ArkService;

import java.util.ArrayList;
import java.util.List;

/**
 * This is a sample class that demonstrates how to use ArkService to implement chat functionality.
*/
public class ChatCompletionsExample {
  public static void main(String[] args) {
    // Get the API key from environment variables
    String apiKey = System.getenv("ARK_API_KEY");

    // Create an ArkService instance
    ArkService arkService = ArkService.builder().apiKey(apiKey)
            .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
            .build();

    // Initialize the message list.
    List<ChatCompletionRequestMessage> chatMessages = new ArrayList<>();

    // Create user message
    ChatCompletionRequestMessage userMessage = ChatCompletionRequestUserMessage.builder()
        .role(ChatCompletionRequestMessageType.USER) // Set the message role to user
        .content(ChatCompletionMessageContent.ofString("What are common cruciferous plants? Output in json")) // set message content
        .build();

    // Add user message to the message list
    chatMessages.add(userMessage);

    // Create chat completion request
    ChatCompletionRequest chatCompletionRequest = ChatCompletionRequest.builder()
        .model("dola-seed-2-1-turbo-260628")//Replace with Model ID
        .messages(chatMessages) // Set message list
        .responseFormat(ChatCompletionResponseFormat.builder().type(ResponseFormatType.JSON_OBJECT).build())
        .thinking(Thinking.builder().type(ThinkingMode.DISABLED).build())
        .build();

    // Send chat completion request and print response
    try {
      // Get response and print the message content of each choice
      arkService.createChatCompletion(chatCompletionRequest)
          .getChoices()
          .forEach(choice -> System.out.println(choice.getMessage().getContent()));
    } catch (Exception e) {
      System.out.println("Request failed: " + e.getMessage());
    } finally {
      // Shut down service executor
      arkService.shutdownExecutor();
    }
  }
}
```

**OpenAI SDK**

```Python
from openai import OpenAI
import os

# Initialize the client.
client = OpenAI(
    # Get ModelArk API Key from environment variables (environment variables must be set in advance)
    api_key=os.environ.get("ARK_API_KEY"),
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3"
)

# Call ModelArk model to generate a response
completion = client.chat.completions.create(
    model="dola-seed-2-1-turbo-260628",  #Replace with Model ID
    messages=[
        {"role": "user", "content": "What are common cruciferous plants? Output in json"}
    ],
    response_format={"type":"json_object"},
    extra_body={
         "thinking": {
             "type": "disabled" # Do not use deep reasoning capabilities
             # "type": "enabled" # Use deep reasoning skills
         }
     },
)

# Print original response content
print(completion.choices[0].message.content)
```

Response preview

```JSON
{
  "common_cruciferous_plants": [
    "Bokchoy",
    "Radish",
    "Rapeseed greens",
    "Cabbage",
    "Cauliflower",
    "Broccoli",
    "Mustard greens",
    "Pickled mustard tuber",
    "Potherb mustard",
    "Turnip",
    "Kale",
    "Shepherd's purse",
    "Orychophragmus violaceus (February orchid)",
    "Peppergrass",
    "Violet"
  ]
}
```

# Mode comparison: **`json_schema`** vs `json_object`

`json_schema` is an evolved version of `json_object`. Both modes support JSON structured output, and their specific similarities and differences are listed below.

> The `json_schema` feature is currently in beta testing, please evaluate it carefully before using it in a production environment.

|Structured output |`json_schema` |`json_object` |
|---|---|---|
|Generate JSON response |Yes |Yes |
|Customizable JSON structure |Yes |No<br><br>Only guarantees valid JSON response |
|Recommended |Yes |No |
|Supported models |See [Structured output (beta)](model-list.md#86588b72) |See [Structured output (beta)](model-list.md#86588b72) |
|Strict mode<br><br>> Generate responses strictly in accordance with the defined structure. |Supported<br><br>Takes effect by setting **strict** to `true`.<br><br><br>* See [JSON Schema syntax support](structured-output-beta.md#07ec5656) for syntax explanation. An explicit error will be displayed if an unsupported structure is provided. |Not applicable |
|Configuration method |...,<br><br>"response_format": {<br><br>"type": "json_schema",<br><br>"json_schema":{<br><br>"name":"my_schema",<br><br>"strict": true,<br><br>"schema": {...}<br><br>}<br><br>},<br><br>... |...,<br><br>"response_format": {<br><br>"type": "json_object"<br><br>},<br><br>... |

# Recommended usage steps

## 1. Define the structure

Define the JSON structure of the model response in the **schema** parameter. You can refer to the example in [Quick start](structured-output-beta.md#87d19412).

* The strict mode:

   \*Strict mode enabled (`strict: true`): The model outputs content strictly in accordance with the structure defined in **schema**. Keywords supported by ModelArk can be found in [Appendix 1. JSON Schema syntax support](structured-output-beta.md#07ec5656). ModelArk will display an explicit error if there is an obviously unsupported definition.

   * Strict mode disabled (`strict: false` or the **strict** parameter is not configured): The model outputs content with a valid JSON structure, prioritizes referencing the structure defined in **schema**, and will not validate syntax or throw errors.

* Parameter order: The model will output data according to the parameter order defined in **schema**. Please pay attention to the order of parameters at the same level.

> **Tip**

> Better generation quality can be achieved by designing JSON Schema and prompts. We strongly recommend that you read [Appendix 2. JSON Schema definition suggestions](structured-output-beta.md#3267c790) and [Appendix 3. Prompt suggestions](structured-output-beta.md#05849c36).

## 2. Configure JSON Schema in the API

Specify the structured output mode in the API

```JSON
...,
"response_format": {
  "type": "json_schema",
  "json_schema": {
    "name":"my_schema",
    "strict": true,
    "schema": {...}
  }
},
...
```

For complete code samples, see [Quick start](structured-output-beta.md#87d19412).

> **Tip**

> Do not use it together with sampling parameters such as **frequency_penalty** and **presence_penalty**, as this may cause abnormal model output.

## 3. Handle error cases

The model output structure may still contain errors due to output length limits, task complexity, unclear format, etc. You can try to adjust the instructions, or split the task into simpler subtasks.

# Appendix 1: JSON Schema syntax support

> **Tip**

* > The keywords are classified by the scope. See the full set of valid JSON Schema keywords at: https://json\-schema.org/understanding\-json\-schema/keywords

* > ModelArk supports the output format constraint semantics for the keywords listed below.

* > ModelArk will ignore keywords in the JSON Schema specification that have no format constraint semantics.

* > If you use explicitly unsupported keywords, ModelArk will throw an explicit error.

* > Do not use it together with sampling parameters such as **frequency_penalty** and **presence_penalty**, as this may cause abnormal model output.

## Public keywords at the Schema level

* [type](https://www.learnjsonschema.com/2020-12/validation/type/)

   * integer

   * number

   * string

   * boolean

   * null

   * array

   * object

* [$ref](https://www.learnjsonschema.com/2020-12/core/ref/)

   * Only local relative references starting with `#` are supported.

* [$defs](https://www.learnjsonschema.com/2020-12/core/defs/)

* [const](https://www.learnjsonschema.com/2020-12/validation/const/)

* [enum](https://www.learnjsonschema.com/2020-12/validation/enum/)

* [anyOf](https://www.learnjsonschema.com/2020-12/applicator/anyof/)

* [oneOf](https://www.learnjsonschema.com/2020-12/applicator/oneof/)

   * The exactly one semantics are not strictly guaranteed.

* [allOf](https://www.learnjsonschema.com/2020-12/applicator/allof/)

   * The all semantics are not strictly guaranteed

## Keywords related to type

* "type": "array"

   * [prefixItems](https://www.learnjsonschema.com/2020-12/applicator/prefixitems/)

   * [items](https://www.learnjsonschema.com/2020-12/applicator/items/)

   * [unevaluatedItems](https://www.learnjsonschema.com/2020-12/unevaluated/unevaluateditems/)

* "type": "object"

   * [properties](https://www.learnjsonschema.com/2020-12/applicator/properties/)

   * [required](https://www.learnjsonschema.com/2020-12/validation/required/)

   * [additionalProperties](https://www.learnjsonschema.com/2020-12/applicator/additionalproperties/)

   * [unevaluatedProperties](https://www.learnjsonschema.com/2020-12/unevaluated/unevaluatedproperties/)

# Appendix 2: JSON Schema definition suggestions

### Parameter naming and description

Parameters with vague names or without description make it difficult for the model to tell the meaning. Use clear and meaningful English names (such as user_name) together with `description` to explain the purpose of the parameter in detail.

Before

```JSON
{
  "type": "object",
  "properties": {
    "v1": {
      "type": "string"
    }
  }
}
```

After

```JSON
{
  "type": "object",
  "properties": {
    "user_name": {
      "type": "string",
      "description": "User name"
    }
  }
}
```

### Parameter type and structure design

#### Avoid redundant nesting and unnecessary complexity

Do not overuse $ref, expand the structure as much as possible at one time. Meaningless nesting will increase the difficulty of model generation and the probability of errors.

Before

```JSON
{
  "type": "object",
  "properties": {
    "date": {
      "type": "object",
      "properties": {
        "value": {
          "type": "string",
          "description": "Date"
        }
      }
    }
  }
}
```

After

```JSON
{
  "type": "object",
  "properties": {
    "date": {
      "type": "string",
      "description": "Date, in the format YYYY-MM-DD"
    }
  }
}
```

#### Define suitable parameter types and provide examples

> Note: The type should match the actual business scenario as closely as possible. Do not use string for numbers and booleans.

Before

```JSON
{
  "score": {
    "type": "string"
  }
}
```

After

```JSON
{
  "score": {
    "type": "integer",
    "description": "Score, an integer between 0 and 100"
  }
}
```

### Parameter value and constraint design

Clarify valid values and formats

Before

```JSON
{
  "status": {
    "type": "string"
  }
}
```

After

```JSON
{
  "status": {
    "type": "string",
    "description": "Processing status, valid values: pending, success or failed",
    "enum": ["pending", "success", "failed"]
  }
}
```

### Structure hierarchy and required parameters

Explicitly mark required parameters in all required structures, so that the model will always output all required parameters with a more standardized format.

When using required, it is recommended to always add `"additionalProperties": false`.

Before

```JSON
{
  "type": "object",
  "properties": {
    "steps": { "type": "array", "items": { "type": "string" } },
    "final_answer": { "type": "string" }
  }
  // No required is specified
}
```

After

```JSON
{
  "type": "object",
  "properties": {
    "steps": { "type": "array", "items": { "type": "string" } },
    "final_answer": { "type": "string" }
  },
  "required": ["steps", "final_answer"],
  "additionalProperties": false
}
```

### Business semantics should be concise and clear to avoid ambiguity

Before

```JSON
{
  "type": "object",
  "properties": {
    "id": { "type": "string", "description": "User or order ID" }
  }
}
```

After

```JSON
{
  "type": "object",
  "properties": {
    "user_id": { "type": "string", "description": "User ID" },
    "order_id": { "type": "string", "description": "Order ID" }
  }
}
```

## Use tools for evaluation and optimization

To avoid inconsistencies between JSON Schema and programming language type definitions, it is recommended to use native language tools, for example, [Pydantic](https://docs.pydantic.dev/latest/) for Python, and [Zod](https://zod.dev/) for TypeScript.

# Appendix 3. Prompt engineering tips

### Specify task objectives and express intent concisely

* Just directly describe the task you actually want the model to complete, no need to repeatedly emphasize instructions such as "Please output in JSON format" or "Please output in the following format".

* Do not repeat schema structure information in the prompt to avoid contradiction or redundancy.

Before

```Plaintext
Please output in the following JSON format, and include the parameters steps and final_answer: 8x + 9 = 32, x+y=1.
```

After

```Plaintext
Please solve: 8x + 9 = 32, x + y = 1.
```

### Write business content combined with structured information instead of format guidance

* Focus on the "content", not the "output format".

* The more specific the business description is, the easier it is for LLM to generate content that conforms to the schema.

Before

```Plaintext
Please output a JSON that includes the parameters steps and final_answer.
```

After

```Plaintext
Please solve step by step: 8x + 9 = 32, x+y=1, and write the final answer.
```
