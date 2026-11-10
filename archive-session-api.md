# Archive session

`POST https://ark.ap-southeast.bytepluses.com/api/v3/sessions/{session_id}/archive`

After a task ends, call this API to archive the session if you need to release runtime resources while retaining the session configuration and run records for querying. The `status` of an active session must be `idle`. Other active states do not meet the archive conditions, and the API returns 400 `InvalidAction`. If the server detects tool tasks whose status is `queued` or `running`, it also returns `InvalidAction`. Wait for the tasks to finish or cancel them, and then try again.

After a session with `status` set to `idle` is successfully archived, the server sets it to `terminated`, records the time when it first entered that state in `archived_at`, and starts reclaiming runtime resources.

You can also call this API for sessions whose `status` is already `terminated`. For repeated calls, the existing `archived_at` remains unchanged. For historical sessions that were already terminated before the archive feature was launched and do not have `archived_at`, the server does not backfill it with the time of this call.

Archived sessions can no longer be updated, upgraded, or have events written to them, but their configuration and run records can still be queried. Repeated archiving does not extend the retention period of events, trace records, or media data such as images and videos.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**session_id** `string` `Required`  |  Session ID

The ID of the session to archive, starting with `sesn-`. You can get it from the create session response or the session list.

## Response parameters

**agent** `object`  |  Agent information

A snapshot of the agent configuration saved when the session was created. It includes the model, system prompt, tools, MCP services, skills, and other configurations actually used by the session. Subsequent updates to the agent do not affect the snapshot in this session.

**created_at** `string`  |  Creation time

`agent.created_at`

The time when the agent creation was completed, in RFC 3339 format.

**id** `string`  |  Agent ID

`agent.id`

Agent ID currently used by the session.

**name** `string`  |  Agent name

`agent.name`

Agent name currently used by the session.

**type** `string`  |  Object type

`agent.type`

Object type. Fixed to `agent`.

**updated_at** `string`  |  Update time

`agent.updated_at`

The time when the agent configuration was last updated, in RFC 3339 format.

**version** `integer`  |  Version number

`agent.version`

Agent version number currently used by the session.

**base_agent** `string`  |  Base agent type

`agent.base_agent`

Base agent type currently used by the session.

* `ark_agent_preview`: a general agent.

**description** `string`  |  Agent description

`agent.description`

Agent description currently used by the session.

**display_name** `string`  |  Display name

`agent.display_name`

Agent display name currently used by the session.

**mcp_servers** `object[]`  |  MCP service list

`agent.mcp_servers`

List of MCP service configurations currently used by the session.

**name** `string`  |  MCP service name.

`agent.mcp_servers.name`

MCP service name referenced by `tools[].mcp_server_name`.

**type** `string`  |  MCP access method

`agent.mcp_servers.type`

MCP service access method. Fixed to `url`.

**url** `string`  |  MCP service URL

`agent.mcp_servers.url`

MCP service access URL snapshot created when the session is created.

**metadata** `object`  |  Custom metadata

`agent.metadata`

Custom key\-value pairs set when the agent was created. The server does not define fixed subparameters.

**model** `object`  |  Model configuration

`agent.model`

Model configuration currently used by the session.

**id** `string`  |  Model or endpoint ID

`agent.model.id`

Model ID or inference endpoint ID currently used by the session.

**protocol** `string`  |  Invocation protocol

`agent.model.protocol`

Protocol used for session model calls:

* `ark_responses`: ModelArk Responses protocol.

* `openai_chat_completions`: OpenAI Chat Completions compatible protocol.

* `openai_responses`: OpenAI Responses compatible protocol.

* `anthropic_messages`: Anthropic Messages compatible protocol.

**reasoning_effort** `string`  |  Reasoning effort level

`agent.model.reasoning_effort`

The reasoning effort level currently used by the session.

**service_tier** `string`  |  Inference service tier

`agent.model.service_tier`

The reasoning service tier currently used by the session.

**speed** `string`  |  Inference speed

