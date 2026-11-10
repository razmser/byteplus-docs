# Managed Agents

ARK Managed Agents: the full lifecycle of Agent / Skill / Env / Session / File / Memory Store / Vault, plus the shortcut workflows around a session.


* The `agent` domain covers all eight resource types: the control plane (Agent/Skill/Env definitions) goes through ForTop/OpenTOP, the data plane (session runtime, files) connects directly;

* The shortcut workflows split by lifecycle stage: `+new-agent` / `+iterate` create and modify, `+new` starts a session, `+tail` / `+debug` / `+export` observe and export;

* Typical chain: `agent create` → `env create` → `session create` → `session events send/stream`.


<span id="debug-command"></span>
## +debug

Diagnose a managed agent session (events, errors, and execution state by session\-id)

```Bash
arkcli +debug <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--limit` |int |Maximum recent events to inspect |
|`--session-thread-id` |string |Filter events by session thread id |


<span id="export-command"></span>
## +export

Export managed agent session diagnostics to a local archive

```Bash
arkcli +export <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--output` |string |Output .tar.gz path |
|`--limit` |int |Maximum events to export |
|`--session-thread-id` |string |Filter events by session thread id |


<span id="iterate-command"></span>
## +iterate

Update a managed agent, create a fresh session, then optionally chat

```Bash
arkcli +iterate <agent-id>
```



|Flag |Type |Description |
|---|---|---|
|`--env-id` |string |Environment id for the new session; defaults to the latest environment (alias of \-\-environment\-id) |
|`--environment-id` |string |Environment id for the new session; defaults to the latest environment |
|`--title` |string |New session title |
|`--resource` |string |Resources array for the new session |
|`--tos-path` |string |TOS directory to mount, e.g. tos://bucket/prefix/ |
|`--vault-id` |string_slice |Vault id for the new session; comma\-separated or repeated |
|`--vault-ids` |string_slice |Vault IDs for the new session; comma\-separated or repeated |
|`--tags` |string |Session tags map or tag array |
|`--agent-overrides` |string |AgentWithOverrides object; inline JSON/YAML or @file |
|`--environment-overrides` |string |EnvironmentWithOverrides object; inline JSON/YAML or @file |
|`--message` |string |Send one message after creating the session, then exit after idle |
|`--session-thread-id` |string |Target session thread id for the first message / REPL |
|`--no-event-deltas` |bool |Do not request PRD event_start/event_delta frames; use complete events only |
|`--raw` |bool |Print raw NDJSON stream events when chatting |
|`--no-chat` |bool |Only update agent and create session; do not enter chat/REPL |
|`--diff` |bool |Preview update/session/chat requests without changing remote state |
|`--file` |string |Path to a JSON/YAML UpdateAgentRequest payload. Use \- or omit \-\-file to read stdin |
|`--version` |int |Optional current agent version; omitted or 0 reads GetAgent automatically, a positive value pins the concurrency check |
|`--name` |string |Agent name |
|`--display-name` |string |Agent display name |
|`--model` |string |Model ID or object with id/provider/speed/thinking/reasoning_effort/service_tier |
|`--speed` |string |Legacy\-compatible model speed |
|`--thinking` |string |Model thinking mode |
|`--reasoning-effort` |string |Model reasoning effort |
|`--service-tier` |string |Model service tier |
|`--description` |string |Agent description |
|`--system` |string |System prompt, or @file to read from disk |
|`--tool` |string_slice |Replace tool list; repeatable or pass an array, use [] to clear |
|`--mcp-server` |string_slice |Replace MCP server list; repeatable or pass an array, use [] to clear |
|`--skill` |string_slice |Replace skill list; repeatable or pass an array, use [] to clear |
|`--multiagent` |string |Multiagent config object |
|`--metadata` |string |Metadata patch map object; null value deletes a key |


<span id="domain-new"></span>
## +new

ARK Managed Agent runtime commands: start a new agent session (`+new session`), create a new agent definition (`+new-agent`), and iterate on an existing agent (`+iterate`).


* Task\-oriented entry points covering the define → session → iterate chain;

* Underlying resources are managed by the `agent` domain; these commands are the shortest path to getting an agent running.


<span id="new-session-command"></span>
### +new session

Create a fresh managed\-agent session, then chat

```Bash
arkcli +new session [agent-id]
```



|Flag |Type |Description |
|---|---|---|
|`--agent-id` |string |Agent id for creating a fresh chat session |
|`--agent-version` |int |Agent version for the fresh chat session |
|`--env-id` |string |Environment id for the fresh chat session (alias of \-\-environment\-id) |
|`--environment-id` |string |Environment id for the fresh chat session |
|`--title` |string |Fresh session title |
|`--resource` |string |Resources array for the fresh session |
|`--tos-path` |string |TOS directory to mount, e.g. tos://bucket/prefix/ |
|`--vault-id` |string_slice |Vault id for the fresh session; comma\-separated or repeated |
|`--vault-ids` |string_slice |Vault IDs for the fresh session; comma\-separated or repeated |
|`--tags` |string |Fresh session tags map or tag array |
|`--agent-overrides` |string |AgentWithOverrides object; inline JSON/YAML or @file |
|`--environment-overrides` |string |EnvironmentWithOverrides object; inline JSON/YAML or @file |
|`--message` |string |Send one message and exit after the session returns idle |
|`--session-thread-id` |string |Target session thread id |
|`--no-event-deltas` |bool |Do not request PRD event_start/event_delta frames; use complete events only |
|`--max-event-chars` |int |Maximum characters shown for pretty event text; default 2000, 0 means unlimited |
|`--raw` |bool |Print raw NDJSON stream events |


