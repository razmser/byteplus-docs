# List sessions

`GET https://ark.ap-southeast.bytepluses.com/api/v3/sessions`

Query of the session list under the current account by page. Supports filtering by archive status, agent, status, tags, and creation time. Each result includes the agent, environment, resource, and tag information currently visible to the session.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Query parameters

> Passed as URL query string.

**agent_id** `string`  |  Agent ID

Filter by agent ID. Only sessions of the specified agent are returned.

**created_at_gt** `string`  |  Creation time lower limit (exclusive)

Creation time is greater than this value (ISO 8601), exclusive of the boundary.

**created_at_gte** `string`  |  Creation time lower limit (inclusive)

Creation time is greater than or equal to this value (ISO 8601), inclusive of the boundary.

**created_at_lt** `string`  |  Creation time upper limit (exclusive)

Creation time is less than this value (ISO 8601), exclusive of the boundary.

**created_at_lte** `string`  |  Creation time upper limit (inclusive)

Creation time is less than or equal to this value (ISO 8601), inclusive of the boundary.

**deployment_id** `string`  |  Deployment ID

Filter by deployment ID. Only sessions created by the specified deployment are returned. Regular sessions do not match this condition.

**include_archived** `boolean` `Default false`  |  Include archived sessions

Whether to include archived sessions in the results.

* **Omitted or set to ** **`false`**: Returns only sessions that do not include `archived_at` and whose `status` is not `terminated`.

* **Set to ** **`true`**: Sessions are not excluded based on termination status or `archived_at`. Sessions that were terminated historically but whose termination time cannot be recovered may not include `archived_at`.

**limit** `integer` `Default 20`  |  Items per page

Number of sessions returned on a single page.

**Range**: `[1, 100]`

**memory_store_id** `string`  |  Memory store ID

Filter by mounted memory store. Only sessions whose resource list contains the specified `memory_store_id` are returned.

**order** `string` `Default desc`  |  Sort order

Sort order.`asc` indicates ascending order by creation time, and `desc` indicates descending order by creation time.

**page** `string`  |  Next page identifier

Next page identifier. Omit this parameter for the first request; for subsequent requests, pass the `next_page` value from the previous response as is, and keep `order` and the filter conditions unchanged.

**scene** `string`  |  Session scene

Filter by session scene tag. If omitted or an empty value is passed, no filtering is applied.

Common values are `default` and `dreams`. Passing other values does not cause an error, but returns an empty list.

**status** `string[]`  |  Session status

Filter by the session status. You can pass this parameter multiple times; the server returns sessions that match any of the statuses. Options: `idle`, `running`, `terminated`, `rescheduling`, `initializing`, `failed`, `upgrading`.

**tags** `object[]`  |  Filter by tags

Filter by tags in the format `tags[<KEY>]=<VALUE>`, for example `tags[team]=agent-infra`. Multiple different tags are combined by intersection; when the value is empty, matching is performed only by tag key.

**key** `string` `Required`  |  Tag key

`tags.key`

Tag key, used for classification and retrieval.

**value** `string`  |  Tag value

`tags.value`

Used to record the specific content for this tag category.

## Response parameters

**data** `object[]`  |  Session list

A list of sessions that match the query conditions, sorted in the direction specified by `order`. By default, sessions are returned in descending order by creation time, with the most recently created sessions first; if there are no matching results, an empty array `[]` is returned.

**agent** `object`  |  Agent information

`data.agent`

Snapshot of the agent configuration saved when the session was created. Subsequent updates to the agent do not affect the snapshot in this session.

**created_at** `string`  |  Creation time

`data.agent.created_at`

The time when the agent creation was completed, in RFC 3339 format.

**id** `string`  |  Agent ID

`data.agent.id`

Unique identifier of the agent.

**name** `string`  |  Agent name

`data.agent.name`

Agent name, used for users to identify and manage the agent.

**type** `string`  |  Object type

`data.agent.type`

Object type. Fixed to `agent`.

**updated_at** `string`  |  Update time

`data.agent.updated_at`

The time when the agent configuration was last updated, in RFC 3339 format.

**version** `integer`  |  Version number

`data.agent.version`

The agent version number snapshot created when the session was created.

**base_agent** `string`  |  Base agent type

`data.agent.base_agent`

Base agent type currently used by the session.

* `ark_agent_preview`: a general agent.

**context_management** `object[]`  |  Context management

`data.agent.context_management`

Context management policy frozen when the session is created. If no custom policy has been saved in the snapshot or the saved policy does not include `compact_threshold`, the response shows the platform's current default compression policy or default threshold. The current default threshold is `192000` tokens.

Default display values in the response are not written back to the session snapshot. An explicitly saved `compact_threshold` takes precedence in the response.

**type** `string`  |  Policy type

