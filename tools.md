# Tools

A model can understand tasks and generate content, but it cannot operate files, execute commands, or access business systems directly through reasoning alone. After you configure tools for an agent, the agent can call the corresponding tools and continue processing the task based on the tool results, covering the entire workflow from understanding requirements to performing actions.

## Select tools

Select the corresponding tool type based on the resources the task needs to access and the party that executes the tool.

* When a task needs to operate on files, execute commands, or retrieve web page information, use the base toolset. A self\-hosted environment only changes where some of these tools are executed.

* When a task needs to access an external system that has been integrated through an MCP server, use MCP tools.

* When a task only needs to call a small number of your own business operations, and those operations are executed by the business application, use custom tools.

### Use the base toolset

When a task needs to process files, run commands, or retrieve web page information, use the current basic toolset `agent_toolset_20260916` for newly created agents. Typical use cases include organizing input files, generating and running code, and retrieving and summarizing public information.

After you add `agent_toolset_20260916` to an agent, the following tools are enabled by default.

|Tool |Configuration name |Note |
|---|---|---|
|Bash |`bash` |Execute Bash commands in the sandbox. |
|Read |`read` |Read files in the sandbox. |
|Write |`write` |Write or overwrite files in the sandbox. |
|Edit |`edit` |Perform string replacement on files. |
|Glob |`glob` |Find files by name. |
|Grep |`grep` |Search text content by regular expression. |
|Web Fetch |`web_fetch` |Fetch content from a specified URL. |
|Web Search |`web_search` |Start a web search. |

Different versions of the base toolset contain the same tools. Historical values are used only for compatibility with existing agent configurations. Use `agent_toolset_20260916` for newly created agents.

> **Note**

> The web search tool is billed based on the actual number of calls.

### Integrate MCP tools

When a task needs to access an external system that has been integrated through an MCP server, or needs to reuse a set of standardized external tools, use the MCP toolset `mcp_toolset`. Typical use cases include querying project data, reading business information, and triggering external workflows.

The MCP toolset provides the tools exposed by an MCP server to the agent through `type: "mcp_toolset"`.`mcp_server_name` must be the same as `mcp_servers[].name`; if you only declare an MCP server without adding the corresponding `mcp_toolset`, the agent cannot call the tools on that server.

MCP tools in both cloud and self\-hosted environments are executed on the ModelArk side and are not handed off to the enterprise worker.

You can set the default status of tools under the server through `default_config`, and then override it by tool name through `configs`. For the complete configuration and authentication method, see [MCP](mcp.md).

If `default_config` is explicitly provided but `permission_policy` is omitted, MCP tools follow the `always_ask` policy at runtime, and basic tools follow the `always_allow` policy. To prevent MCP calls from being interrupted unexpectedly, we recommend explicitly configuring the permission policy.

### Integrate custom tools

When a task only needs to call a small number of your own business operations and you do not need to deploy a separate MCP server for these operations, use the custom tool `custom`. Typical use cases include querying orders, submitting tickets, and triggering approvals. The business application is responsible for performing the actual operation and then returning the result to the agent.

Custom tools are declared through `type: "custom"`. Each tool must be configured with a name, a purpose description, and an input parameter schema. The following example uses `curl` to create an agent and uses `jq` to save the returned agent ID. The example also explicitly disables the basic toolset to prevent the main agent from falling back to enabling all basic tools:

**Curl**

```Bash
AGENT_RESPONSE=$(curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/agents" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "CustomToolDemo",
    "model": {
      "id": "dola-seed-2-1-turbo-260628"
    },
    "system": "You are an assistant that can call business tools. Call the declared custom tool when external business capabilities are needed. Do not fabricate tool results.",
    "tools": [
      {
        "type": "agent_toolset_20260916",
        "default_config": {
          "enabled": false,
          "permission_policy": {
            "type": "always_allow"
          }
        }
      },
      {
        "type": "custom",
        "name": "query_order",
        "description": "Query the order status by order ID. Use only when the user requests order information.",
        "input_schema": {
          "type": "object",
          "properties": {
            "order_id": {
              "type": "string",
              "description": "Order ID."
            }
          },
          "required": ["order_id"],
          "additionalProperties": false
        }
      }
    ]
  }') || exit 1

jq . <<<"$AGENT_RESPONSE" || exit 1
AGENT_ID=$(jq -er '.id' <<<"$AGENT_RESPONSE") || exit 1
export AGENT_ID
printf 'Agent ID: %s\n' "$AGENT_ID"
```

