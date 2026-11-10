# Cloud-deployed MCP / remote MCP

## Core features

* Multi\-turn tool calling: Multi\-turn MCP calls are supported for complex tasks, such as multi\-step data query and analysis. The tool output from the previous turn is automatically used as the model input for the next turn. For example: MCP data query → model analysis → secondary supplementary query.

* Flexible hybrid calling: Can be used together with user\-defined functions. There is currently no limit on the number of tool combinations.

## Prerequisites

> **Tip**

> If you're new to ModelArk, see [Quick start](quick-start.md) to get up and running quickly.

You can visit the MCP marketplace to obtain the URL of cloud\-deployed MCP or remote MCP you want to use.

## Quick start

> **Tip**

* > In the following sample code for common invocation scenarios, first replace `<ARK_API_KEY>` with the actual key, and replace `server_label`/`server_url` with the actual MCP server information.

* > During testing, you need to add the header '`ark-beta-mcp: true`' when calling this tool.

**cURL**

```Bash
curl --location 'https://ark.ap-southeast.bytepluses.com/api/v3/responses' \
--header "Authorization: Bearer <ARK_API_KEY>" \
--header 'Content-Type: application/json' \
--header 'ark-beta-mcp: true' \
--data '{
    "model": "dola-seed-2-1-turbo-260628",
    "stream": true,
    "tools": [
        {
            "type": "mcp",
            "server_label": "deepwiki",
            "server_url": "https://mcp.deepwiki.com/mcp",
            "require_approval": "never"
        }
    ],
    "input": [
        {
            "role": "user",
            "content": [
                {
                    "type": "input_text",
                    "text": "Check the documentation for the byteplus/ai-app-lab repo"
                }
            ]
        }
    ]
}'
```

**Python SDK**

```Python
from arkruntime import Ark
import os

# Get the API key from environment variables. See configuration method at: https://ai.byteplus.com/ark/region:ap-southeast-1/docs/quick-start
api_key = os.getenv('ARK_API_KEY')

# Initialize the client and enable MCP
client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key
)

# Initiate a basic MCP invocation request
response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
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
                    "text": "Check the documentation for the byteplus/ai-app-lab repo"
                }
            ]
        }
    ],
    extra_headers={"ark-beta-mcp": "true"},
    stream=True  # Get streaming output
)

# Print the response
for chunk in response:
    if hasattr(chunk, 'delta'):
        print(chunk.delta, end="", flush=True)
```

**Java SDK**

