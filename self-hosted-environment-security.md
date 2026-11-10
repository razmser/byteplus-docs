# Self-hosted environment security

This topic describes the security responsibility model, data boundaries, and best practices for self\-hosted environments, helping enterprise security, platform, and operations teams complete pre\-deployment security reviews.

## Security responsibility model

Self\-hosted environments use a **shared responsibility model**: ModelArk is responsible for control plane security, and the enterprise is responsible for execution plane security.

|Scope |ModelArk responsibility |Your responsibility |
|---|---|---|
|Model inference and agent orchestration |Provides platform capabilities and secures the inference workflow |Configuration and usage |
|Work queue and event stream |Server\-side authentication, transport encryption, and persistence |Securely store credentials |
|Worker and sandbox |Not responsible for the enterprise\-side runtime environment |Deployment, isolation, monitoring, and operations |
|Enterprise network and internal systems |Not managed |Configure access permissions and enforce least privilege |
|Enterprise\-side tools |Dispatches the primary agent's Bash and file tool calls and receives execution results |Restrict file, command, process, and network permissions on the worker side |
|ModelArk\-side tools |Execute web, MCP, and multimodal tools |Select and configure tools according to data boundary requirements |
|Custom tools |Provides call and result events |Execute on the business application side and restrict actual permissions |
|Credential management |Validates the ModelArk API key provided by the caller |Allocate, store, rotate, and revoke API keys for business applications and workers |

## Trust boundary

The trust boundary of a self\-hosted environment lies between the ModelArk control plane and the enterprise execution plane:

* **ModelArk control plane**: Hosts model inference, agent orchestration, session management, and event streams. Tool calls and tool results pass through the control plane.

* **Enterprise worker**: Deployed on enterprise infrastructure, polls the work queue, and executes the primary agent's `bash`, `read`, `write`, `edit`, `glob`, and `grep` in the enterprise sandbox.

* **Enterprise sandbox**: Each session should use an independent execution environment, such as a container, VM, or isolated directory on a physical machine.

* **ModelArk\-side tools**: Web, MCP, and multimodal tools are executed on the ModelArk side and are not constrained by the file and network policies of the enterprise sandbox.

> **Note**

> Self\-hosting does not mean all business data stays in the enterprise environment. Tool calls and tool results pass through the ModelArk control plane; web, MCP, and multimodal tools are also executed on the ModelArk side. When you need to restrict data from leaving the domain, disable tools that do not meet data boundary requirements, and filter sensitive information in tool results on the worker or business application side.

## How data flows

|Data |Whether it enters the ModelArk control plane |Whether it remains in the enterprise environment |Note |
|---|---|---|---|
|User message |Yes |Optional retention |Messages sent by the business application are persisted in the ModelArk event stream. |
|Enterprise\-side tool call parameters |Yes |Received by the worker |Workers only receive the primary agent's Bash and file tool calls |
|ModelArk\-side tool call parameters |Yes |Does not enter the enterprise sandbox by default |Web, MCP, and multimodal tools are executed on the ModelArk side |
|Tool results |Yes |Can be retained |Results returned by enterprise workers, business applications, or ModelArk drive the next round of inference. |
|Enterprise file content |Not uploaded by default |Yes |File reads and writes are completed locally in the enterprise sandbox |
|Command execution process |No |Yes |Commands such as Bash are executed by enterprise hosts |
|Network requests |Depends on the tool type |Determined by the tool type |Requests initiated by Bash are controlled by the enterprise policies; requests from web, MCP, and multimodal tools are initiated on the ModelArk side. |

## Credential security

### Credential separation

Business applications and workers currently both use standard ModelArk API keys. To reduce the exposure of a single credential, we recommend allocating independent API keys separately:

|Credential type |Purpose |Holder |Permission scope |
|---|---|---|---|
|Business application API key |Create sessions, send messages, and subscribe to events |Business application |Depends on the account, project, and credential policy associated with the API key |
|Worker API Key |Poll and process work items |Worker host |Depends on the account, project, and credential policy associated with the API key, and is not automatically restricted to a single environment. |

> **Warning**

> Worker API keys are not environment\-specific credentials.`MA_ENVIRONMENT_ID` is used only to identify the work queue and does not narrow the authorization scope of the API key. If an API key is leaked, an attacker may access other ModelArk resources that the credential is authorized to operate on. Do not place any API key in the agent working directory, tool process environment, or logs.

### Credential storage

* Store the API keys used by business applications and workers separately in an enterprise secret manager, such as Kubernetes Secret, HashiCorp Vault, or an equivalent service.

* Do not write API keys to code repositories, Docker images, startup script bodies, the `/workspace` directory, or log files.

* Allocate independent API keys for different deployment environments or worker clusters to facilitate separate rotation, revocation, and auditing.

