# Archive agent

`POST https://ark.ap-southeast.bytepluses.com/api/v3/agents/{agent_id}/archive`

When an agent is no longer used to create new sessions but its configuration and historical versions still need to be retained for querying, call this API to archive it.

After the agent is archived, it can no longer be updated or used to create new sessions, but its details and historical versions can still be queried. Existing sessions are not affected and continue to use the configuration snapshot saved when they were created.

This API supports idempotent calls; repeated archiving does not change the initial `archived_at`.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**agent_id** `string` `Required`  |  Agent ID

The ID of the agent to archive, starting with `agent-`. You can obtain this value from the create agent response or the agent list.

## Response parameters

**created_at** `string`  |  Creation time

Time when the agent was created, in UTC in RFC 3339 format.

**id** `string`  |  Agent ID

Unique identifier of the agent, starting with `agent-`. This value is required when updating or deleting the agent, and when creating a session.

**name** `string`  |  Agent name

Name used to identify and manage the agent.

**type** `string`  |  Object type

Type of the returned object, indicating that the object is an agent.

Value is fixed to `agent`.

**updated_at** `string`  |  Update time

Time when the agent was last updated, in UTC in RFC 3339 format.

**version** `integer`  |  Version number

Current version number of the agent configuration.

**archived_at** `string`  |  Archive time

Time when the agent was first archived, in UTC in RFC 3339 format. This parameter is returned after the archive API succeeds and remains unchanged for repeated archiving.

**base_agent** `string`  |  Base agent type

Base agent type specified when creating an agent.

* `ark_agent_preview`: a general agent.

**context_management** `object[]`  |  Context management

Context management policy for the agent version.

* If no custom policy has been saved, or if the saved policy does not include `compact_threshold`, the response displays the platform's current default compaction policy or default threshold; the current default threshold is `192000` tokens.

* The default display value in the response is not written back to the agent version. An explicitly saved `compact_threshold` takes precedence in the response.

**type** `string`  |  Policy type

`context_management.type`

Policy type, fixed as `compaction`.

**compact_threshold** `integer`  |  Compaction threshold

`context_management.compact_threshold`

The automatic compaction trigger threshold displayed in the query response, measured in tokens. If no value is explicitly saved, the public API response fills in the platform's current default value, which is `192000`; this displayed value is not written back to the agent version or session snapshot.

**instructions** `string`  |  Summary instructions

`context_management.instructions`

User\-configured compaction summary instructions.

**description** `string`  |  Agent description

The agent's purpose, capability scope, and use cases.

**display_name** `string`  |  Display name

The display name of the agent.

**mcp_servers** `object[]`  |  MCP service list

List of MCP services configured for the agent.

**name** `string`  |  MCP service name.

`mcp_servers.name`

The name used to identify the MCP service.

**type** `string`  |  Access method

`mcp_servers.type`

The access method for the MCP service.

Value is fixed to `url`.

**url** `string`  |  Service address

`mcp_servers.url`

The access address of the MCP service.

**metadata** `object`  |  Custom metadata

Custom key\-value metadata of the agent.

**model** `object`  |  Model configuration

Model configuration used by the agent.

**id** `string`  |  Model or endpoint ID

`model.id`

The model ID or inference endpoint ID called by the agent. For example, model ID `dola-seed-2-1-turbo-260628`, or inference endpoint ID `ep-20260907123456-abcd1`.

**reasoning_effort** `string`  |  Reasoning effort level

`model.reasoning_effort`

The reasoning effort tier used by the model during deep reasoning. Options: `none`, `minimal`, `low`, `medium`, `high`, `xhigh`, `max`.

**service_tier** `string`  |  Inference service tier

`model.service_tier`

The inference service tier used for model calls. Options: `auto`, `default`, `fast`.

**speed** `string`  |  Inference speed tier

`model.speed`

The inference speed tier used by the model. Options: `standard`, `fast`.

**thinking** `string`  |  Deep reasoning

`model.thinking`

The model's deep reasoning mode.`auto` indicates that the model decides whether to enable deep reasoning. Options: `enabled` / `disabled` / `auto`.

**multiagent** `object`  |  Multi Agent configuration

Collaboration configuration between the agent and sub\-agents.

**type** `string`  |  Collaboration mode

`multiagent.type`

Multi\-agent collaboration mode:

* **`multiagent_20261001`**: Uses `subagents` to configure predefined agents, self (`type` is `self`), and inline agents.

* **`coordinator`**: Use `agents` to configure predefined agents or self (`type` is `self`).

**agents** `object[]`  |  Sub\-agent list

`multiagent.agents`

When `type` is `coordinator`, returns the list of callable predefined agents or self. For other collaboration modes, this parameter is not returned or an empty array is returned.

**type** `string`  |  Sub\-agent source

`multiagent.agents.type`

The source of the sub\-agent.`agent` references an existing agent; `self` reuses the current agent configuration as a sub\-agent for collaboration.

**display_name** `string`  |  Member agent display name

`multiagent.agents.display_name`

The display name of the member agent. This parameter is not returned if the referenced agent does not exist.

**id** `string`  |  Agent ID

`multiagent.agents.id`

The ID of the referenced agent.

**version** `integer`  |  Agent version

`multiagent.agents.version`

The version number of the referenced agent.

**subagents** `object`  |  Subagent configuration