<span id="new-agent-command"></span>
## +new\-agent

Create a managed agent from structured fields, optionally forking an existing agent

```Bash
arkcli +new-agent
```



|Flag |Type |Description |
|---|---|---|
|`--fork` |string |Existing agent id to copy before applying overrides |
|`--from` |string |Alias of \-\-fork |
|`--no-echo` |bool |Skip the final GetAgent echo and print CreateAgent response only |
|`--file` |string |Path to a JSON/YAML CreateAgentRequest payload. Use \- or omit \-\-file to read stdin |
|`--name` |string |Agent name |
|`--display-name` |string |Agent display name |
|`--base-agent` |string |Base agent id |
|`--model` |string |Model ID or object with id/provider/speed/thinking/reasoning_effort/service_tier |
|`--speed` |string |Legacy\-compatible model speed |
|`--thinking` |string |Model thinking mode |
|`--reasoning-effort` |string |Model reasoning effort |
|`--service-tier` |string |Model service tier |
|`--description` |string |Agent description |
|`--system` |string |System prompt, or @file to read from disk |
|`--tool` |string_slice |Full Tools array replacement. If omitted, arkcli sends the default agent_toolset_20260701; pass [] to disable defaults |
|`--mcp-server` |string_slice |MCP server object; repeatable or pass an array |
|`--skill` |string_slice |Custom/Ark skill object; repeatable or pass an array. Bare skill\-xxx IDs omit Version to use the latest |
|`--skill-zip` |string_slice |Local skill zip package to upload via CreateSkill and attach as a custom skill; repeatable |
|`--multiagent` |string |Multiagent config object |
|`--metadata` |string |Metadata map object |
|`--tags` |string |Tags map or tag array |
|`--show-system` |bool |Show the full System prompt in table output |
|`--yes` |bool |In an interactive terminal, skip the [y/N] model\-activation prompt. Non\-interactive mode refuses activation unless ARKCLI_ALLOW_HEADLESS_ACTIVATION=1 is set. |


<span id="tail-command"></span>
## +tail

Tail managed agent session events

```Bash
arkcli +tail <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--after` |string |Cursor/event id after |
|`--before` |string |Cursor/event id before |
|`--since` |string |Cursor/time lower bound |
|`--created-after` |string |Event created_at lower bound (RFC3339) |
|`--created-before` |string |Event created_at upper bound (RFC3339) |
|`--type` |string_slice |Event type filter; repeatable or comma\-separated |
|`--session-thread-id` |string |Filter by session thread id |
|`--no-event-deltas` |bool |Do not request PRD event_start/event_delta frames; use complete events only |
|`--max-event-chars` |int |Maximum characters shown for pretty event text; default 2000, 0 means unlimited |
|`--order` |string |Sort order: asc \| desc |
|`--raw` |bool |Print raw NDJSON events instead of the human\-readable tail view |


<span id="domain-agent"></span>
## agent

Full lifecycle management for ARK Managed Agents: eight resource types — Agent, Skill, Environment (Env), Session, File, Memory Store, Vault (credentials / MCP OAuth).


* Control plane (Agent/Skill/Env definitions) prefers ForTop/OpenTOP; the data plane (session runtime, file upload/download) connects directly, and the CLI routes automatically;

* Typical chain: `agent create` → `env create` → `session create` → drive execution via `session events send/stream`;

* `session compact/upgrade` and `env work stats` handle long\-session compaction, hot upgrades, and compute usage auditing;

* `vault credentials` and `vault oauth-flow` manage secrets without exposing them in shell history.


<span id="agent-mcp-login-command"></span>
### agent +mcp\-login

Create a vault MCP OAuth flow, open the authorization URL, and wait for callback

```Bash
arkcli agent +mcp-login
```



|Flag |Type |Description |
|---|---|---|
|`--vault-id` |string (required) |Vault id |
|`--display-name` |string |Credential display name |
|`--name` |string |Alias of \-\-display\-name |
|`--mcp-server-url` |string (required) |MCP server URL |
|`--callback-host` |string |Local callback listen host (default: 127.0.0.1) |
|`--callback-port` |int |Local callback listen port; 0 means auto\-pick |
|`--callback-path` |string |Local callback HTTP path (default: /callback) |
|`--timeout` |string |Maximum time to wait for callback, e.g. 5m or 300s (default: 5m0s) |
|`--source` |string |Flow source marker (default: arkcli\-mcp\-login) |
|`--client-id` |string |OAuth client id for custom MCP/provider override mode |
|`--client-secret` |string |OAuth client secret, or @file to read from disk |
|`--no-open` |bool |Do not open browser automatically; print authorization URL instead |


<span id="agent-agent-create-command"></span>
### agent agent create

Create a managed agent definition (name, model, system prompt, skills, tools)