`agent.model.speed`

Model inference speed tier.

**thinking** `string`  |  Thinking mode

`agent.model.thinking`

Thinking mode currently used by the session.

**multiagent** `object`  |  Multi Agent configuration

`agent.multiagent`

Multi\-agent configuration currently used by the session.

**agents** `object[]`  |  Member agents

`agent.multiagent.agents`

The member agent list snapshot created when the session was created.

**type** `string`  |  Reference type

`agent.multiagent.agents.type`

Member reference type:

* `agent`: References another agent.

* `self`: References the current agent.

**description** `string`  |  Agent description

`agent.multiagent.agents.description`

The member agent description snapshot created when the session was created.

**display_name** `string`  |  Display name

`agent.multiagent.agents.display_name`

The member agent display name snapshot created when the session was created.

**id** `string`  |  Agent ID

`agent.multiagent.agents.id`

The ID of the member agent; when `type` is `self`, the ID of the current agent is returned.

**mcp_servers** `object[]`  |  MCP service list

`agent.multiagent.agents.mcp_servers`

The member agent MCP service configuration list snapshot created when the session was created.

**name** `string`  |  MCP service name.

`agent.multiagent.agents.mcp_servers.name`

MCP service name referenced by `tools[].mcp_server_name`.

**type** `string`  |  MCP access method

`agent.multiagent.agents.mcp_servers.type`

MCP service access method. Fixed to `url`.

**url** `string`  |  MCP service URL

`agent.multiagent.agents.mcp_servers.url`

MCP service access URL snapshot created when the session is created.

**model** `object`  |  Model configuration

`agent.multiagent.agents.model`

The member agent model configuration snapshot created when the session was created.

**id** `string`  |  Model or endpoint ID

`agent.multiagent.agents.model.id`

The model ID or inference endpoint ID used by the member agent.

**speed** `string`  |  Inference speed

`agent.multiagent.agents.model.speed`

The model inference speed tier used by the member agent.

**name** `string`  |  Agent name

`agent.multiagent.agents.name`

The member agent name snapshot created when the session was created.

**skills** `object[]`  |  Skill list

`agent.multiagent.agents.skills`

The member agent skill configuration list snapshot created when the session was created.

**type** `string`  |  Skill type

`agent.multiagent.agents.skills.type`

Skill source type:

* `ark`: a preset skill from the ModelArk platform.

* `custom`: a custom skill created by the current account.

**display_name** `string`  |  Skill name

`agent.multiagent.agents.skills.display_name`

The skill display name recorded when the session was created.

**skill_id** `string`  |  Skill ID

`agent.multiagent.agents.skills.skill_id`

The skill ID used by the session.

**use_latest** `boolean`  |  Use the latest version

`agent.multiagent.agents.skills.use_latest`

Whether to select the latest version of this skill when creating the session.`true` indicates that the latest version at that time is selected, and `false` indicates that the version specified by `version` is used.

**version** `string`  |  Skill version

`agent.multiagent.agents.skills.version`

The skill version number snapshot created when the session was created.

**system** `string`  |  System prompt

`agent.multiagent.agents.system`

The member agent system prompt snapshot created when the session was created.

**tools** `object[]`  |  Tool list

`agent.multiagent.agents.tools`

The member agent tool configuration list snapshot created when the session was created.

**type** `string`  |  Tool type

`agent.multiagent.agents.tools.type`

Tool type:

* `agent_toolset_20260916`: the current version of the ModelArk built\-in toolset.

* `mcp_toolset`: A tool set provided by the specified MCP service.

* `custom`: A custom tool executed by the client.

   Options: `agent_toolset_20260916`, `agent_toolset_20260701`, `mcp_toolset`, `custom`.

**configs** `object[]`  |  Tool configuration

`agent.multiagent.agents.tools.configs`

Per\-tool configuration snapshot created when the session is created.

**name** `string`  |  Tool name

`agent.multiagent.agents.tools.configs.name`

The tool name recorded in the session snapshot.