`data.agent.context_management.type`

Policy type, fixed as `compaction`.

**compact_threshold** `integer`  |  Compaction threshold

`data.agent.context_management.compact_threshold`

The automatic compaction trigger threshold displayed in the query response, measured in tokens. If no value is explicitly saved, the public API response fills in the platform's current default value, which is `192000`; this displayed value is not written back to the agent version or session snapshot.

**instructions** `string`  |  Summary instructions

`data.agent.context_management.instructions`

User\-configured compaction summary instructions.

**description** `string`  |  Agent description

`data.agent.description`

Agent description, used to describe the agent's purpose, capability scope, applicable scenarios, and so on.

**display_name** `string`  |  Display name

`data.agent.display_name`

Agent display name currently used by the session.

**mcp_servers** `object[]`  |  MCP service list

`data.agent.mcp_servers`

List of configured MCP services.

**name** `string`  |  MCP service name.

`data.agent.mcp_servers.name`

MCP service name referenced by `tools[].mcp_server_name`.

**type** `string`  |  MCP access method

`data.agent.mcp_servers.type`

MCP service access method. Fixed to `url`.

**url** `string`  |  MCP service URL

`data.agent.mcp_servers.url`

MCP service access URL snapshot created when the session is created.

**metadata** `object`  |  Custom metadata

`data.agent.metadata`

Custom key\-value pairs set when the agent was created. The server does not define fixed subparameters.

**model** `object`  |  Model configuration

`data.agent.model`

The model configuration used by the agent includes the model or inference endpoint ID and the inference speed tier.

**id** `string`  |  Model or endpoint ID

`data.agent.model.id`

The model ID or inference endpoint ID called by the agent. For example, model ID `dola-seed-2-1-turbo-260628`, or inference endpoint ID `ep-20260907123456-abcd1`.

**protocol** `string`  |  Invocation protocol

`data.agent.model.protocol`

Protocol used for session model calls:

* `ark_responses`: ModelArk Responses protocol.

* `openai_chat_completions`: OpenAI Chat Completions compatible protocol.

* `openai_responses`: OpenAI Responses compatible protocol.

* `anthropic_messages`: Anthropic Messages compatible protocol.

**reasoning_effort** `string`  |  Reasoning effort level

`data.agent.model.reasoning_effort`

The reasoning effort level currently used by the session.

**service_tier** `string`  |  Inference service tier

`data.agent.model.service_tier`

The reasoning service tier currently used by the session.

**speed** `string` `Default standard`  |  Inference speed

`data.agent.model.speed`

Inference speed mode. Options: `standard`, `fast`.

**thinking** `string`  |  Thinking mode

`data.agent.model.thinking`

Thinking mode currently used by the session.

**multiagent** `object`  |  Multi Agent configuration

`data.agent.multiagent`

Multi\-agent configuration currently used by the session.

**type** `string`  |  Collaboration mode

`data.agent.multiagent.type`

Multi\-agent collaboration mode.`multiagent_20261001` uses `subagents`; `coordinator` uses `agents`.

**agents** `object[]`  |  Member agents

`data.agent.multiagent.agents`

When `type` is `coordinator`, the list of member agents recorded when the session was created is returned.

**type** `string`  |  Reference type

`data.agent.multiagent.agents.type`

Member reference type:

* `agent`: References another agent configuration that was recorded when the session was created.

* `self`: Uses the effective configuration of the current agent when the session was created.

**context_management** `object[]`  |  Context management

`data.agent.multiagent.agents.context_management`

The member agent context management policy that is recorded when the session is created. If no custom policy has been saved in the member snapshot or the saved policy does not include `compact_threshold`, the response shows the platform's current default compression policy or default threshold. The current default threshold is `192000` tokens.

Default display values in the response are not written back to the session snapshot. An explicitly saved `compact_threshold` takes precedence in the response.

**type** `string`  |  Policy type

`data.agent.multiagent.agents.context_management.type`

Policy type, fixed as `compaction`.

**compact_threshold** `integer`  |  Compaction threshold

`data.agent.multiagent.agents.context_management.compact_threshold`

The automatic compaction trigger threshold displayed in the query response, measured in tokens. If no value is explicitly saved, the public API response fills in the platform's current default value, which is `192000`; this displayed value is not written back to the agent version or session snapshot.

**instructions** `string`  |  Summary instructions

`data.agent.multiagent.agents.context_management.instructions`

User\-configured compaction summary instructions.

**description** `string`  |  Agent description

`data.agent.multiagent.agents.description`

The member agent description snapshot created when the session was created.

**display_name** `string`  |  Display name

`data.agent.multiagent.agents.display_name`

The member agent display name snapshot created when the session was created.

**id** `string`  |  Agent ID

`data.agent.multiagent.agents.id`