After configuration, when the agent needs to query an order, it sends `agent.custom_tool_use`. The business application reads the tool name and input parameters, performs the query, and then returns the result through `user.custom_tool_result`. For the complete integration process and examples for multimedia use cases, see [[Basics] How to use custom tools in Managed Agents](managed-agent-custom-tool-tutorial.md).

## Configure the basic toolset

Before creating an agent, first determine which tools the task must use, which tools can modify data, and which calls require confirmation from the business application. Explicitly configure `default_config.enabled` and `permission_policy`, and then override individual tools through `configs`. This can prevent differences in the default behavior of different tool types from causing deviations in the permission scope.

> **Note**

> Do not disable the main agent's basic tools by omitting `tools`, passing an empty array `[]`, or configuring only other tools such as MCP or custom tools. When the main agent does not find a basic toolset configuration, it falls back to enabling all basic tools and uses `always_allow`. To restrict the tool scope, explicitly configure `agent_toolset_20260916`, set `default_config.enabled` to `false`, and then enable the required tools one by one. For details, see [Check the default tool scope](tools.md#tool-defaults-and-boundaries).

### Enable all basic tools

If the agent needs to use all basic execution capabilities, mount `agent_toolset_20260916`, and explicitly enable the toolset and permission policy:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/agents \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "CodeAssistant",
    "model": {
      "id": "dola-seed-2-1-turbo-260628"
    },
    "tools": [
      {
        "type": "agent_toolset_20260916",
        "default_config": {
          "enabled": true,
          "permission_policy": {
            "type": "always_allow"
          }
        }
      }
    ]
  }'
```

### Disable some tools

If the agent should not access web pages, you can keep the toolset enabled, and then disable `web_fetch` and `web_search` through `configs`:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/agents \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "OfflineDocAssistant",
    "model": {
      "id": "dola-seed-2-1-turbo-260628"
    },
    "tools": [
      {
        "type": "agent_toolset_20260916",
        "default_config": {
          "enabled": true,
          "permission_policy": {
            "type": "always_allow"
          }
        },
        "configs": [
          {
            "name": "web_fetch",
            "enabled": false
          },
          {
            "name": "web_search",
            "enabled": false
          }
        ]
      }
    ]
  }'
```

Replace `<tool_name>` with the name of the tool to disable:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/agents \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "DisableSpecifiedTool",
    "model": {
      "id": "dola-seed-2-1-turbo-260628"
    },
    "tools": [
      {
        "type": "agent_toolset_20260916",
        "default_config": {
          "enabled": true,
          "permission_policy": {
            "type": "always_allow"
          }
        },
        "configs": [
          {
            "name": "<tool_name>",
            "enabled": false
          }
        ]
      }
    ]
  }'
```

### Enable only required tools

If the agent only needs to read and retrieve sandbox files, first disable the entire toolset, and then explicitly enable `read`, `glob`, and `grep`:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/agents \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "ReadOnlyAuditingAgent",
    "model": {
      "id": "dola-seed-2-1-turbo-260628"
    },
    "tools": [
      {
        "type": "agent_toolset_20260916",
        "default_config": {
          "enabled": false,
          "permission_policy": {
            "type": "always_ask"
          }
        },
        "configs": [
          {
            "name": "read",
            "enabled": true,
            "permission_policy": {
              "type": "always_allow"
            }
          },
          {
            "name": "glob",
            "enabled": true,
            "permission_policy": {
              "type": "always_allow"
            }
          },
          {
            "name": "grep",
            "enabled": true,
            "permission_policy": {
              "type": "always_allow"
            }
          }
        ]
      }
    ]
  }'
```