```Java
package com.example.arkdemo;


import com.byteplus.ark.runtime.model.responses.constant.ResponsesConstants;
import com.byteplus.ark.runtime.model.responses.event.StreamEvent;
import com.byteplus.ark.runtime.model.responses.request.ResponsesInput;
import com.byteplus.ark.runtime.model.responses.request.CreateResponsesRequest;
import com.byteplus.ark.runtime.model.responses.tool.ToolMCP;
import com.byteplus.ark.runtime.model.responses.tool.mcp.MCPRequireApproval;
import com.byteplus.ark.runtime.model.responses.event.reasoningsummary.ReasoningSummaryTextDeltaEvent;
import com.byteplus.ark.runtime.model.responses.event.outputitem.OutputItemDoneEvent;
import com.byteplus.ark.runtime.model.responses.event.outputtext.OutputTextDeltaEvent;
import com.byteplus.ark.runtime.service.ArkService;
import okhttp3.ConnectionPool;
import okhttp3.Dispatcher;
import com.byteplus.ark.runtime.model.responses.common.ResponsesThinking;
import com.byteplus.ark.runtime.model.responses.event.response.ResponseCompletedEvent;


import java.time.Duration;
import java.util.Collections;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.TimeUnit;


public class mcpjava {


    // Fixed model to use
    private static final String model = "dola-seed-2-1-turbo-260628";


    public static void main(String[] args) {
        // 1. Validate authentication credentials (the ARK_API_KEY environment variable must be configured)
        String apiKey = System.getenv("ARK_API_KEY");
        if (apiKey == null) {
            System.out.println("ARK_API_KEY environment variable not set");
            return;
        }


        // 2. Initialize ArkService (configure connection pool, concurrency, timeout, and so on)
        ConnectionPool connectionPool = new ConnectionPool(5, 1, TimeUnit.SECONDS);
        Dispatcher dispatcher = new Dispatcher();
        dispatcher.setMaxRequests(5000);
        dispatcher.setMaxRequestsPerHost(5000);
        ArkService service = ArkService.builder()
                .dispatcher(dispatcher)
                .timeout(Duration.ofHours(1))
                .connectionPool(connectionPool)
                .apiKey(apiKey)
                .build();


        // 3. Build an MCP tool call request
        CreateResponsesRequest mcpRequest = CreateResponsesRequest.builder()
                .model(model)
                .stream(true) // Use streaming output to get results in real time
                // Configure the MCP tool: specify the service label, service URL, and approval mode
                .tools(Collections.singletonList(
                        ToolMCP.builder()
                                .serverLabel("deepwiki-test") // MCP service label
                                .serverUrl("https://mcp.deepwiki.com/mcp") // MCP service API URL
                                // Approval mode: NEVER (no approval required), ALWAYS (approval required), AUTO (automatic approval)
                                .requireApproval(MCPRequireApproval.builder()
                                        .mode(ResponsesConstants.MCP_APPROVAL_MODE_NEVER)
                                        .build())
                                .build()
                ))
                // Input query instruction: let the MCP tool query the specified repository structure
                .input(ResponsesInput.builder()
                        .stringValue("View the expressjs/express of this repo")
                        .build())
                // Enable the model thinking process (viewable in streaming output)
                .thinking(ResponsesThinking.builder()
                        .type(ResponsesConstants.THINKING_TYPE_ENABLED)
                        .build())
                .build();


        // 4. Build a custom header (set ark-beta-mcp:true)
        Map<String, String> customHeaders = new HashMap<>();
        customHeaders.put("ark-beta-mcp", "true");


// 5. Execute the MCP tool call and process the streaming output (pass in the custom header)
        try {
            System.out.println("----- [MCP Tool Call] Start calling -----");
// Pass in the headers parameter during streaming calls
            service.streamResponse(mcpRequest, customHeaders)
                    .doOnError(Throwable::printStackTrace) // Exception handling
                    .blockingForEach(mcpjava::printStreamEvent); // Listen for streaming events
        } catch (Exception e) {
            System.err.println("MCP Tool Call Error: " + e.getMessage());
            e.printStackTrace();
        }


// 6. Shut down the service executor and release resources
        service.shutdownExecutor();
    }


// Track whether the reasoning process prefix has been output
    private static boolean isReasoningPrefixPrinted = false;
// Track whether the tool return prefix has been output
    private static boolean isToolPrefixPrinted = false;
// Streaming event handling: print the thinking process, response content, and tool call status
    private static void printStreamEvent(StreamEvent event) {
// Print reasoning process deltas (such as how the model analyzes MCP tool calls)
        if (event instanceof ReasoningSummaryTextDeltaEvent) {
            ReasoningSummaryTextDeltaEvent reasoningEvent = (ReasoningSummaryTextDeltaEvent) event;
            String delta = reasoningEvent.getDelta();
// Add the prefix only when the reasoning process starts
            if (!isReasoningPrefixPrinted && delta.trim().length() > 0) {
                System.out.print("[Reasoning process] " + delta);
                isReasoningPrefixPrinted = true;
            } else {
                System.out.print(delta);
            }
// If a newline character is encountered, reset the prefix flag
            if (delta.contains("\n")) {
                isReasoningPrefixPrinted = false;
            }
        }
// Print the text response delta (real-time output of results returned by the MCP tool)
        else if (event instanceof OutputTextDeltaEvent) {
            OutputTextDeltaEvent textEvent = (OutputTextDeltaEvent) event;
            String delta = textEvent.getDelta();
// Add the prefix only when the tool return starts
            if (!isToolPrefixPrinted && delta.trim().length() > 0) {
                System.out.print("[Tool return] " + delta);
                isToolPrefixPrinted = true;
            } else {
                System.out.print(delta);
            }
// If a newline character is encountered, reset the prefix flag
            if (delta.contains("\n")) {
                isToolPrefixPrinted = false;
            }
        }
// Print the output item completion event
        else if (event instanceof OutputItemDoneEvent) {
            OutputItemDoneEvent itemEvent = (OutputItemDoneEvent) event;
            System.out.println("\n[Output completed] " + itemEvent.getItem().getType());
// Reset all prefix flags
            isReasoningPrefixPrinted = false;
            isToolPrefixPrinted = false;
        }
// The overall response is completed. Print the call usage.
        else if (event instanceof ResponseCompletedEvent) {
            ResponseCompletedEvent completedEvent = (ResponseCompletedEvent) event;
            System.out.println("\n[Response completed] Call usage: " + completedEvent.getResponse().getUsage());
// Reset all prefix flags
            isReasoningPrefixPrinted = false;
            isToolPrefixPrinted = false;
        }
// Print all other unhandled event types (for debugging)
        else {
            System.out.println("\n[Unhandled event] Type: " + event.getClass().getName());
// Reset all prefix flags
            isReasoningPrefixPrinted = false;
            isToolPrefixPrinted = false;
        }
    }
}
```

