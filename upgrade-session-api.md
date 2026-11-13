# Upgrade session

`POST https://ark.ap-southeast.bytepluses.com/api/v3/sessions/{session_id}/upgrades`

Keep the same session and refresh the agent, environment, or vault configuration it currently uses. An upgrade preserves the original session ID and existing session content. It cannot rebind the session to another agent, environment, or vault.

A session must meet the following conditions before it can be upgraded:

* The current status is `idle`.

* The previous turn ended with `end_turn`.

* All threads have finished their current work, and no threads are in the `running` or `rescheduling` status.

* There are no pending cross\-thread tasks, result callbacks, tool authorizations, or tool results.

* There are no other upgrade requests in progress.

* The session and the agent and environment to refresh are not archived.

The `context_management` setting recorded in the session cannot be modified through this API. When other agent configurations are upgraded, the original policy is still retained. If you need to use a new context management policy, update the agent and then create a new session.

When refreshing the environment configuration, this API does not modify the environment template, resource specifications, or prebuild scripts. To change these configurations, first update or create an environment, and then create a new session.

After the request is accepted, the session status changes to `upgrading`. Poll by following the instructions of [Query session details](get-session-api.md), wait until the status returns to `idle`, and then continue sending events. For multi\-agent sessions, you can also use [List threads](list-threads-api.md) to check the status of each thread.

Choose an API based on your goal:

* To keep the same session and refresh the current agent, environment, or vault configuration, use this API.

* **To modify only the title or tags**, use [Update session title and tags](update-session-api.md).

* **If you need a new session or need to change bound resources**, use [Create session](create-session-api.md).

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**session_id** `string` `Required`  |  Session ID

The ID of the session to upgrade, in the format `sesn-...`.

### Body parameters

**agent** `object`  |  Agent configuration

The agent configuration used after the session upgrade.`id` must be the same as the agent ID of the current session, in the format `agent-...`.

**id** `string` `Required`  |  Agent ID

`agent.id`

Agent ID, in the form `agent-...`. It must match the agent ID of the current session.

**type** `string` `Required`  |  Object type

`agent.type`

Fixed as `agent_with_upgrades`.

**display_name** `string`  |  Display name

`agent.display_name`

Agent display name used after the upgrade. If omitted, the current value is retained.

**mcp_servers** `object[]`  |  MCP server list used after the upgrade

`agent.mcp_servers`

If omitted, the current configuration is retained; passing `[]` clears it; passing a non\-empty array replaces it as a whole.

**name** `string` `Required`  |  MCP service name.

`agent.mcp_servers.name`

MCP service name referenced by `tools[].mcp_server_name`.

**type** `string` `Required`  |  MCP access method

`agent.mcp_servers.type`

MCP service access method. Fixed to `url`.

**url** `string` `Required`  |  MCP service URL

`agent.mcp_servers.url`

The access address of the MCP service. Duplicate addresses cannot be configured within the same agent.

**model** `object`  |  Model configuration used after the session upgrade

`agent.model`

Supports switching the model or inference endpoint, and adjusting runtime parameters and custom model service configuration. If a subparameter is omitted, the current value is retained.

**id** `string`  |  Model or endpoint ID

`agent.model.id`

Target model ID or inference endpoint ID, such as `seed-2-0-pro-260328` or `ep-20260907123456-abcd1`.

The target model or inference endpoint must belong to the current project and support agentic use. A 400 error is returned in the following cases:

* The ID is empty.

* The resource is unavailable, metadata is missing, or it does not belong to the current project.

* The target model does not support agentic use.

A 403 `ModelNotOpen` error code is returned if the target model has not been activated.

When `id` changes, the server revalidates the model parameters. Unspecified parameters continue to use their values before the upgrade.

**reasoning_effort** `string`  |  Reasoning effort

`agent.model.reasoning_effort`

The reasoning effort used by the model after the upgrade.

**service_tier** `string`  |  Service tier

