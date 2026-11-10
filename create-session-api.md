# Create session

`POST https://ark.ap-southeast.bytepluses.com/api/v3/sessions`

Create a session and bind it to the specified agent and environment. When creating a session, you can override some agent or environment configurations for this session. After the session is created, you can use the returned session ID to send events, query the status, upgrade the configuration, archive the session, or delete the session. Archived agents and environments cannot be used to create new sessions.

Prepare the agent and environment before calling this API. To mount files, memory stores, or credentials, you must first create the resources through the corresponding APIs and obtain their IDs.

Choose an API based on your goal:

* Use this API when you need a new session or need to change the bound agent or environment.

* **To modify only the title or tags**, use [Update session title and tags](update-session-api.md).

* **To keep the same session and refresh the configuration**, use [Upgrade session](upgrade-session-api.md).

By default, a single agent can have up to 1000 active sessions at the same time. When the quota is reached, the create session API returns HTTP 429 `SessionQuotaExceeded`. The actual quota may vary depending on the account configuration.

> **Tip**

* > In `resources`, you can mount files uploaded through the Files API by using `file_id`; files whose `purpose` is `agent` or `user_data` can be used. Whether a file can be directly included in the model context depends on subsequent `user.message` content blocks and model capabilities.

* > Passing an `agent_with_overrides` object lets you temporarily adjust the referenced agent's model, system prompt, tools, MCP services, skills, or multi\-agent collaboration configuration for this session. The override does not create a new agent version.

* > Passing an `environment` override object lets you temporarily adjust the referenced environment's certain runtime configuration for this session. The override does not modify the original environment and does not affect other sessions.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Body parameters

**agent** `string / object` `Required`  |  Agent identifier

Specify the agent used by the session. You can directly pass in the agent ID. To specify an agent version, pass in an agent reference object and set `id` and `version`.

To temporarily adjust the agent configuration for this session, pass an `agent_with_overrides` object. The override is written only to the session's agent snapshot and does not modify the base agent.

Agent ID `string`

Directly pass in an agent ID. The latest version of the agent is used when the session starts.

Agent reference `object`

Agent reference object. Specify the agent through `id` and the agent version used by the session through `version`; when `version` is omitted, the latest version is used.

**id** `string` `Required`  |  Agent ID

`agent.id`

Agent ID to use for creating the session.

**type** `string` `Required`  |  Reference type

`agent.type`

Reference type. Fixed to `agent`.

**version** `integer`  |  Version number

`agent.version`

Agent version number. If omitted, the latest version is used.

Agent configuration override `object`

Agent override objectBased on the agent version specified by `id`, the overrides affect only this session and do not create a new version or modify the base agent.

* If a parameter is omitted, the corresponding configuration of the base agent is used.

* Passing `system` replaces the system prompt in this session.

* Passing `model` overrides only the provided subparameters; omitted model subparameters use the configuration of the base agent.

* Passing `[]` for `tools`, `mcp_servers`, or `skills` clears the corresponding list; passing a non\-empty array replaces the entire list and does not merge it with the base configuration.

* Passing `multiagent` replaces the entire multi\-agent collaboration configuration; passing `[]` for `agents` clears the sub\-agent list.

**id** `string` `Required`  |  Agent ID

`agent.id`

Base agent ID. If missing or empty, HTTP 400 `MissingParameter` is returned.

**type** `string` `Required`  |  Override type

`agent.type`

Override type. Fixed to `agent_with_overrides`.

**context_management** `object[]`  |  Context management override

`agent.context_management`

Custom context management policy saved only for this session. Pass at most one item.

* **If omitted or ** **`null`** ** is passed**, the configuration of the base agent version is used.

* **Passing **  **`[]`** : clears the custom policy and uses the default runtime behavior instead. This does not mean automatic compression is disabled.

* **Passing one item** replaces the configuration of the base agent version as a whole.

**type** `string` `Required`  |  Policy type

`agent.context_management.type`

Policy type, fixed as `compaction`.

**compact_threshold** `integer`  |  Compaction threshold

`agent.context_management.compact_threshold`

The user\-configured trigger threshold for automatic compaction, measured in tokens. If omitted or set to `null`, no custom threshold is saved and the runtime uses the default behavior.

**Min**: `50000`

**instructions** `string`  |  Summary instructions

`agent.context_management.instructions`

Custom instructions used when generating compaction summaries. If omitted, set to `null`, or set to an empty string, no custom instructions are saved; a non\-empty value cannot contain only whitespace characters.

**mcp_servers** `object[]`  |  MCP service list

`agent.mcp_servers`

List of MCP services used for this session. If omitted, the original configuration is retained; passing `[]` clears it; passing a non\-empty array replaces it as a whole.

**type** `string` `Required`  |  Access method

`agent.mcp_servers.type`

The access method for the MCP service.

Value is fixed to `url`.

**url** `string` `Required`  |  Service address

`agent.mcp_servers.url`