```Bash
arkcli agent agent create
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML CreateAgentRequest payload. Use \- or omit \-\-file to read stdin |
|`--name` |string |Agent name |
|`--display-name` |string |Agent display name |
|`--base-agent` |string |Base agent id |
|`--model` |string |Model ID or object with id/provider/speed/thinking/reasoning_effort/service_tier |
|`--speed` |string |Legacy\-compatible model speed |
|`--thinking` |string |Model thinking mode |
|`--reasoning-effort` |string |Model reasoning effort |
|`--service-tier` |string |Model service tier |
|`--description` |string |Agent description |
|`--system` |string |System prompt, or @file to read from disk |
|`--tool` |string_slice |Full Tools array replacement. If omitted, arkcli sends the default agent_toolset_20260701; pass [] to disable defaults |
|`--mcp-server` |string_slice |MCP server object; repeatable or pass an array |
|`--skill` |string_slice |Custom/Ark skill object; repeatable or pass an array. Bare skill\-xxx IDs omit Version to use the latest |
|`--skill-zip` |string_slice |Local skill zip package to upload via CreateSkill and attach as a custom skill; repeatable |
|`--multiagent` |string |Multiagent config object |
|`--metadata` |string |Metadata map object |
|`--tags` |string |Tags map or tag array |
|`--show-system` |bool |Show the full System prompt in table output |
|`--yes` |bool |In an interactive terminal, skip the [y/N] model\-activation prompt. Non\-interactive mode refuses activation unless ARKCLI_ALLOW_HEADLESS_ACTIVATION=1 is set. |


<span id="agent-agent-delete-command"></span>
### agent agent delete

Delete a managed agent and cascade its sessions (requires confirmation, or pass \-\-yes)

```Bash
arkcli agent agent delete <agent-id>
```



|Flag |Type |Description |
|---|---|---|
|`--yes` |bool |Skip interactive confirmation for hard delete and cascading session deletion |


<span id="agent-agent-get-command"></span>
### agent agent get

Show a managed agent definition (config, version, bound skills; \-\-show\-system reveals the full system prompt)

```Bash
arkcli agent agent get <agent-id>
```



|Flag |Type |Description |
|---|---|---|
|`--version` |int |Agent version |
|`--show-system` |bool |Show the full System prompt in table output |


<span id="agent-agent-list-command"></span>
### agent agent list

List managed agents with filters (name, id, time range) and pagination

```Bash
arkcli agent agent list
```



|Flag |Type |Description |
|---|---|---|
|`--created-at-gte` |string |Filter by create time lower bound (RFC3339) |
|`--created-at-lte` |string |Filter by create time upper bound (RFC3339) |
|`--limit` |int |Page size |
|`--page` |string |Page cursor/page number |
|`--id` |string |Filter by agent id |
|`--ids` |string_slice |Filter by agent IDs, comma\-separated or repeated |
|`--name` |string |Filter by agent name |
|`--display-name` |string |Filter by agent display name |
|`--sort-by` |string |Sort field |
|`--sort-order` |string |Sort order: Asc \| Desc |
|`--show-system` |bool |Show the full System prompt in table output |


<span id="agent-agent-update-command"></span>
### agent agent update

Update a managed agent definition (name, model, skills; \-\-file accepts a JSON/YAML payload)

```Bash
arkcli agent agent update <agent-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML UpdateAgentRequest payload. Use \- or omit \-\-file to read stdin |
|`--version` |int |Optional current agent version; omitted or 0 reads GetAgent automatically, a positive value pins the concurrency check |
|`--name` |string |Agent name |
|`--display-name` |string |Agent display name |
|`--model` |string |Model ID or object with id/provider/speed/thinking/reasoning_effort/service_tier |
|`--speed` |string |Legacy\-compatible model speed |
|`--thinking` |string |Model thinking mode |
|`--reasoning-effort` |string |Model reasoning effort |
|`--service-tier` |string |Model service tier |
|`--description` |string |Agent description |
|`--system` |string |System prompt, or @file to read from disk |
|`--tool` |string_slice |Replace tool list; repeatable or pass an array, use [] to clear |
|`--mcp-server` |string_slice |Replace MCP server list; repeatable or pass an array, use [] to clear |
|`--skill` |string_slice |Replace skill list; repeatable or pass an array, use [] to clear |
|`--multiagent` |string |Multiagent config object |
|`--metadata` |string |Metadata patch map object; null value deletes a key |


<span id="agent-agent-versions-command"></span>
### agent agent versions

List version history of a managed agent

```Bash
arkcli agent agent versions <agent-id>
```



|Flag |Type |Description |
|---|---|---|
|`--limit` |int |Page size |
|`--page` |string |Page cursor/page number |


<span id="agent-env-create-command"></span>
### agent env create

Create a managed agent environment

```Bash
arkcli agent env create
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--name` |string |Environment name |
|`--description` |string |Environment description |
|`--config` |string |Env config object |
|`--setup-script` |string |Environment setup script, or @file; placed in Config.SetupScript |
|`--metadata` |string |Metadata map object |
|`--scope` |string |Environment scope |
|`--runtime-type` |string |Environment runtime: cloud \| self_hosted (Config.Type) |


<span id="agent-env-delete-command"></span>
### agent env delete

Delete a managed agent environment

```Bash
arkcli agent env delete <environment-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--yes` |bool |Skip interactive deletion confirmation |


<span id="agent-env-get-command"></span>
### agent env get

Get a managed agent environment

