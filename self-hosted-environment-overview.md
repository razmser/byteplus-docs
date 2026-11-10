# Self-hosted environment overview

This topic describes the use cases, how it works, and capability boundaries of the ModelArk Managed Agents self\-hosted environment, helping you decide whether to choose a self\-hosted environment.

## What is a self\-hosted environment

A self\-hosted environment runs an agent's tool execution, file system, and network on your enterprise's own infrastructure, while ModelArk continues to host model inference, agent orchestration, session management, and event streams.

In the current tool system, the execution capabilities migrated to enterprise workers are the primary agent's `bash`, `read`, `write`, `edit`, `glob`, and `grep`. The executors of web, MCP, multimodal tools, and custom tools do not change with the environment type.

**ModelArk is responsible for**:

* Model inference and agent orchestration

* Session lifecycle management

* Event stream persistence and real\-time push

* Work queue generation and scheduling

**The enterprise is responsible for**:

* Deploying worker processes to poll for and execute work items

* Providing a sandbox execution environment (container, VM, or physical machine)

* Managing the file system, network policies, and internal resource access used by enterprise\-side tools

* Controlling images, compute resources, and auditing

A self\-hosted environment continues to use ModelArk\-hosted agent orchestration, session lifecycle, and event protocols, but its execution\-plane capabilities differ from the cloud environment. Self\-hosted sessions do not support mounting files, memory stores, or TOS objects through `resources`, and dependency packages, environment variables, network policies, and artifact storage cannot be configured through the environment. Related inputs, runtime configurations, and artifact transfer must be prepared and managed by the enterprise in workers and sandboxes.

## Supported use cases

The following scenarios are suitable for using a self\-hosted environment:

* **Sensitive code or files cannot enter the platform cloud sandbox**: Data compliance requires enterprise code, secrets, or internal files to stay within the enterprise's control.

* **The agent needs to access enterprise intranet APIs, databases, or code repositories**: Tool calls need to access services that are reachable only on the enterprise intranet and cannot be exposed over the public internet.

* **The enterprise needs to control images, network policies, and compute resources**: The agent execution environment has customization requirements, such as specifying the operating system, preinstalled tools, or network allowlists.

* **The enterprise already has container, Kubernetes, or MicroVM infrastructure**: You want to reuse existing cluster scheduling, monitoring, and logging systems.

## Unsupported use cases

The following scenarios are not recommended for using a self\-hosted environment:

* **You want zero operations and maintenance and on\-demand automatic sandbox startup**: The cloud environment is fully managed by ModelArk and does not require the enterprise to deploy or maintain workers.

* **You do not have the capability to securely isolate tool execution**: A self\-hosted environment requires the enterprise to implement sandbox isolation. Otherwise, the agent may access host resources beyond its authorization.

* **You only need to call a small number of external business functions**: If the tool only calls existing enterprise HTTP APIs, consider [custom tools](tools.md) or [MCP](mcp.md) first. You do not need to deploy workers.

## Differences from the cloud environment

|Dimension |Cloud environment |Self\-hosted environment |
|---|---|---|
|Primary agent's Bash and file tools |Executed in the ModelArk cloud sandbox |Executed by enterprise workers in enterprise sandboxes |
|Web, MCP, and multimodal tools |Executed on the ModelArk side |Executed on the ModelArk side |
|Custom tools |Executed by the business application |Executed by the business application |
|Sandbox lifecycle |Managed by ModelArk |Managed by the enterprise |
|Files and network in enterprise sandboxes |Not applicable |Enterprise policies only constrain tools executed by workers |
|Dependency packages and environment variables |Configured through the environment |Managed by the enterprise |
|Session resources |Files, memory store, or TOS objects can be mounted through `resources` |Mounting through `resources` is not supported; input files are prepared by the enterprise execution plane. |
|Artifact storage |Can be configured through `config.tos` |Configuration through `config.tos` is not supported; artifact persistence and transfer are the responsibility of the enterprise execution plane. |
|Worker |Not required |Deployment required |
|Tool call results |Enter the ModelArk control plane |Enter the ModelArk control plane |
|Operations and maintenance responsibilities |Mainly ModelArk |The enterprise is responsible for the execution plane |