The access address of the MCP service. Duplicate MCP service addresses cannot be configured within the same agent.

**name** `string`  |  MCP service name.

`agent.mcp_servers.name`

MCP service name, up to 255 characters long. It is used to identify the service in the current agent. When configuring an MCP toolset, `tools[].mcp_server_name` must be set to this name.

* If the name is omitted or does not comply with the naming rules, the server generates a name based on `url`.

* The server converts names that comply with the naming rules to lowercase.

* If multiple names conflict, the server adds numeric suffixes to subsequent names.

**model** `object`  |  Model runtime parameter overrides

`agent.model`

Model configuration that takes effect only for this session. You can set `id`, `speed`, `service_tier`, `thinking`, and `reasoning_effort`; omitted subparameters inherit the corresponding configuration from the base agent.

**id** `string`  |  Model ID override

`agent.model.id`

Model ID used for this session. If omitted, the base agent's model is used; if specified, it affects only this session and does not modify the base agent.

The model must be activated in the current project and support agentic use. If the model ID is invalid or does not support agentic use, HTTP 400 is returned; if the model is not activated, HTTP 403 `ModelNotOpen` is returned.

**reasoning_effort** `string`  |  Reasoning effort level

`agent.model.reasoning_effort`

Reasoning effort level. If omitted, the base agent's configuration is used; if an empty string is passed, the current model's default configuration is used.

If an unsupported value is passed, HTTP 400 `InvalidParameter` is returned. For the supported values and defaults of each model, see [the list of parameters supported by models](model-parameter-support.md). Options: `none` / `minimal` / `low` / `medium` / `high` / `xhigh` / `max`.

**service_tier** `string`  |  Inference service tier

`agent.model.service_tier`

Inference service tier. If omitted, the base agent's configuration is used; if an empty string is passed, the current model's default configuration is used.

If an unsupported value is passed, HTTP 400 `InvalidParameter` is returned. For the supported values and defaults of each model, see [the list of parameters supported by models](model-parameter-support.md). Options: `auto` / `default` / `fast`.

**speed** `string`  |  Inference speed

`agent.model.speed`

Model inference speed tier. If omitted, the configuration of the base agent is used; if an empty string is passed, the default configuration of the model service is used.

Not all models support `fast`; if an unsupported value is passed, HTTP 400 `InvalidParameter` is returned. Options: `standard` / `fast`.

**thinking** `string`  |  Thinking mode

`agent.model.thinking`

Thinking mode.`auto` indicates that the model determines whether to enable thinking; if omitted, the configuration of the base agent is used; if an empty string is passed, the default configuration of the current model is used.

If an unsupported value is passed, HTTP 400 `InvalidParameter` is returned. For the supported values and defaults of each model, see [the list of parameters supported by models](model-parameter-support.md). Options: `enabled` / `disabled` / `auto`.

**multiagent** `object`  |  Multi Agent configuration

`agent.multiagent`

Multi\-agent collaboration configuration used for this session. If omitted, the base agent configuration is retained; if provided, it replaces the entire configuration, and passing `[]` for `agents` clears the sub\-agent list.

**type** `string` `Required`  |  Collaboration mode

`agent.multiagent.type`

Multi\-agent collaboration mode.`multiagent_20261001` uses `subagents`; `coordinator` uses `agents`.

**agents** `object[]`  |  Sub\-agent list

`agent.multiagent.agents`

When `type` is `coordinator`, use this sub\-agent list to configure predefined agents and self. You can specify up to 20 items. Passing `[]` clears the `agents` member list of the base agent.

**type** `string` `Required`  |  Sub\-agent source

`agent.multiagent.agents.type`

The source of the sub\-agent.`agent` indicates an existing agent in the current account and project; `self` indicates reusing the configuration of the current primary agent. Both sources can be used for `multiagent_20261001.predefined_agents` and `coordinator.agents`. When using self, do not provide `id` or `version`.

**id** `string`  |  Sub\-agent ID

`agent.multiagent.agents.id`

Sub\-agent ID. This parameter is required when `type` is `agent`; omit it when `type` is `self`.

**version** `integer`  |  Sub\-agent version

`agent.multiagent.agents.version`

The sub\-agent version number. It takes effect only when `type` is `agent`. If omitted, the latest version of the sub\-agent is used.

**subagents** `object`  |  Subagent configuration

`agent.multiagent.subagents`

When `type` is `multiagent_20261001`, use this parameter to configure subagents.

**type** `string` `Required`  |  Subagent toggle

`agent.multiagent.subagents.type`

Whether subagents can be created. Options: `enabled`, `disabled`.

**inline_agents** `object`  |  Inline agent configuration

`agent.multiagent.subagents.inline_agents`

Runtime inline agent configuration.

**type** `string` `Required`  |  Inline agent toggle

`agent.multiagent.subagents.inline_agents.type`

Whether inline agents can be created at runtime with a name and system instructions. Options: `enabled`, `disabled`.

**predefined_agents** `object[]`  |  Predefined members