The ID of the member agent; when `type` is `self`, the ID of the current agent is returned.

**mcp_servers** `object[]`  |  MCP service list

`data.agent.multiagent.agents.mcp_servers`

The member agent MCP service configuration list snapshot created when the session was created.

**name** `string`  |  MCP service name.

`data.agent.multiagent.agents.mcp_servers.name`

MCP service name referenced by `tools[].mcp_server_name`.

**type** `string`  |  MCP access method

`data.agent.multiagent.agents.mcp_servers.type`

MCP service access method. Fixed to `url`.

**url** `string`  |  MCP service URL

`data.agent.multiagent.agents.mcp_servers.url`

MCP service access URL snapshot created when the session is created.

**model** `object`  |  Model configuration

`data.agent.multiagent.agents.model`

The member agent model configuration snapshot created when the session was created.

**id** `string`  |  Model or endpoint ID

`data.agent.multiagent.agents.model.id`

The model ID or inference endpoint ID used by the member agent.

**speed** `string`  |  Inference speed

`data.agent.multiagent.agents.model.speed`

The model inference speed tier used by the member agent.

**name** `string`  |  Agent name

`data.agent.multiagent.agents.name`

The member agent name snapshot created when the session was created.

**skills** `object[]`  |  Skill list

`data.agent.multiagent.agents.skills`

The member agent skill configuration list snapshot created when the session was created.

**type** `string`  |  Skill type

`data.agent.multiagent.agents.skills.type`

Skill source type:

* `ark`: a preset skill from the ModelArk platform.

* `custom`: a custom skill created by the current account.

**display_name** `string`  |  Skill name

`data.agent.multiagent.agents.skills.display_name`

The skill display name recorded when the session was created.

**skill_id** `string`  |  Skill ID

`data.agent.multiagent.agents.skills.skill_id`

The skill ID used by the session.

**use_latest** `boolean`  |  Use the latest version

`data.agent.multiagent.agents.skills.use_latest`

Whether to select the latest version of this skill when creating the session.`true` indicates that the latest version at that time is selected, and `false` indicates that the version specified by `version` is used.

**version** `string`  |  Skill version

`data.agent.multiagent.agents.skills.version`

The skill version number snapshot created when the session was created.

**system** `string`  |  System prompt

`data.agent.multiagent.agents.system`

The member agent system prompt snapshot created when the session was created.

**tools** `object[]`  |  Tool list

`data.agent.multiagent.agents.tools`

The member agent tool configuration list snapshot created when the session was created.

**type** `string`  |  Tool type

`data.agent.multiagent.agents.tools.type`

Tool type:

* `agent_toolset_20260916`: the current version of the ModelArk built\-in toolset.

* `mcp_toolset`: A toolset provided by the specified MCP service.

* `custom`: A custom tool executed by the client.

   Options: `agent_toolset_20260916`, `agent_toolset_20260701`, `mcp_toolset`, `custom`.

**configs** `object[]`  |  Tool configuration

`data.agent.multiagent.agents.tools.configs`

Per\-tool configuration snapshot created when the session is created.

**name** `string`  |  Tool name

`data.agent.multiagent.agents.tools.configs.name`

The tool name recorded in the session snapshot.

**enabled** `boolean`  |  Whether enabled

`data.agent.multiagent.agents.tools.configs.enabled`

Whether the tool is enabled.

**permission_policy** `object`  |  Permission policy

`data.agent.multiagent.agents.tools.configs.permission_policy`

The invocation permission policy used by the tool.

**type** `string`  |  Policy type

`data.agent.multiagent.agents.tools.configs.permission_policy.type`

Policy type. `always_allow` means direct calls are allowed, and `always_ask` means user confirmation is required before each call.

**default_config** `object`  |  Default tool configuration

`data.agent.multiagent.agents.tools.default_config`

The default configuration used by tools that are not separately listed in `configs`.

**enabled** `boolean`  |  Whether enabled

`data.agent.multiagent.agents.tools.default_config.enabled`

Whether tools that are not configured separately are enabled by default.

**permission_policy** `object`  |  Permission policy

`data.agent.multiagent.agents.tools.default_config.permission_policy`

The invocation permission policy used by tools that are not configured separately.

**type** `string`  |  Policy type

`data.agent.multiagent.agents.tools.default_config.permission_policy.type`

Policy type. `always_allow` means direct calls are allowed, and `always_ask` means user confirmation is required before each call.

**description** `string`  |  Custom tool description

`data.agent.multiagent.agents.tools.description`

Custom tool description snapshot created when the session is created, used to record the tool's capabilities and applicable scenarios.

**input_schema** `object`  |  Input parameter definition

`data.agent.multiagent.agents.tools.input_schema`