**Go SDK**

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

const (
    mcpServerURL  = "https://mcp.deepwiki.com/mcp"
    mcpLabel      = "deepwiki"
    mcpBetaHeader = "ark-beta-mcp"
    userPrompt    = "Check the structure of the mark3labs/mcp-go repository"
)

func main() {
    nonStream()
    fmt.Println()
    stream()
}

func newClient() *arkruntime.Client {
    return arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
}

func nonStream() {
    fmt.Println("===== non-streaming =====")
    client := newClient()
    ctx := context.Background()

    // Round 1: ask the model to call the MCP tool.
    req1 := &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            {OneOf: responses.NewItemEasyMessageInputItemSum(buildUserMessage())},
        }),
        Tools: []responses.Tool{mcpTool(false)},
    }
    resp1, err := client.CreateResponses(
        ctx,
        req1,
        arkruntime.WithCustomHeader(mcpBetaHeader, "true"),
    )
    if err != nil {
        fmt.Printf("round 1 error: %v\n", err)
        return
    }
    fmt.Printf("response: %+v\n", resp1)

    approvalRequestID := findApprovalRequestID(resp1.Output)
    if approvalRequestID == "" {
        fmt.Println("no approval request emitted; nothing to approve")
        return
    }

    // Round 2: approve the requested MCP call.
    req2 := &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            {OneOf: responses.NewItemFunctionMcpApprovalResponseInputItemSum(
                responses.ItemFunctionMcpApprovalResponse{
                    Type:              responses.ItemFunctionMcpApprovalResponseTypeMcpApprovalResponse,
                    ApprovalRequestID: approvalRequestID,
                    Approve:           true,
                },
            )},
        }),
        Tools:              []responses.Tool{mcpTool(false)},
        PreviousResponseID: responses.NewOptString(resp1.ID),
    }
    resp2, err := client.CreateResponses(
        ctx,
        req2,
        arkruntime.WithCustomHeader(mcpBetaHeader, "true"),
    )
    if err != nil {
        fmt.Printf("round 2 error: %v\n", err)
        return
    }
    fmt.Printf("response: %+v\n", resp2)

    // Round 3: let the model call the MCP tool without approval.
    req3 := &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            {OneOf: responses.NewItemEasyMessageInputItemSum(buildUserMessage())},
        }),
        Tools: []responses.Tool{mcpTool(true)},
    }
    resp3, err := client.CreateResponses(
        ctx,
        req3,
        arkruntime.WithCustomHeader(mcpBetaHeader, "true"),
    )
    if err != nil {
        fmt.Printf("round 3 error: %v\n", err)
        return
    }
    fmt.Printf("response: %+v\n", resp3)
}

func stream() {
    fmt.Println("===== streaming =====")
    client := newClient()
    ctx := context.Background()

    req1 := &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            {OneOf: responses.NewItemEasyMessageInputItemSum(buildUserMessage())},
        }),
        Tools: []responses.Tool{mcpTool(false)},
    }
    responseID, approvalRequestID, err := streamAndCapture(ctx, client, req1)
    if err != nil {
        fmt.Printf("round 1 stream error: %v\n", err)
        return
    }
    if approvalRequestID == "" {
        fmt.Println("no approval request emitted; nothing to approve")
        return
    }

    req2 := &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            {OneOf: responses.NewItemFunctionMcpApprovalResponseInputItemSum(
                responses.ItemFunctionMcpApprovalResponse{
                    Type:              responses.ItemFunctionMcpApprovalResponseTypeMcpApprovalResponse,
                    ApprovalRequestID: approvalRequestID,
                    Approve:           true,
                },
            )},
        }),
        Tools:              []responses.Tool{mcpTool(false)},
        PreviousResponseID: responses.NewOptString(responseID),
    }
    if _, _, err := streamAndCapture(ctx, client, req2); err != nil {
        fmt.Printf("round 2 stream error: %v\n", err)
        return
    }

    req3 := &responses.ResponsesRequest{
        Model: "dola-seed-2-1-turbo-260628",
        Input: responses.NewInputItemArrayResponsesInput([]responses.InputItem{
            {OneOf: responses.NewItemEasyMessageInputItemSum(buildUserMessage())},
        }),
        Tools: []responses.Tool{mcpTool(true)},
    }
    if _, _, err := streamAndCapture(ctx, client, req3); err != nil {
        fmt.Printf("round 3 stream error: %v\n", err)
    }
}