`agent.multiagent.subagents.predefined_agents`

List of predefined agent and self members, up to 20 items.

* When a referenced agent uses `multiagent_20261001`, its child members can continue to be recorded within the session depth limit.

* When a referenced agent uses `coordinator`, it is treated as a leaf member.

**type** `string` `Required`  |  Sub\-agent source

`agent.multiagent.subagents.predefined_agents.type`

The source of the sub\-agent.`agent` indicates an existing agent in the current account and project; `self` indicates reusing the configuration of the current primary agent. Both sources can be used for `multiagent_20261001.predefined_agents` and `coordinator.agents`. When using self, do not provide `id` or `version`.

**id** `string`  |  Sub\-agent ID

`agent.multiagent.subagents.predefined_agents.id`

Sub\-agent ID. This parameter is required when `type` is `agent`; omit it when `type` is `self`.

**version** `integer`  |  Sub\-agent version

`agent.multiagent.subagents.predefined_agents.version`

The sub\-agent version number. It takes effect only when `type` is `agent`. If omitted, the latest version of the sub\-agent is used.

**skills** `object[]`  |  Skill list

`agent.skills`

List of skills used for this session. If omitted, the original configuration is retained; passing `[]` clears it; passing a non\-empty array replaces it as a whole.

**skill_id** `string` `Required`  |  Skill ID

`agent.skills.skill_id`

The ID of the skill to attach to the agent.

**type** `string` `Required`  |  Source of the skill

`agent.skills.type`

Source of the skill.

* `ark`: ModelArk preset skill.

* `custom`: a custom skill created by the current account through the Skills API.

**version** `string`  |  Skill version

`agent.skills.version`

The version number of the skill to be attached. If omitted, the latest version of the skill is used.

**system** `string`  |  System prompt override

`agent.system`

System prompt used for this session. If omitted, the configuration of the base agent is used.

**tools** `object[]`  |  Tool list

`agent.tools`

List of tools used for this session. If omitted, the original configuration is retained; passing `[]` clears it; passing a non\-empty array replaces it as a whole.

**type** `string` `Required`  |  Tool type

`agent.tools.type`

Tool type.

* `agent_toolset_20260916`: Use ModelArk's built\-in tools, including `bash`, `edit`, `read`, `write`, `glob`, `grep`, `web_fetch`, and `web_search`.

* `mcp_toolset`: Use tools provided by the specified MCP service.

* `custom`: Use a custom tool executed by the client.

   Options: `agent_toolset_20260401`, `agent_toolset_20260701`, `agent_toolset_20260916`, `mcp_toolset`, `custom`.

**configs** `object[]`  |  Tool configuration list

`agent.tools.configs`

Set the enabled status and permission policy for tools by tool name.

**name** `string` `Required`  |  Tool name

`agent.tools.configs.name`

The tool name corresponding to the tool configuration.

**enabled** `boolean`  |  Whether enabled

`agent.tools.configs.enabled`

Whether to enable this tool.

**permission_policy** `object`  |  Permission policy

`agent.tools.configs.permission_policy`

The permission policy used by this tool.

**type** `string` `Required`  |  Policy type

`agent.tools.configs.permission_policy.type`

Tool call permission policy.

* `always_allow`: always allows calls.

* `always_ask`: Confirmation is required before each use.

**default_config** `object`  |  Default tool configuration

`agent.tools.default_config`

The default enabled status and permission policy used for tools that are not individually configured in `configs`.

**enabled** `boolean`  |  Whether enabled

`agent.tools.default_config.enabled`

Whether to enable by default tools that are not separately configured in `configs`.

* `true`: enabled by default.

* `false`: disabled by default.

**permission_policy** `object`  |  Permission policy

`agent.tools.default_config.permission_policy`

The default permission policy used by tools that are not separately configured in `configs`.

**type** `string` `Required`  |  Policy type

`agent.tools.default_config.permission_policy.type`

Tool call permission policy.

* `always_allow`: always allows calls.

* `always_ask`: Confirmation is required before each use.

**description** `string`  |  Custom tool description

`agent.tools.description`

Describes the function and use cases of the custom tool, so the model can determine when to call the tool.

> **Tip**

> This parameter is required when `type` is `custom` and must be 1–10000 characters in length.

**input_schema** `object`  |  Input parameter definition

`agent.tools.input_schema`

Defines the input parameters and constraints for the custom tool.

> **Note**

* > When creating an agent, the server verifies that `input_schema.type` is `object` and that `input_schema.properties` is a JSON object, but does not check each property definition.

* > When running a session, the server checks whether the complete `input_schema` is a valid JSON Schema. Successfully creating an agent does not necessarily mean that the schema can run normally.

* > The tool call parameters generated by the model are only validated to check whether they are valid JSON. Before running a custom tool, your application should validate the parameters again according to `input_schema`.

**properties** `object`  |  Parameter property definition

`agent.tools.input_schema.properties`