**enabled** `boolean`  |  Whether enabled

`agent.multiagent.agents.tools.configs.enabled`

Whether the tool is enabled.

**permission_policy** `object`  |  Permission policy

`agent.multiagent.agents.tools.configs.permission_policy`

The invocation permission policy used by the tool.

**type** `string`  |  Policy type

`agent.multiagent.agents.tools.configs.permission_policy.type`

Policy type. `always_allow` means direct calls are allowed, and `always_ask` means user confirmation is required before each call.

**default_config** `object`  |  Default tool configuration

`agent.multiagent.agents.tools.default_config`

The default configuration used by tools that are not separately listed in `configs`.

**enabled** `boolean`  |  Whether enabled

`agent.multiagent.agents.tools.default_config.enabled`

Whether tools that are not configured separately are enabled by default.

**permission_policy** `object`  |  Permission policy

`agent.multiagent.agents.tools.default_config.permission_policy`

The invocation permission policy used by tools that are not configured separately.

**type** `string`  |  Policy type

`agent.multiagent.agents.tools.default_config.permission_policy.type`

Policy type. `always_allow` means direct calls are allowed, and `always_ask` means user confirmation is required before each call.

**description** `string`  |  Custom tool description

`agent.multiagent.agents.tools.description`

Custom tool description snapshot created when the session is created, used to record the tool's capabilities and applicable scenarios.

**input_schema** `object`  |  Input parameter definition

`agent.multiagent.agents.tools.input_schema`

Returns the custom tool input parameter JSON Schema recorded in the agent snapshot of the returned session.

**properties** `object`  |  Parameter property definition

`agent.multiagent.agents.tools.input_schema.properties`

Returns the JSON Schema property mapping recorded in the session's agent snapshot. Each key is a parameter name, and each value is the JSON Schema child object for the corresponding parameter.

**required** `string[]`  |  List of required parameter names

`agent.multiagent.agents.tools.input_schema.required`

List of parameter names that must be provided when returning the called custom tool.

**type** `string`  |  Schema type

`agent.multiagent.agents.tools.input_schema.type`

The returned top\-level type of the JSON Schema. It is fixed as `object`.

**mcp_server_name** `string`  |  MCP service name.

`agent.multiagent.agents.tools.mcp_server_name`

The MCP service name referenced by `mcp_toolset` in the session.

**name** `string`  |  Custom tool name

`agent.multiagent.agents.tools.name`

Custom tool name snapshot created when the session is created.

**version** `integer`  |  Version number

`agent.multiagent.agents.version`

The member agent version number snapshot created when the session was created.

**type** `string`  |  Collaboration mode

`agent.multiagent.type`

Multi\-agent collaboration mode, fixed as `coordinator`.

**skills** `object[]`  |  Skill list

`agent.skills`

List of skill configurations currently used by the session.

**type** `string`  |  Skill type

`agent.skills.type`

Skill source type:

* `ark`: a preset skill from the ModelArk platform.

* `custom`: a custom skill created by the current account.

**display_name** `string`  |  Skill name

`agent.skills.display_name`

The skill display name recorded when the session was created.

**skill_id** `string`  |  Skill ID

`agent.skills.skill_id`

The skill ID used by the session.

**use_latest** `boolean`  |  Use the latest version

`agent.skills.use_latest`

Whether to select the latest version of this skill when creating the session.`true` indicates that the latest version at that time is selected, and `false` indicates that the version specified by `version` is used.

**version** `string`  |  Skill version

`agent.skills.version`

The skill version number snapshot created when the session was created.

**system** `string`  |  System prompt

`agent.system`

System prompt currently used by the session.

**tools** `object[]`  |  Tool list

`agent.tools`

List of tool configurations currently used by the session.

**type** `string`  |  Tool type

`agent.tools.type`

Tool type:

* `agent_toolset_20260916`: the current version of the ModelArk built\-in toolset.

* `mcp_toolset`: A tool set provided by the specified MCP service.