```Bash
arkcli agent env get <environment-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |


<span id="agent-env-list-command"></span>
### agent env list

List managed agent environments

```Bash
arkcli agent env list
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--limit` |int |Page size |
|`--page` |string |Page cursor/page number |
|`--id` |string |Filter by environment id |
|`--name` |string |Filter by environment name |
|`--sort-by` |string |Sort field |
|`--sort-order` |string |Sort order: Asc \| Desc |


<span id="agent-env-update-command"></span>
### agent env update

Update a managed agent environment

```Bash
arkcli agent env update <environment-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--name` |string |Environment name |
|`--description` |string |Environment description |
|`--config` |string |Env config object |
|`--setup-script` |string |Environment setup script, or @file; placed in Config.SetupScript |
|`--metadata` |string |Metadata map object |
|`--scope` |string |Environment scope |


<span id="agent-env-work-get-command"></span>
### agent env work get

Get a self\-hosted work item

```Bash
arkcli agent env work get <environment-id> <work-id>
```


<span id="agent-env-work-list-command"></span>
### agent env work list

List self\-hosted environment work items

```Bash
arkcli agent env work list <environment-id>
```



|Flag |Type |Description |
|---|---|---|
|`--limit` |int |Page size |
|`--page` |string |Page cursor/page number |


<span id="agent-env-work-stats-command"></span>
### agent env work stats

Get self\-hosted work queue statistics

```Bash
arkcli agent env work stats <environment-id>
```


<span id="agent-env-work-stop-command"></span>
### agent env work stop

Request a work item to stop

```Bash
arkcli agent env work stop <environment-id> <work-id>
```



|Flag |Type |Description |
|---|---|---|
|`--force` |bool |Immediately mark work stopped; does not prove the remote sandbox has exited |
|`--yes` |bool |Confirm stopping this work item |


<span id="agent-file-delete-command"></span>
### agent file delete

Delete a file from the ARK Files store

```Bash
arkcli agent file delete <file-id>
```



|Flag |Type |Description |
|---|---|---|
|`--yes` |bool |Confirm deletion |


<span id="agent-file-get-command"></span>
### agent file get

Get a file's metadata from the ARK Files store

```Bash
arkcli agent file get <file-id>
```


<span id="agent-file-list-command"></span>
### agent file list

List files uploaded to the ARK Files store

```Bash
arkcli agent file list
```



|Flag |Type |Description |
|---|---|---|
|`--limit` |int |Page size |
|`--after` |string |Cursor/file id after |
|`--order` |string |Sort order: asc \| desc |
|`--purpose` |string |File purpose (default: user_data) |
|`--session-id` |string |Filter files by managed\-agent session scope |


<span id="agent-file-upload-command"></span>
### agent file upload

Upload a local file or URL to the ARK Files store and return a file_id

```Bash
arkcli agent file upload
```



|Flag |Type |Description |
|---|---|---|
|`--path` |string |Local file path to upload as multipart field file |
|`--url` |string |HTTP/HTTPS URL or tos:// URI to register; mutually exclusive with \-\-path |
|`--purpose` |string |File purpose (default: user_data) |
|`--session-id` |string |Attach Ark\-Agent\-Session\-Id header for managed\-agent session\-scoped files |
|`--tos` |string |TOS storage object, e.g. '{bucket: b, prefix: arkfiles/}' |
|`--preprocess-configs` |string |Preprocess configs object |
|`--expire-at` |int |UTC Unix timestamp in seconds |
|`--wait-active` |bool |Poll file get until status becomes active before returning |
|`--wait-timeout` |int |Maximum seconds to wait with \-\-wait\-active; default 120 |
|`--wait-interval` |int |Polling interval in seconds with \-\-wait\-active; default 2 |


<span id="agent-file-wait-command"></span>
### agent file wait

Wait until a file reaches active status

```Bash
arkcli agent file wait <file-id>
```



|Flag |Type |Description |
|---|---|---|
|`--wait-timeout` |int |Maximum seconds to wait; default 120 |
|`--wait-interval` |int |Polling interval in seconds; default 2 |


<span id="agent-memory-store-create-command"></span>
### agent memory\-store create

Create a memory store

```Bash
arkcli agent memory-store create
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--name` |string |Memory store name |
|`--description` |string |Memory store description |


<span id="agent-memory-store-creators-command"></span>
### agent memory\-store creators

List memory store creators

```Bash
arkcli agent memory-store creators
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |


<span id="agent-memory-store-delete-command"></span>
### agent memory\-store delete

Delete a memory store

```Bash
arkcli agent memory-store delete <store-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--yes` |bool |Skip interactive deletion confirmation |


<span id="agent-memory-store-get-command"></span>
### agent memory\-store get

Get a memory store