Returns the custom tool input parameter JSON Schema recorded in the agent snapshot of the returned session.

**properties** `object`  |  Parameter property definition

`data.agent.multiagent.agents.tools.input_schema.properties`

Returns the JSON Schema property mapping recorded in the session's agent snapshot. Each key is a parameter name, and each value is the JSON Schema child object for the corresponding parameter.

**required** `string[]`  |  List of required parameter names

`data.agent.multiagent.agents.tools.input_schema.required`

List of parameter names that must be provided when returning the called custom tool.

**type** `string`  |  Schema type

`data.agent.multiagent.agents.tools.input_schema.type`

The returned top\-level type of the JSON Schema. It is fixed as `object`.

**mcp_server_name** `string`  |  MCP service name.

`data.agent.multiagent.agents.tools.mcp_server_name`

The MCP service name referenced by `mcp_toolset` in the session.

**name** `string`  |  Custom tool name

`data.agent.multiagent.agents.tools.name`

Custom tool name snapshot created when the session is created.

**version** `integer`  |  Version number

`data.agent.multiagent.agents.version`

The member agent version number snapshot created when the session was created.

**subagents** `object`  |  Subagent configuration

`data.agent.multiagent.subagents`

When `type` is `multiagent_20261001`, the subagent configuration recorded when the session was created is returned.

**type** `string`  |  Subagent toggle

`data.agent.multiagent.subagents.type`

Whether the current agent is allowed to create subagents. Options: `enabled`, `disabled`.

**inline_agents** `object`  |  Inline agent configuration

`data.agent.multiagent.subagents.inline_agents`

Runtime inline agent configuration.

**type** `string`  |  Inline agent toggle

`data.agent.multiagent.subagents.inline_agents.type`

Whether inline agents can be created at runtime with a name and system instructions. Options: `enabled`, `disabled`.

**predefined_agents** `object[]`  |  Predefined members

`data.agent.multiagent.subagents.predefined_agents`

The list of predefined agents and self references recorded for `multiagent_20261001` when the session was created.

* When a referenced agent uses `multiagent_20261001`, its child members can continue to be recorded within the session depth limit.

* When a referenced agent uses `coordinator`, it is treated as a leaf member.

**type** `string`  |  Reference type

`data.agent.multiagent.subagents.predefined_agents.type`

Member reference type:

* `agent`: References another agent configuration that was recorded when the session was created.

* `self`: Uses the effective configuration of the current agent when the session was created.

**context_management** `object[]`  |  Context management

`data.agent.multiagent.subagents.predefined_agents.context_management`

The member agent context management policy that is recorded when the session is created. If no custom policy has been saved in the member snapshot or the saved policy does not include `compact_threshold`, the response shows the platform's current default compression policy or default threshold. The current default threshold is `192000` tokens.

Default display values in the response are not written back to the session snapshot. An explicitly saved `compact_threshold` takes precedence in the response.

**type** `string`  |  Policy type

`data.agent.multiagent.subagents.predefined_agents.context_management.type`

Policy type, fixed as `compaction`.

**compact_threshold** `integer`  |  Compaction threshold

`data.agent.multiagent.subagents.predefined_agents.context_management.compact_threshold`

The automatic compaction trigger threshold displayed in the query response, measured in tokens. If no value is explicitly saved, the public API response fills in the platform's current default value, which is `192000`; this displayed value is not written back to the agent version or session snapshot.

**instructions** `string`  |  Summary instructions

`data.agent.multiagent.subagents.predefined_agents.context_management.instructions`

User\-configured compaction summary instructions.

**description** `string`  |  Agent description

`data.agent.multiagent.subagents.predefined_agents.description`

The member agent description snapshot created when the session was created.

**display_name** `string`  |  Display name

`data.agent.multiagent.subagents.predefined_agents.display_name`

The member agent display name snapshot created when the session was created.

**id** `string`  |  Agent ID

`data.agent.multiagent.subagents.predefined_agents.id`

The ID of the member agent; when `type` is `self`, the ID of the current agent is returned.

**mcp_servers** `object[]`  |  MCP service list

`data.agent.multiagent.subagents.predefined_agents.mcp_servers`

The member agent MCP service configuration list snapshot created when the session was created.

**name** `string`  |  MCP service name.

`data.agent.multiagent.subagents.predefined_agents.mcp_servers.name`

MCP service name referenced by `tools[].mcp_server_name`.

**type** `string`  |  MCP access method

`data.agent.multiagent.subagents.predefined_agents.mcp_servers.type`

MCP service access method. Fixed to `url`.

**url** `string`  |  MCP service URL

`data.agent.multiagent.subagents.predefined_agents.mcp_servers.url`

MCP service access URL snapshot created when the session is created.

**model** `object`  |  Model configuration