func buildUserMessage() responses.ItemEasyMessage {
    return responses.ItemEasyMessage{
        Role: responses.NewOptMessageRole(responses.MessageRoleUser),
        Content: responses.NewContentItemArrayMessageContent([]responses.ContentItem{
            {
                OneOf: responses.NewContentItemTextContentItemSum(responses.ContentItemText{
                    Type: responses.ContentItemTextTypeInputText,
                    Text: userPrompt,
                }),
            },
        }),
    }
}

func mcpTool(skipApproval bool) responses.Tool {
    mcp := responses.McpTool{
        Type:              responses.McpToolTypeMcp,
        ServerLabel:       mcpLabel,
        ServerURL:         mcpServerURL,
        ServerDescription: responses.NewOptString("Query a GitHub repository's documentation"),
    }
    if skipApproval {
        mcp.RequireApproval = responses.NewOptMcpRequireApproval(
            responses.NewMcpApprovalModeMcpRequireApproval(responses.McpApprovalModeNever),
        )
    }
    return responses.Tool{OneOf: responses.NewMcpToolToolSum(mcp)}
}

func findApprovalRequestID(output []responses.OutputItem) string {
    for i := len(output) - 1; i >= 0; i-- {
        if request, ok := output[i].OneOf.GetItemFunctionMcpApprovalRequest(); ok {
            return request.ID.Or("")
        }
    }
    return ""
}

func streamAndCapture(
    ctx context.Context,
    client *arkruntime.Client,
    req *responses.ResponsesRequest,
) (string, string, error) {
    stream, err := client.CreateResponsesStream(
        ctx,
        req,
        arkruntime.WithCustomHeader(mcpBetaHeader, "true"),
    )
    if err != nil {
        return "", "", err
    }

    var responseID string
    var approvalRequestID string
    for {
        event, err := stream.Recv()
        if err == io.EOF {
            return responseID, approvalRequestID, nil
        }
        if err != nil {
            return responseID, approvalRequestID, err
        }

        switch event.OneOf.Type {
        case responses.ResponseCreatedEventResponseStreamEventSum:
            responseID = event.OneOf.ResponseCreatedEvent.Response.ID
        case responses.ResponseReasoningSummaryTextDeltaEventResponseStreamEventSum:
            fmt.Print(event.OneOf.ResponseReasoningSummaryTextDeltaEvent.Delta.Or(""))
        case responses.ResponseTextDeltaEventResponseStreamEventSum:
            fmt.Print(event.OneOf.ResponseTextDeltaEvent.Delta.Or(""))
        case responses.ResponseTextDoneEventResponseStreamEventSum:
            fmt.Printf("\n[text done] %s\n", event.OneOf.ResponseTextDoneEvent.Text.Or(""))
        case responses.ResponseOutputItemDoneEventResponseStreamEventSum:
            item := event.OneOf.ResponseOutputItemDoneEvent.Item
            if request, ok := item.OneOf.GetItemFunctionMcpApprovalRequest(); ok {
                approvalRequestID = request.ID.Or("")
                fmt.Printf(
                    "\n[mcp approval request] name=%s arguments=%s\n",
                    request.Name,
                    request.Arguments,
                )
            }
        case responses.ResponseMcpCallArgumentsDeltaEventResponseStreamEventSum:
            fmt.Print(event.OneOf.ResponseMcpCallArgumentsDeltaEvent.Delta)
        case responses.ResponseMcpCallCompletedEventResponseStreamEventSum:
            fmt.Println("\n[mcp call completed]")
        case responses.ResponseMcpListToolsInProgressEventResponseStreamEventSum:
            fmt.Println("\n[listing mcp tools]")
        case responses.ResponseMcpListToolsCompletedEventResponseStreamEventSum:
            fmt.Println("\n[mcp tools listed]")
        }
    }
}
```

## Parameter description

For details, see [Create a model response](create-model-responses-api.md).

## Supported models

See [Tool use](model-list.md#f44ceef7).

> For model calls, make sure the MCP service has been activated and the required model permissions have been requested. Users who have not activated the service cannot use MCP features.

## How it works

You can call MCP tools (including cloud\-deployed MCP and remote MCP) through the `Responses API`. Compared to function calling, the MCP tools run entirely in the cloud: the large model connects to a remote server to complete calls, so local MCP calls are not supported.

## Usage guide

This section describes common usage scenarios and how to configure their parameters, helping you make the most out of MCP tools.

### 1. Filter MCP tools

An MCP server may provide multiple tools. You can view the full tool list on the service details page. During API calls, you can use the `allowed_tools` parameter to specify the tools to use, saving time and getting results that better match your expectations.

> **Tip**

> The `allowed_tools.tool_names` array is used to list the names of the MCP tools that the model needs to call.

```Python
"tools": [
    {
            "type": "mcp",
            "server_label": "mcp_tools",
            "server_url": "https://mcpserver/mcp",
            "allowed_tools": {
                "tool_names": [ # List the MCP tools to use
                    "tool1",
                    "tool2"
                ]
            }
        }
]
```

### 2. Configure authentication information

Currently, some MCP tools require authentication to be configured in the header. Typically, you configure it using the `Authorization` parameter. Example:

> **Tip**

> Replace `headers.Authorization` with the authentication information required by the MCP tool provider, such as `Bearer <token>`.

```JSON
...
"tools": [
  {
    "type": "mcp",
    "server_label": "info-quest-mcp",
    "server_url": "https://mcp.infoquest.bytepluses.com/mcp",
    "require_approval": "never",
    "headers": {
      "Authorization": "******"
    }
  }
]
...
```

## Scenario example: multi\-turn calls

**Scenario**: Call the Ocean Engine Qianchuan MCP to analyze live streaming data. Execution requires user confirmation and supports multi\-turn follow\-up queries.

```Python
from arkruntime import Ark
import os

