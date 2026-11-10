# Streaming output

In real\-time conversations, long\-form content generation, or displaying the thinking process, waiting for the full response before showing results can leave users without any visible progress for a long time. Streaming output lets the client display generated content as it is received. If your application needs faster feedback, incremental processing, or to preserve results received before an interruption and mark them as incomplete, this topic helps you enable streaming output in your existing model call APIs, correctly determine terminal states, and manage connections.

> **Tip**

> If you're new to ModelArk, see [Quick start](quick-start.md) to get up and running quickly.

## Quick start

|Demo |Benefits |
|---|---|
|![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/advanced-usage/streaming-output-effect-preview-body-only-20261021.gif) |Show content earlier: rendering can start after the first chunk is received, without waiting for the full response. |
||Provide real\-time feedback: chat interfaces, long\-form content generation, and thinking processes can be updated continuously. |
||Support incremental processing: the client can separately aggregate the main content, thinking content, and tool parameters. |
||Preserve partial results: if the connection is interrupted before the terminal state, received content can be preserved but must be marked as incomplete. |

The timing in the animation is only used to demonstrate the return method and does not represent the actual time to first chunk or total generation time.

### Integrate with your existing API

Enabling streaming output does not require migrating APIs. Based on the API currently used by your application, add the streaming request parameter and adapt to the corresponding response events:

|Current API |Request change |Response handling |
|---|---|---|
|Chat API |Set `stream=true` in the request body. |Aggregate `delta` by `choices[].index` and determine the result based on `finish_reason`. For implementation details, see [Handle streaming responses](streaming-output.md#response-processing). |
|Responses API |Set `stream=true` in the request body; the Go SDK uses `CreateResponsesStream`. |Dispatch by event `type` and aggregate using composite indexes. For implementation details, see [Handle streaming responses](streaming-output.md#response-processing). |
|Messages API |Set `stream=true` in the request body. |Aggregate by message and content block lifecycle, and determine the result using both `stop_reason` and `message_stop`. For implementation details, see [Handle streaming responses](streaming-output.md#response-processing). |

Choose the API example that matches your existing application. The three types of APIs have different event structures and must not share event parsing branches. For a first try, you can directly run the Chat API cURL or Python example.

Before running the Python ModelArk SDK example, run `python -m pip install --upgrade arkruntime`; before running the Go ModelArk SDK example, run `go get github.com/volcengine/ark-runtime-go@latest`. The Messages Python REST example requires Python 3.9 or later and `requests` to be installed.

### Chat API

**cURL**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
    "messages": [
        {
            "role": "user",
            "content": "What are some common cruciferous plants?"
        }
    ],
    "stream": true
  }'
```

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
    model="dola-seed-2-1-turbo-260628",
    messages=[
        {"role": "user", "content": "What are common cruciferous plants?"},
    ],
    stream=True,
)

# Ensure the connection is closed automatically to prevent connection leaks.
with completion:
    for chunk in completion:
        if chunk.choices[0].delta.content is not None:
            print(chunk.choices[0].delta.content, end="")
```

**Go**

```Go
package main

import (
    "github.com/volcengine/ark-runtime-go/arkruntime"
    "github.com/volcengine/ark-runtime-go/arkruntime/model/chat"
    "context"
    "fmt"
    "io"
    "os"
)

func main() {
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        // The base URL for model invocation
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )

    ctx := context.Background()

    fmt.Println("----- standard request -----")
    req := &chat.ChatCompletionRequest{
        // Replace with Model ID
        Model: "dola-seed-2-1-turbo-260628",
        Messages: []chat.ChatCompletionRequestMessage{
            userMessage("What are some common cruciferous plants?"),
        },
        Stream: chat.NewOptBool(true),
    }

    // Use CreateChatCompletionStream; the non-streaming method cannot yield chunks.
    resp, err := client.CreateChatCompletionStream(ctx, req)
    if err != nil {
        fmt.Printf("standard chat error: %v", err)
        return
    }

    defer resp.Close()
    for {
        chunk, err := resp.Recv()
        if err == io.EOF {
            break
        }
        if err != nil {
            fmt.Printf("stream error: %v", err)
            break
        }
        fmt.Print(chunk.Choices[0].Delta.Content.Or(""))
    }
    fmt.Println()
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
package com.volcengine.ark.runtime;

import com.volcengine.ark.runtime.models.chat.*;
import com.volcengine.ark.runtime.service.ArkService;
import java.util.ArrayList;
import java.util.List;

public class ChatCompletionsExample {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        // The base URL for model invocation
        ArkService service = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();
        final List<ChatCompletionRequestMessage> messages = new ArrayList<>();
        final ChatCompletionRequestMessage userMessage = ChatCompletionRequestUserMessage.builder().role(ChatCompletionRequestMessageType.USER).content(ChatCompletionMessageContent.ofString("What are common cruciferous plants?")).build();
        messages.add(userMessage);

        ChatCompletionRequest chatCompletionRequest = ChatCompletionRequest.builder()
               .model("dola-seed-2-1-turbo-260628") // Replace with Model ID
               .messages(messages)
               .stream(true)
               .thinking(Thinking.builder().type(ThinkingMode.DISABLED).build())
               .build();
        try {
            service.streamChatCompletion(chatCompletionRequest)
                    .doOnError(Throwable::printStackTrace) // Print stream errors.
                    .blockingForEach(response -> {
                        if (response.getChoices() != null && !response.getChoices().isEmpty()) {
                            Object content = response.getChoices().get(0).getDelta().getContent();
                            if (content != null) {
                                System.out.print(content); // Keep chunks contiguous.
                            }
                        }
                    });
        } finally {
            service.shutdownExecutor();
        }
    }
}
```

**OpenAI SDK**

```Python
import os
from openai import OpenAI

client = OpenAI(
    # Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
    api_key=os.environ.get("ARK_API_KEY"),
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
    )

completion = client.chat.completions.create(
    #Replace with Model ID
    model = "dola-seed-2-1-turbo-260628",
    messages=[
        {"role": "user", "content": "What are some common cruciferous plants?"},
    ],
    stream=True,
)

# Ensure the connection is closed automatically to prevent connection leaks.
with completion:
    for chunk in completion:
        if chunk.choices[0].delta.content is not None:
            print(chunk.choices[0].delta.content, end="")
```

### Responses API

The following Go example calls the dedicated streaming method `CreateResponsesStream`, which sends the request in streaming mode, so you do not need to set `stream=true` again in the request object.

**cURL**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/responses \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "model": "dola-seed-2-1-turbo-260628",
      "input": "What are the common cruciferous plants?",
      "stream": true
  }'
```

**Python**

```Python
import os
from arkruntime import Ark
from arkruntime.types.responses.response_completed_event import ResponseCompletedEvent
from arkruntime.types.responses.response_reasoning_summary_text_delta_event import ResponseReasoningSummaryTextDeltaEvent
from arkruntime.types.responses.response_output_item_added_event import ResponseOutputItemAddedEvent
from arkruntime.types.responses.response_text_delta_event import ResponseTextDeltaEvent
from arkruntime.types.responses.response_text_done_event import ResponseTextDoneEvent

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')

client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# The context manager closes the stream on normal completion and exceptions.
with client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="What are the common cruciferous plants?",
    stream=True,
) as response:
    for event in response:
        if isinstance(event, ResponseReasoningSummaryTextDeltaEvent):
            print(event.delta, end="")
        if isinstance(event, ResponseOutputItemAddedEvent):
            print("\nOutput item " + event.type + " start:")
        if isinstance(event, ResponseTextDeltaEvent):
            print(event.delta, end="")
        if isinstance(event, ResponseTextDoneEvent):
            print("\nOutput text done.")
        if isinstance(event, ResponseCompletedEvent):
            print("Response Completed. Usage = " + event.response.usage.model_dump_json())