This configuration is suitable for read\-only tasks such as code review and information retrieval.

### Set permission policies

`always_allow` means tool calls do not require confirmation from the business application, and `always_ask` means pausing the session before execution and waiting for confirmation from the business application. Permission policies do not change the tool executor. We recommend explicitly configuring the default policy for the toolset in the request, and overriding individual higher\-risk tools through `configs[].permission_policy`.

For the complete configuration method for permission policies, see [Tool permission policy](tool-permission-policy.md).

## Verify and handle tool calls

After creating or updating an agent, verify that the configuration has taken effect in the following order:

1. **Query agent configuration**: Call [Get agent](get-agent-api.md) and check whether `tools` in the response meets expectations; when using MCP tools, also check `mcp_servers`.

2. **Refresh session configuration**: Create a new session, or call [Upgrade session](upgrade-session-api.md) to upgrade an existing session. Created sessions do not automatically use the new version of the agent.

3. **Trigger a tool call**: Send a test task that can trigger the target tool.

4. **Check event closure**: Check the session events and confirm that the tool call, execution result, and final response form a complete loop.

The following uses querying order status and organizing the results as an example to describe the main event flow. The specific events depend on the tool executor and the permission policy.

### Handle cloud environment tool calls

In a cloud environment, ModelArk executes basic tools and MCP tools. The following diagram shows how the agent initiates a call, waits for business application confirmation as needed, and returns the result.`always_allow` executes the tool directly; `always_ask` first pauses the session for the business application to approve or reject the call.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/managed-agent/managed-tool-event-sequence.svg)

#### Automatic execution

When `always_allow` is used, ModelArk executes the tool directly and returns the result:

* **Basic tools**: The call event is `agent.tool_use`, and the result event is `agent.tool_result`.

* **MCP tools**: The call event is `agent.mcp_tool_use`, and the result event is `agent.mcp_tool_result`.

Multiple tools may be called in the same turn. Use `tool_use_id` or `mcp_tool_use_id` to pair each result with the corresponding call. Do not rely only on the order in which events arrive.

#### Read oversized tool results

In a cloud environment, when the session has sandbox read/write capabilities, if the recoverable plain text returned by a built\-in tool or MCP tool is too long, ModelArk saves the full result in a sandbox file for the current session and returns a summary in the text block of `agent.tool_result` or `agent.mcp_tool_result`. This prevents the full result from repeatedly occupying the model context.

The client can display or log `content` as a normal tool result. The path in the summary is only for the agent in the current session. When the task depends on omitted content, the agent uses `read` or `grep` to continue reading from that path. The client does not need to handle this path.

This handling applies only to recoverable plain\-text results in a cloud environment. Recoverability requires the session to have sandbox read/write capabilities. In self\-hosted environments, for non\-plain\-text results, when the session does not have sandbox read/write capabilities, when the agent cannot continue reading, or when the upstream does not provide recoverable content, the event preserves the tool's original visible result.

#### Execute after confirmation

When a basic tool or MCP tool uses `always_ask`, the session pauses and waits for business application confirmation:

1. The session returns `agent.tool_use` or `agent.mcp_tool_use`.

2. The session returns `session.status_idle`, where `stop_reason.type=requires_action`, and `stop_reason.event_ids` lists the call events for processing.

3. The business application sends `user.tool_confirmation`, specifies the call event through `tool_use_id`, and sets `result` to `allow` or `deny`.

4. After all pending confirmation events are handled, the session returns `session.status_running`. Tools which are allowed to run continue to run, and rejected results are passed to the agent to adjust subsequent processing.

> **Handle confirmation results**

> When rejecting a call, you can pass the reason back to the agent through `deny_message`. The agent chooses another tool, falls back to degraded processing, or explains to the end user that it cannot continue based on the rejection reason.