```Bash
arkcli agent memory-store get <store-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |


<span id="agent-memory-store-list-command"></span>
### agent memory\-store list

List memory stores

```Bash
arkcli agent memory-store list
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--limit` |int |Page size |
|`--page` |string |Page cursor/page number |
|`--name` |string |Filter by memory store name |
|`--created-by` |string_slice |Created\-by user IDs, comma\-separated or repeated |


<span id="agent-memory-store-memories-batch-create-command"></span>
### agent memory\-store memories batch\-create

Batch create memories

```Bash
arkcli agent memory-store memories batch-create <store-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--items` |string |Memory items array |


<span id="agent-memory-store-memories-create-command"></span>
### agent memory\-store memories create

Create a memory

```Bash
arkcli agent memory-store memories create <store-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--path` |string |Memory path |
|`--content` |string |Memory content, or @file to read from disk |


<span id="agent-memory-store-memories-delete-command"></span>
### agent memory\-store memories delete

Delete a memory

```Bash
arkcli agent memory-store memories delete <store-id> [memory-id]
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--yes` |bool |Skip interactive deletion confirmation |
|`--memory-id` |string |Memory id |


<span id="agent-memory-store-memories-get-command"></span>
### agent memory\-store memories get

Get a memory

```Bash
arkcli agent memory-store memories get <store-id> [memory-id]
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--memory-id` |string |Memory id |
|`--view` |string |Memory view: basic \| full |


<span id="agent-memory-store-memories-list-command"></span>
### agent memory\-store memories list

List memories in a memory store

```Bash
arkcli agent memory-store memories list <store-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--limit` |int |Page size |
|`--page` |string |Page cursor/page number |
|`--path-prefix` |string |Memory path prefix (default: /) |
|`--updated-after` |string |Filter memories updated after this time (RFC3339) |
|`--depth` |int |List depth |


<span id="agent-memory-store-memories-update-command"></span>
### agent memory\-store memories update

Update a memory

```Bash
arkcli agent memory-store memories update <store-id> [memory-id]
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--memory-id` |string |Memory id |
|`--path` |string |Memory path |
|`--content` |string |Memory content, or @file to read from disk |
|`--content-sha256` |string |Expected current content sha256 for optimistic locking |


<span id="agent-memory-store-update-command"></span>
### agent memory\-store update

Update a memory store

```Bash
arkcli agent memory-store update <store-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--name` |string |Memory store name |
|`--description` |string |Memory store description |


<span id="agent-model-config-command"></span>
### agent model config

Query supported runtime parameter values and defaults for a foundation model

```Bash
arkcli agent model config <foundation-model-name>
```



|Flag |Type |Description |
|---|---|---|
|`--provider` |string |Model provider used to look up metadata (does not change account or product) (default: ark) |
|`--key` |string_slice |Metadata key; repeat or comma\-separate; omitted queries all keys |


<span id="agent-model-list-command"></span>
### agent model list

List foundation models available as managed\-agent backbones

```Bash
arkcli agent model list
```



|Flag |Type |Description |
|---|---|---|
|`--usage` |string |Model usage: agent (primary agent model, default) or tool (tool model) |
|`--query` |string |User intent or keyword for enriching/ranking the ArkModels managed\-agent whitelist |
|`--name` |string |Filter by model name substring |
|`--refresh-cache` |bool |Refresh the full\-version ArkModels cache synchronously; respect failure cooldown; query mode also refreshes search caches |
|`--include-all` |bool |Skip the selected usage's support filter; include all models with capability flags |
|`--primary-only` |bool |Keep only primary versions from the complete model catalog |


<span id="agent-session-compact-command"></span>
### agent session compact

Compact the active context of an idle managed\-agent session

```Bash
arkcli agent session compact <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--instructions` |string |Optional compaction instructions, or @file |
|`--session-thread-id` |string |Target session thread id |
|`--timeout` |int |Completion timeout in seconds; default 120 |
|`--poll` |bool |Poll the event list instead of opening the SSE stream |
|`--raw` |bool |Print raw NDJSON events |


<span id="agent-session-create-command"></span>
### agent session create

Create a managed agent session

```Bash
arkcli agent session create
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--agent-id` |string |Agent id |
|`--agent-version` |int |Agent version |
|`--agent` |string |Resolved agent object; overrides \-\-agent\-id/\-\-agent\-version when set |
|`--env-id` |string |Environment id (alias of \-\-environment\-id) |
|`--environment-id` |string |Environment id |
|`--title` |string |Session title |
|`--resource` |string |Resources array |
|`--tos-path` |string |TOS directory to mount, e.g. tos://bucket/prefix/; appends a type=tos resource |
|`--vault-id` |string_slice |Vault id; comma\-separated or repeated |
|`--vault-ids` |string_slice |Vault IDs, comma\-separated or repeated |
|`--tags` |string |Tags map or tag array |
|`--agent-overrides` |string |AgentWithOverrides object; inline JSON/YAML or @file |
|`--environment-overrides` |string |EnvironmentWithOverrides object; inline JSON/YAML or @file |


<span id="agent-session-delete-command"></span>
### agent session delete

Delete a managed agent session

```Bash
arkcli agent session delete <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--yes` |bool |Skip interactive deletion confirmation |


<span id="agent-session-events-list-command"></span>
### agent session events list

List managed agent session events

```Bash
arkcli agent session events list <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML request body. Use \- or omit \-\-file to read stdin |
|`--limit` |int |Page size |
|`--page` |string |Page cursor/page number |
|`--after` |string |Cursor/event id after |
|`--before` |string |Cursor/event id before |
|`--since` |string |Cursor/time lower bound |
|`--created-after` |string |Event created_at lower bound (RFC3339) |
|`--created-before` |string |Event created_at upper bound (RFC3339) |
|`--type` |string_slice |Event type filter; repeatable or comma\-separated |
|`--session-thread-id` |string |Filter by session thread id |
|`--order` |string |Sort order: asc \| desc |


<span id="agent-session-events-send-command"></span>
### agent session events send

Send managed agent session events