`data.agent.multiagent.subagents.predefined_agents.model`

The member agent model configuration snapshot created when the session was created.

**id** `string`  |  Model or endpoint ID

`data.agent.multiagent.subagents.predefined_agents.model.id`

The model ID or inference endpoint ID used by the member agent.

**speed** `string`  |  Inference speed

`data.agent.multiagent.subagents.predefined_agents.model.speed`

The model inference speed tier used by the member agent.

**name** `string`  |  Agent name

`data.agent.multiagent.subagents.predefined_agents.name`

The member agent name snapshot created when the session was created.

**skills** `object[]`  |  Skill list

`data.agent.multiagent.subagents.predefined_agents.skills`

The member agent skill configuration list snapshot created when the session was created.

**type** `string`  |  Skill type

`data.agent.multiagent.subagents.predefined_agents.skills.type`

Skill source type:

* `ark`: a preset skill from the ModelArk platform.

* `custom`: a custom skill created by the current account.

**display_name** `string`  |  Skill name

`data.agent.multiagent.subagents.predefined_agents.skills.display_name`

The skill display name recorded when the session was created.

**skill_id** `string`  |  Skill ID

`data.agent.multiagent.subagents.predefined_agents.skills.skill_id`

The skill ID used by the session.

**use_latest** `boolean`  |  Use the latest version

`data.agent.multiagent.subagents.predefined_agents.skills.use_latest`

Whether to select the latest version of this skill when creating the session.`true` indicates that the latest version at that time is selected, and `false` indicates that the version specified by `version` is used.

**version** `string`  |  Skill version

`data.agent.multiagent.subagents.predefined_agents.skills.version`

The skill version number snapshot created when the session was created.

**system** `string`  |  System prompt

`data.agent.multiagent.subagents.predefined_agents.system`

The member agent system prompt snapshot created when the session was created.

**tools** `object[]`  |  Tool list

`data.agent.multiagent.subagents.predefined_agents.tools`

The member agent tool configuration list snapshot created when the session was created.

**type** `string`  |  Tool type

`data.agent.multiagent.subagents.predefined_agents.tools.type`

Tool type:

* `agent_toolset_20260916`: the current version of the ModelArk built\-in toolset.

* `mcp_toolset`: A toolset provided by the specified MCP service.

* `custom`: A custom tool executed by the client.

   Options: `agent_toolset_20260916`, `agent_toolset_20260701`, `mcp_toolset`, `custom`.

**configs** `object[]`  |  Tool configuration

`data.agent.multiagent.subagents.predefined_agents.tools.configs`

Per\-tool configuration snapshot created when the session is created.

**name** `string`  |  Tool name

`data.agent.multiagent.subagents.predefined_agents.tools.configs.name`

The tool name recorded in the session snapshot.

**enabled** `boolean`  |  Whether enabled

`data.agent.multiagent.subagents.predefined_agents.tools.configs.enabled`

Whether the tool is enabled.

**permission_policy** `object`  |  Permission policy

`data.agent.multiagent.subagents.predefined_agents.tools.configs.permission_policy`

The invocation permission policy used by the tool.

**type** `string`  |  Policy type

`data.agent.multiagent.subagents.predefined_agents.tools.configs.permission_policy.type`

Policy type. `always_allow` means direct calls are allowed, and `always_ask` means user confirmation is required before each call.

**default_config** `object`  |  Default tool configuration

`data.agent.multiagent.subagents.predefined_agents.tools.default_config`

The default configuration used by tools that are not separately listed in `configs`.

**enabled** `boolean`  |  Whether enabled

`data.agent.multiagent.subagents.predefined_agents.tools.default_config.enabled`

Whether tools that are not configured separately are enabled by default.

**permission_policy** `object`  |  Permission policy

`data.agent.multiagent.subagents.predefined_agents.tools.default_config.permission_policy`

The invocation permission policy used by tools that are not configured separately.

**type** `string`  |  Policy type

`data.agent.multiagent.subagents.predefined_agents.tools.default_config.permission_policy.type`

Policy type. `always_allow` means direct calls are allowed, and `always_ask` means user confirmation is required before each call.

**description** `string`  |  Custom tool description

`data.agent.multiagent.subagents.predefined_agents.tools.description`

Custom tool description snapshot created when the session is created, used to record the tool's capabilities and applicable scenarios.

**input_schema** `object`  |  Input parameter definition

`data.agent.multiagent.subagents.predefined_agents.tools.input_schema`

Returns the custom tool input parameter JSON Schema recorded in the agent snapshot of the returned session.

**properties** `object`  |  Parameter property definition

`data.agent.multiagent.subagents.predefined_agents.tools.input_schema.properties`

Returns the JSON Schema property mapping recorded in the session's agent snapshot. Each key is a parameter name, and each value is the JSON Schema child object for the corresponding parameter.