```

**Go**

```Go
package main

import (
    "context"
    "fmt"
    "io"
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

    resp, err := client.CreateResponsesStream(ctx, &responses.ResponsesRequest{
        Model:    "dola-seed-2-1-turbo-260628",
        Input:    responses.NewStringResponsesInput("What are the common cruciferous plants?"),

    })
    if err != nil {
        fmt.Printf("stream error: %v", err)
        return
    }
    defer resp.Close()
    for {
        event, err := resp.Recv()
        if err == io.EOF {
            break
        }
        if err != nil {
            fmt.Printf("stream error: %v", err)
            return
        }
        handleEvent(event)
    }
}
func handleEvent(event *responses.ResponseStreamEvent) {
    switch event.OneOf.Type {
    case responses.ResponseReasoningSummaryTextDeltaEventResponseStreamEventSum:
        print(event.OneOf.ResponseReasoningSummaryTextDeltaEvent.Delta.Or(""))
    case responses.ResponseReasoningSummaryTextDoneEventResponseStreamEventSum:
        fmt.Printf("\nAggregated reasoning text: %s\n", event.OneOf.ResponseReasoningSummaryTextDoneEvent.Text.Or(""))
    case responses.ResponseTextDeltaEventResponseStreamEventSum:
        print(event.OneOf.ResponseTextDeltaEvent.Delta.Or(""))
    case responses.ResponseTextDoneEventResponseStreamEventSum:
        fmt.Printf("\nAggregated output text: %s\n", event.OneOf.ResponseTextDoneEvent.Text.Or(""))
    case responses.ResponseCompletedEventResponseStreamEventSum:
        fmt.Println("\nResponse completed.")
    case responses.ResponseIncompleteEventResponseStreamEventSum:
        fmt.Println("\nResponse incomplete; inspect incomplete details before retrying.")
    case responses.ResponseFailedEventResponseStreamEventSum:
        fmt.Println("\nResponse failed; inspect the response error before retrying.")
    case responses.ResponseErrorEventResponseStreamEventSum:
        fmt.Println("\nStream error event received.")
    }
}
```

**Java**

```Java
package com.ark.example;
import com.volcengine.ark.runtime.service.ArkService;
import com.volcengine.ark.runtime.models.responses.*;
public class demo {
    public static void main(String[] args) {
        String apiKey = System.getenv("ARK_API_KEY");
        // The base URL for model invocation
        ArkService arkService = ArkService.builder().apiKey(apiKey)
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();

        ResponsesRequest request = ResponsesRequest.builder()
                .model("dola-seed-2-1-turbo-260628")
                .stream(true)
                .input(ResponsesInput.ofString("What are the common cruciferous plants?"))

                .build();
        try {
            arkService.streamResponse(request)
                    .doOnError(Throwable::printStackTrace)
                    .blockingForEach(event -> {
                        if (event instanceof ResponseReasoningSummaryTextDeltaEvent) {
                            System.out.print(((ResponseReasoningSummaryTextDeltaEvent) event).getDelta());
                        }
                        if (event instanceof ResponseOutputItemAddedEvent) {
                            System.out.println("OutputItem " + (((ResponseOutputItemAddedEvent) event).getItem().getType()) + " Start: ");
                        }
                        if (event instanceof ResponseTextDeltaEvent) {
                            System.out.print(((ResponseTextDeltaEvent) event).getDelta());
                        }
                        if (event instanceof ResponseTextDoneEvent) {
                            System.out.println("OutputText End.");
                        }
                        if (event instanceof ResponseOutputItemDoneEvent) {
                            System.out.println("OutputItem " + ((ResponseOutputItemDoneEvent) event).getItem().getType() + " End.");
                        }
                        if (event instanceof ResponseFunctionCallArgumentsDoneEvent) {
                            System.out.println("FunctionCall Arguments: " + ((ResponseFunctionCallArgumentsDoneEvent) event).getArguments());
                        }
                        if (event instanceof ResponseCompletedEvent) {
                            System.out.println("Response Completed. Usage = " + ((ResponseCompletedEvent) event).getResponse().getUsage());
                        }
                    });
        } finally {
            arkService.shutdownExecutor();
        }
    }
}
```

**OpenAI SDK**

```Python
import os
from openai import OpenAI

