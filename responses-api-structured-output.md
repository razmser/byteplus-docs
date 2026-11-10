# Structured output (Responses API)

When you need the model to output a program\-processable standard format (mainly JSON) instead of natural language, you can enable structured output to support standardized processing or display.

By configuring the **text.format** object, you can demand the model to output in JSON format, and you can also define the JSON structure to limit the model's output parameters.

Compared with controlling the model to output in JSON format through prompts, using the structured output capability has the following advantages:


* Reliable output: The output structure conforms to the expected data type, including parameter hierarchy, name, type, order, etc., avoiding missing necessary parameters or generating hallucinated valid values.

* Easy to use: You can define the output format via API parameters, so you can use simpler prompts and you don't have to repeatedly emphasize or use strong wording in prompts.


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">This capability is still in the beta phase. Proceed with caution when using it in the production environment.</div>


<span id="0047487f"></span>
## Supported models

For large language models of version 250615 and later, the Responses API is supported by default unless otherwise specified. For more information, see [Structured output (beta)](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1330310#86588b72).

<span id="7503fb44"></span>
## API reference

[Responses API](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1569618)

<span id="607e37db"></span>
## json_object mode

This code sample shows how to use the Responses API to implement JSON object structured output.


<Tabs>
<Tab zoneid="Bi8YPRo3Af" title="Curl">
<TabTitle>Curl</TabTitle>

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "seed-2-0-lite-260228",
    "thinking": { "type": "disabled" },
    "text": {
      "format": {
        "type": "json_object"
      }
    },
    "input": [
      {
        "role": "system",
        "content": "You are a professional math teaching assistant. When receiving a user math problem, you need to output in a structured JSON format. The returned JSON should include the explanation and answer fields."
      },
      {
        "role": "user",
        "content": "What is the approximate value of the square root of three"
      }
    ]
  }'
```



</Tab>
<Tab zoneid="bK90rUweLj" title="Python">
<TabTitle>Python</TabTitle>

```Python
import os
from arkruntime import Ark

api_key = os.getenv('ARK_API_KEY')

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

response = client.responses.create(
    model="seed-2-0-lite-260228",
    input=[
        {"role": "system", "content":
         "You are a professional math teaching assistant. When receiving a user math problem, you need to output in a structured JSON format. The returned JSON should include the explanation and answer fields."
        },
        {"role": "user", "content": "What is the approximate value of the square root of three"}
    ],
    text={"format":{"type": "json_object"}}
)

print(response)
```



</Tab>
<Tab zoneid="pJHKfUakFq" title="Go">
<TabTitle>Go</TabTitle>

```Go
package main

import (
    "context"
    "fmt"
    "os"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/responses"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        // Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    ctx := context.Background()

    resp, err := client.CreateResponses(ctx, &responses.ResponsesRequest{
        Model: "seed-2-0-lite-260228",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            easyMessage(responses.MessageRoleSystem, "You are a professional math teaching assistant. When receiving a user math problem, you need to output in a structured JSON format. The returned JSON should include the explanation and answer fields."),
            easyMessage(responses.MessageRoleUser, "What is the approximate value of the square root of three"),
        }),
        Text: responses.NewOptResponseTextConfig(responses.ResponseTextConfig{Format: responses.TextFormat{Type: responses.TextFormatTypeJSONObject}}),
    })
    if err != nil {
        fmt.Printf("stream error: %v", err)
        return
    }
    fmt.Println(resp)
}

func easyMessage(role responses.MessageRole, content string) responses.InputItem {
    return responses.InputItem{
        OneOf: responses.NewItemEasyMessageInputItemSum(
            responses.ItemEasyMessage{
                Role:    responses.NewOptMessageRole(role),
                Content: responses.NewStringMessageContent(content),
            },
        ),
    }
}
```



</Tab>
<Tab zoneid="GvyAOUC2bP" title="Java">
<TabTitle>Java</TabTitle>

```Java
package com.ark.example;
import com.volcengine.ark.runtime.models.responses.*;
import com.volcengine.ark.runtime.service.ArkService;
public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        //The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("seed-2-0-lite-260228")
                .input(ResponsesInput.ofList(java.util.Arrays.asList(ItemEasyMessage.builder().role(MessageRole.SYSTEM).content(

                                MessageContent.ofString("You are a professional math teaching assistant. When receiving a user math problem, you need to output in a structured JSON format. The returned JSON should include the explanation and answer fields.")
                        ).build(), ItemEasyMessage.builder().role(MessageRole.USER).content(

                                MessageContent.ofString("What is the approximate value of the square root of three")
                        ).build())))
                .text(ResponseTextConfig.builder().format(TextFormat.builder().type(TextFormatType.JSON_OBJECT).build()).build())
                .build();
        Response resp = arkService.createResponse(request);
        System.out.println(resp);

        arkService.shutdownExecutor();
    }
}
```



</Tab>
<Tab zoneid="XMzZYmvfNt" title="OpenAI SDK">
<TabTitle>OpenAI SDK</TabTitle>

```Python
import os
from openai import OpenAI