**required** `string[]`  |  List of required parameter names

`data.agent.multiagent.subagents.predefined_agents.tools.input_schema.required`

List of parameter names that must be provided when returning the called custom tool.

**type** `string`  |  Schema type

`data.agent.multiagent.subagents.predefined_agents.tools.input_schema.type`

The returned top\-level type of the JSON Schema. It is fixed as `object`.

**mcp_server_name** `string`  |  MCP service name.

`data.agent.multiagent.subagents.predefined_agents.tools.mcp_server_name`

The MCP service name referenced by `mcp_toolset` in the session.

**name** `string`  |  Custom tool name

`data.agent.multiagent.subagents.predefined_agents.tools.name`

Custom tool name snapshot created when the session is created.

**version** `integer`  |  Version number

`data.agent.multiagent.subagents.predefined_agents.version`

The member agent version number snapshot created when the session was created.

**skills** `object[]`  |  Skill list

`data.agent.skills`

The skill configuration list snapshot created when the session was created.

**type** `string`  |  Skill type

`data.agent.skills.type`

Skill source type:

* `ark`: a preset skill from the ModelArk platform.

* `custom`: a custom skill created by the current account.

**display_name** `string`  |  Skill name

`data.agent.skills.display_name`

The skill display name recorded when the session was created.

**skill_id** `string`  |  Skill ID

`data.agent.skills.skill_id`

The skill ID used by the session.

**use_latest** `boolean`  |  Use the latest version

`data.agent.skills.use_latest`

Whether to select the latest version of this skill when creating the session.`true` indicates that the latest version at that time is selected, and `false` indicates that the version specified by `version` is used.

**version** `string`  |  Skill version

`data.agent.skills.version`

The skill version number snapshot created when the session was created.

**system** `string`  |  System prompt

`data.agent.system`

System prompt, which defines the agent's role and behavior rules.

**tools** `object[]`  |  Tool list

`data.agent.tools`

List of configured tools.

**type** `string`  |  Tool type

`data.agent.tools.type`

Tool type:

* `agent_toolset_20260916`: the current version of the ModelArk built\-in toolset.

* `mcp_toolset`: A toolset provided by the specified MCP service.

* `custom`: A custom tool executed by the client.

   Options: `agent_toolset_20260916`, `agent_toolset_20260701`, `mcp_toolset`, `custom`.

**configs** `object[]`  |  Tool configuration

`data.agent.tools.configs`

Per\-tool configuration snapshot created when the session is created.

**name** `string`  |  Tool name

`data.agent.tools.configs.name`

The tool name recorded in the session snapshot.

**enabled** `boolean`  |  Whether enabled

`data.agent.tools.configs.enabled`

Whether the tool is enabled.

**permission_policy** `object`  |  Permission policy

`data.agent.tools.configs.permission_policy`

The invocation permission policy used by the tool.

**type** `string`  |  Policy type

`data.agent.tools.configs.permission_policy.type`

Policy type. `always_allow` means direct calls are allowed, and `always_ask` means user confirmation is required before each call.

**default_config** `object`  |  Default tool configuration

`data.agent.tools.default_config`

The default configuration used by tools that are not separately listed in `configs`.

**enabled** `boolean`  |  Whether enabled

`data.agent.tools.default_config.enabled`

Whether tools that are not configured separately are enabled by default.

**permission_policy** `object`  |  Permission policy

`data.agent.tools.default_config.permission_policy`

The invocation permission policy used by tools that are not configured separately.

**type** `string`  |  Policy type

`data.agent.tools.default_config.permission_policy.type`

Policy type. `always_allow` means direct calls are allowed, and `always_ask` means user confirmation is required before each call.

**description** `string`  |  Custom tool description

`data.agent.tools.description`

Custom tool description snapshot created when the session is created, used to record the tool's capabilities and applicable scenarios.

**input_schema** `object`  |  Input parameter definition

`data.agent.tools.input_schema`

Returns the custom tool input parameter JSON Schema recorded in the agent snapshot of the returned session.

**properties** `object`  |  Parameter property definition

`data.agent.tools.input_schema.properties`

Returns the JSON Schema property mapping recorded in the session's agent snapshot. Each key is a parameter name, and each value is the JSON Schema child object for the corresponding parameter.

**required** `string[]`  |  List of required parameter names

`data.agent.tools.input_schema.required`

List of parameter names that must be provided when returning the called custom tool.

**type** `string`  |  Schema type

`data.agent.tools.input_schema.type`

The returned top\-level type of the JSON Schema. It is fixed as `object`.

**mcp_server_name** `string`  |  MCP service name.

`data.agent.tools.mcp_server_name`

The MCP service name referenced by `mcp_toolset` in the session.