```Bash
arkcli agent session events send <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML request body. Use \- or omit \-\-file to read stdin |
|`--events` |string |Events array; more than one event is sent serially in order (non\-atomic) |
|`--event` |string |Single event object |
|`--type` |string |Single event type, e.g. user.message |
|`--text` |string |Text for user.message |
|`--content` |string |Content string, or @file to read from disk |
|`--image` |string_slice |Image file id or @local path; repeatable or comma\-separated |
|`--document` |string_slice |Document/PDF file id or @local path; repeatable or comma\-separated |
|`--purpose` |string |File purpose for local \-\-image/\-\-document uploads (default: user_data) |
|`--wait-timeout` |int |Seconds for local uploads, initial \-\-stream/\-\-wait stage, or \-\-poll; default 120 |
|`--wait-interval` |int |Polling interval in seconds for local multimodal uploads; default 2 |
|`--session-thread-id` |string |Target session thread id |
|`--no-event-deltas` |bool |Do not request PRD event_start/event_delta frames; use complete events only |
|`--max-event-chars` |int |Maximum characters shown for pretty event text; default 2000, 0 means unlimited |
|`--stream` |bool |After sending, open the SSE event stream and wait until terminal; falls back to event\-list polling on timeout |
|`--wait` |bool |Compatibility alias for \-\-stream; large payloads may return immediately unless \-\-stream is explicit |
|`--wait-fallback-timeout` |int |Seconds to keep polling after the stream timeout; default 120 |
|`--poll` |bool |After sending, poll events list until terminal instead of opening a stream; useful for large or long\-running requests |
|`--poll-interval` |int |Polling interval in seconds with \-\-poll; default 2 |
|`--raw` |bool |With \-\-stream, \-\-wait, or \-\-poll, print raw NDJSON events instead of human\-readable lines |
|`--tool-use-id` |string |Tool use id for user.tool_confirmation |
|`--custom-tool-use-id` |string |Custom tool use id for user.custom_tool_result |
|`--result` |string |Result value, e.g. allow \| deny |


<span id="agent-session-events-stream-command"></span>
### agent session events stream

Stream managed agent session events

```Bash
arkcli agent session events stream <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--after` |string |Cursor/event id after |
|`--before` |string |Cursor/event id before |
|`--since` |string |Cursor/time lower bound |
|`--created-after` |string |Event created_at lower bound (RFC3339) |
|`--created-before` |string |Event created_at upper bound (RFC3339) |
|`--type` |string_slice |Event type filter; repeatable or comma\-separated |
|`--session-thread-id` |string |Filter by session thread id |
|`--no-event-deltas` |bool |Do not request PRD event_start/event_delta frames; use complete events only |
|`--max-event-chars` |int |Maximum characters shown for pretty event text; default 2000, 0 means unlimited |
|`--order` |string |Sort order: asc \| desc |


<span id="agent-session-get-command"></span>
### agent session get

Get a managed agent session

```Bash
arkcli agent session get <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |


<span id="agent-session-list-command"></span>
### agent session list

List managed agent sessions

```Bash
arkcli agent session list
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--limit` |int |Page size |
|`--page` |int |Page number |
|`--agent-id` |string |Filter by agent id |
|`--agent-version` |int |Filter by agent version |
|`--environment-id` |string |Filter by environment id |
|`--memory-store-id` |string |Filter by memory store id |
|`--start` |string |Filter by session start time lower bound (RFC3339) |
|`--end` |string |Filter by session start/update time upper bound (RFC3339) |
|`--order` |string |Sort order: asc \| desc |
|`--status` |string_slice |Filter by session status; repeatable or comma\-separated |
|`--tags` |string |Tags map or tag array filter |


<span id="agent-session-resources-add-command"></span>
### agent session resources add

Add a resource to a managed agent session

```Bash
arkcli agent session resources add <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML request body. Use \- or omit \-\-file to read stdin |
|`--path` |string |Local file path to upload, wait until active, then mount as a file resource |
|`--file-id` |string |Files API file id. Use `arkcli agent file upload` or `arkcli agent file list` to obtain one. |
|`--type` |string |Resource type: file |
|`--mount-path` |string |Path appended to the backend\-managed /mnt/session/uploads/ prefix |
|`--purpose` |string |File upload purpose when \-\-path is used (default: user_data) |
|`--tos` |string |TOS storage object for file upload when \-\-path is used |
|`--preprocess-configs` |string |Preprocess configs object for file upload when \-\-path is used |
|`--expire-at` |int |UTC Unix timestamp in seconds for file upload when \-\-path is used |
|`--wait-timeout` |int |Maximum seconds for local file activation; with \-\-wait, also bounds Agent response wait (default 120) |
|`--wait-interval` |int |Polling interval in seconds; default 2 |


<span id="agent-session-resources-delete-command"></span>
### agent session resources delete

Delete a managed agent session resource

```Bash
arkcli agent session resources delete <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--yes` |bool |Skip interactive deletion confirmation |
|`--resource-id` |string |Session resource id |


<span id="agent-session-resources-get-command"></span>
### agent session resources get

Show one resource attached to a managed agent session

```Bash
arkcli agent session resources get <session-id> [resource-id]
```



|Flag |Type |Description |
|---|---|---|
|`--resource-id` |string |Session resource id |
|`--file-id` |string |Match a file resource by file_id |
|`--mount-path` |string |Match a file resource by mount_path |


<span id="agent-session-resources-list-command"></span>
### agent session resources list

List resources mounted to a managed agent session

```Bash
arkcli agent session resources list <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML request body. Use \- or omit \-\-file to read stdin |


<span id="agent-session-resources-update-command"></span>
### agent session resources update

Update a managed agent session resource

```Bash
arkcli agent session resources update <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--resource-id` |string |Session resource id |
|`--authorization-token` |string |New authorization token for github_repository resources |


<span id="agent-session-threads-get-command"></span>
### agent session threads get

Get a managed agent session thread

```Bash
arkcli agent session threads get <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML request body. Use \- or omit \-\-file to read stdin |
|`--session-thread-id` |string |Session thread id |


<span id="agent-session-threads-list-command"></span>
### agent session threads list

List managed agent session threads

```Bash
arkcli agent session threads list <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML request body. Use \- or omit \-\-file to read stdin |
|`--limit` |int |Page size |
|`--page` |string |Page cursor/page number |


<span id="agent-session-update-command"></span>
### agent session update

Update a managed agent session