The JSON Schema `properties` object for custom tool input parameters. The key is the parameter name, and the value is the JSON Schema subobject for the corresponding parameter.

**required** `string[]`  |  List of required parameter names

`agent.tools.input_schema.required`

List of required parameter names in custom tool input. Each name in the list must match a parameter name defined in `properties`.

**type** `string` `Default object`  |  Schema type

`agent.tools.input_schema.type`

Specifies the top\-level type of the JSON Schema. It is fixed as `object`.

**mcp_server_name** `string`  |  MCP service name.

`agent.tools.mcp_server_name`

The MCP service name to which the MCP tool belongs.

**name** `string`  |  Custom tool name

`agent.tools.name`

The name of the custom tool.

> **Tip**

> This parameter is required when `type` is `custom`. It must be 1–128 characters in length and can contain only letters, digits, underscores, and hyphens, and custom tool names within the same agent must be unique.

**version** `integer`  |  Version number

`agent.version`

Base agent version number. If omitted, the latest version is used; if a version is specified, the overrides for this session are applied based on the configuration saved in that version.

**environment_id** `string`  |  Environment ID

When running a session directly with an existing environment, enter the ID of that environment. To temporarily adjust the available runtime configuration for this session, use `environment` instead.

**environment** `object`  |  Environment overrides

Use this object to temporarily adjust the runtime configuration of an existing environment for this session.

> **Tip**

> Specify the base environment with `id`. The configuration takes effect only for this session and does not modify the original environment. If `environment_id` is also provided, the environment IDs must be the same.

**id** `string` `Required`  |  Environment ID

`environment.id`

The environment ID to use as the basis for this configuration override.

**type** `string` `Required`  |  Object type

`environment.type`

Object type, fixed as `environment_with_overrides`.

**config** `object`  |  Overridden runtime configuration

`environment.config`

Runtime configuration overrides for the environment used in this session; if omitted, the original environment configuration is used.

**type** `string` `Required`  |  Environment type

`environment.config.type`

Runtime environment type: `cloud` indicates a ModelArk\-managed cloud runtime environment, and `self_hosted` indicates a user self\-hosted environment. When overridden, it must remain consistent with the base environment.

**env** `object`  |  Environment variable

`environment.config.env`

Environment variables used by this session, passed as string key\-value pairs consisting of variable names and variable values.

> **Tip**

* > If *env* is omitted, the environment variables of the base environment are used; if an object is passed, it replaces them as a whole; if an empty object `{}` is passed, all environment variables are cleared.

* > It can contain up to 64 key\-value pairs. All keys and values combined cannot exceed 32 KiB. Keys cannot be empty and cannot contain `=` or NUL characters. Values cannot contain NUL characters.

**packages** `object`  |  Dependency package configuration

`environment.config.packages`

Dependency packages preinstalled when this session starts.

> **Tip**

* > If *packages* is omitted, the dependency package configuration of the base environment is used; if an object is passed, it replaces the configuration as a whole; if an empty object `{}` is passed, all dependency package configurations are cleared.

* > After leading and trailing whitespace is removed, each dependency package name cannot be empty, cannot contain NUL characters, and cannot start with a hyphen.

**apt** `string[]`  |  APT packages

`environment.config.packages.apt`

apt dependencies.

**cargo** `string[]`  |  Cargo packages

`environment.config.packages.cargo`

cargo dependencies.

**gem** `string[]`  |  Ruby Gem packages

`environment.config.packages.gem`

gem dependencies.

**go** `string[]`  |  Go modules

`environment.config.packages.go`

Go module dependencies.

**npm** `string[]`  |  npm packages

`environment.config.packages.npm`

npm dependencies.

**pip** `string[]`  |  Python packages

`environment.config.packages.pip`

pip dependencies.

**type** `string`  |  Configuration type

`environment.config.packages.type`

Fixed as `packages`.

**setup_script** `string`  |  Initialization script

`environment.config.setup_script`

Initialization script executed when this session starts.

> **Tip**

> If *setup_script* is omitted, the initialization script of the base environment is used; if a non\-empty string is passed, it replaces the original value; if an empty string `""` is passed, it is cleared.

> **Note**

* > The script runs as Bash in the `/workspace` directory, and its content cannot exceed 256 KiB (262144 bytes). If the limit is exceeded, the script is not executed, but the session may still continue running.

* > If the script exits with a non\-zero status code, operations completed before exit, such as file writes, are not automatically rolled back, and the session may still continue running. After creation, check the dependencies, directories, or marker files prepared by the script. Do not determine whether initialization succeeded based only on the session status.

**tos** `object`  |  Artifact storage overrides

`environment.config.tos`

Session\-level artifact storage overrides. The `tos` object takes effect as a whole.

If `tos` is omitted, the configuration of the base environment is retained; if `{}` is passed, the configuration is cleared and default storage is used; if a non\-empty object is passed, both `bucket` and `prefix` must be provided.

**bucket** `string`  |  TOS bucket

`environment.config.tos.bucket`