## How it works

The collaboration process between the ModelArk control plane and the enterprise execution plane is as follows:

1. The business application creates a session through the Session API and sends a user message.

2. The ModelArk control plane completes model inference, generates tool calls, and writes them to the work queue.

3. The enterprise worker polls the environment's work queue and claims a work item.

4. When the primary agent calls `bash`, `read`, `write`, `edit`, `glob`, or `grep`, the worker executes it in the enterprise sandbox and returns the result.

5. When the primary agent calls web, MCP, or multimodal tools, ModelArk completes the execution. When the agent calls custom tools, the business application completes the execution.

6. Tool results enter the ModelArk control plane and drive the next round of inference.

7. After the task ends, the worker cleans up the execution environment and continues polling for the next work item.

> **Tip**

> Tool calls and tool results pass through the ModelArk control plane, and agent orchestration and model inference are still completed on the ModelArk side. Self\-hosting only changes where the primary agent's Bash and file tools are executed. It does not change the executors of web, MCP, multimodal, and custom tools. For details, see [Self\-hosted environment security](self-hosted-environment-security.md).

## Core objects

|Object |Note |
|---|---|
|**Environment** |Agent runtime configuration. When `type` is `self_hosted`, it indicates a self\-hosted environment. Defines the execution location, but does not include configurations for dependency packages, environment variables, or network policies. |
|**Worker** |A process deployed by the enterprise, responsible for polling the work queue, maintaining heartbeats, and executing the primary agent's Bash and file tools. |
|**Work Item** |A task item generated by ModelArk for each session that requires tool execution, containing the information needed for session execution. |
|**Session** |An agent instance that runs in an environment and is used to execute specific tasks. The work ID is the same as the session ID. |
|**Events** |Atomic messages exchanged between the application and the agent, including user messages, tool calls, tool results, and status updates. |

## How a task runs

A self\-hosted task is completed jointly by the business application, the ModelArk control plane, and the enterprise worker. The business application starts tasks and executes custom tools. ModelArk handles model inference, agent orchestration, and task scheduling. The worker executes the primary agent's Bash and file tools in the enterprise sandbox.

After the business application starts a task, ModelArk generates a work item. The worker retrieves the work item and claims it with the ACK API. Each executor then handles tool calls and returns the results to ModelArk. The agent continues inference based on the results or ends the task.

1. **The business application starts the task**: The business application calls the [Create session](create-session-api.md) API to create a session bound to a self\-hosted environment. It then calls the [Send session event](send-event-api.md) API to send a `user.message` event. After ModelArk receives the event, it starts the current round of agent orchestration and model inference.

2. **ModelArk generates work to be executed**: The ModelArk control plane generates a work item for the current session and writes it to the environment's work queue. The work item is used to hand off the session execution task to the enterprise worker.

3. **The worker retrieves and claims work**: The worker calls the [Poll work with long polling](poll-work-api.md) API to retrieve a work item from the work queue. It then calls the [Acknowledge work](acknowledge-work-api.md) API (ACK) to confirm that the current worker will execute it. After the ACK succeeds, the worker calls the [Stream session events](stream-events-api.md) API to subscribe to the corresponding session events, and calls the [Renew work lease](record-work-heartbeat-api.md) API to send heartbeats, keeping the work lease valid.

4. **Each executor handles tool calls**: When the primary agent calls `bash`, `read`, `write`, `edit`, `glob`, or `grep`, the worker executes it in the enterprise sandbox. When it calls web, MCP, or multimodal tools, ModelArk executes them. When it calls custom tools, the business application executes them.

5. **Tool results drive the next round of inference**: Each executor returns tool results to ModelArk. ModelArk passes the results to the primary agent to continue model inference. If new tool calls are generated later, each executor continues handling them according to the boundaries described in the previous step.