`agent.model.service_tier`

The inference service tier used for model calls after the upgrade.

**speed** `string`  |  Inference speed

`agent.model.speed`

Model inference speed tier. If omitted, the current value is retained. Options: `standard`, `fast`.

**thinking** `string`  |  Thinking configuration

`agent.model.thinking`

Thinking mode configuration used by the model after the upgrade.

**multiagent** `object`  |  Multi\-agent configuration used after the upgrade:

`agent.multiagent`

* Omitted: retains the current configuration.

* Pass `null`: clears the configuration and switches to a single agent.

* Pass an object: updates the current configuration. A session that already uses `multiagent_20261001` can update this configuration. A session that uses a single agent or `coordinator` can switch to `multiagent_20261001`; switching from `multiagent_20261001` back to `coordinator` is not allowed.

**type** `string` `Required`  |  Collaboration mode

`agent.multiagent.type`

Multi\-agent collaboration mode:

* `multiagent_20261001`: uses `subagents`.

* `coordinator`: uses `agents`.

If the current session already uses `multiagent_20261001`, it cannot be switched back to `coordinator`.

**agents** `object[]`  |  Member agents

`agent.multiagent.agents`

`coordinator` uses this parameter to configure the member agent list. It supports predefined agents and self, with up to 20 items; passing `[]` clears the list.`multiagent_20261001` does not use this parameter.

**type** `string` `Required`  |  Member source

`agent.multiagent.agents.type`

Member source.`agent` indicates an existing agent in the current account and project; `self` indicates reusing the current primary agent. Self can be used for `multiagent_20261001.predefined_agents` and `coordinator.agents`. When using self, do not provide `id` or `version`.

**id** `string`  |  Agent ID

`agent.multiagent.agents.id`

Member agent ID. This parameter is required when `type` is `agent`; omit it when `type` is `self`.

**version** `integer`  |  Agent version

`agent.multiagent.agents.version`

The member agent version number. It takes effect only when `type` is `agent`. If omitted, the latest version is used.

**subagents** `object`  |  Subagent configuration

`agent.multiagent.subagents`

When `type` is `multiagent_20261001`, use this parameter to configure subagents; omit it when `type` is `coordinator`.

**type** `string` `Required`  |  Subagent toggle

`agent.multiagent.subagents.type`

Whether the current agent is allowed to create subagents. Options: `enabled`, `disabled`.

**inline_agents** `object`  |  Inline agent configuration

`agent.multiagent.subagents.inline_agents`

Inline agent configuration. If omitted, the current value is retained. When it is switched to `multiagent_20261001`, this is enabled by default.

**type** `string` `Required`  |  Inline agent toggle

`agent.multiagent.subagents.inline_agents.type`

Whether inline agents can be created at runtime with a name and system instructions. Options: `enabled`, `disabled`.

**predefined_agents** `object[]`  |  Predefined members

`agent.multiagent.subagents.predefined_agents`

List of predefined agent and self members, up to 20 items. If omitted, the current list is retained; if `[]` is passed, the list is cleared.

* When a referenced agent uses `multiagent_20261001`, its child members can continue to be recorded within the session depth limit.

* When a referenced agent uses `coordinator`, it is treated as a leaf member.

**type** `string` `Required`  |  Member source

`agent.multiagent.subagents.predefined_agents.type`

Member source.`agent` indicates an existing agent in the current account and project; `self` indicates reusing the current primary agent. Self can be used for `multiagent_20261001.predefined_agents` and `coordinator.agents`. When using self, do not provide `id` or `version`.

**id** `string`  |  Agent ID

`agent.multiagent.subagents.predefined_agents.id`

Member agent ID. This parameter is required when `type` is `agent`; omit it when `type` is `self`.

**version** `integer`  |  Agent version

`agent.multiagent.subagents.predefined_agents.version`

The member agent version number. It takes effect only when `type` is `agent`. If omitted, the latest version is used.