User TOS bucket name. The bucket must be in the same region as the current ModelArk API access region; otherwise, the request will be rejected.

**prefix** `string`  |  Object prefix

`environment.config.tos.prefix`

The storage directory prefix for agent artifacts in the user bucket.

**resources** `object[]`  |  Initial resources

Files, memory stores, or TOS directories mounted to the runtime environment when the session starts.

> **Tip**

> When `config.type` of the environment used by the session is `self_hosted`, `resources` cannot be passed.

File resource `object`

A file uploaded through the Files API and mounted to the session.

**type** `string` `Required`  |  Resource type.

`resources.type`

Resource type, fixed as `file`.

**file_id** `string` `Required`  |  File ID

`resources.file_id`

The ID of the file to mount to the session, obtained after uploading the file through the Files API.

> **Tip**

> In the same request, file resources with the same `file_id` and the same final effective `mount_path` are considered duplicates and cannot be mounted at the same time.

**mount_path** `string`  |  Mount path

`resources.mount_path`

The mount path of the file in the session runtime environment. If omitted or an empty value is passed, the original file name used during file upload is used, and the final path is `/mnt/session/uploads/<filename>`.

> **Tip**

> A leading `/` in the path is automatically removed. The path must not contain `..` or `.` path segments, backslashes, double slashes `//`, or control characters, and must not end with `/`.

Memory store resource `object`

A memory store mounted to the session.

**type** `string` `Required`  |  Resource type.

`resources.type`

Resource type, fixed as `memory_store`.

**memory_store_id** `string` `Required`  |  Memory store ID

`resources.memory_store_id`

The ID of the memory store to mount to the session, returned by the create memory store API, in the form `memstore-...`.

> **Tip**

* > In the same request, the same `memory_store_id` can be mounted only once.

* > You can add up to 10 resources of type `memory_store`.

TOS resource `object`

A TOS object directory mounted to the session.

**type** `string` `Required`  |  Resource type.

`resources.type`

Resource type, fixed as `tos`.

**tos_bucket** `string` `Required`  |  TOS bucket

`resources.tos_bucket`

The name of the TOS bucket that contains the directory to mount.

**tos_region** `string`  |  TOS region

`resources.tos_region`

The ID of the region where the source bucket is located. If omitted, the current service region is used; if explicitly provided, it must be the same as the current service region.

**tos_key** `string` `Required`  |  TOS directory prefix

`resources.tos_key`

The object prefix of the TOS directory to be mounted.

> **Tip**

* > The prefix must end with `/`; it cannot be `/` or start with `/`, and cannot contain backslashes, double slashes `//`, `.` or `..` path segments, or control characters.

* > In the same request, TOS resources with the same combination of `tos_bucket`, `tos_key`, and `tos_region` are considered duplicates and cannot be mounted at the same time.

**access** `string` `Default read_only`  |  Access permissions

`resources.access`

The session's access permissions for the TOS directory:

* `read_only`: The agent can read directory contents, but cannot modify them.

* `read_write`: The agent can read and modify directory contents.

**mount_path** `string`  |  Mount path

`resources.mount_path`

The mount path of the TOS directory in the session runtime environment. Default: `/mnt/session/storage`

**tags** `object[]`  |  Tag list

A list of tags associated with the session, used for classification and retrieval. Up to 20 items. Tag keys in the same request cannot be duplicated.

**key** `string` `Required`  |  Tag key

`tags.key`

Tag key, used for classification and retrieval. Required, up to 128 characters. It supports letters, digits, spaces, and `_.: / = + - @`.

**value** `string`  |  Tag value

`tags.value`

Used to record the specific content for this tag category.

**title** `string`  |  Session title

Session title, used to help users identify and manage the session. Up to 255 characters.

**vault_ids** `string[]`  |  Vault ID list

List of vault IDs bound to the session.

**scene** `string` `Default default`  |  Session scene

Session scene marker.

* `default`: regular session.

* `dreams`: a session used for Dream memory organization tasks.

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

**context_management** `object[]`  |  Context management

`agent.context_management`

Context management policy frozen when the session is created. If no custom policy has been saved in the snapshot or the saved policy does not include `compact_threshold`, the response shows the platform's current default compression policy or default threshold. The current default threshold is `192000` tokens.

Default display values in the response are not written back to the session snapshot. An explicitly saved `compact_threshold` takes precedence in the response.

**type** `string`  |  Policy type

`agent.context_management.type`

Policy type, fixed as `compaction`.

**compact_threshold** `integer`  |  Compaction threshold

`agent.context_management.compact_threshold`

The automatic compaction trigger threshold displayed in the query response, measured in tokens. If no value is explicitly saved, the public API response fills in the platform's current default value, which is `192000`; this displayed value is not written back to the agent version or session snapshot.

**instructions** `string`  |  Summary instructions

`agent.context_management.instructions`

User\-configured compaction summary instructions.

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

**type** `string`  |  Collaboration mode

`agent.multiagent.type`