# Get API Key: https://ai.byteplus.com/ark/region:ap-southeast-1/apikey
api_key = os.getenv('ARK_API_KEY')

client = OpenAI(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key,
)

# The context manager closes the stream on normal completion and exceptions.
with client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    input="What are the common cruciferous plants?",
    stream=True
) as response:
    for event in response:
        if event.type == "response.reasoning_summary_text.delta":
            print(event.delta, end="")
        if event.type == "response.output_item.added":
            print("\nOutput item " + event.type + " start:")
        if event.type == "response.output_text.delta":
            print(event.delta, end="")
        if event.type == "response.output_item.done":
            print("\nOutput text done.")
        if event.type == "response.completed":
            print("\nResponse Completed. Usage = " + event.response.usage.model_dump_json())
```

### Messages API

**cURL**

```Bash
curl -N https://ark.ap-southeast.bytepluses.com/api/compatible/v1/messages \
  -H "Content-Type: application/json" \
  -H "x-api-key: $ARK_API_KEY" \
  -d '{
    "model": "dola-seed-2-1-turbo-260628",
    "max_tokens": 256,
    "stream": true,
    "messages": [
      {
        "role": "user",
        "content": "Introduce what a vector database is in one sentence."
      }
    ]
  }'
```

**Python**

```Python
import json
import os

import requests

buffers = {}
block_types = {}