**name** `string`  |  Custom tool name

`data.agent.tools.name`

Custom tool name snapshot created when the session is created.

**created_at** `string`  |  Creation time

`data.created_at`

The session creation time in RFC 3339 format.

**environment_id** `string`  |  Environment ID

`data.environment_id`

The environment ID actually used by the session.

**id** `string`  |  Session ID

`data.id`

The unique identifier of the session, in the format `sesn-xxxxx`.

**status** `string`  |  Session status

`data.status`

The running status of the session. Options: `idle`, `running`, `terminated`, `rescheduling`, `initializing`, `failed`, `upgrading`.

**type** `string`  |  Object type

`data.type`

Object type. Fixed to `session`.

**updated_at** `string`  |  Update time

`data.updated_at`

The time when the session information was last updated, in RFC 3339 format.

**archived_at** `string`  |  Archive time

`data.archived_at`

The time when the session was first archived, as a UTC timestamp in RFC 3339 format. When the archive API is called, the server changes `idle` sessions to `terminated`.

> **Tip**

> For historical sessions that were terminated before the archive feature was launched, the server cannot restore the original termination time, so it may not return this parameter.

**deployment_id** `string`  |  Deployment ID

`data.deployment_id`

The ID of the deployment that created this session. This parameter is returned only for sessions created by a deployment. It is not returned for regular sessions.

**environment** `object`  |  Environment information

`data.environment`

A snapshot of the environment configuration actually used by the session. Subsequent changes to the original environment do not change this snapshot.

**created_at** `string`  |  Creation time

`data.environment.created_at`

The time when the environment creation was completed, in RFC 3339 format.

**id** `string`  |  Environment ID

`data.environment.id`

Unique identifier of the environment.

**name** `string`  |  Environment name

`data.environment.name`

The environment name, used to distinguish this environment in the environment list and session configuration.

**type** `string`  |  Object type

`data.environment.type`

Session environment snapshot type.`environment` indicates direct use of the base environment; `environment_with_overrides` indicates that the current session uses environment configuration overrides.

**updated_at** `string`  |  Update time

`data.environment.updated_at`

The time when the environment configuration was last updated, in RFC 3339 format.

**config** `object`  |  Environment configuration

`data.environment.config`

Runtime configuration for the environment, including the runtime type, network policy, dependency packages, and environment variables.

**type** `string`  |  Environment type

`data.environment.config.type`

Runtime environment type: `cloud` indicates a ModelArk\-managed environment, and `self_hosted` indicates a user self\-hosted environment.

**env** `object`  |  Environment variable

`data.environment.config.env`

Environment variable key\-value pairs in the session runtime environment

**networking** `object`  |  Outbound network policy

`data.environment.config.networking`

Container outbound network policy configuration.

**type** `string`  |  Outbound network policy type

`data.environment.config.networking.type`

The outbound network policy currently used by the session. Options: `unrestricted`, `limited`.

**allow_package_managers** `boolean`  |  Allow package managers

`data.environment.config.networking.allow_package_managers`

Whether access to package manager repositories is allowed in limited outbound mode.

**allowed_hosts** `string[]`  |  Allowed hosts

`data.environment.config.networking.allowed_hosts`

The list of external domain names that the sandbox is allowed to directly access in limited outbound mode.

**packages** `object`  |  Dependency package configuration

`data.environment.config.packages`

Configuration for dependency packages preinstalled at startup.

**apt** `string[]`  |  apt package

`data.environment.config.packages.apt`

List of system packages preinstalled through apt when the session environment starts.

**cargo** `string[]`  |  Cargo packages

`data.environment.config.packages.cargo`

List of Rust crates preinstalled through Cargo when the session environment starts.

**gem** `string[]`  |  gem packages

`data.environment.config.packages.gem`

List of Ruby packages preinstalled through RubyGems when the session environment starts.

**go** `string[]`  |  Go modules

`data.environment.config.packages.go`

List of module dependencies preinstalled through the Go toolchain when the session environment starts.

**npm** `string[]`  |  npm packages

`data.environment.config.packages.npm`

List of Node.js packages preinstalled through npm when the session environment starts.

**pip** `string[]`  |  pip package

`data.environment.config.packages.pip`

List of Python packages preinstalled through pip when the session environment starts.

**type** `string`  |  Configuration type

`data.environment.config.packages.type`

Dependency package configuration type, fixed as `packages`.

**setup_script** `string`  |  Startup script

`data.environment.config.setup_script`

Initialization script executed when the session runtime environment starts; leave it empty or unset to execute no script.

**tos** `object`  |  Artifact storage configuration

`data.environment.config.tos`

Persistent storage configuration for agent artifacts. Empty means no user bucket is configured, and artifacts are stored in ModelArk managed TOS.