```Bash
arkcli agent session update <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--title` |string |Session title |
|`--tag` |string |Tags patch map object (merge semantics server\-side) |
|`--tags` |string |Tags map or tag array |
|`--agent` |string |Agent patch object; intended for tools/mcp_servers only |


<span id="agent-session-upgrade-command"></span>
### agent session upgrade

Upgrade an existing session's runtime configuration (returns acceptance, not completion)

```Bash
arkcli agent session upgrade <session-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Upgrade request JSON/YAML file (snake_case); explicit flags override its fields |
|`--agent` |string |Agent upgrade object in snake_case; inline JSON/YAML or @file |
|`--environment` |string |Environment upgrade object in snake_case; inline JSON/YAML or @file |
|`--agent-version` |int |Agent version to load; omitted or 0 preserves the current snapshot |
|`--model` |string |Model ID or model upgrade object (snake_case JSON/YAML or @file) |
|`--speed` |string |Legacy\-compatible model speed |
|`--thinking` |string |Model thinking mode |
|`--reasoning-effort` |string |Model reasoning effort |
|`--service-tier` |string |Model service tier |
|`--vault-ids` |string |Bound vault IDs as a JSON/YAML array or @file; refresh only, cannot change the set |
|`--initial-events` |string |Initial events array (JSON/YAML or @file); optional, [] means no event |
|`--compact` |bool |Include the upgrade /compact event; false explicitly sets initial_events to [] |


<span id="agent-skill-create-command"></span>
### agent skill create

Upload a local skill zip package and create a custom managed\-agent skill

```Bash
arkcli agent skill create
```



|Flag |Type |Description |
|---|---|---|
|`--zip` |string (required) |Path to a local skill zip package |
|`--display-title` |string |Display title for the uploaded skill |
|`--protection-enabled` |bool |Enable content protection for the created skill |


<span id="agent-skill-delete-command"></span>
### agent skill delete

Delete a custom managed\-agent skill (all skill versions must be deleted first)

```Bash
arkcli agent skill delete <skill-id>
```



|Flag |Type |Description |
|---|---|---|
|`--yes` |bool |Confirm permanent deletion |
|`--version` |string |Delete only this skill version; omit to delete the skill (requires no remaining versions) |


<span id="agent-skill-download-command"></span>
### agent skill download

Download a custom managed\-agent skill version archive

```Bash
arkcli agent skill download <skill-id>
```



|Flag |Type |Description |
|---|---|---|
|`--version` |string |Skill semantic version; defaults to the latest version |
|`--output` |string |Local output zip path; defaults to\-v.zip |


<span id="agent-skill-get-command"></span>
### agent skill get

Get a custom or Ark managed\-agent skill through TOP GetSkill

```Bash
arkcli agent skill get <skill-id>
```


<span id="agent-skill-github-import-command"></span>
### agent skill github import

Scan and import public skills from a GitHub repository

```Bash
arkcli agent skill github import <repository-input>
```



|Flag |Type |Description |
|---|---|---|
|`--path` |string_slice |Repository\-relative skill path; repeatable |
|`--all` |bool |Import every valid candidate |
|`--selected` |string |Selection array from @file with Path and optional ProtectionEnabled |
|`--protection-enabled` |bool |Enable content protection for the created skill |


<span id="agent-skill-github-scan-command"></span>
### agent skill github scan

Scan public skills from a GitHub repository

```Bash
arkcli agent skill github scan <repository-input>
```


<span id="agent-skill-list-command"></span>
### agent skill list

List TOP custom/ark managed agent skills

```Bash
arkcli agent skill list
```



|Flag |Type |Description |
|---|---|---|
|`--source` |string |Skill source: custom \| ark \| all |
|`--limit` |int |Page size (alias of \-\-page\-size) |
|`--page` |string |Page number (alias of \-\-page\-number) |
|`--page-number` |int |Page number |
|`--page-size` |int |Page size |
|`--query` |string |Search query |
|`--id` |string |Filter by skill id |
|`--ids` |string_slice |Filter custom skills by IDs; repeatable or comma\-separated |
|`--name` |string |Filter by skill name |
|`--display-title` |string |Filter by skill display title |
|`--sort-by` |string |Custom skill sort field for TOP ListSkills |
|`--sort-order` |string |Custom skill sort order for TOP ListSkills |
|`--create-time-gte` |string |Custom skill create time lower bound for TOP ListSkills |
|`--create-time-lte` |string |Custom skill create time upper bound for TOP ListSkills |


<span id="agent-skill-search-command"></span>
### agent skill search

Search custom/Ark skills by user query and emit agent skill refs

```Bash
arkcli agent skill search <query>
```



|Flag |Type |Description |
|---|---|---|
|`--limit` |int |Page size |
|`--page` |string |Page cursor/page number |
|`--source` |string |Skill source: custom \| ark \| all |


<span id="agent-skill-set-protection-command"></span>
### agent skill set\-protection

Enable or disable content protection for a custom managed\-agent skill

```Bash
arkcli agent skill set-protection <skill-id>
```



|Flag |Type |Description |
|---|---|---|
|`--enabled` |bool (required) |Content protection state |


<span id="agent-skill-update-command"></span>
### agent skill update

Upload a new version for an existing custom managed\-agent skill

```Bash
arkcli agent skill update <skill-id>
```



|Flag |Type |Description |
|---|---|---|
|`--zip` |string (required) |Path to the new local skill zip package |
|`--display-title` |string |Display title for the new skill version |


<span id="agent-skill-versions-command"></span>
### agent skill versions

List versions of a custom or Ark managed\-agent skill

```Bash
arkcli agent skill versions <skill-id>
```



|Flag |Type |Description |
|---|---|---|
|`--limit` |int |Page size |
|`--page` |string |Page cursor |


<span id="agent-vault-create-command"></span>
### agent vault create

Create a credential vault

```Bash
arkcli agent vault create
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--display-name` |string |Vault display name |
|`--metadata` |string |Metadata map object |


<span id="agent-vault-credentials-create-command"></span>
### agent vault credentials create

Create a credential in a vault

```Bash
arkcli agent vault credentials create <vault-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--display-name` |string |Credential display name |
|`--auth` |string |Credential auth object |
|`--auth-type` |string |Auth type: environment_variable \| static_bearer \| mcp_oauth |
|`--mcp-server-url` |string |MCP server URL for bearer/OAuth credentials |
|`--token` |string |Bearer token or environment value; use @file for a local file |
|`--access-token` |string |Current OAuth access token; use @file for a local file |
|`--secret-name` |string |Environment variable name for environment_variable auth |
|`--secret-value` |string |Environment variable value; use @file for a local file |
|`--networking-type` |string |ENV networking: limited \| unrestricted |
|`--allowed-host` |string_slice |Allowed host for limited ENV networking; repeatable or comma\-separated |
|`--client-id` |string |OAuth refresh client id |
|`--client-secret` |string |OAuth refresh client secret; use @file |
|`--refresh-token` |string |OAuth refresh token; use @file |
|`--token-endpoint` |string |OAuth token endpoint |
|`--resource` |string |OAuth resource |
|`--scope` |string |OAuth scope |
|`--metadata` |string |Metadata map object |


<span id="agent-vault-credentials-delete-command"></span>
### agent vault credentials delete

Delete a credential

```Bash
arkcli agent vault credentials delete <vault-id> <credential-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--yes` |bool |Skip interactive deletion confirmation |


<span id="agent-vault-credentials-get-command"></span>
### agent vault credentials get

Get a credential

```Bash
arkcli agent vault credentials get <vault-id> <credential-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |


