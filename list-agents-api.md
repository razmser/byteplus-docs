# List agents

`GET https://ark.ap-southeast.bytepluses.com/api/v3/agents`

Query agents under the current account by page. Supports filtering by archive status, creation time, name, and display name. Results are sorted by creation time from newest to oldest.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Query parameters

> Passed as URL query string.

**created_at_gte** `string`  |  Creation time lower limit

Filter by creation time and return agents whose creation time is later than or equal to this value. It is in ISO 8601 format.

**created_at_lte** `string`  |  Creation time upper limit

Filter by creation time and return agents whose creation time is earlier than or equal to this value. It is in ISO 8601 format.

**display_name** `string`  |  Display name

Fuzzy match by agent display name. When passed together with other filter parameters, agents that meet all conditions are returned.

**include_archived** `boolean` `Default false`  |  Include archived agents

Whether to include archived agents in the results. If omitted or set to `false`, only unarchived agents are returned; if set to `true`, both archived and unarchived agents are returned.

**limit** `integer` `Default 20`  |  Items per page

Number of items returned per page.

**Range**: `[1, 100]`

**name** `string`  |  Agent name

Fuzzy match by agent name. When passed together with other filter parameters, agents that meet all conditions are returned.

**page** `string`  |  Next page identifier

Next page identifier.

* Omit this parameter for the first request.

* For subsequent requests, pass in the `next_page` value from the previous page's response as is.

* This value is not a page number. Do not parse or modify it.

* When paging continuously, keep other filter parameters unchanged.

## Response parameters

**data** `object[]`  |  Agent list

List of agents returned on the current page. If there is no match, an empty array `[]` is returned.

**created_at** `string`  |  Creation time

`data.created_at`

The time when the agent was created, in RFC 3339 format.

**id** `string`  |  Agent ID

`data.id`

Unique identifier of the agent, starting with `agent-`. This value is required when creating a session or managing an agent.

**name** `string`  |  Agent name

`data.name`

Name used to identify and manage the agent.

**type** `string`  |  Object type

`data.type`

Type of the returned object, indicating that the object is an agent.

Value is fixed to `agent`.

**updated_at** `string`  |  Update time

`data.updated_at`

The time when the agent was last updated, in RFC 3339 format.

**version** `integer`  |  Version number

`data.version`

Agent configuration version number.

**archived_at** `string`  |  Archive time

`data.archived_at`

Time when the agent was first archived, in UTC in RFC 3339 format. This parameter is not returned when the agent is not archived.

**base_agent** `string`  |  Base agent type

`data.base_agent`

Base agent type specified when creating an agent.

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

**display_name** `string`  |  Member agent display name

`data.multiagent.agents.display_name`

The display name of the member agent. This parameter is not returned if the referenced agent does not exist.

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

**display_name** `string`  |  Member agent display name

`data.multiagent.subagents.predefined_agents.display_name`

The display name of the member agent. This parameter is not returned if the referenced agent does not exist.

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

The system prompt you configured and saved for the agent, used to define the agent's role and behavioral requirements.

**tags** `object[]`  |  Tag list

`data.tags`

List of tags for the agent.

**key** `string`  |  Tag key

`data.tags.key`

Tag key, used for classification and retrieval.

**value** `string`  |  Tag value

`data.tags.value`

The value of the tag.

**tools** `object[]`  |  Tool list

`data.tools`

The tool list you configured for the agent.

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

**enabled** `boolean`  |  Whether the tool is enabled by default

`data.tools.default_config.enabled`

Whether to enable by default tools that are not separately configured in `configs`.

* `true`: enabled by default.

* `false`: disabled by default.

**permission_policy** `object`  |  Default permission policy

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

Returns the custom tool input parameter JSON Schema saved in the agent.

**properties** `object`  |  Parameter property definition

`data.tools.input_schema.properties`

Returns the JSON Schema property mapping saved in the agent. Each key is a parameter name, and each value is the JSON Schema child object for the corresponding parameter.

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