api_key = os.getenv('ARK_API_KEY')
client = Ark(
    base_url='https://ark.ap-southeast.bytepluses.com/api/v3',
    api_key=api_key
)

# 1. Request of round 1: Initiate MCP call approval
first_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    tools=[
        {
            "type": "mcp",
            "server_label": "qianchuan-mcp",
            "server_url": "https://mcp.qianchuan.com/mcp",
            "require_approval": "always"
        }
    ],
    input=[
        {
            "role": "user",
            "content": [{"type": "input_text", "text": "Analyze today's livestream room bidding promotion data"}]
        }
    ],
    extra_headers={"ark-beta-mcp": "true"},
    stream=True
)

# Extract the approval ID and the previous response ID
last_chunk = None
for chunk in first_response:
    last_chunk = chunk
previous_response_id = last_chunk.response.id
approval_id = last_chunk.response.output[-1].id
print(f"Previous response ID: {previous_response_id}, approval ID: {approval_id}")

# 2. Request of round 2: approve and continue execution
second_response = client.responses.create(
    model="dola-seed-2-1-turbo-260628",
    tools=[
        {
            "type": "mcp",
            "server_label": "qianchuan-mcp",
            "server_url": "https://mcp.qianchuan.com/mcp",
            "require_approval": "always"
        }
    ],
    input=[
        {
            "type": "mcp_approval_response",
            "approve": True,  # Approve
            "approval_request_id": approval_id
        }
    ],
    extra_headers={"ark-beta-mcp": "true"},
    previous_response_id=previous_response_id,  # Link to the previous request
    stream=True
)

# Print the response of round 2
for chunk in second_response:
    if hasattr(chunk, 'delta'):
        print(chunk.delta, end="", flush=True)
```

## Notes

1. Currently, cloud\-deployed MCP / remote MCP can be called only through the Responses API, and **only MCP connected through streamable HTTP links is supported**.

2. **Function naming conflicts**: If a user\-defined function has the same name as an MCP built\-in tool (such as `mcp_call`), the model automatically determines the call priority. No additional configuration is required.

3. **Default rate limiting rules**: The default account\-level rate limit is `1000 RPM` (requests per minute). Exceeding the rate limit will cause the request to fail. To adjust it, submit a support ticket.

4. **Error message**: The `caching` parameter is not supported currently. Using this parameter will return a 400 error message.

## Billing

It consumes tokens like the model. No additional fee is charged for MCP tool calls.

## Related references

* [Create a model response](create-model-responses-api.md)