with requests.post(
    "https://ark.ap-southeast.bytepluses.com/api/compatible/v1/messages",
    headers={
        "Content-Type": "application/json",
        "x-api-key": os.environ["ARK_API_KEY"],
    },
    json={
        "model": "dola-seed-2-1-turbo-260628",
        "max_tokens": 256,
        "stream": True,
        "messages": [
            {"role": "user", "content": "Please explain what a vector database is in one sentence."}
        ],
    },
    stream=True,
    timeout=1800,
) as response:
    response.raise_for_status()

    for line in response.iter_lines(decode_unicode=True):
        if not line or not line.startswith("data: "):
            continue
        event = json.loads(line.removeprefix("data: "))
        event_type = event.get("type")

        if event_type == "content_block_start":
            index = event["index"]
            block_types[index] = event.get("content_block", {}).get("type", "")
            buffers[index] = {
                "text": [],
                "thinking": [],
                "signature": [],
                "partial_json": [],
            }
        elif event_type == "content_block_delta":
            index = event["index"]
            delta = event.get("delta", {})
            delta_type = delta.get("type")
            field = {
                "text_delta": "text",
                "thinking_delta": "thinking",
                "signature_delta": "signature",
                "input_json_delta": "partial_json",
            }.get(delta_type)
            if field:
                fragment = delta.get(field, "")
                state = buffers.setdefault(
                    index,
                    {"text": [], "thinking": [], "signature": [], "partial_json": []},
                )
                state[field].append(fragment)
                if delta_type == "text_delta":
                    print(fragment, end="", flush=True)
        elif event_type == "content_block_stop":
            index = event["index"]
            state = buffers.get(index, {})
            partial_json = "".join(state.get("partial_json", []))
            if block_types.get(index) == "tool_use" and partial_json:
                print(f"\nTool arguments: {json.loads(partial_json)}")
        elif event_type == "message_delta":
            stop_reason = event.get("delta", {}).get("stop_reason")
            if stop_reason:
                print(f"\nStop reason: {stop_reason}")
        elif event_type == "error":
            raise RuntimeError(json.dumps(event.get("error", {}), ensure_ascii=False))
        elif event_type == "message_stop":
            break
```

**Go**

```Go
package main

import (
    "bufio"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
    "strings"
)

type streamEvent struct {
    Type         string       `json:"type"`
    Index        int          `json:"index"`
    ContentBlock contentBlock `json:"content_block"`
    Delta        delta        `json:"delta"`
    Error        any          `json:"error"`
}

type contentBlock struct {
    Type string `json:"type"`
}

type delta struct {
    Type        string `json:"type"`
    Text        string `json:"text"`
    Thinking    string `json:"thinking"`
    Signature   string `json:"signature"`
    PartialJSON string `json:"partial_json"`
    StopReason  string `json:"stop_reason"`
}

type blockState struct {
    BlockType   string
    Text        strings.Builder
    Thinking    strings.Builder
    Signature   strings.Builder
    PartialJSON strings.Builder
}

func main() {
    body := `{"model":"dola-seed-2-1-turbo-260628","max_tokens":256,"stream":true,"messages":[{"role":"user","content":"Please explain what a vector database is in one sentence."}]}`
    req, err := http.NewRequest("POST", "https://ark.ap-southeast.bytepluses.com/api/compatible/v1/messages", strings.NewReader(body))
    if err != nil {
        log.Fatal(err)
    }
    req.Header.Set("Content-Type", "application/json")
    req.Header.Set("x-api-key", os.Getenv("ARK_API_KEY"))

    resp, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }
    defer resp.Body.Close()
    if resp.StatusCode < 200 || resp.StatusCode >= 300 {
        log.Fatalf("request failed: %s", resp.Status)
    }

    scanner := bufio.NewScanner(resp.Body)
    blocks := make(map[int]*blockState)
    for scanner.Scan() {
        line := scanner.Text()
        if ! strings.HasPrefix(line, "data: ") {
            continue
        }

        var event streamEvent
        if err := json.Unmarshal([]byte(strings.TrimPrefix(line, "data: ")), &event); err != nil {
            log.Fatal(err)
        }
        switch event.Type {
        case "content_block_start":
            blocks[event.Index] = &blockState{BlockType: event.ContentBlock.Type}
        case "content_block_delta":
            state := blocks[event.Index]
            if state == nil {
                state = &blockState{}
                blocks[event.Index] = state
            }
            switch event.Delta.Type {
            case "text_delta":
                state.Text.WriteString(event.Delta.Text)
                fmt.Print(event.Delta.Text)
            case "thinking_delta":
                state.Thinking.WriteString(event.Delta.Thinking)
            case "signature_delta":
                state.Signature.WriteString(event.Delta.Signature)
            case "input_json_delta":
                state.PartialJSON.WriteString(event.Delta.PartialJSON)
            }
        case "content_block_stop":
            state := blocks[event.Index]
            if state != nil && state.BlockType == "tool_use" && state.PartialJSON.Len() > 0 {
                var arguments any
                if err := json.Unmarshal([]byte(state.PartialJSON.String()), &arguments); err != nil {
                    log.Fatal(err)
                }
                fmt.Printf("\nTool arguments: %v\n", arguments)
            }
        case "message_delta":
            if event.Delta.StopReason != "" {
                fmt.Printf("\nStop reason: %s\n", event.Delta.StopReason)
            }
        case "error":
            log.Fatalf("stream error: %v", event.Error)
        case "message_stop":
            return
        }
    }
    if err := scanner.Err(); err != nil {
        log.Fatal(err)
    }
}
```

**Java**

```Java
import com.volcengine.ark.runtime.service.ArkService;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import okhttp3.MediaType;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.RequestBody;
import okhttp3.Response;