**bucket** `string`  |  TOS bucket

`data.environment.config.tos.bucket`

User TOS bucket name saved in the session environment snapshot.

**prefix** `string`  |  Object prefix

`data.environment.config.tos.prefix`

Object storage directory prefix saved in the session environment snapshot.

**description** `string`  |  Environment description

`data.environment.description`

Environment description, used to describe the environment's purpose, network policy, dependency packages, and other configurations.

**metadata** `object`  |  Custom metadata

`data.environment.metadata`

User\-defined metadata.

**overridden_fields** `string[]`  |  Overridden parameters

`data.environment.overridden_fields`

The list of `config` subparameter names actually overridden by the current session relative to the base environment. Returned only when *type* is `environment_with_overrides` and overridden parameters exist.

**scope** `string`  |  Scope

`data.environment.scope`

Environment scope: `organization` indicates that it is shared within the organization, and `account` indicates that it is visible within the account.

**resources** `object[]`  |  Resource list

`data.resources`

The list of resources currently mounted to the session's runtime environment.

File resource `object`

A file uploaded through the Files API and mounted to the session.

**created_at** `string`  |  Mount time

`data.resources.created_at`

The time when the resource was mounted to the session, in RFC 3339 format.

**id** `string`  |  Resource ID

`data.resources.id`

Session resource ID.

**type** `string`  |  Resource type.

`data.resources.type`

Resource type, fixed as `file`.

**updated_at** `string`  |  Update time

`data.resources.updated_at`

The time when the session resource information was last updated, in RFC 3339 format.

**access** `string`  |  Access permissions

`data.resources.access`

File access permissions, fixed as `read_only`.

**file_id** `string`  |  File ID

`data.resources.file_id`

The currently available file ID.

**mount_path** `string`  |  Mount path

`data.resources.mount_path`

The full mount path of the file in the session runtime environment.

Memory store resource `object`

A memory store mounted to the session.

**created_at** `string`  |  Mount time

`data.resources.created_at`

The time when the resource was mounted to the session, in RFC 3339 format.

**id** `string`  |  Resource ID

`data.resources.id`

Session resource ID.

**type** `string`  |  Resource type.

`data.resources.type`

Resource type, fixed as `memory_store`.

**updated_at** `string`  |  Update time

`data.resources.updated_at`

The time when the session resource information was last updated, in RFC 3339 format.

**access** `string`  |  Access permissions

`data.resources.access`

The session's access permissions for the memory store.

* `read_only`: The agent can only read the memory store.

**memory_store_id** `string`  |  Memory store ID

`data.resources.memory_store_id`

The ID of the memory store mounted to the session.

TOS resource `object`

A TOS object directory mounted to the session.

**created_at** `string`  |  Mount time

`data.resources.created_at`

The time when the resource was mounted to the session, in RFC 3339 format.

**id** `string`  |  Resource ID

`data.resources.id`

Session resource ID.

**type** `string`  |  Resource type.

`data.resources.type`

Resource type, fixed as `tos`.

**updated_at** `string`  |  Update time

`data.resources.updated_at`

The time when the session resource information was last updated, in RFC 3339 format.

**access** `string`  |  Access permissions

`data.resources.access`

The session's access permissions for the TOS directory. Options: `read_only`, `read_write`.

**mount_path** `string`  |  Mount path

`data.resources.mount_path`

The mount path of the TOS directory in the session runtime environment. If no mount path was specified when the session was created, this parameter is not returned.

**tos_bucket** `string`  |  TOS bucket

`data.resources.tos_bucket`

The bucket name where the TOS mount source is located.

**tos_key** `string`  |  TOS directory prefix

`data.resources.tos_key`

The object prefix of the mounted directory in TOS.

**tos_region** `string`  |  TOS region

`data.resources.tos_region`

The ID of the region where the TOS mount source is located.

**scene** `string`  |  Session scene

`data.scene`

Session scene marker.

* `default`: regular session.

* `dreams`: a session used for Dream memory organization tasks.

**tags** `object[]`  |  Tag list

`data.tags`

A list of session tags. Each item contains `key` and an optional `value`.

**key** `string`  |  Tag key

`data.tags.key`

Tag key, used for classification and retrieval.

**value** `string`  |  Tag value

`data.tags.value`

Used to record the specific content for this tag category.

**title** `string`  |  Session title

`data.title`

Session title, used to help users identify the session in the session list.

**vault_ids** `string[] / null`  |  Vault ID list

`data.vault_ids`

List of vault IDs bound to the session. The agent can access the credentials in these vaults.

**next_page** `string`  |  Next page identifier

The next page identifier returned by the server. The client does not need to parse it. To continue paging, pass this value as is as the `page` parameter in the next request; if this field is not returned, it indicates that the last page has been reached.