> During the `requires_action` stage, handle all confirmation events in `stop_reason.event_ids` first. Do not use a new `user.message` as a substitute for confirmation. For `always_allow`, `always_ask`, and override rules, see [Tool permission policy](tool-permission-policy.md).

### Handle self\-hosted tool calls

In a self\-hosted environment, executors are assigned by tool type:

|Tool type |Executor |Result handling |
|---|---|---|
|The main agent's `bash`, `read`, `write`, `edit`, `glob`, and `grep`. |Enterprise worker and enterprise sandbox |The worker passes the result back through `user.tool_result`. |
|`web_fetch`, `web_search` |ModelArk side |ModelArk writes the result to the session event stream. |
|Multimodal tools |ModelArk side |ModelArk writes images, videos, or other tool results to the session event stream. |
|MCP tool |ModelArk side |ModelArk returns the result through `agent.mcp_tool_result`. |
|Custom tools |Business application |The business application passes the result back through `user.custom_tool_result`. |

The preceding six enterprise\-side tools support only the main agent. Sub\-agents do not execute these tools through the enterprise worker.

The following process applies only to the six enterprise\-side tools of the main agent:

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/managed-agent/self-hosted-tool-event-sequence.svg)

1. The session returns `agent.tool_use` and initiates a Bash or file tool call.

2. The session returns `session.status_idle`, where `stop_reason.type=requires_action`.

3. The worker obtains the corresponding work and executes the tool in the enterprise sandbox.

4. The worker passes the result back through `user.tool_result`.`tool_use_id` must be set to the corresponding `agent.tool_use.id`. If tool execution fails, set `is_error` to `true` and return the failure description in `content`.

5. After all pending results are passed back, the session returns `session.status_running`, and the agent continues processing the task based on the tool results.

During execution, the worker continuously calls heartbeat to maintain the work lease; for the complete poll, acknowledge, heartbeat, and execution environment configuration processes, see [Configure and run a self-hosted environment](configure-self-hosted-environment.md).

### Handle custom tool calls

Custom tools are executed by the business application, with the following event loop:

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/managed-agent/custom-tool-event-sequence.svg)

1. The session returns `agent.custom_tool_use`, which contains the tool name, input parameters, and call event ID.

2. The session returns `session.status_idle`, where `stop_reason.type=requires_action`, and `stop_reason.event_ids` lists the call events pending processing.

3. The business application performs the corresponding operation based on the tool name and input parameters.

4. The business application sends `user.custom_tool_result` and fills the original call event ID into `custom_tool_use_id`. If multiple pending calls exist in the same turn, return results for each call separately.

5. After all pending results are passed back, the session returns `session.status_running`, and the agent continues processing the task based on the tool results.

For the complete example, see [[Basics] How to use custom tools in Managed Agents](managed-agent-custom-tool-tutorial.md). For the parameter structure of all tool events, see [Session event structure reference](sessions-events-reference-v2.md).

## Update tool configuration

When updating the tools that an agent can use, `tools` is treated as a whole when replaced. To avoid accidentally removing other available tools, follow these steps to read, merge, and write back the complete `tools` array:

1. **Read the current configuration**: Call [Get agent](get-agent-api.md) to get the latest `version` and the complete `tools` array.

2. **Merge the tool array**: In the local copy, keep the tools that do not need to be modified, and adjust only the target tools.

3. **Submit the update**: Call [Update agent](update-agent-api.md) and submit the current `version` and the complete merged `tools` array.

4. **Check the result**: Query the agent again to confirm that the version number and tool configuration have been updated.

5. **Verify the call**: Create a new session, or upgrade an existing session and then verify the tool call.

The value semantics of `tools` in an update request are as follows:

|Value |Configuration result |Usage tips |
|---|---|---|
|Omitted or `null` |Retains the current `tools` configuration. |Use this when only modifying other agent parameters. |
|Empty array `[]` |Only clears the saved `tools` configuration and does not disable the basic tools of the main agent. At runtime, the main agent still falls back to enabling all basic tools and uses `always_allow`. |To restrict basic tools, use explicit toolset configuration instead. |
|Non\-empty array |Replaces the current configuration with the entire array in the request. |Read and merge all tools that need to be retained first. |