public class MessagesStreamingExample {
    private static class BlockState {
        private String blockType = "";
        private final StringBuilder text = new StringBuilder();
        private final StringBuilder thinking = new StringBuilder();
        private final StringBuilder signature = new StringBuilder();
        private final StringBuilder partialJson = new StringBuilder();
    }

    public static void main(String[] args) throws IOException {
        OkHttpClient client = ArkService.defaultApiKeyClient(
                System.getenv("ARK_API_KEY"), ArkService.DEFAULT_TIMEOUT);
        ObjectMapper mapper = ArkService.defaultObjectMapper();
        Map<Integer, BlockState> blocks = new HashMap<>();
        String json = "{\"model\":\"dola-seed-2-1-turbo-260628\",\"max_tokens\":256,"
                + "\"stream\":true,\"messages\":[{\"role\":\"user\","
                + "\"content\":\"Please explain what a vector database is in one sentence.\"}]}";
        Request request = new Request.Builder()
                .url("https://ark.ap-southeast.bytepluses.com/api/compatible/v1/messages")
                .header("Content-Type", "application/json")
                .header("x-api-key", System.getenv("ARK_API_KEY"))
                .post(RequestBody.create(MediaType.parse("application/json"), json))
                .build();
        try (Response response = client.newCall(request).execute()) {
            if (!response.isSuccessful() || response.body() == null) {
                throw new IOException("Request failed: " + response.code());
            }
            String line;
            while ((line = response.body().source().readUtf8Line()) != null) {
                if (!line.startsWith("data: ")) {
                    continue;
                }
                JsonNode event = mapper.readTree(line.substring("data: ".length()));
                String eventType = event.path("type").asText();
                int index = event.path("index").asInt(-1);

                if ("content_block_start".equals(eventType)) {
                    BlockState state = new BlockState();
                    state.blockType = event.path("content_block").path("type").asText();
                    blocks.put(index, state);
                } else if ("content_block_delta".equals(eventType)) {
                    JsonNode delta = event.path("delta");
                    String deltaType = delta.path("type").asText();
                    BlockState state = blocks.computeIfAbsent(index, ignored -> new BlockState());
                    if ("text_delta".equals(deltaType)) {
                        String fragment = delta.path("text").asText();
                        state.text.append(fragment);
                        System.out.print(fragment);
                    } else if ("thinking_delta".equals(deltaType)) {
                        state.thinking.append(delta.path("thinking").asText());
                    } else if ("signature_delta".equals(deltaType)) {
                        state.signature.append(delta.path("signature").asText());
                    } else if ("input_json_delta".equals(deltaType)) {
                        state.partialJson.append(delta.path("partial_json").asText());
                    }
                } else if ("content_block_stop".equals(eventType)) {
                    BlockState state = blocks.get(index);
                    if (state != null && "tool_use".equals(state.blockType)
                            && state.partialJson.length() > 0) {
                        JsonNode arguments = mapper.readTree(state.partialJson.toString());
                        System.out.println("\nTool arguments: " + arguments);
                    }
                } else if ("message_delta".equals(eventType)) {
                    String stopReason = event.path("delta").path("stop_reason").asText();
                    if (!stopReason.isEmpty()) {
                        System.out.println("\nStop reason: " + stopReason);
                    }
                } else if ("error".equals(eventType)) {
                    throw new IOException("Stream error: " + event.path("error"));
                } else if ("message_stop".equals(eventType)) {
                    break;
                }
            }
        } finally {
            client.dispatcher().executorService().shutdown();
            client.connectionPool().evictAll();
        }
    }
}
```

The Chat and Responses examples preferentially reuse the ModelArk SDK or a compatible SDK. The current ModelArk SDK does not provide a unified native Messages resource method, so the Messages examples use REST; the Java example reuses the API key HTTP client from the ModelArk SDK. The three types of APIs use different event structures and must not share event parsing branches.

## Supported models

Streaming output applies to LLM that can be called through the Chat API, Responses API, or Messages API. The model ID in the code is only an example value and does not constitute a streaming output allowlist. If a model has API\-specific or streaming\-specific limits, see the [Model list](model-list.md) and the corresponding API reference.

## Configure streaming requests

The examples in quick start already include the `stream=true` required to enable streaming output. When streaming output is combined with deep reasoning or tool calls, the corresponding capability parameters must also be configured in the same request body.

### Combined with deep reasoning

**Chat API**

```json
{
  "model": "dola-seed-2-1-turbo-260628",
  "messages": [{"role": "user", "content": "Prove that the sum of two odd numbers is even."}],
  "thinking": {"type": "enabled"},
  "stream": true
}
```

**Responses API**

```json
{
  "model": "dola-seed-2-1-turbo-260628",
  "input": "Prove that the sum of two odd numbers is even.",
  "thinking": {"type": "enabled"},
  "stream": true
}
```

**Messages API**

```json
{
  "model": "dola-seed-2-1-turbo-260628",
  "max_tokens": 1024,
  "thinking": {"type": "enabled"},
  "messages": [{"role": "user", "content": "Prove that the sum of two odd numbers is even."}],
  "stream": true
}
```

The request structures for the three API types cannot be mixed. For how reasoning content and the main text are separated, see [Response examples and parsing](streaming-output.md#response-fields).

### Combined with tool calls

**Chat API**

```json
{
  "model": "dola-seed-2-1-turbo-260628",
  "messages": [{"role": "user", "content": "Check the weather in Beijing."}],
  "tools": [{
    "type": "function",
    "function": {
      "name": "get_weather",
      "description": "Get weather for a city.",
      "parameters": {
        "type": "object",
        "properties": {"city": {"type": "string"}},
        "required": ["city"]
      }
    }
  }],
  "stream": true
}
```

**Responses API**

```json
{
  "model": "dola-seed-2-1-turbo-260628",
  "input": "Check the weather in Beijing.",
  "tools": [{
    "type": "function",
    "name": "get_weather",
    "description": "Get weather for a city.",
    "parameters": {
      "type": "object",
      "properties": {"city": {"type": "string"}},
      "required": ["city"]
    }
  }],
  "stream": true
}
```

**Messages API**

```json
{
  "model": "dola-seed-2-1-turbo-260628",
  "max_tokens": 1024,
  "messages": [{"role": "user", "content": "Check the weather in Beijing."}],
  "tools": [{
    "name": "get_weather",
    "description": "Get weather for a city.",
    "input_schema": {
      "type": "object",
      "properties": {"city": {"type": "string"}},
      "required": ["city"]
    }
  }],
  "stream": true
}
```

Tool arguments may be returned across multiple events. Do not parse JSON or execute the tool before the complete arguments arrive; for the specific aggregation keys and completion boundaries, see [Response examples and parsing](streaming-output.md#response-fields) and [Determine response results](streaming-output.md#terminal-and-errors).

## Process streaming responses

### Response examples and parsing

The following examples keep only the parameters involved in parsing and omit common metadata such as request IDs and timestamps. The text and token counts in the events are structural examples and do not represent fixed output.

**Chat API**

```Plain Text
data: {"choices":[{"index":0,"delta":{"reasoning_content":"Searching for common plants."}}],"usage":null}