**skills** `object[]`  |  Skill list used after the upgrade

`agent.skills`

If omitted, the current configuration is retained; passing `[]` clears it; passing a non\-empty array replaces it as a whole.

**type** `string` `Required`  |  Skill source

`agent.skills.type`

Skill source.

* `ark`: a preset skill from ModelArk.

* `custom`: a custom skill created by the current account through the Skills API.

**skill_id** `string`  |  Skill ID

`agent.skills.skill_id`

The skill ID to bind.

**version** `string`  |  Skill version

`agent.skills.version`

The skill version number to bind. If omitted, the latest version of the skill is used.

**system** `string`  |  System prompt used after the upgrade

`agent.system`

If omitted, the current value is retained.

**tools** `object[]`  |  Tool list used after the upgrade

`agent.tools`

If omitted, the current configuration is retained; passing `[]` clears it; passing a non\-empty array replaces it as a whole.

**type** `string` `Required`  |  Tool type

`agent.tools.type`

Tool type.

* `agent_toolset_20260916`: Use ModelArk's built\-in basic toolset.

* `mcp_toolset`: Use tools provided by the specified MCP service.

* `custom`: Use a custom tool executed by the client.

   Options: `agent_toolset_20260401`, `agent_toolset_20260701`, `agent_toolset_20260916`, `mcp_toolset`, `custom`.

**configs** `object[]`  |  Tool configuration list

`agent.tools.configs`

Sets the tool enabled status, permission policy, and tool model by tool name.

**name** `string` `Required`  |  Tool name

`agent.tools.configs.name`

The tool name corresponding to the tool configuration.

**enabled** `boolean`  |  Whether enabled

`agent.tools.configs.enabled`

Whether to enable this tool.

**permission_policy** `object`  |  Permission policy

`agent.tools.configs.permission_policy`

The invocation permission policy used by the tool.

**type** `string` `Required`  |  Policy type

`agent.tools.configs.permission_policy.type`

Policy type. `always_allow` means direct calls are allowed, and `always_ask` means user confirmation is required before each call.

**default_config** `object`  |  Default tool configuration

`agent.tools.default_config`

The default enabled status and permission policy used for tools that are not individually configured in `configs`.

**enabled** `boolean`  |  Whether enabled

`agent.tools.default_config.enabled`

Whether tools that are not configured separately are enabled by default.

**permission_policy** `object`  |  Permission policy

`agent.tools.default_config.permission_policy`

The invocation permission policy used by tools that are not configured separately.

**type** `string` `Required`  |  Policy type

`agent.tools.default_config.permission_policy.type`

Policy type. `always_allow` means direct calls are allowed, and `always_ask` means user confirmation is required before each call.

**description** `string`  |  Custom tool description

`agent.tools.description`

Describes the function and use cases of the custom tool, so the model can determine when to call the tool.

**input_schema** `object`  |  Input parameter definition

`agent.tools.input_schema`

Defines the input parameters and constraints for the custom tool.

**properties** `object`  |  Parameter property definition

`agent.tools.input_schema.properties`

The JSON Schema `properties` object for custom tool input parameters. The key is the parameter name, and the value is the JSON Schema subobject for the corresponding parameter.

**required** `string[]`  |  List of required parameter names

`agent.tools.input_schema.required`

List of required parameter names in custom tool input. Each name must match a parameter name defined in `properties`.

**type** `string` `Default object`  |  Schema type

`agent.tools.input_schema.type`

The top\-level type of the JSON Schema. It is fixed as `object`.

**mcp_server_name** `string`  |  MCP service name.

`agent.tools.mcp_server_name`

The MCP service name referenced by the `mcp_toolset` tool. It must match `mcp_servers[].name`.

**name** `string`  |  Custom tool name

`agent.tools.name`

The name of the custom tool.

> **Tip**

> This parameter is required when `type` is `custom`. It can contain only letters, digits, underscores, and hyphens, and must be unique within the same agent.