* Keep only the credential suffix in logs, such as `...a1b2`, and do not output the full credential.

### Credential rotation and revocation

* Rotate worker API keys regularly according to the enterprise credential policy.

* If a credential is leaked or an employee leaves, immediately revoke the existing API key and generate a new key.

* During rotation, start the worker with the new API key first. After confirming that it is online, revoke the old key and stop the old worker.

## Sandbox isolation

Each session should use an independent execution environment to prevent data leaks and privilege escalation across sessions.

### Basic requirements

* Use an independent working directory or sandbox for each session.

* Run workers and agent tools as a non\-root user.

* Limit CPU, memory, process count, and execution time.

* **Do not mount the following directories to the sandbox**:

   * Docker socket (`/var/run/docker.sock`)

   * Host root directory (`/`)

   * Credential directories, such as `/etc/ark/` and `~/.aws/`

   * Working directories of other sessions

### Long\-running worker mode

If you use a long\-running worker (`EnvironmentWorker`), ensure that the worker's root working directory is used only for agent execution, and let the SDK create an independent subdirectory for each session. To scale out, start multiple worker processes or pods first, and set a separate `MA_WORKER_ID` for each instance.

Do not let multiple workers share the same session working directory, and do not use credential directories, host system directories, or other business directories as the worker working directory.

### Separate sandbox per session

We recommend using one Kubernetes pod per session. Each pod has an independent file system, network namespace, and resource limits.

## File system security

Agents access the enterprise file system through Bash and file tools. You must restrict the accessible scope:

* Restrict the root directory that the agent can read from and write to, such as `/workspace`, and prohibit access to system directories such as `/etc`, `/proc`, and `/sys`.

* **Prevent path traversal**: verify that file paths do not contain `..` or escape through symbolic links.

* Limit file types and quantities to prevent the agent from exhausting disk space by writing a large number of files.

* Custom file tools should repeat path validation to ensure paths are within the allowed scope.

* Clean up temporary session data after the task ends.

## Network security

A self\-hosted worker only needs outbound access to the ModelArk API and does not need to open inbound ports:

* The worker host only needs outbound HTTPS access to `https://ark.ap-southeast.bytepluses.com/api/v3`.

* If the primary agent needs to access enterprise intranet services through Bash, configure a least\-privilege domain or address allowlist.

* Prevent Bash from accessing unauthorized networks through proxies or tunnels.

* Do not directly expose database administration credentials to the agent's tool calls.

> **Note**

> Enterprise network policies only constrain requests initiated by the worker and sandbox.`web_fetch`, `web_search`, MCP, and multimodal tools are executed on the ModelArk side and do not use the network egress of the enterprise sandbox. When reviewing data egress risks, separately check the inputs, credentials, and results of these tools.

## Tool execution security

The worker executes the primary agent's `bash`, `read`, `write`, `edit`, `glob`, and `grep` on the enterprise side. We recommend implementing the following controls:

* Validate tool names and input parameter schemas, and reject undefined tool calls.

* Add manual approval or secondary confirmation for high\-risk tools, such as database writes and production deployments.

* The worker persists execution status and results by tool call ID; when it receives the same call again, it reuses the result directly and does not execute the tool again.

* Distinguish read\-only operations from write operations, and apply stricter permission control to write operations.

* **Restrict shell commands**: prohibit dangerous commands such as `sudo`, `chmod 777`, and `rm -rf /`.

* Clear environment variables that contain credentials before executing agent tools.

> **Note**

* > **Server\-side idempotency boundary**: the server's idempotent handling of tool results applies only when results are returned, and cannot prevent the worker from executing local tools again. The worker must save the tool call ID and the "in progress" status before execution, and save the result after execution; when it receives a duplicate call, it should reuse the existing result directly.

* > **Unknown execution status**: if the worker exits after the tool has executed but before the result has been saved, it cannot confirm after recovery whether side effects such as sending emails or writing to the database have occurred. Do not automatically retry such calls. Mark the status as unknown and have the business side verify it.

* > **Execution ledger persistence**: the execution ledger must be stored in storage that the worker can still read after it restarts. The Go SDK's `EnvironmentWorker` writes the ledger to the worker's working directory, so that directory must also be persisted. When using another SDK or a custom worker, first confirm whether the current version provides an equivalent mechanism; if not, implement one yourself.

## Logging and audit

### Recommended logging

* Worker startup, shutdown, and abnormal exits

* Poll, Ack, Heartbeat, and Complete operations for work items

* Tool call names, parameters, execution time, and result status

* Authentication failure events

* Credential rotation and revocation operations

Associate the following identifiers in logs to facilitate cross\-system tracing:

* **`session_id`**: session ID

* **`work_id`**: work item ID (same as the session ID)