data: {"choices":[{"index":0,"delta":{"content":"Common cruciferous plants include Chinese cabbage, cabbage, and rapeseed."}}],"usage":null}

data: {"choices":[{"index":0,"delta":{},"finish_reason":"stop"}],"usage":null}

data: [DONE]
```

Parsing notes:

* First, create independent state for each candidate result by `choices[].index`. When only one candidate is requested by default, this index is usually `0`, but the client should not hard\-code all chunks to be written to the same buffer.

* Append `delta.content` and `delta.reasoning_content` from the same candidate result to the main\-text and reasoning buffers, respectively; the exact semantics of `reasoning_content` depend on the model.

* When `delta.encrypted_content` returns a complete value, save or overwrite it as a whole instead of concatenating it incrementally as a string.

* During tool calls, continue to create independent argument buffers under the candidate result by `delta.tool_calls[].index`. For the result classification of `finish_reason` and `[DONE]`, see [Determine response results](streaming-output.md#terminal-and-errors).

**Responses API**

```Plain Text
event: response.reasoning_summary_text.delta
data: {"type":"response.reasoning_summary_text.delta","item_id":"rs_...","output_index":0,"summary_index":0,"delta":"Searching for common plants.","sequence_number":4}

event: response.output_text.delta
data: {"type":"response.output_text.delta","item_id":"msg_...","output_index":1,"content_index":0,"delta":"Common cruciferous plants include Chinese cabbage, cabbage, and rapeseed.","sequence_number":5}

event: response.completed
data: {"type":"response.completed","response":{"id":"resp_...","status":"completed","usage":{"input_tokens":18,"output_tokens":24,"total_tokens":42}},"sequence_number":6}