## Troubleshoot tool issues

First locate the interruption point based on the session event flow, and then perform the corresponding check:

* **The call event for the target tool does not appear**: check the agent configuration, session version, and task trigger conditions.

* **`stop_reason.type=requires_action`** ** appears**: return confirmation or tool results based on the blocking event type.

* **The tool result contains an error**: check the input, permissions, or execution environment based on the call ID.

* **`session.error`** ** appears**: decide whether to wait, retry after correction, or create a new session based on the error type and retry status.

* **The tool can execute but the permission scope is too broad**: check file, network, and credential isolation boundaries.

### Check configuration, version, and trigger conditions

If `agent.tool_use`, `agent.mcp_tool_use`, or `agent.custom_tool_use` does not appear in the event flow, check the following in order:

1. Query the agent to confirm that the target tool or toolset has been saved. For MCP tools, you also need to check `mcp_servers`.

2. Confirm the agent version used by the current session; agent updates do not automatically affect existing sessions.

3. Check the configuration and trigger conditions by tool type.

4. Use the same explicit test task to compare the new and old sessions, so that task differences are not mistakenly identified as version issues.

|Tool type |Configuration check |Task trigger conditions |
|---|---|---|
|Basic tools |`tools` contains `type: "agent_toolset_20260916"`; confirm that the target tool is enabled based on `default_config.enabled` and the override results from `configs`. |The test task explicitly requires reading actual files, executing commands, or accessing web pages, and provides a file path, command target, or search topic. Plain\-text question answering does not require tool calls. |
|MCP tool |`mcp_servers[].name` matches `tools[].mcp_server_name`; confirm that the target MCP tool is enabled based on `default_config` and `configs`. |The test task explicitly requires accessing the corresponding external system and provides a project, record, or other business identifier. |
|Custom tools |`tools` contains `type: "custom"`, and the correct `name`, `description`, and `input_schema` are saved.`description` must describe the applicable conditions. |The test task explicitly requires executing this business operation and provides the required information in `input_schema`. |

The agent determines whether to call a tool based on the task content and tool description. A single missed tool call does not directly prove that the configuration is invalid; first reproduce the issue with a test task that explicitly requires the target capability.

#### Check default tool scope

The main agent and sub\-agents use different runtime behavior when no basic toolset is configured:

|Configuration |Main agent |Sub\-agent |Recommendations |
|---|---|---|---|
|Omit `tools`, pass `[]`, or configure only other tool types |Falls back to enabling all basic tools, with the permission policy set to `always_allow`. |Does not provide basic tools; memory tools enabled for the session are not affected by this rule. |Do not rely on omission behavior; explicitly configure the basic toolset. |
|Configure `agent_toolset_20260916` and omit `default_config` |The server saves `enabled=true` and `always_allow`. |Provides basic tools according to the same configuration. |Explicitly specify the enabled state and permission policy in the request. |
|Configure `default_config.enabled=false`, then enable items individually |Provides only the tools explicitly enabled in `configs`. |Provides only the tools explicitly enabled in `configs`. |Use this when you need a minimal toolset. |

To determine whether a sub\-agent actually executed a tool, use `agent.tool_use` and `agent.tool_result` in the sub\-thread as the source of truth. Claims in the system prompt or final response that a tool was executed do not mean that the runtime environment actually provided or executed that tool.

#### Check the default permission policy

The permission policy has the following default branches:

|Configuration |Basic tools |MCP tool |
|---|---|---|
|Omit the entire `default_config` |The server saves `always_allow`. |The server saves `always_allow`. |
|Provided `default_config` but omitted `permission_policy` |Use `always_allow` at runtime. |Use `always_ask` at runtime. |
|Explicitly configure `permission_policy` |Use the configured `always_allow` or `always_ask`. |Use the configured `always_allow` or `always_ask`. |