Multi\-agent collaboration mode.`multiagent_20261001` uses `subagents`; `coordinator` uses `agents`.

**agents** `object[]`  |  Member agents

`agent.multiagent.agents`

When `type` is `coordinator`, the list of member agents recorded when the session was created is returned.

**type** `string`  |  Reference type

`agent.multiagent.agents.type`

Member reference type:

* `agent`: References another agent configuration that was recorded when the session was created.

* `self`: Uses the effective configuration of the current agent when the session was created.

**context_management** `object[]`  |  Context management

`agent.multiagent.agents.context_management`

The member agent context management policy that is recorded when the session is created. If no custom policy has been saved in the member snapshot or the saved policy does not include `compact_threshold`, the response shows the platform's current default compression policy or default threshold. The current default threshold is `192000` tokens.

Default display values in the response are not written back to the session snapshot. An explicitly saved `compact_threshold` takes precedence in the response.

**type** `string`  |  Policy type

`agent.multiagent.agents.context_management.type`

Policy type, fixed as `compaction`.

**compact_threshold** `integer`  |  Compaction threshold

`agent.multiagent.agents.context_management.compact_threshold`

The automatic compaction trigger threshold displayed in the query response, measured in tokens. If no value is explicitly saved, the public API response fills in the platform's current default value, which is `192000`; this displayed value is not written back to the agent version or session snapshot.

**instructions** `string`  |  Summary instructions

`agent.multiagent.agents.context_management.instructions`

User\-configured compaction summary instructions.

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

* `mcp_toolset`: A toolset provided by the specified MCP service.

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

**models** `object[]`  |  Tool model list

`agent.multiagent.agents.tools.configs.models`

The list of model IDs or inference endpoint IDs for image generation or video generation tools recorded in the session snapshot.

**id** `string`  |  Model or endpoint ID

`agent.multiagent.agents.tools.configs.models.id`

The model ID or endpoint ID bound to this tool.

**default** `boolean`  |  Whether this is the default model

`agent.multiagent.agents.tools.configs.models.default`

Whether to use it as the default model for this tool.

**name** `string`  |  Model name

`agent.multiagent.agents.tools.configs.models.name`

The model name returned by the server based on the model ID or inference endpoint ID.

**version** `string`  |  Version

`agent.multiagent.agents.tools.configs.models.version`

The model version returned by the server based on the model ID or endpoint ID.

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

**subagents** `object`  |  Subagent configuration

`agent.multiagent.subagents`

When `type` is `multiagent_20261001`, the subagent configuration recorded when the session was created is returned.

**type** `string`  |  Subagent toggle

`agent.multiagent.subagents.type`

Whether the current agent is allowed to create subagents. Options: `enabled`, `disabled`.

**inline_agents** `object`  |  Inline agent configuration

`agent.multiagent.subagents.inline_agents`

Runtime inline agent configuration.

**type** `string`  |  Inline agent toggle

`agent.multiagent.subagents.inline_agents.type`

Whether inline agents can be created at runtime with a name and system instructions. Options: `enabled`, `disabled`.

**predefined_agents** `object[]`  |  Predefined members

`agent.multiagent.subagents.predefined_agents`

The list of predefined agents and self references recorded for `multiagent_20261001` when the session was created.

* When a referenced agent uses `multiagent_20261001`, its child members can continue to be recorded within the session depth limit.

* When a referenced agent uses `coordinator`, it is treated as a leaf member.

**type** `string`  |  Reference type

`agent.multiagent.subagents.predefined_agents.type`

Member reference type:

* `agent`: References another agent configuration that was recorded when the session was created.

* `self`: Uses the effective configuration of the current agent when the session was created.

**context_management** `object[]`  |  Context management

`agent.multiagent.subagents.predefined_agents.context_management`

The member agent context management policy that is recorded when the session is created. If no custom policy has been saved in the member snapshot or the saved policy does not include `compact_threshold`, the response shows the platform's current default compression policy or default threshold. The current default threshold is `192000` tokens.

Default display values in the response are not written back to the session snapshot. An explicitly saved `compact_threshold` takes precedence in the response.

**type** `string`  |  Policy type

`agent.multiagent.subagents.predefined_agents.context_management.type`

Policy type, fixed as `compaction`.

**compact_threshold** `integer`  |  Compaction threshold

`agent.multiagent.subagents.predefined_agents.context_management.compact_threshold`

The automatic compaction trigger threshold displayed in the query response, measured in tokens. If no value is explicitly saved, the public API response fills in the platform's current default value, which is `192000`; this displayed value is not written back to the agent version or session snapshot.

**instructions** `string`  |  Summary instructions

`agent.multiagent.subagents.predefined_agents.context_management.instructions`

User\-configured compaction summary instructions.

**description** `string`  |  Agent description

`agent.multiagent.subagents.predefined_agents.description`

The member agent description snapshot created when the session was created.

**display_name** `string`  |  Display name

`agent.multiagent.subagents.predefined_agents.display_name`