api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

response = client.responses.create(
    model="seed-2-0-lite-260228",
    input=[
        {"role": "system", "content":
        "You are a professional math teaching assistant. When receiving a user math problem, you need to output in a structured JSON format. The returned JSON should include the explanation and answer fields."
        },
        {"role": "user", "content": "What is the approximate value of the square root of three"}
    ],
    text={"format":{"type": "json_object"}}
)

print(response)
```



</Tab>
</Tabs>


Response preview

```JSON
{
    "explanation": "The square root of three (√3) is an irrational number, meaning it cannot be expressed as a simple fraction and its decimal expansion is non-repeating and non-terminating. To find its approximate value, we can use methods such as long division, a calculator, or reference known approximations. A commonly used approximate value is rounded to two decimal places, which is 1.73. For more precision, it can be approximated to three decimal places as 1.732, or further to 1.73205 for five decimal places.",
    "answer": "Approximately 1.73 (rounded to two decimal places) or 1.732 (rounded to three decimal places)"
}
```


<span id="f4619f55"></span>
## json_schema mode

This code sample shows how to use the Responses API to implement JSON structured output that conforms to the schema parameter definition.


<Tabs>
<Tab zoneid="dgpfhbWodG" title="Curl">
<TabTitle>Curl</TabTitle>

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "seed-2-0-lite-260228",
    "input": [
        {
            "role": "user",
            "content": [
                {
                    "type": "input_text",
                    "text": "return in json format how can I solve 8x + 7 = -23"
                }
            ]
        }
    ],
    "thinking": {
        "type": "disabled"
    },
    "stream": false,
    "text": {
        "format": {
            "type": "json_schema",
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
            }
        }
    }
  }'
```



</Tab>
<Tab zoneid="XxBvcTmkZi" title="OpenAI SDK">
<TabTitle>OpenAI SDK</TabTitle>

```Python
import os
from openai import OpenAI
from pydantic import BaseModel
api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)
class Step(BaseModel):
    explanation: str  # Step details
    output: str       # Step computation results

class MathResponse(BaseModel):
    steps: list[Step]       # List of solution steps
    final_answer: str       # Final answer
response = client.responses.parse(
    model="seed-2-0-lite-260228",
    input=[
        {
            "role": "user",
            "content": [
                {
                    "type": "input_text",
                    "text": "return in json format how can I solve 8x + 7 = -23"
                }
            ]
        }
    ],
    text_format=MathResponse
)

print(response.output_parsed)
```



</Tab>
</Tabs>


Response preview

```JSON
{
    "steps": [
        {
            "explanation": "Subtract 7 from both sides to isolate the term with x",
            "output": "8x = -23 - 7"
        },
        {
            "explanation": "Simplify the right side",
            "output": "8x = -30"
        },
        {
            "explanation": "Divide both sides by 8 to solve for x",
            "output": "x = -30/8"
        },
        {
            "explanation": "Simplify the fraction",
            "output": "x = -15/4"
        }
    ],
    "final_answer": "x = -15/4"
}
```


<span id="f5afc902"></span>
## Mode comparison: `json_object` and `json_schema`

`json_schema` is an evolved version of `json_object`. Both modes support JSON structured output, and their specific similarities and differences are listed below.


<span aceTableMode="list" aceTableWidth="1,2,2"></span>
|Structured output |`json_schema` |`json_object` |
|---|---|---|
|Generate JSON response |Yes |Yes |
|Customizable JSON structure |Yes |No<br><br>Only guarantees valid JSON response |
|Recommended |Yes |No |
|Supported models |See [Structured output (beta)](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1330310#86588b72). |See [Structured output (beta)](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1330310#86588b72). |
|Strict mode |Supported<br><br>Takes effect by setting **strict** to `true`. |Not applicable |
|Configuration method |```JSON```<br>```"text": {```<br>```    "format": {```<br>```        "type": "json_schema",```<br>```        "name": "my_schema",```<br>```        "strict": true,```<br>```        "schema": {```<br>```            ...```<br>```        }```<br>```    }```<br>```}```<br> |```JSON```<br>```"text": {```<br>```  "format": {```<br>```    "type": "json_object"```<br>```  }```<br>```}```<br> |


<span id="b9885e72"></span>
## Limitations


* The QPS rate limits for the Responses API are as follows. If you need to increase the rate limit, submit [Ticket](https://console.byteplus.com/workorder/create?step=2&SubProductID=P00001514).



<span aceTableMode="list" aceTableWidth="1,1"></span>
|API name |QPS rate limits per account |
|---|---|
|Create model response |None |
|Query model response |20 |
|List input items |20 |
|Delete model response |20 |