* `custom`: A custom tool executed by the client.

   Options: `agent_toolset_20260916`, `agent_toolset_20260701`, `mcp_toolset`, `custom`.

**configs** `object[]`  |  Tool configuration

`agent.tools.configs`

Per\-tool configuration snapshot created when the session is created.

**name** `string`  |  Tool name

`agent.tools.configs.name`

The tool name recorded in the session snapshot.

**enabled** `boolean`  |  Whether enabled

`agent.tools.configs.enabled`

Whether the tool is enabled.

**permission_policy** `object`  |  Permission policy

`agent.tools.configs.permission_policy`

The invocation permission policy used by the tool.

**type** `string`  |  Policy type

`agent.tools.configs.permission_policy.type`

Policy type. `always_allow` means direct calls are allowed, and `always_ask` means user confirmation is required before each call.

**default_config** `object`  |  Default tool configuration

`agent.tools.default_config`

The default configuration used by tools that are not separately listed in `configs`.

**enabled** `boolean`  |  Whether enabled

`agent.tools.default_config.enabled`

Whether tools that are not configured separately are enabled by default.

**permission_policy** `object`  |  Permission policy

`agent.tools.default_config.permission_policy`

The invocation permission policy used by tools that are not configured separately.

**type** `string`  |  Policy type

`agent.tools.default_config.permission_policy.type`

Policy type. `always_allow` means direct calls are allowed, and `always_ask` means user confirmation is required before each call.

**description** `string`  |  Custom tool description

`agent.tools.description`

Custom tool description snapshot created when the session is created, used to record the tool's capabilities and applicable scenarios.

**input_schema** `object`  |  Input parameter definition

`agent.tools.input_schema`

Returns the custom tool input parameter JSON Schema recorded in the agent snapshot of the returned session.

**properties** `object`  |  Parameter property definition

`agent.tools.input_schema.properties`

Returns the JSON Schema property mapping recorded in the session's agent snapshot. Each key is a parameter name, and each value is the JSON Schema child object for the corresponding parameter.

**required** `string[]`  |  List of required parameter names

`agent.tools.input_schema.required`

List of parameter names that must be provided when returning the called custom tool.

**type** `string`  |  Schema type

`agent.tools.input_schema.type`

The returned top\-level type of the JSON Schema. It is fixed as `object`.

**mcp_server_name** `string`  |  MCP service name.

`agent.tools.mcp_server_name`

The MCP service name referenced by `mcp_toolset` in the session.

**name** `string`  |  Custom tool name

`agent.tools.name`

Custom tool name snapshot created when the session is created.

**created_at** `string`  |  Creation time

The session creation time in RFC 3339 format.

**environment_id** `string`  |  Environment ID

The environment ID bound when the session was created.

**id** `string`  |  Session ID

The unique identifier of the session, in the format `sesn-xxxxx`.

**stats** `object`  |  Duration statistics

Duration statistics for the session, including the cumulative runtime duration and the total duration from creation to the current time or termination.

**active_seconds** `number`  |  Cumulative running time

`stats.active_seconds`

The cumulative duration for which at least one thread in the session is in the `running` state, in seconds. This value is the same as `usage.active_seconds`.

**duration_seconds** `number`  |  Total session duration

`stats.duration_seconds`

The total duration from session creation to the current query time, in seconds. After the session is terminated, this value is fixed as the duration from creation to termination.

**status** `string`  |  Session status

The running status of the session. Options: `idle`, `running`, `terminated`, `rescheduling`, `initializing`, `failed`, `upgrading`.

**type** `string`  |  Object type

Object type. Fixed to `session`.

**updated_at** `string`  |  Update time

The time when the session information was last updated, in RFC 3339 format.

**archived_at** `string`  |  Archive time

The time when the session was first archived, as a UTC timestamp in RFC 3339 format. When the archive API is called, the server changes `idle` sessions to `terminated`.

> **Tip**

> For historical sessions that were terminated before the archive feature was launched, the server cannot restore the original termination time, so it may not return this parameter.

