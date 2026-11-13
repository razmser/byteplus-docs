# List agent versions

`GET https://ark.ap-southeast.bytepluses.com/api/v3/agents/{agent_id}/versions`

Queries historical versions of the specified agent with pagination. Results are sorted by version number in descending order. This API is only used for queries; to restore a historical configuration, read that version and call the update agent API.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**agent_id** `string` `Required`  |  Agent ID

The ID of the agent whose historical versions you want to query.

### Query parameters

> Passed as URL query string.

**limit** `integer` `Default 20`  |  Items per page

Number of items returned per page.

**Range**: `[1, 100]`

**page** `string`  |  Next page identifier

Next page identifier.

* Omit this parameter for the first request.

* For subsequent requests, pass in the `next_page` value from the previous page's response as is.

* Do not parse or modify this value.

## Response parameters

**data** `object[]`  |  Version list

The list of historical agent versions returned on the current page.

**created_at** `string`  |  Version generation time

`data.created_at`

The generation time of this agent version, in RFC 3339 format.

* **Version 1**: Corresponds to the agent creation time.

* **Subsequent versions**: Correspond to the time when the configuration was updated and a new version was generated.

**id** `string`  |  Agent ID

`data.id`

The unique identifier of the agent. It starts with `agent-` and can be used to create sessions. Historical versions of the same agent use the same ID and are distinguished by `version`.

**name** `string`  |  Agent name

`data.name`

Name used to identify and manage the agent.

**type** `string`  |  Object type

`data.type`

Type of the returned object, indicating that the object is an agent.

Value is fixed to `agent`.

**updated_at** `string`  |  Version generation time

`data.updated_at`

Same as `created_at`, indicating the generation time of this agent version. It is in RFC 3339 format.

**version** `integer`  |  Version number

`data.version`

Historical version number of the agent.

**archived_at** `string`  |  Archive time

`data.archived_at`

Time when the agent was first archived, in UTC in RFC 3339 format. This parameter is not returned when the agent is not archived.

**base_agent** `string`  |  Base agent type

`data.base_agent`

The base agent type saved in this historical version.

Options: `ark_agent_preview`.

**context_management** `object[]`  |  Context management

`data.context_management`

Context management policy for the agent version. When you create, update, or query an agent, if no custom policy has been saved or the saved policy does not include `compact_threshold`, the response shows the platform's current default compression policy or default threshold. The current default threshold is `192000` tokens.

The default display value in the response is not written back to the agent version. An explicitly saved `compact_threshold` takes precedence in the response.

**type** `string`  |  Policy type

`data.context_management.type`

Policy type, fixed as `compaction`.

**compact_threshold** `integer`  |  Compaction threshold

`data.context_management.compact_threshold`

The automatic compaction trigger threshold displayed in the query response, measured in tokens. If no value is explicitly saved, the public API response fills in the platform's current default value, which is `192000`; this displayed value is not written back to the agent version or session snapshot.

**instructions** `string`  |  Summary instructions

`data.context_management.instructions`

User\-configured compaction summary instructions.

**description** `string`  |  Agent description

`data.description`

The agent's purpose, capability scope, and use cases.

**display_name** `string`  |  Display name

`data.display_name`

The display name of the agent.

**mcp_servers** `object[]`  |  MCP service list

`data.mcp_servers`

List of MCP services configured for the agent.

**name** `string`  |  MCP service name.

`data.mcp_servers.name`

The name used to identify the MCP service.

**type** `string`  |  Access method

`data.mcp_servers.type`

The access method for the MCP service.

Value is fixed to `url`.

**url** `string`  |  Service address

`data.mcp_servers.url`

The access address of the MCP service.

**metadata** `object`  |  Custom metadata

`data.metadata`

Custom key\-value metadata of the agent.

**model** `object`  |  Model configuration

`data.model`

Model configuration used by the agent.

**id** `string`  |  Model or endpoint ID

`data.model.id`

The model ID or inference endpoint ID called by the agent. For example, model ID `dola-seed-2-1-turbo-260628`, or inference endpoint ID `ep-20260907123456-abcd1`.

**reasoning_effort** `string`  |  Reasoning effort level

`data.model.reasoning_effort`

The reasoning effort tier used by the model during deep reasoning. Options: `none`, `minimal`, `low`, `medium`, `high`, `xhigh`, `max`.

**service_tier** `string`  |  Inference service tier

`data.model.service_tier`

The inference service tier used for model calls. Options: `auto`, `default`, `fast`.

**speed** `string`  |  Inference speed tier

`data.model.speed`

The inference speed tier used by the model. Options: `standard`, `fast`.

**thinking** `string`  |  Deep reasoning

`data.model.thinking`

The model's deep reasoning mode.`auto` indicates that the model decides whether to enable deep reasoning. Options: `enabled` / `disabled` / `auto`.

**multiagent** `object`  |  Multi Agent configuration

`data.multiagent`

Collaboration configuration between the agent and sub\-agents.

**type** `string`  |  Collaboration mode

`data.multiagent.type`

Multi\-agent collaboration mode:

* `multiagent_20261001`: Uses `subagents` to configure predefined agents, self, and inline agents.

* `coordinator`: Uses `agents` to configure predefined agents or self.

**agents** `object[]`  |  Sub\-agent list

`data.multiagent.agents`

When `type` is `coordinator`, the subagent list is returned. When `type` is `multiagent_20261001`, an empty array is returned or this parameter is omitted.

**type** `string`  |  Sub\-agent source

`data.multiagent.agents.type`

The source of the sub\-agent.`agent` references an existing agent. `self` reuses the agent configuration that owns the current member list, but does not inherit its member list. Self can be used for `multiagent_20261001.predefined_agents` and `coordinator.agents`. When self is used, `id` and `version` are not returned.