<span id="agent-vault-credentials-list-command"></span>
### agent vault credentials list

List credentials in a vault

```Bash
arkcli agent vault credentials list <vault-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--limit` |int |Page size |
|`--page` |string |Page cursor/page number |
|`--id` |string |Filter by credential id |
|`--name` |string |Filter by credential name |


<span id="agent-vault-credentials-update-command"></span>
### agent vault credentials update

Update a credential

```Bash
arkcli agent vault credentials update <vault-id> <credential-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--display-name` |string |Credential display name |
|`--auth` |string |Credential auth object |
|`--auth-type` |string |Auth type: environment_variable \| static_bearer \| mcp_oauth |
|`--mcp-server-url` |string |MCP server URL for bearer/OAuth credentials |
|`--token` |string |Bearer token or environment value; use @file for a local file |
|`--access-token` |string |Current OAuth access token; use @file for a local file |
|`--secret-name` |string |Environment variable name for environment_variable auth |
|`--secret-value` |string |Environment variable value; use @file for a local file |
|`--networking-type` |string |ENV networking: limited \| unrestricted |
|`--allowed-host` |string_slice |Allowed host for limited ENV networking; repeatable or comma\-separated |
|`--client-id` |string |OAuth refresh client id |
|`--client-secret` |string |OAuth refresh client secret; use @file |
|`--refresh-token` |string |OAuth refresh token; use @file |
|`--token-endpoint` |string |OAuth token endpoint |
|`--resource` |string |OAuth resource |
|`--scope` |string |OAuth scope |
|`--metadata` |string |Metadata map object |


<span id="agent-vault-delete-command"></span>
### agent vault delete

Delete a credential vault

```Bash
arkcli agent vault delete <vault-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--yes` |bool |Skip interactive deletion confirmation |


<span id="agent-vault-get-command"></span>
### agent vault get

Get a credential vault

```Bash
arkcli agent vault get <vault-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |


<span id="agent-vault-list-command"></span>
### agent vault list

List credential vaults

```Bash
arkcli agent vault list
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--limit` |int |Page size |
|`--page` |string |Page cursor/page number |
|`--id` |string |Filter by vault id |
|`--name` |string |Filter by vault name |


<span id="agent-vault-oauth-flow-create-command"></span>
### agent vault oauth\-flow create

Create a vault MCP OAuth flow and return the authorization URL

```Bash
arkcli agent vault oauth-flow create
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--vault-id` |string |Vault id |
|`--display-name` |string |Credential display name |
|`--mcp-server-url` |string |MCP server URL |
|`--redirect-url` |string |Business success redirect URL for the OAuth callback |
|`--source` |string |Flow source marker, e.g. arkcli\-mcp\-login |
|`--client-id` |string |OAuth client id for custom MCP/provider override mode |
|`--client-secret` |string |OAuth client secret, or @file to read from disk |


<span id="agent-vault-oauth-provider-list-command"></span>
### agent vault oauth\-provider list

List registered MCP providers usable for agent MCP servers and vault credentials

```Bash
arkcli agent vault oauth-provider list
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--limit` |int |Page size |
|`--page-token` |string |Next page token |


<span id="agent-vault-update-command"></span>
### agent vault update

Update a credential vault

```Bash
arkcli agent vault update <vault-id>
```



|Flag |Type |Description |
|---|---|---|
|`--file` |string |Path to a JSON/YAML payload. Use \- or omit \-\-file to read stdin |
|`--display-name` |string |Vault display name |
|`--metadata` |string |Metadata map object |