**version** `integer`  |  Version number

`agent.version`

Target agent version number. Unlike when creating a session, omitting this or passing `0` means continuing to use the agent configuration of the current session, and it does not automatically switch to the latest version.

* To switch, explicitly provide a positive integer version number.

* When a version is specified, the configuration saved for that version is used first, and then parameters with the same names provided in this request are applied.

**environment** `object`  |  Environment configuration

A reference to the environment used by the current session, used to refresh the latest configuration of that environment.`id` is used to confirm the environment to refresh and must be the same as the environment ID currently bound to the session.

**id** `string` `Required`  |  Environment ID

`environment.id`

Environment ID, in the form `env-...`. It must match the environment ID of the current session.

**type** `string` `Required`  |  Object type

`environment.type`

Fixed as `environment_with_upgrades`.

**config** `object`  |  Configuration changes for the session runtime environment

`environment.config`

Cloud environments support replacing egress policies, appending dependency packages, merging environment variables, and replacing initialization scripts; self\-hosted environments do not accept these runtime parameters.

**env** `object`  |  Environment variables to add or update

`environment.config.env`

passed as string key\-value pairs. The server merges them with the current configuration, and variables with the same name use the value in this request.

**networking** `object`  |  Container egress policy to use after this upgrade

`environment.config.networking`

If omitted, the current policy is retained; if provided, it replaces the entire policy.

**type** `string` `Required`  |  Outbound network policy type

`environment.config.networking.type`

The type of container outbound network policy used by the environment. Options: `unrestricted`, `limited`.

> **Tip**

* > `unrestricted` means outbound destinations are not restricted, but the platform's general security blocking policies still apply.

* > `limited` means access is allowed only to destinations covered by the DNS/IP rules required by the platform, domain names in `allowed_hosts`, and additional package repository domains added when `allow_package_managers` is enabled.

* > `limited` is a fail\-closed policy: if no allowed domains are configured, the sandbox can still start normally, but all external network access is blocked.

**allow_package_managers** `boolean`  |  Package manager access

`environment.config.networking.allow_package_managers`

Whether access to package manager repositories is allowed in limited outbound mode.

> **Tip**

> When enabled, the platform adds package manager repository domains to the `limited` outbound rules for installing `apt`, `cargo`, `gem`, `go`, `npm`, and `pip` dependencies. When disabled or omitted, dependencies declared in `packages` do not automatically allow access to external package repositories.

> If package manager repository domains are not configured on the platform side, enabling this parameter causes the request to fail.

**allowed_hosts** `string[]`  |  Allowed hosts

`environment.config.networking.allowed_hosts`

The list of external domain names that the sandbox is allowed to directly access in limited outbound mode.

> **Tip**

* > Only bare domain names or leftmost wildcard domain names are supported, such as `example.com` and `*.example.com`.

* > URLs, ports, paths, query parameters, IP addresses, and `localhost` are not supported. Middle wildcards and multi\-level wildcards are also not supported.

* > When omitted or passed as an empty array, no additional external domain names are allowed.

**packages** `object`  |  Dependency packages to add or update

`environment.config.packages`

The server merges them with the current configuration by package name: packages with the same name use the version in this request, and new packages are appended to the existing list.

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

**setup_script** `string`  |  Initialization script used after the upgrade

`environment.config.setup_script`

If omitted, the current value is retained; if an empty string is passed, it is cleared.

**type** `string`  |  Runtime environment type

`environment.config.type`

Runtime environment type. If omitted, the current session's environment type is used. If you need to specify it explicitly, you can only specify the current environment type: use `cloud` for a cloud environment and `self_hosted` for a self\-hosted environment. Upgrades do not support switching environment types. If you specify a value that does not match the current environment, 403 `OperationDenied.EnvironmentTypeMismatch` is returned.

**vault_ids** `string[]`  |  Vault ID list

The list of vault IDs bound to the current session, used to reload the credential configurations in these vaults without changing the bindings between the session and the vaults.