**id** `string`  |  Agent ID

`data.multiagent.agents.id`

The ID of the referenced agent.

**version** `integer`  |  Agent version

`data.multiagent.agents.version`

The version number of the referenced agent.

**subagents** `object`  |  Subagent configuration

`data.multiagent.subagents`

When `type` is `multiagent_20261001`, the subagent configuration is returned.

**type** `string`  |  Subagent toggle

`data.multiagent.subagents.type`

Whether the current agent is allowed to create subagents. Options: `enabled`, `disabled`.

**inline_agents** `object`  |  Inline agent configuration

`data.multiagent.subagents.inline_agents`

Runtime inline agent configuration.

**type** `string`  |  Inline agent toggle

`data.multiagent.subagents.inline_agents.type`

Whether inline agents can be created at runtime with a name and system instructions. Options: `enabled`, `disabled`.

**predefined_agents** `object[]`  |  Predefined members

`data.multiagent.subagents.predefined_agents`

List of predefined agents and self.

**type** `string`  |  Sub\-agent source

`data.multiagent.subagents.predefined_agents.type`

The source of the sub\-agent.`agent` references an existing agent. `self` reuses the agent configuration that owns the current member list, but does not inherit its member list. Self can be used for `multiagent_20261001.predefined_agents` and `coordinator.agents`. When self is used, `id` and `version` are not returned.

**id** `string`  |  Agent ID

`data.multiagent.subagents.predefined_agents.id`

The ID of the referenced agent.

**version** `integer`  |  Agent version

`data.multiagent.subagents.predefined_agents.version`

The version number of the referenced agent.

**skills** `object[]`  |  Skill list

`data.skills`

List of skills attached to the agent.

**type** `string`  |  Source of the skill

`data.skills.type`

Source of the skill.

* `ark`: ModelArk preset skill.

* `custom`: a custom skill created by the current account through the Skills API.

**display_name** `string`  |  Display name of the skill

`data.skills.display_name`

Display name of the skill version currently used by the agent. Not returned if the corresponding version does not have a display name.

**skill_id** `string`  |  Skill ID

`data.skills.skill_id`

ID of the skill attached to the agent.

**version** `string`  |  Skill version

`data.skills.version`

Version number of the skill attached to the agent.

**system** `string`  |  System prompt

`data.system`

Your system prompt saved in this historical version, which is used to define the role and behavior requirements of this agent version.

**tools** `object[]`  |  Tool list

`data.tools`

The tool list you configured for the agent is saved in this historical version.

**type** `string`  |  Tool type

`data.tools.type`

Tool type.

* `agent_toolset_20260916`: Use ModelArk's built\-in tools, including `bash`, `edit`, `read`, `write`, `glob`, `grep`, `web_fetch`, and `web_search`.

* `mcp_toolset`: Use tools provided by the specified MCP service.

* `custom`: Use a custom tool executed by the client.

   Options: `agent_toolset_20260401`, `agent_toolset_20260701`, `agent_toolset_20260916`, `mcp_toolset`, `custom`.

**configs** `object[]`  |  Tool configuration list

`data.tools.configs`

Set the enabled status and permission policy for tools by tool name.

**name** `string`  |  Tool name

`data.tools.configs.name`

The tool name corresponding to the tool configuration.

**enabled** `boolean`  |  Whether enabled

`data.tools.configs.enabled`

Whether to enable this tool.

**permission_policy** `object`  |  Permission policy

`data.tools.configs.permission_policy`

The permission policy used by this tool.

**type** `string`  |  Policy type

`data.tools.configs.permission_policy.type`

Tool call permission policy.

* `always_allow`: always allows calls.

* `always_ask`: Confirmation is required before each use.

**default_config** `object`  |  Default tool configuration

`data.tools.default_config`

The default enabled status and permission policy used for tools that are not individually configured in `configs`.

**enabled** `boolean`  |  Whether enabled

`data.tools.default_config.enabled`

Whether to enable by default tools that are not separately configured in `configs`.

* `true`: enabled by default.

* `false`: disabled by default.

**permission_policy** `object`  |  Permission policy

`data.tools.default_config.permission_policy`

The default permission policy used by tools that are not separately configured in `configs`.

**type** `string`  |  Policy type

`data.tools.default_config.permission_policy.type`

Tool call permission policy.

* `always_allow`: always allows calls.

* `always_ask`: Confirmation is required before each use.

**description** `string`  |  Custom tool description

`data.tools.description`

Describes the function and use cases of the custom tool, so the model can determine when to call the tool.

**input_schema** `object`  |  Input parameter definition

`data.tools.input_schema`

Returns the custom tool input parameter JSON Schema saved in the agent version.

**properties** `object`  |  Parameter property definition

`data.tools.input_schema.properties`

Returns the JSON Schema property mapping saved in the agent version. Each key is a parameter name, and each value is the JSON Schema child object for the corresponding parameter.

**required** `string[]`  |  List of required parameter names

`data.tools.input_schema.required`

List of parameter names that must be provided when returning the called custom tool.

**type** `string`  |  Schema type

`data.tools.input_schema.type`

The returned top\-level type of the JSON Schema. It is fixed as `object`.

**mcp_server_name** `string`  |  MCP service name.

`data.tools.mcp_server_name`

The MCP service name to which the MCP tool belongs.

**name** `string`  |  Custom tool name

`data.tools.name`

The name of the custom tool.

**next_page** `string`  |  Next page identifier

Next page identifier. Use this value as the `page` parameter for the next request exactly as returned. This parameter is not returned when the last page has been reached.