`multiagent.subagents`

When `type` is `multiagent_20261001`, returns the subagent configuration, including rules for creating inline agents and callable predefined members.

**type** `string`  |  Subagent toggle

`multiagent.subagents.type`

Whether the current agent can create subagents. Options: `enabled`, `disabled`.

**inline_agents** `object`  |  Inline agent configuration

`multiagent.subagents.inline_agents`

Rules for the current agent to create inline agents, including whether inline agents can be created by name and system prompt.

**type** `string`  |  Inline agent toggle

`multiagent.subagents.inline_agents.type`

Whether the current agent can create inline agents by name and system prompt as needed for the task. Options: `enabled`, `disabled`.

**predefined_agents** `object[]`  |  Predefined members

`multiagent.subagents.predefined_agents`

List of predefined members callable by the current agent. The self member (`type` is `self`) indicates the current agent itself.

**type** `string`  |  Sub\-agent source

`multiagent.subagents.predefined_agents.type`

The source of the sub\-agent.`agent` references an existing agent; `self` reuses the current agent configuration as a sub\-agent for collaboration.

**display_name** `string`  |  Member agent display name

`multiagent.subagents.predefined_agents.display_name`

The display name of the member agent. This parameter is not returned if the referenced agent does not exist.

**id** `string`  |  Agent ID

`multiagent.subagents.predefined_agents.id`

The ID of the referenced agent.

**version** `integer`  |  Agent version

`multiagent.subagents.predefined_agents.version`

The version number of the referenced agent.

**skills** `object[]`  |  Skill list

List of skills attached to the agent.

**type** `string`  |  Source of the skill

`skills.type`

Source of the skill.

* `ark`: ModelArk preset skill.

* `custom`: a custom skill created by the current account through the Skills API.

**display_name** `string`  |  Display name of the skill

`skills.display_name`

Display name of the skill version currently used by the agent. Not returned if the corresponding version does not have a display name.

**skill_id** `string`  |  Skill ID

`skills.skill_id`

ID of the skill attached to the agent.

**version** `string`  |  Skill version

`skills.version`

Version number of the skill attached to the agent.

**system** `string`  |  System prompt

The system prompt you configured and saved for the agent, used to define the agent's role and behavioral requirements.

**tags** `object[]`  |  Tag list

List of tags for the agent.

**key** `string`  |  Tag key

`tags.key`

Tag key, used for classification and retrieval.

**value** `string`  |  Tag value

`tags.value`

The value of the tag.

**tools** `object[]`  |  Tool list

The tool list you configured for the agent.

**type** `string`  |  Tool type

`tools.type`

Tool type.

* The server may return legacy values that are compatible with earlier basic toolsets.

* `agent_toolset_20260916`: Use ModelArk's built\-in tools, including `bash`, `edit`, `read`, `write`, `glob`, `grep`, `web_fetch`, and `web_search`.

* `mcp_toolset`: Use tools provided by the specified MCP service.

* `custom`: Use a custom tool executed by the client.

**configs** `object[]`  |  Tool configuration list

`tools.configs`

Set the enabled status and permission policy for tools by tool name.

**name** `string`  |  Tool name

`tools.configs.name`

The tool name corresponding to the tool configuration.

**enabled** `boolean`  |  Whether enabled

`tools.configs.enabled`

Whether to enable this tool.

**permission_policy** `object`  |  Permission policy

`tools.configs.permission_policy`

The permission policy used by this tool.

**type** `string`  |  Policy type

`tools.configs.permission_policy.type`

Tool call permission policy.

* `always_allow`: always allows calls.

* `always_ask`: Confirmation is required before each use.

**default_config** `object`  |  Default tool configuration

`tools.default_config`

The default enabled status and permission policy used for tools that are not individually configured in `configs`.

**enabled** `boolean`  |  Whether the tool is enabled by default

`tools.default_config.enabled`

Whether to enable by default tools that are not separately configured in `configs`.

* `true`: enabled by default.

* `false`: disabled by default.

**permission_policy** `object`  |  Default permission policy

`tools.default_config.permission_policy`

The default permission policy used by tools that are not separately configured in `configs`.

**type** `string`  |  Policy type

`tools.default_config.permission_policy.type`

Tool call permission policy.

* `always_allow`: always allows calls.

* `always_ask`: Confirmation is required before each use.

**description** `string`  |  Custom tool description

`tools.description`

Describes the function and use cases of the custom tool, so the model can determine when to call the tool.

**input_schema** `object`  |  Input parameter definition

`tools.input_schema`

Returns the custom tool input parameter JSON Schema saved in the agent.

**properties** `object`  |  Parameter property definition

`tools.input_schema.properties`

Returns the JSON Schema property mapping saved in the agent. Each key is a parameter name, and each value is the JSON Schema child object for the corresponding parameter.

**required** `string[]`  |  List of required parameter names

`tools.input_schema.required`

List of parameter names that must be provided when returning the called custom tool.

**type** `string`  |  Schema type

`tools.input_schema.type`

The returned top\-level type of the JSON Schema. It is fixed as `object`.

**mcp_server_name** `string`  |  MCP service name.

`tools.mcp_server_name`

The MCP service name to which the MCP tool belongs.

**name** `string`  |  Custom tool name

`tools.name`

The name of the custom tool.