data: [DONE]
```

Parsing notes:

* Aggregate `response.output_text.delta` by `item_id`, `output_index`, and `content_index`, and aggregate `response.reasoning_summary_text.delta` by `item_id`, `output_index`, and `summary_index`.

* Aggregate function arguments by `item_id` and `output_index` for `response.function_call_arguments.delta`; the corresponding `response.function_call_arguments.done` only indicates that the arguments for that function are complete.

* `response.status` in `response.completed` determines whether the response is complete, and `response.usage` provides the usage for this request; `[DONE]` only indicates that the transmission has ended.

**Messages API**

```Plain Text
event: message_start
data: {"type":"message_start","message":{"id":"msg_...","type":"message","role":"assistant","content":[],"stop_reason":null,"usage":{"input_tokens":18,"output_tokens":0}}}

event: content_block_start
data: {"type":"content_block_start","index":0,"content_block":{"type":"text","text":""}}

event: content_block_delta
data: {"type":"content_block_delta","index":0,"delta":{"type":"text_delta","text":"Common cruciferous plants include Chinese cabbage, cabbage, and rapeseed."}}

event: content_block_stop
data: {"type":"content_block_stop","index":0}

event: message_delta
data: {"type":"message_delta","delta":{"stop_reason":"end_turn","stop_sequence":null},"usage":{"input_tokens":18,"output_tokens":24}}