**environment** `object`  |  Environment information

The environment configuration actually used by the session. Subsequent changes to the original environment do not automatically change this session.

**created_at** `string`  |  Creation time

`environment.created_at`

The time when the environment creation was completed, in RFC 3339 format.

**id** `string`  |  Environment ID

`environment.id`

Unique identifier of the environment.

**name** `string`  |  Environment name

`environment.name`

The environment name, used to distinguish this environment in the environment list and session configuration.

**type** `string`  |  Object type

`environment.type`

Session environment snapshot type.`environment` indicates direct use of the base environment; `environment_with_overrides` indicates that the current session uses environment configuration overrides.

**updated_at** `string`  |  Update time

`environment.updated_at`

The time when the environment configuration was last updated, in RFC 3339 format.

**config** `object`  |  Environment configuration

`environment.config`

Runtime configuration for the environment, including the runtime type, network policy, dependency packages, and environment variables.

**type** `string`  |  Environment type

`environment.config.type`

Session runtime environment type: `cloud` indicates a ModelArk\-managed environment, and `self_hosted` indicates a user self\-hosted environment.

**env** `object`  |  Environment variable

`environment.config.env`

Environment variable key\-value pairs in the session runtime environment

**networking** `object`  |  Outbound network policy

`environment.config.networking`

Container outbound network policy configuration.

**type** `string`  |  Outbound network policy type

`environment.config.networking.type`

The outbound network policy currently used by the session.`unrestricted` means outbound targets are not restricted.

**packages** `object`  |  Dependency package configuration

`environment.config.packages`

Configuration for dependency packages preinstalled at startup.

**apt** `string[]`  |  apt package

`environment.config.packages.apt`

List of system packages preinstalled through apt when the session environment starts.

**cargo** `string[]`  |  Cargo packages

`environment.config.packages.cargo`

List of Rust crates preinstalled through Cargo when the session environment starts.

**gem** `string[]`  |  gem packages

`environment.config.packages.gem`

List of Ruby packages preinstalled through RubyGems when the session environment starts.

**go** `string[]`  |  Go modules

`environment.config.packages.go`

List of module dependencies preinstalled through the Go toolchain when the session environment starts.

**npm** `string[]`  |  npm packages

`environment.config.packages.npm`

List of Node.js packages preinstalled through npm when the session environment starts.

**pip** `string[]`  |  pip package

`environment.config.packages.pip`

List of Python packages preinstalled through pip when the session environment starts.

**type** `string`  |  Configuration type

`environment.config.packages.type`

Dependency package configuration type, fixed as `packages`.

**setup_script** `string`  |  Startup script

`environment.config.setup_script`

Initialization script executed when the session runtime environment starts; leave it empty or unset to execute no script.

**tos** `object`  |  Artifact storage configuration

`environment.config.tos`

Persistent storage configuration for agent artifacts. Empty means no user bucket is configured, and artifacts are stored in ModelArk managed TOS.

**bucket** `string`  |  TOS bucket

`environment.config.tos.bucket`

User TOS bucket name saved in the session environment snapshot.

**prefix** `string`  |  Object prefix

`environment.config.tos.prefix`

Object storage directory prefix saved in the session environment snapshot.

**description** `string`  |  Environment description

`environment.description`

Environment description, used to describe the environment's purpose, network policy, dependency packages, and other configurations.

**metadata** `object`  |  Custom metadata

`environment.metadata`

User\-defined metadata.

**overridden_fields** `string[]`  |  Overridden parameters

`environment.overridden_fields`

The list of `config` subparameter names actually overridden by the current session relative to the base environment. Returned only when *type* is `environment_with_overrides` and overridden parameters exist.

**scope** `string`  |  Scope

`environment.scope`

Environment scope: `organization` indicates that it is shared within the organization, and `account` indicates that it is visible within the account.

**resources** `object[]`  |  Resource list

The array of currently mounted resources. The resource `type` can be `file`, `memory_store`, or `tos`.