The member agent display name snapshot created when the session was created.

**id** `string`  |  Agent ID

`agent.multiagent.subagents.predefined_agents.id`

The ID of the member agent; when `type` is `self`, the ID of the current agent is returned.

**mcp_servers** `object[]`  |  MCP service list

`agent.multiagent.subagents.predefined_agents.mcp_servers`

The member agent MCP service configuration list snapshot created when the session was created.

**name** `string`  |  MCP service name.

`agent.multiagent.subagents.predefined_agents.mcp_servers.name`

MCP service name referenced by `tools[].mcp_server_name`.

**type** `string`  |  MCP access method

`agent.multiagent.subagents.predefined_agents.mcp_servers.type`

MCP service access method. Fixed to `url`.

**url** `string`  |  MCP service URL

`agent.multiagent.subagents.predefined_agents.mcp_servers.url`

MCP service access URL snapshot created when the session is created.

**model** `object`  |  Model configuration

`agent.multiagent.subagents.predefined_agents.model`

The member agent model configuration snapshot created when the session was created.

**id** `string`  |  Model or endpoint ID

`agent.multiagent.subagents.predefined_agents.model.id`

The model ID or inference endpoint ID used by the member agent.

**speed** `string`  |  Inference speed

`agent.multiagent.subagents.predefined_agents.model.speed`

The model inference speed tier used by the member agent.

**name** `string`  |  Agent name

`agent.multiagent.subagents.predefined_agents.name`

The member agent name snapshot created when the session was created.

**skills** `object[]`  |  Skill list

`agent.multiagent.subagents.predefined_agents.skills`

The member agent skill configuration list snapshot created when the session was created.

**type** `string`  |  Skill type

`agent.multiagent.subagents.predefined_agents.skills.type`

Skill source type:

* `ark`: a preset skill from the ModelArk platform.

* `custom`: a custom skill created by the current account.

**display_name** `string`  |  Skill name

`agent.multiagent.subagents.predefined_agents.skills.display_name`

The skill display name recorded when the session was created.

**skill_id** `string`  |  Skill ID

`agent.multiagent.subagents.predefined_agents.skills.skill_id`

The skill ID used by the session.

**use_latest** `boolean`  |  Use the latest version

`agent.multiagent.subagents.predefined_agents.skills.use_latest`

Whether to select the latest version of this skill when creating the session.`true` indicates that the latest version at that time is selected, and `false` indicates that the version specified by `version` is used.

**version** `string`  |  Skill version

`agent.multiagent.subagents.predefined_agents.skills.version`

The skill version number snapshot created when the session was created.

**system** `string`  |  System prompt

`agent.multiagent.subagents.predefined_agents.system`

The member agent system prompt snapshot created when the session was created.

**tools** `object[]`  |  Tool list

`agent.multiagent.subagents.predefined_agents.tools`

The member agent tool configuration list snapshot created when the session was created.

**type** `string`  |  Tool type

`agent.multiagent.subagents.predefined_agents.tools.type`

Tool type:

* `agent_toolset_20260916`: the current version of the ModelArk built\-in toolset.

* `mcp_toolset`: A toolset provided by the specified MCP service.

* `custom`: A custom tool executed by the client.

   Options: `agent_toolset_20260916`, `agent_toolset_20260701`, `mcp_toolset`, `custom`.

**configs** `object[]`  |  Tool configuration

`agent.multiagent.subagents.predefined_agents.tools.configs`

Per\-tool configuration snapshot created when the session is created.

**name** `string`  |  Tool name

`agent.multiagent.subagents.predefined_agents.tools.configs.name`

The tool name recorded in the session snapshot.

**enabled** `boolean`  |  Whether enabled

`agent.multiagent.subagents.predefined_agents.tools.configs.enabled`

Whether the tool is enabled.

**models** `object[]`  |  Tool model list

`agent.multiagent.subagents.predefined_agents.tools.configs.models`

The list of model IDs or inference endpoint IDs for image generation or video generation tools recorded in the session snapshot.

**id** `string`  |  Model or endpoint ID

`agent.multiagent.subagents.predefined_agents.tools.configs.models.id`

The model ID or endpoint ID bound to this tool.

**default** `boolean`  |  Whether this is the default model

`agent.multiagent.subagents.predefined_agents.tools.configs.models.default`

Whether to use it as the default model for this tool.

**name** `string`  |  Model name

`agent.multiagent.subagents.predefined_agents.tools.configs.models.name`

The model name returned by the server based on the model ID or inference endpoint ID.

**version** `string`  |  Version

`agent.multiagent.subagents.predefined_agents.tools.configs.models.version`

The model version returned by the server based on the model ID or endpoint ID.

**permission_policy** `object`  |  Permission policy

`agent.multiagent.subagents.predefined_agents.tools.configs.permission_policy`

The invocation permission policy used by the tool.

**type** `string`  |  Policy type

`agent.multiagent.subagents.predefined_agents.tools.configs.permission_policy.type`