event: message_stop
data: {"type":"message_stop"}
```

Parsing notes:

* Build a buffer by content block `index`, then append `text`, `thinking`, `signature`, or `partial_json` to the corresponding parameter based on `delta.type`.

* `content_block_stop` only completes the content block matching `index`. A tool content block should parse the aggregated `partial_json` at this point, but the entire message is still not complete.

* Read the result reason from `message_delta.delta.stop_reason`, and end consumption after `message_stop` arrives. For the overwrite rules of usage snapshots, see [Record usage](streaming-output.md#usage-statistics).

A non\-empty delta parameter only indicates that the current event carries a new fragment; it does not mean the response is complete. If `usage` is missing or `null`, mark the statistics as not collected, not as `0`.

### Determine the response result

A semantic terminal state is the API's explicit conclusion about the status of this generation result, used to determine whether the result is complete, partial, waiting for a tool, or failed.`[DONE]`, EOF, or connection closure is only the end of transmission and cannot by itself prove that generation succeeded.

|API |Semantic signal |Result classification |Client action |
|---|---|---|---|
|Chat |`finish_reason=stop` |Complete |Submit the body result for that `choice.index`. |
|Chat |`finish_reason=length` or `content_filter` |Partial |Keep the received content and mark it as incomplete; for the former, adjust the output or input length after confirming business needs, and for the latter, do not bypass the block through automatic retries. |
|Chat |`finish_reason=tool_calls` |Tool intermediate state |Mark each tool\-argument buffer under this candidate result as complete, parse the arguments, and enter the tool\-calling flow; do not treat it as the final text answer. |
|Chat |`data: [DONE]` |Transmission ended only |Determine the state based on each previous candidate result's `finish_reason`; if a semantic terminal state is missing, treat it as an early interruption. |
|Responses |`response.completed` and `response.status=completed` |Complete |Submit the aggregated output items. |
|Responses |`response.incomplete` |Partial |Read `response.incomplete_details`, keep the partial result, and then decide whether to continue generation or adjust the limits. |
|Responses |`response.failed` or `error` |Failure |Read `response.error` or the `code` and `message` in the error event; retry the request only when the reason is retryable and the business allows repeat generation. |
|Responses |`data: [DONE]` |Transmission ended only |Determine the state based on the previous `response.completed`, `response.incomplete`, `response.failed`, or `error`. |
|Messages |`stop_reason=end_turn` or `stop_reason=stop_sequence`, followed by `message_stop` |Complete |Submit the completed content blocks; when a stop sequence is hit, also record `stop_sequence`. |
|Messages |`stop_reason=max_tokens`, followed by `message_stop` |Partial |Keep the received content and mark it as incomplete; adjust `max_tokens` or the input length after confirming business needs. |
|Messages |`stop_reason=tool_use`, followed by `message_stop` |Tool intermediate state |Run the completed tool calls and continue according to the Messages tool\-calling flow; do not treat them as the final text answer. |
|Messages |`error` event or HTTP request failure |Failure |Keep the error information and disconnect; retry the request only when the reason is retryable and the business allows repeat generation. |
|Messages |Only `message_stop` is received, with no recognizable `stop_reason` |Unconfirmed |Do not mark the result as complete; record the original events and handle it as an abnormal result. |

### Record usage

**Chat API**

* **Complete usage**: Set the request body parameter `stream_options.include_usage` to `true`. The server usually adds a data block with `choices=[]` before `data: [DONE]`; its `usage` indicates the token usage for the entire request.

* **Per\-chunk cumulative usage**: Set `stream_options.chunk_include_usage` to `true`. The `usage` in each data block indicates the cumulative value up to the current time; the client should keep only the latest value and must not add values chunk by chunk.

* **Model exception**: The model `deepseek-v4-1-flash-economy-260910` does not support `stream_options.include_usage`. By default, this model returns `usage` in the data block with `finish_reason` and does not additionally return a separate usage data block.

**Responses API**

Read the usage for the current request from `response.usage` in the `response.completed`, `response.incomplete`, or `response.failed` terminal\-state event. If the connection is interrupted before the terminal state and this parameter is not returned, mark the statistics as not collected, not as `0`.

**Messages API**

Messages usage is a cumulative snapshot, not a delta that needs to be summed:

1. When `message_start` is received, initialize the current snapshot with `message_start.message.usage`. This snapshot includes input and cache\-related usage; when generation has just started, `output_tokens` is usually `0`.

2. When a `message_delta` with `usage` is received, overwrite the current snapshot with the same\-named parameters in the event. Do not add `input_tokens`, `output_tokens`, or cache tokens to the previous snapshot.

3. After `message_stop` is received, mark the latest snapshot as the final statistics for this request.

4. If the connection is interrupted before `message_stop`, only the last snapshot can be kept and marked as “not finally confirmed”; if no usage is received, mark it as “not collected” and do not record it as `0`.

### Handle connection interruptions

If EOF, a timeout, or network disconnection occurs before a normal terminal state, the received content is only a partial result. The client should mark the partial result and release the connection; retry the request only when an idempotency strategy is in place and the business allows repeat generation. This topic does not define resumable transfer.

Messages ends with `message_stop` and does not use `[DONE]` as a substitute for the message terminal state; result classification is still determined by the earlier `message_delta.delta.stop_reason`.

## How it works

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/advanced-usage/streaming-output-sequence-20261020.svg)

* **Establish a connection**: The client sends a request containing `stream=true` and maintains an independent SSE connection for each concurrent request.

* **Aggregate deltas**: The client dispatches data by the event type of the selected API, then uses the index declared by the event to append fragments to the corresponding output item.

* **Determine completion**: The client uses the semantic terminal state of the selected API to submit the result; if the connection is interrupted before the terminal state, it marks the received content as a partial result and releases the connection.

## Usage notes and limits

### Runtime observability

|Observation dimension |Expected impact |Metric or signal |Threshold or unit |
|---|---|---|---|
|Time to first content segment |The client can start processing content before the complete response ends; this does not mean the total generation time is shortened. |The time from sending the request to receiving the first body or thinking delta, recorded in milliseconds. |Milliseconds; this topic does not provide a fixed threshold. |
|Token usage |The statistics locations differ across the three types of APIs, and statistics may be missing when an interruption occurs. |Valid `usage` data blocks in Chat; `response.usage` in Responses terminal\-state events; `message_start.message.usage` or `message_delta.usage` in Messages. |Tokens; missing values are not recorded as zero, and cumulative snapshots are not added repeatedly. |
|Connection resources |Streaming objects that are not closed continue to occupy client connection resources. |Number of active connections, client timeouts, whether the consumption loop exits, and whether stream objects are closed. |Number of connections, seconds; set alerts based on business capacity. |

### Limits and unsupported items

|Dimension |Specific value |Consequence |Fix |
|---|---|---|---|
|Parameter type |`stream` is not a `boolean` |The request parameter type does not conform to the definitions of Chat API, Responses API, or Messages API. |Pass only `true` or `false`. |
|Capability combination |Responses API with `stream=true` when creating a prefix cache |You cannot use streaming output in the same request that creates a prefix cache. |First create a prefix cache with a non\-streaming request, then continue subsequent requests through `previous_response_id`. |
|Connection release |Client stops consuming but does not close the streaming object. |Connection and socket resources may remain occupied. |Close the streaming object on both normal and exception paths; set bounded connection and read timeouts. |

## Related documents

* [Chat API](chat-api.md): Check the chunk structure, `delta`, `finish_reason`, and `stream_options`.

* [Responses API](create-model-responses-api.md): Check typed events, response status, and `response.usage`.

* [Messages API](messages-api.md): Check message and content block events, `stop_reason`, and public usage parameters.

* [Function Calling](function-calling.md): Learn about tool definitions, parameter return, and multi\-turn tool execution flows.

* [Context cache](context-cache.md): Learn about the combination limitations of streaming output with implicit cache, explicit cache, and prefix cache.