File resource `object`

A file uploaded through the Files API and mounted to the session.

**created_at** `string`  |  Mount time

`resources.created_at`

The time when the resource was mounted to the session, in RFC 3339 format.

**id** `string`  |  Resource ID

`resources.id`

Session resource ID.

**type** `string`  |  Resource type.

`resources.type`

Resource type, fixed as `file`.

**updated_at** `string`  |  Update time

`resources.updated_at`

The time when the session resource information was last updated, in RFC 3339 format.

**access** `string`  |  Access permissions

`resources.access`

File access permissions, fixed as `read_only`.

**file_id** `string`  |  File ID

`resources.file_id`

The currently available file ID.

**mount_path** `string`  |  Mount path

`resources.mount_path`

The full mount path of the file in the session runtime environment.

Memory store resource `object`

A memory store mounted to the session.

**created_at** `string`  |  Mount time

`resources.created_at`

The time when the resource was mounted to the session, in RFC 3339 format.

**id** `string`  |  Resource ID

`resources.id`

Session resource ID.

**type** `string`  |  Resource type.

`resources.type`

Resource type, fixed as `memory_store`.

**updated_at** `string`  |  Update time

`resources.updated_at`

The time when the session resource information was last updated, in RFC 3339 format.

**access** `string`  |  Access permissions

`resources.access`

The session's access permissions for the memory store.

* `read_only`: The agent can only read the memory store.

**memory_store_id** `string`  |  Memory store ID

`resources.memory_store_id`

The ID of the memory store mounted to the session.

TOS resource `object`

A TOS object directory mounted to the session.

**created_at** `string`  |  Mount time

`resources.created_at`

The time when the resource was mounted to the session, in RFC 3339 format.

**id** `string`  |  Resource ID

`resources.id`

Session resource ID.

**type** `string`  |  Resource type.

`resources.type`

Resource type, fixed as `tos`.

**updated_at** `string`  |  Update time

`resources.updated_at`

The time when the session resource information was last updated, in RFC 3339 format.

**access** `string`  |  Access permissions

`resources.access`

The session's access permissions for the TOS directory. Options: `read_only`, `read_write`.

**mount_path** `string`  |  Mount path

`resources.mount_path`

The mount path of the TOS directory in the session runtime environment. If no mount path was specified when the session was created, this parameter is not returned.

**tos_bucket** `string`  |  TOS bucket

`resources.tos_bucket`

The bucket name where the TOS mount source is located.

**tos_key** `string`  |  TOS directory prefix

`resources.tos_key`

The object prefix of the mounted directory in TOS.

**tos_region** `string`  |  TOS region

`resources.tos_region`

The ID of the region where the TOS mount source is located.

**scene** `string`  |  Session scene

Session scene marker.

* `default`: regular session.

* `dreams`: a session used for Dream memory organization tasks.

**tags** `object[]`  |  Tag list

An array of session tags. Each item contains `key` and an optional `value`.

**key** `string`  |  Tag key

`tags.key`

The tag key, used to identify the classification dimension.

**value** `string`  |  Tag value

`tags.value`

Used to record the specific content for this tag category.

**title** `string`  |  Session title

Session title, used for quick identification and retrieval.

**usage** `object`  |  Usage statistics

Cumulative runtime duration, model tokens, and server\-side search request statistics during session execution. If usage statistics are temporarily unavailable, the server does not return this parameter.

**cache_read_input_tokens** `integer`  |  Cache read tokens

`usage.cache_read_input_tokens`

The cumulative number of input tokens read from the cache by the session.

**input_tokens** `integer`  |  Total input tokens

`usage.input_tokens`

The cumulative number of input tokens consumed by the session.

**output_tokens** `integer`  |  Total output tokens

`usage.output_tokens`

The cumulative number of output tokens generated by the session.

**vault_ids** `string[] / null`  |  Vault ID list

The list of vault IDs accessible to the session. The agent can use the credentials in them during runtime.