Policy type. `always_allow` means direct calls are allowed, and `always_ask` means user confirmation is required before each call.

**default_config** `object`  |  Default tool configuration

`agent.multiagent.subagents.predefined_agents.tools.default_config`

The default configuration used by tools that are not separately listed in `configs`.

**enabled** `boolean`  |  Whether enabled

`agent.multiagent.subagents.predefined_agents.tools.default_config.enabled`

Whether tools that are not configured separately are enabled by default.

**permission_policy** `object`  |  Permission policy

`agent.multiagent.subagents.predefined_agents.tools.default_config.permission_policy`

The invocation permission policy used by tools that are not configured separately.

**type** `string`  |  Policy type

`agent.multiagent.subagents.predefined_agents.tools.default_config.permission_policy.type`

Policy type. `always_allow` means direct calls are allowed, and `always_ask` means user confirmation is required before each call.

**description** `string`  |  Custom tool description

`agent.multiagent.subagents.predefined_agents.tools.description`

Custom tool description snapshot created when the session is created, used to record the tool's capabilities and applicable scenarios.

**input_schema** `object`  |  Input parameter definition

`agent.multiagent.subagents.predefined_agents.tools.input_schema`

Returns the custom tool input parameter JSON Schema recorded in the agent snapshot of the returned session.

**properties** `object`  |  Parameter property definition

`agent.multiagent.subagents.predefined_agents.tools.input_schema.properties`

Returns the JSON Schema property mapping recorded in the session's agent snapshot. Each key is a parameter name, and each value is the JSON Schema child object for the corresponding parameter.

**required** `string[]`  |  List of required parameter names

`agent.multiagent.subagents.predefined_agents.tools.input_schema.required`

List of parameter names that must be provided when returning the called custom tool.

**type** `string`  |  Schema type

`agent.multiagent.subagents.predefined_agents.tools.input_schema.type`

The returned top\-level type of the JSON Schema. It is fixed as `object`.

**mcp_server_name** `string`  |  MCP service name.

`agent.multiagent.subagents.predefined_agents.tools.mcp_server_name`

The MCP service name referenced by `mcp_toolset` in the session.

**name** `string`  |  Custom tool name

`agent.multiagent.subagents.predefined_agents.tools.name`

Custom tool name snapshot created when the session is created.

**version** `integer`  |  Version number

`agent.multiagent.subagents.predefined_agents.version`

The member agent version number snapshot created when the session was created.

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

* `mcp_toolset`: A toolset provided by the specified MCP service.

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

**models** `object[]`  |  Tool model list

`agent.tools.configs.models`

The list of model IDs or inference endpoint IDs for image generation or video generation tools recorded in the session snapshot.

**id** `string`  |  Model or endpoint ID

`agent.tools.configs.models.id`

The model ID or endpoint ID bound to this tool.

**default** `boolean`  |  Whether this is the default model

`agent.tools.configs.models.default`

Whether to use it as the default model for this tool.

**name** `string`  |  Model name

`agent.tools.configs.models.name`

The model name returned by the server based on the model ID or inference endpoint ID.

**version** `string`  |  Version

`agent.tools.configs.models.version`

The model version returned by the server based on the model ID or endpoint ID.

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

The environment ID actually used by the session.

**id** `string`  |  Session ID

Unique identifier of the session, in the format of a string that starts with `sesn_`.

**status** `string`  |  Session status

The running status of the session. After a normal creation succeeds, the initial status is `idle`, and you can continue sending events. Options: `idle` / `running` / `terminated` / `rescheduling` / `initializing` / `failed` / `upgrading`.

**type** `string`  |  Object type

Object type. Fixed to `session`.

**updated_at** `string`  |  Update time

The time when the session information was last updated, in RFC 3339 format.

**archived_at** `string`  |  Archive time

The time when the session was first archived, as a UTC timestamp in RFC 3339 format. When the archive API is called, the server changes `idle` sessions to `terminated`.

> **Tip**

> For historical sessions that were terminated before the archive feature was launched, the server cannot restore the original termination time, so it may not return this parameter.

**environment** `object`  |  Environment information

A snapshot of the environment configuration actually used by the session. Subsequent changes to the original environment do not change this snapshot.

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

Runtime environment type: `cloud` indicates a ModelArk\-managed environment, and `self_hosted` indicates a user self\-hosted environment.

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

The list of resources actually mounted to the runtime environment after the session is created.

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

List of tags associated with the session, used for classification and retrieval.

**key** `string`  |  Tag key

`tags.key`

Tag key, used for classification and retrieval. Required, up to 128 characters. It supports letters, digits, spaces, and `_.: / = + - @`.

**value** `string`  |  Tag value

`tags.value`

Used to record the specific content for this tag category.

**title** `string`  |  Session title

Session title, used to help users identify the session in the session list.

**vault_ids** `string[] / null`  |  Vault ID list

The list of vault IDs accessible to the session. The agent can use the credentials in them during runtime.