### Handle requires_action

When the session returns `stop_reason.type=requires_action`, first read `stop_reason.event_ids`, and then perform the corresponding action based on the type of each blocking event:

|Blocking event |Scenario |Fix |
|---|---|---|
|`agent.tool_use` or `agent.mcp_tool_use` |Tools in the cloud environment use `always_ask`. |Send `user.tool_confirmation`, put the blocking event ID in `tool_use_id`, and set `allow` or `deny`. When confirming an MCP call, also use `tool_use_id`; `mcp_tool_use_id` is used only to associate `agent.mcp_tool_result`. |
|`agent.tool_use` |The main agent in a self\-hosted environment calls `bash`, `read`, `write`, `edit`, `glob`, or `grep`, and then the worker runs it. |After the worker completes execution, send `user.tool_result` and use `tool_use_id` to associate the call; if execution fails, set `is_error: true` and return an error description. |
|`agent.custom_tool_use` |The business application executes the custom tool. |Send `user.custom_tool_result` and use `custom_tool_use_id` to associate the call; if execution fails, set `is_error: true` and return an error description. |

The same turn may contain multiple blocking events. The session resumes only after all events in `stop_reason.event_ids` are handled. Do not use a new `user.message` instead of a confirmation or tool result.

### Troubleshoot tool result errors

When a tool result contains an error, first associate it with the corresponding call event, and then locate the executor:

* **`agent.tool_result`**: Use `tool_use_id` to check the original tool input, and then check the file path, environment permissions, network policy, and execution logs.

* **`agent.mcp_tool_result`**: Use `mcp_tool_use_id` to check the original tool input, and then check the MCP tool enabled state, credentials, and server response information.

* `user.tool_result` or `user.custom_tool_result`: Check the execution logs of the business application or worker, and confirm that `is_error`, `content`, and the call ID match the actual result.

Do not resend the original task before the tool result has been fully processed; otherwise, the same tool may be executed again.

### Handle session errors

`session.error` is not necessarily caused by tool configuration. First read `error.type`, `error.message`, and `error.retry_status.type`, and locate the issue together with the last event before the error.

|Retry status. |Fix |
|---|---|
|`retrying` |The server is automatically retrying. Continue consuming events and do not resend the task. |
|`exhausted` |The retry attempts for the current turn have been exhausted. Wait until the session returns to `idle`, then fix the input or configuration before sending the task again. |
|`terminal` |The session will enter `terminated`, and you can no longer send events. Create a new session after fixing the configuration. |

If the error occurs during the MCP call phase, continue checking based on the error type:

* `mcp_authentication_failed_error`: Check the authentication configuration of the MCP server, the vaults referenced by the session, and credential validity.

* **`mcp_connection_failed_error`**: Check the MCP server URL, network connectivity, and service status.

After fixing the MCP configuration, update the agent and create a new session or upgrade the existing session. If `error.type` is not an MCP\-related type and there is no tool call event before the error, check model calls or session runtime issues first. Do not attribute all `session.error` events to tools. For event parameters and other error types, see [Session event structure reference](sessions-events-reference-v2.md).

### Check file and network isolation

> **Note**

> Disabling `write`, `edit`, `web_fetch`, or `web_search` does not mean strong isolation is in place.`read`, `glob`, and `grep` may still access sensitive files; if the agent can still use `bash`, or the environment allows unrestricted network access, commands may still read or modify files and access the network. When strong isolation is required, also restrict the basic tools, environment file and network policies, and external credential permissions.

## Related documents

[Agent](define-agent.md)

An agent is a configuration template that contains basic information, system prompt, and extended capabilities.

[Skills](skills.md)

Skills are used to add domain knowledge, operation processes, and best practices to an agent.

[MCP](mcp.md)

MCP (Model Context Protocol) is used to connect tools and data sources from third\-party systems to an agent.

[Tool permission policies](tool-permission-policy.md)

Tool permission policies are used to control whether tool calls initiated by an agent are executed automatically or paused for confirmation from business applications.