The set of IDs passed in must match the currently bound set, regardless of order or duplicates.

Omitting this parameter keeps the existing bindings unchanged. Passing `[]` applies only when the current session is not bound to a vault.

## Response parameters

**agent** `object`  |  Agent configuration

The agent configuration after the session upgrade. If the request includes `agent`, the refreshed configuration is returned. If the request does not include `agent`, the configuration used before the upgrade is returned.

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

Base agent type used by the current configuration.

* `ark_agent_preview`: a general agent.

**context_management** `object[]`  |  Context management

`agent.context_management`

The context management policy recorded when the session is created. This parameter cannot be modified when the session is upgraded. If no custom policy has been saved in the snapshot or the saved policy does not include `compact_threshold`, the response shows the platform's current default compression policy or default threshold. The current default threshold is `192000` tokens.

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

MCP server configuration list used by the current session.

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

Model configuration actually used by the current session.

**id** `string`  |  Model or endpoint ID

`agent.model.id`

Model ID or inference endpoint ID currently used by the session.

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

Multi\-agent configuration used by the current session.

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

The member agent context management policy recorded when the session is created. This parameter cannot be modified when the session is upgraded. If no custom policy has been saved in the member snapshot or the saved policy does not include `compact_threshold`, the response shows the platform's current default compression policy or default threshold. The current default threshold is `192000` tokens.

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

**reasoning_effort** `string`  |  Reasoning effort level

`agent.multiagent.agents.model.reasoning_effort`

The reasoning effort level currently used by the member agent.

**service_tier** `string`  |  Inference service tier

`agent.multiagent.agents.model.service_tier`

The inference service tier currently used by the member agent.

**speed** `string`  |  Inference speed

`agent.multiagent.agents.model.speed`

The model inference speed tier used by the member agent.

**thinking** `string`  |  Thinking mode

`agent.multiagent.agents.model.thinking`

The thinking mode currently used by the member agent.

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

The member agent context management policy recorded when the session is created. This parameter cannot be modified when the session is upgraded. If no custom policy has been saved in the member snapshot or the saved policy does not include `compact_threshold`, the response shows the platform's current default compression policy or default threshold. The current default threshold is `192000` tokens.

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

**reasoning_effort** `string`  |  Reasoning effort level

`agent.multiagent.subagents.predefined_agents.model.reasoning_effort`

The reasoning effort level currently used by the member agent.

**service_tier** `string`  |  Inference service tier

`agent.multiagent.subagents.predefined_agents.model.service_tier`

The inference service tier currently used by the member agent.

**speed** `string`  |  Inference speed

`agent.multiagent.subagents.predefined_agents.model.speed`

The model inference speed tier used by the member agent.

**thinking** `string`  |  Thinking mode

`agent.multiagent.subagents.predefined_agents.model.thinking`

The thinking mode currently used by the member agent.

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

Skill configuration list used by the current session.

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

List of tool configurations used by the current session; the parameter structure is the same as `tools` in the agent object.

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

The environment ID actually used by the upgraded session.

**id** `string`  |  Session ID

The session ID, in the format `sesn-...`, which is the same as `session_id` in the request.

**stats** `object`  |  Duration statistics

The session's cumulative running time and total lifecycle duration.

**active_seconds** `number`  |  Cumulative running time

`stats.active_seconds`

The cumulative number of seconds that the session was running. This is the same as `usage.active_seconds`.

Only completed running intervals are counted; the current unfinished `running` interval is not included for now.

**duration_seconds** `number`  |  Total session duration

`stats.duration_seconds`

The total duration from session creation to the current query time, in seconds. After the session is terminated, the returned value is the duration from creation to termination.

**status** `string`  |  Session status

The running status of the session. After the request is accepted, `upgrading` is returned. Wait until the status returns to `idle` before continuing to send events. Options: `idle` / `running` / `terminated` / `rescheduling` / `initializing` / `failed` / `upgrading`.