6. **The task ends and the execution environment is cleaned up**: The preceding process continues until the session enters `idle` or a terminal state. The worker then completes the work item, cleans up the enterprise sandbox used for this task, and continues polling for the next work item.

## Data and responsibility boundaries

A self\-hosted environment only changes where some tools are executed. Model inference and event processing still take place in the ModelArk control plane. The following are the key data flows:

|Data |Whether it enters the ModelArk control plane |Whether it remains in the enterprise environment |Note |
|---|---|---|---|
|User message |Yes |Optional retention |Messages sent by the business application are persisted in the ModelArk event stream. |
|Tool call parameters |Yes |Partial |Workers only receive the primary agent's Bash and file tool calls |
|Tool results |Yes |Can be retained |Results returned by enterprise workers, business applications, or ModelArk drive the next round of inference. |
|Original enterprise files |Not uploaded by default |Yes |File read results may contain file content and should be filtered according to data policies before being returned. |
|Command execution process |No |Yes |Commands such as Bash are executed by enterprise hosts |
|Network requests |Depends on the tool type |Determined by the tool type |Requests initiated by Bash are controlled by the enterprise; requests from web, MCP, and multimodal tools are initiated on the ModelArk side. |

> **Note**

> Self\-hosting does not mean all business data stays in the enterprise environment. Tool calls and tool results pass through the ModelArk control plane; web, MCP, and multimodal tools are also executed on the ModelArk side. When you need to restrict data from leaving the domain, disable tools that do not meet data boundary requirements, and filter sensitive information in tool results on the worker or business application side. For details, see [Self\-hosted environment security](self-hosted-environment-security.md).

## Supported worker capabilities

* Ark Runtime SDK (Go, Python, Java), supporting outbound long polling to claim work items.

* Resident worker mode (`EnvironmentWorker`), suitable for long\-running worker hosts.

* Per\-session independent sandbox mode (`HandleItem`), suitable for one pod per session in Kubernetes.

* Heartbeat lease renewal, default TTL 300 seconds.

* **Unclaimed work reclamation**: The `reclaim_older_than_ms` parameter is the claim timeout threshold, in milliseconds. If a worker does not claim the work item within this time after pulling it, another worker can fetch the work item again.

> **Note**

> This parameter is used only to set the claim timeout threshold for unclaimed work. After a work item is claimed, it is not affected by this parameter. When a worker's heartbeat is interrupted, the server updates the work item status to `stopped` after the lease expires and a 60\-second grace period has elapsed. Besides, it does not automatically assign the work item to another worker. When the original worker that claimed the work item resumes heartbeats using the same non\-empty worker ID as in the ACK, the work item that was stopped due to lease expiration can be restored directly to `active`.

## Manage the environment lifecycle

If you no longer want new sessions to use a self\-hosted environment but still need to retain the configuration for queries, call [Archive environment](archive-environment-api.md):

```Bash
curl -sS --fail-with-body \
  "https://ark.ap-southeast.bytepluses.com/api/v3/environments/<ENVIRONMENT_ID>/archive" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{}'
```

Archiving only changes the availability of the environment configuration on the ModelArk side. ModelArk still retains the configuration for queries. Enterprise\-side workers, sandboxes, files, logs, and artifacts are not copied, migrated, or backed up, and the archive operation does not stop workers or clean up enterprise\-side resources.

After archiving, the environment can no longer be updated or used to create new sessions, but existing sessions continue to use the environment configuration saved when they were created and complete tasks according to the original worker protocol. Before deleting an environment, you must delete all associated sessions.

## Next step

* **Configure and run a self\-hosted environment**: [Configure and run a self-hosted environment](configure-self-hosted-environment.md); choose the Go, Python, or Java SDK to start a worker and complete one session tool execution.

* **Self\-hosted environment security notes**: Read [Self-hosted environment security notes](self-hosted-environment-security.md) to learn about trust boundaries, credential management, and sandbox isolation.