* **`environment_id`**: environment ID

* **`request_id`**: ModelArk API request ID

### Do not log

* Full credential content (log only the last digits)

* Full text of user messages (if it contains sensitive information)

* Sensitive data in tool results (such as database query results or internal API responses)

## Worker disconnection and work reclaiming

### Heartbeat mechanism

The worker uses heartbeats to maintain the lease on claimed work items. The default TTL is 300 seconds. The worker should renew the lease before the TTL expires. If heartbeats are interrupted, the work item enters `stopped` after the lease from the most recent successful heartbeat expires and a 60\-second grace period passes.

The server does not automatically assign claimed work items to other workers. If a work item stops because its lease expires, the original worker that claimed the work item can use the same non\-empty `Ark-Worker-ID` in Ack and Heartbeat requests, and restore the work item directly to `active` through a subsequent Heartbeat. This process does not requeue the work item. Work items claimed by anonymous workers or other workers, or work items stopped for other reasons, cannot be restored through Heartbeat.

### Reclaim unclaimed work

`reclaim_older_than_ms` is the claim timeout threshold for work, in milliseconds. When calling the Poll API, use this parameter to set how long a work item waits to be claimed after it is fetched. The worker must call the claim work API within this period; if it is not claimed before the timeout, the work item remains `queued` and other workers can fetch it again.

> **Note**

> Claimed work is not affected by `reclaim_older_than_ms`. After a worker's heartbeats are interrupted, the server does not assign the work item to another worker based on this parameter. The worker should check `state` and `lease_extended` in each Heartbeat response. Continue execution only when the response is `active` and `lease_extended` is `true`; otherwise, stop immediately and release local resources.

### Late tool results

After a work item enters the `stopped` state, sending a new tool result causes the work item to re\-enter the `queued` state and continue driving the session. This behavior is triggered by a new session event. It is not a reclaim operation and is not an automatic takeover after the worker is disconnected. After a session enters the `terminated` state, it no longer accepts tool results, and the request returns 403 `OperationDenied.SessionTerminated`.

## Pre\-launch security checklist

Before deploying a self\-hosted environment, use the following table to confirm security controls and assess the risk of any unmet items based on the reasons.

|Check item |Cause |
|---|---|
|Use separate API keys for the business application and the worker |This supports independent rotation, revocation, and auditing, and prevents changes to worker credentials from also affecting the business application. |
|Include the account and project permissions of the worker API key in the security review |`MA_ENVIRONMENT_ID` only locates the work queue and does not restrict API key permissions to a single environment. |
|Store the API key in a secret manager |Prevent credentials from entering code repositories, images, working directories, or logs. |
|Use a separate working directory or sandbox for each session |Prevent different sessions from reading or overwriting each other's data. |
|Run the worker and agent tools as a non\-root user |Reduce the risk that tool processes access host system resources. |
|Restrict file system access to the allowed scope and validate paths |Prevent path traversal or symlink escapes from causing the agent to access files outside the working directory. |
|Configure network access with least privilege and do not open inbound ports. |Restrict the agent from accessing unauthorized networks and reduce the exposure of the worker host. |
|Review the data boundaries of web, MCP, and multimodal tools. |These tools are executed on the ModelArk side and are not constrained by the network and file policies of the enterprise sandbox. |
|Validate names and input parameters against the tool schema. |Prevent undefined or malformed tool calls from entering the enterprise execution environment. |
|Add approval or secondary confirmation for high\-risk tools. |Prevent the agent from performing high\-risk operations such as database writes and production deployments without confirmation. |
|Persist the worker execution ledger and reuse results by tool call ID. |When the worker retries or receives a duplicate call, it does not execute operations with side effects, such as sending messages or writing to a database, again. |
|Redact logs |Prevent logs from leaking full credentials, user messages, or sensitive data in tool results. |
|Gracefully exit and clean up resources after the worker receives a stop signal. |Prevent local tasks from continuing to run after the work item has stopped, and promptly release process, file, and network resources. |
|Run drills for worker disconnection handling and unclaimed work reclaiming. |Verify that work item status in exceptional cases matches the documentation, and avoid mistakenly treating claimed work as tasks that can be taken over automatically. |

## Security boundaries

When using a self\-hosted environment, you must understand the following security boundaries:

* The ModelArk control plane does not validate the isolation level of the enterprise sandbox; the enterprise is fully responsible for isolation.

* ModelArk does not monitor the security status of enterprise worker hosts, such as system vulnerabilities or abnormal processes.

* The file and network policies of the enterprise sandbox do not constrain web, MCP, and multimodal tools executed on the ModelArk side.

* While tool permission is pending, the worker must continuously maintain heartbeats while waiting for user approval.

* Enterprise\-customized audit log formats and delivery destinations are not supported.