**type** `string`  |  Object type

Fixed as `session`.

**updated_at** `string`  |  Update time

The time when the session information was last updated, in RFC 3339 format.

**archived_at** `string`  |  Archive time

The time when the session was first archived, as a UTC timestamp in RFC 3339 format. When the archive API is called, the server changes `idle` sessions to `terminated`.

> **Tip**

> For historical sessions that were terminated before the archive feature was launched, the server cannot restore the original termination time, so it may not return this parameter.

**deployment_id** `string`  |  Deployment ID

The ID of the deployment that created this session. Returned when the session is created through a deployment. This parameter is not returned when the session is created directly through the Sessions API.

**environment** `object`  |  Environment configuration

The environment configuration actually used by the current session. Subsequent changes to the original environment do not automatically change this session.

**created_at** `string`  |  Creation time

`environment.created_at`

The time when the environment creation was completed, in RFC 3339 format.

**id** `string`  |  Environment ID

`environment.id`

The environment ID used by the current session.

**name** `string`  |  Environment name

`environment.name`

Environment name used by the current session.

**type** `string`  |  Object type

`environment.type`

Session environment snapshot type.`environment` indicates direct use of the base environment; `environment_with_overrides` indicates that the current session uses environment configuration overrides.

**updated_at** `string`  |  Update time

`environment.updated_at`

The time when the environment configuration was last updated, in RFC 3339 format.

**config** `object`  |  Environment configuration

`environment.config`

The environment configuration used by the current session.

**type** `string`  |  Environment type

`environment.config.type`

Runtime environment type. Options: `cloud`, `self_hosted`.

**env** `object`  |  Environment variable

`environment.config.env`

Environment variable key\-value pairs in the session runtime environment.

**networking** `object`  |  Outbound network policy

`environment.config.networking`

Container outbound network policy.

**type** `string`  |  Outbound network policy type

`environment.config.networking.type`

The outbound network policy currently used by the session. Options: `unrestricted`, `limited`.

**allow_package_managers** `boolean`  |  Allow package managers

`environment.config.networking.allow_package_managers`

Whether access to package manager repositories is allowed in limited outbound mode.

**allowed_hosts** `string[]`  |  Allowed hosts

`environment.config.networking.allowed_hosts`

The list of external domain names that the sandbox is allowed to directly access in limited outbound mode.

**packages** `object`  |  Dependency package configuration

`environment.config.packages`

Dependency packages preinstalled at startup.

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

Initialization script executed when the session runtime environment starts.

**tos** `object`  |  Artifact storage configuration

`environment.config.tos`

TOS storage configuration for environment outputs.

**bucket** `string`  |  TOS bucket

`environment.config.tos.bucket`

User TOS bucket name saved in the session environment snapshot.

**prefix** `string`  |  Object prefix

`environment.config.tos.prefix`

Object storage directory prefix saved in the session environment snapshot.

**description** `string`  |  Environment description

`environment.description`

Environment description used by the current session.

**metadata** `object`  |  Custom metadata

`environment.metadata`

User\-defined metadata.

**overridden_fields** `string[]`  |  Overridden parameters

`environment.overridden_fields`

The list of `config` subparameter names actually overridden by the current session relative to the base environment. Returned only when *type* is `environment_with_overrides` and overridden parameters exist.

**resources** `object[]`  |  Resource list

The list of resources currently mounted to the upgraded session's runtime environment.

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

**tags** `object[]`  |  Tag list

Session tags, used for business\-side retrieval and grouping.

**key** `string`  |  Tag key

`tags.key`

Tag key, used for classification and retrieval.

**value** `string`  |  Tag value

`tags.value`

Used to record the specific content for this tag category.

**title** `string`  |  Session title

The upgraded session title, used to identify and retrieve the session.

**vault_ids** `string[] / null`  |  Vault ID list

The list of vault IDs accessible to the upgraded session. The agent can use the credentials in them during runtime.
