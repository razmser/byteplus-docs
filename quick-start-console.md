# Quick start (console)

The ModelArk Managed Agents console provides a visual interface, so you can create, configure, and test agents without writing API code. You can interactively refine agent configurations in the console. After you verify that the behavior meets expectations, you can use the agent ID to integrate the agent into your business code.

> For first\-time access, sign in to the [ModelArk console](https://ai.byteplus.com/ark/region:ap-southeast-1/managed-agents/agents?projectName=default) and go to the **Managed Agents** module.

## Prerequisites

### 1. Get an API key

Go to the [API keys page](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey), click **Create API key**, and use it as the access key for later integration with your business code.

### 2. Activate the Managed Agents service

Go to the [Managed Agents activation page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?advancedActiveKey=manageAgent) to activate the service.

### 3. Activate the model service

Go to the [Model activation page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement) to activate the models you need to use.

## Build an agent

### 1. Create an agent

An agent is a versioned and reusable intelligent agent configuration asset. It defines roles, capabilities, behavior boundaries, and runtime rules, and serves as the template and baseline for all task runs.

Go to the [ModelArk console](https://ai.byteplus.com/ark/region:ap-southeast-1/managed-agents/agents?projectName=default) and enter the **Managed Agents** module. On the **agents** page, click **Create Agent** and follow the UI guide to fill in the following information:

* **Model configuration and system prompt**: Select a **model** and write a **system prompt** to define the agent's role, working style, and work principles.

* **Skills**: Add a preset **skill** from **BytePlus SkillHub**, or upload a custom **skill** locally to extend the agent's capabilities.

* **Tools**: Enable built\-in tools from the **agent toolset**, such as basic tools like `bash`, `edit`, `read`, and `write`, so the agent can read and write files and run commands.

* **MCPs**: Connect to remote **MCP server** by URL to integrate external system capabilities into the agent.

* **Multi agents**: Combine multiple agents into a collaborative workflow. One agent can call another agent to complete subtasks.

After you save the configuration, the console generates a permanent `agent_id` for the agent. A new version is automatically generated each time you modify the configuration. Version comparison, rollback, and canary release are supported.

### 2. Create an environment

Define the runtime environment template for the agent, including preset dependencies, environment variables, and artifact storage. Bind an environment when creating a session to ensure code runs in a consistent environment.

On the [Environments page](https://ai.byteplus.com/ark/region:ap-southeast-1/managed-agents/environments?projectName=default), click **Create Environment** and follow the UI guide to fill in the following information:

* **Type**: Currently, only **Cloud\-hosted** is supported. The sandbox is started on demand and automatically hibernated by ModelArk.

* **Pre\-packaged**: Add `pip` and `apt` dependencies as needed, such as `numpy` and `pandas`. They will be installed when the sandbox starts for the first time.

* **Environment variables**: Inject key\-value pairs into the sandbox process. They are automatically loaded when the sandbox starts, and can be directly read by code and commands run by the agent. They are commonly used to configure non\-sensitive business parameters, such as setting the time zone to `TZ=Asia/Shanghai`.

* **Product storage**: Optional. If you need to retain the final artifacts that the agent writes to `/mnt/session/outputs/` for the long term, select an authorized TOS directory that you own. If you do not select one, ModelArk managed TOS is used, and you need to download the required files before the platform TTL expires.

After you save the configuration, you get a permanent `environment_id`.

> **Tip**

> Your TOS bucket must be in the same region as the Managed Agents service deployment. When you select a TOS directory for the first time, if the console indicates that it is not authorized, follow the page guide to complete project authorization first.

## Debug an agent

Test the agent interactively in the console. Each debugging run creates a session, which shows the event stream, tool calls, and complete input and output records, and can be replayed at any time.

A session is an independent agent task run. It includes the complete lifecycle from initiation and execution to completion:

1. On the [Sessions page](https://ai.byteplus.com/ark/region:ap-southeast-1/managed-agents/sessions?projectName=default), click **Create**, and select the **agent** and **environment** to bind. By default, a session inherits the artifact storage configuration of the environment.

2. Debug the session on the **Session details** page:

   * **Initiate**: Enter natural language instructions in the **session panel**, or upload attachments, such as spreadsheets, documents, code, and images, as task input.

   * **Observe**: The **Event timeline (trace)**  shows the agent's thinking process, tool calls, code execution, error retries, and other end\-to\-end nodes in real time.

   * **Verify**: After the session ends, view runtime statistics such as **final deliverables**, **total duration**, **token consumption**, and **number of tool calls** to determine whether the agent behavior meets expectations.

Repeatedly adjust the **system prompt**, **tools**, and **skills** until the session debugging run results meet expectations.

## From prototype to code

After the agent configuration is verified, you can integrate it into your business code in two steps.

### 1. Copy IDs from the console

* Copy `agent_id` from the **Agent details page**.

* Copy `environment_id` from the **Environment details page**.

* Go to the [API keys page](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey), click **Create API key**, and use it as the access key.

### 2. Reference the IDs in code to create a session

Enter `agent_id`, `environment_id`, and the API key in the following request to create a session bound to the agent and environment:

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "agent": "agent-xxxxxxxxxxxxxx-xxxxx",
    "environment_id": "env-xxxxxxxxxxxxxx-xxxxx",
    "title": "My first session"
  }'
```

After you get the returned `session_id`, you can use the Events API to send messages to the session and receive agent response events in streaming mode through SSE. For details, see [Session event stream](session-event-stream.md).
