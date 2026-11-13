# Create agent

`POST https://ark.ap-southeast.bytepluses.com/api/v3/agents`

Create a reusable, versioned managed agent. After creation succeeds, the agent ID is returned and can be used to create a session. A single account can create up to 1000 agents.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Body parameters

**model** `object` `Required`  |  Model configuration

Model configuration used by the agent.

* The supported range and default behavior of each runtime parameter depend on the selected model.

* If a value not supported by the model is passed, 400 `InvalidParameter` will be returned by the server.

* **Model parameter support**: For details, see [List of supported model parameters](model-parameter-support.md).

> **Note**

> Select the model corresponding to the specified model ID or inference endpoint ID from the models available in the Managed Agents console, or see [Parameters supported by models](model-parameter-support.md). When you use an inference endpoint ID, the endpoint must belong to the current project. Otherwise, the server returns 400 `InvalidParameter`.

**id** `string` `Required`  |  Model or endpoint ID

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

The inference speed tier used by the model.

**thinking** `string`  |  Deep reasoning

`model.thinking`

The model's deep reasoning mode.`auto` indicates that the model decides whether to enable deep reasoning. Options: `enabled` / `disabled` / `auto`.

**name** `string` `Required`  |  Agent name

Agent name, used to identify and manage the created agent. The length is 1 to 256 characters.

**base_agent** `string` `Default ark_agent_preview`  |  Base agent type

Base agent type.

* `ark_agent_preview`: Create a general agent.

This parameter cannot be modified after creation.

**context_management** `object[]`  |  Context management

Configures the agent's custom context management policy. You can pass at most one item.

If omitted, set to `null`, or set to `[]`, no custom policy is saved. The runtime uses the default behavior, and this does not mean automatic compression is disabled.

**type** `string` `Required`  |  Policy type

`context_management.type`

Policy type, fixed as `compaction`.

**compact_threshold** `integer`  |  Compaction threshold

`context_management.compact_threshold`

The user\-configured trigger threshold for automatic compaction, measured in tokens. If omitted or set to `null`, no custom threshold is saved and the runtime uses the default behavior.

**Min**: `50000`

**instructions** `string`  |  Summary instructions

`context_management.instructions`

Custom instructions used when generating compaction summaries. If omitted, set to `null`, or set to an empty string, no custom instructions are saved; a non\-empty value cannot contain only whitespace characters.

**description** `string`  |  Agent description

Agent description, which describes the agent's purpose and capabilities. Up to 2,048 characters.

**display_name** `string`  |  Display name

Agent display name, up to 256 characters. If omitted or an empty string is passed, this parameter is not returned in the response.

**mcp_servers** `object[]`  |  MCP service configuration

List of MCP services configured for use by the agent. Up to 20 items.

**type** `string` `Required`  |  Access method

`mcp_servers.type`

The access method for the MCP service.

Value is fixed to `url`.

**url** `string` `Required`  |  Service address

`mcp_servers.url`

The access address of the MCP service. Duplicate MCP service addresses cannot be configured within the same agent.

**name** `string`  |  MCP service name.

`mcp_servers.name`

MCP service name, up to 255 characters long. It is used to identify the service in the current agent. When configuring an MCP toolset, `tools[].mcp_server_name` must be set to this name.

* If the name is omitted or does not comply with the naming rules, the server generates a name based on `url`.

* The server converts names that comply with the naming rules to lowercase.

* If multiple names conflict, the server adds numeric suffixes to subsequent names.

**metadata** `object`  |  Custom metadata

User\-defined metadata in key\-value pairs. Up to 16 keys are supported. The key length cannot exceed 64 characters, and the value length cannot exceed 512 characters.

**multiagent** `object`  |  Multi Agent configuration

Multi\-agent collaboration configuration. Up to 20 sub\-agents are supported.

**type** `string` `Required`  |  Collaboration mode

`multiagent.type`

Multi\-agent collaboration mode:

* `multiagent_20261001`: Uses `subagents` to configure predefined agents, self (`type` is `self`), and inline agents.

* `coordinator`: Uses `agents` to configure predefined agents or self (`type` is `self`). Predefined agents are treated as leaf agents.

**agents** `object[]`  |  Sub\-agent list

`multiagent.agents`

`coordinator` uses this parameter to configure the sub\-agent list. It supports predefined agents and self, with up to 20 items.`multiagent_20261001` does not use this parameter. In this case, omit it or pass an empty array.

**type** `string` `Required`  |  Sub\-agent source

`multiagent.agents.type`

Sub\-agent source:

* `agent`: References an existing agent.

   * When used in `multiagent_20261001.predefined_agents`, the referenced agent must also use `multiagent_20261001` so that its member configuration can continue to be expanded within the session depth limit.

   * When a referenced agent uses `coordinator`, it is treated as a leaf member.

* `self`: Reuses the agent configuration that owns the current member list, but does not inherit its member list. This can be used for `multiagent_20261001.predefined_agents` and `coordinator.agents`.

**id** `string`  |  Agent ID

`multiagent.agents.id`

The ID of the referenced agent. When `type` is `agent`, this parameter is used to specify an existing agent that participates in collaboration and is required. When `type` is `self`, the current agent participates in collaboration using its own configuration, and this parameter is not specified.

**version** `integer`  |  Agent version

`multiagent.agents.version`

The version number of the referenced agent. If omitted, the latest version is used.

**subagents** `object`  |  Subagent configuration

`multiagent.subagents`

When `type` is `multiagent_20261001`, use this parameter to configure subagents. Omit this parameter when `type` is `coordinator`.

**type** `string` `Required`  |  Subagent toggle

`multiagent.subagents.type`

Whether the current agent is allowed to create subagents.`enabled` indicates allow, and `disabled` indicates disallow.

**inline_agents** `object`  |  Inline agent configuration

`multiagent.subagents.inline_agents`

Runtime inline agent configuration. If omitted, the server uses the current default policy.

**type** `string` `Required`  |  Inline agent toggle

`multiagent.subagents.inline_agents.type`

Whether inline agents can be created at runtime with a name and system instructions. Options: `enabled`, `disabled`.

**predefined_agents** `object[]`  |  Predefined members

`multiagent.subagents.predefined_agents`

List of predefined agent and self members, up to 20 items. Omitting this parameter or passing an empty array means no predefined members are configured.

**type** `string` `Required`  |  Sub\-agent source

`multiagent.subagents.predefined_agents.type`

Sub\-agent source:

* `agent`: References an existing agent.

   * When used in `multiagent_20261001.predefined_agents`, the referenced agent must also use `multiagent_20261001` so that its member configuration can continue to be expanded within the session depth limit.

   * When a referenced agent uses `coordinator`, it is treated as a leaf member.

* `self`: Reuses the agent configuration that owns the current member list, but does not inherit its member list. This can be used for `multiagent_20261001.predefined_agents` and `coordinator.agents`.

**id** `string`  |  Agent ID

`multiagent.subagents.predefined_agents.id`

The ID of the referenced agent. When `type` is `agent`, this parameter is used to specify an existing agent that participates in collaboration and is required. When `type` is `self`, the current agent participates in collaboration using its own configuration, and this parameter is not specified.

**version** `integer`  |  Agent version

`multiagent.subagents.predefined_agents.version`

The version number of the referenced agent. If omitted, the latest version is used.

**skills** `object[]`  |  Skill configuration

List of skills attached to the agent. Up to 50 skills are supported. If no skill version is specified, the latest version of the skill is used.

**skill_id** `string` `Required`  |  Skill ID

`skills.skill_id`

The ID of the skill to attach to the agent.

**type** `string` `Required`  |  Source of the skill

`skills.type`

Source of the skill.

* `ark`: ModelArk preset skill.

* `custom`: a custom skill created by the current account through the Skills API.

**version** `string`  |  Skill version

`skills.version`

The version number of the skill to be attached. If omitted, the latest version of the skill is used.

**system** `string`  |  System prompt

System prompt that defines the agent's role and behavior rules, up to 100,000 characters.

**tags** `object[]`  |  Tag list

List of user\-defined tags. Up to 50 items can be specified. Within the same request, tag keys must not be duplicated.

**key** `string` `Required`  |  Tag key

`tags.key`

Tag key, used for classification and retrieval. It must be 1 to 128 characters long and cannot start with `volc:` or `maas:`.

**value** `string`  |  Tag value

`tags.value`

The value corresponding to the tag. It can be up to 256 characters long.

**tools** `object[]`  |  Tool configuration

List of tools configured for use by the agent. Up to 128 tools are supported.

* The enabled status and permission policy of built\-in toolsets and MCP tools can be set through `configs` and `default_config`.

* MCP tools are associated with `mcp_servers[].name` through `mcp_server_name`.

* For custom tools, you must provide the name, function description, and input parameter definitions.

**type** `string` `Required`  |  Tool type

`tools.type`

Tool type.

* `agent_toolset_20260916`: Use ModelArk's built\-in tools, including `bash`, `edit`, `read`, `write`, `glob`, `grep`, `web_fetch`, and `web_search`.

* `mcp_toolset`: Use tools provided by the specified MCP service.

* `custom`: Use a custom tool executed by the client.

   Options: `agent_toolset_20260401`, `agent_toolset_20260701`, `agent_toolset_20260916`, `mcp_toolset`, `custom`.

**configs** `object[]`  |  Tool configuration list

`tools.configs`

Set the enabled status and permission policy for tools by tool name.

**name** `string` `Required`  |  Tool name

`tools.configs.name`

The tool name corresponding to the tool configuration.

**enabled** `boolean`  |  Whether enabled

`tools.configs.enabled`

Whether to enable this tool.

**permission_policy** `object`  |  Permission policy

`tools.configs.permission_policy`

The permission policy used by this tool.

**type** `string` `Required`  |  Policy type

`tools.configs.permission_policy.type`

Tool call permission policy.

* `always_allow`: always allows calls.

* `always_ask`: Confirmation is required before each use.

**default_config** `object`  |  Default tool configuration

`tools.default_config`

The default enabled status and permission policy used for tools that are not individually configured in `configs`.

**enabled** `boolean`  |  Whether enabled

`tools.default_config.enabled`

Whether to enable by default tools that are not separately configured in `configs`.

* `true`: enabled by default.

* `false`: disabled by default.

**permission_policy** `object`  |  Permission policy

`tools.default_config.permission_policy`

The default permission policy used by tools that are not separately configured in `configs`.

**type** `string` `Required`  |  Policy type

`tools.default_config.permission_policy.type`

Tool call permission policy.

* `always_allow`: always allows calls.

* `always_ask`: Confirmation is required before each use.

**description** `string`  |  Custom tool description

`tools.description`

Describes the function and use cases of the custom tool, so the model can determine when to call the tool.

> **Tip**

> This parameter is required when `type` is `custom` and must be 1–10000 characters in length.

**input_schema** `object`  |  Input parameter definition

`tools.input_schema`

Defines the input parameters and constraints for the custom tool.

> **Note**

* > When creating an agent, the server verifies that `input_schema.type` is `object` and that `input_schema.properties` is a JSON object, but does not check each property definition.

* > When running a session, the server checks whether the complete `input_schema` is a valid JSON Schema. Successfully creating an agent does not necessarily mean that the schema can run normally.

* > The tool call parameters generated by the model are only validated to check whether they are valid JSON. Before running a custom tool, your application should validate the parameters again according to `input_schema`.

**properties** `object`  |  Parameter property definition

`tools.input_schema.properties`

JSON Schema property mapping for custom tool input parameters. The key is the parameter name, and the value is the JSON Schema subobject for the corresponding parameter. Ensure that each property definition conforms to JSON Schema.

Common keywords include:

* `type`: the parameter type, such as `string`, `number`, `integer`, `boolean`, `array`, or `object`.

* `description`: a description of the parameter's purpose, for the model to understand and generate parameters.

* `enum`: the values allowed for the parameter.

* `items`: the JSON Schema for array elements.

* **Object structure keywords**: used to declare an object's child parameters and required parameters, including `properties` and `required`.

* **Numeric range keywords**: used to declare the value range of numeric parameters, including `minimum` and `maximum`.

Example:

```JSON
{ "city": { "type": "string", "description": "City name" }, "hours": { "type": "integer", "description": "Forecast duration, in hours", "minimum": 1, "maximum": 72 } }
```

**required** `string[]`  |  List of required parameter names

`tools.input_schema.required`

List of required parameter names in custom tool input. Each name in the list must match a parameter name defined in `properties`.

**type** `string` `Default object`  |  Schema type

`tools.input_schema.type`

Specifies the top\-level type of the JSON Schema. It is fixed as `object`.

**mcp_server_name** `string`  |  MCP service name.

`tools.mcp_server_name`

The MCP service name to which the MCP tool belongs.

**name** `string`  |  Custom tool name

`tools.name`

The name of the custom tool.

> **Tip**

> This parameter is required when `type` is `custom`. It must be 1–128 characters in length and can contain only letters, digits, underscores, and hyphens, and custom tool names within the same agent must be unique.

## Response parameters

**created_at** `string`  |  Creation time

Creation time of the agent in ISO 8601 format.

**id** `string`  |  Agent ID

Unique identifier of the agent, starting with `agent-`. This value is required when querying, updating, or deleting the agent, and when creating a session.

**name** `string`  |  Agent name

Name used to identify and manage the agent.

**type** `string`  |  Object type

Type of the returned object, indicating that the object is an agent.

Value is fixed to `agent`.

**updated_at** `string`  |  Update time

Latest update time of the agent in ISO 8601 format.

**version** `integer`  |  Version number

Version number of the agent created this time.

**archived_at** `string`  |  Archive time

Time when the agent was first archived, in UTC in RFC 3339 format. This parameter is not returned when the agent is not archived.

**base_agent** `string`  |  Base agent type

Base agent type used by the agent.

Options: `ark_agent_preview`.

**context_management** `object[]`  |  Context management

Context management policy for the agent version. When you create, update, or query an agent, if no custom policy has been saved or the saved policy does not include `compact_threshold`, the response shows the platform's current default compression policy or default threshold. The current default threshold is `192000` tokens.

The default display value in the response is not written back to the agent version. An explicitly saved `compact_threshold` takes precedence in the response.

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

The inference speed tier used by the model.

**thinking** `string`  |  Deep reasoning

`model.thinking`

The model's deep reasoning mode.`auto` indicates that the model decides whether to enable deep reasoning. Options: `enabled` / `disabled` / `auto`.

**multiagent** `object`  |  Multi Agent configuration

Collaboration configuration between the agent and sub\-agents.

**type** `string`  |  Collaboration mode

`multiagent.type`

Multi\-agent collaboration mode:

* `multiagent_20261001`: Uses `subagents` to configure predefined agents, self (`type` is `self`), and inline agents.

* `coordinator`: Use `agents` to configure predefined agents or self (`type` is `self`).

**agents** `object[]`  |  Sub\-agent list

`multiagent.agents`

When `type` is `coordinator`, the subagent list is returned. When `type` is `multiagent_20261001`, an empty array is returned or this parameter is omitted.

**type** `string`  |  Sub\-agent source

`multiagent.agents.type`

The source of the sub\-agent.`agent` references an existing agent. `self` reuses the agent configuration that owns the current member list, but does not inherit its member list. Self can be used for `multiagent_20261001.predefined_agents` and `coordinator.agents`. When using self, do not provide `id` or `version`.

**id** `string`  |  Agent ID

`multiagent.agents.id`

The ID of the referenced agent.

**version** `integer`  |  Agent version

`multiagent.agents.version`

The version number of the referenced agent.

**subagents** `object`  |  Subagent configuration

`multiagent.subagents`

When `type` is `multiagent_20261001`, the subagent configuration is returned.

**type** `string`  |  Subagent toggle

`multiagent.subagents.type`

Whether the current agent is allowed to create subagents. Options: `enabled`, `disabled`.

**inline_agents** `object`  |  Inline agent configuration

`multiagent.subagents.inline_agents`

Runtime inline agent configuration.

**type** `string`  |  Inline agent toggle

`multiagent.subagents.inline_agents.type`

Whether inline agents can be created at runtime with a name and system instructions. Options: `enabled`, `disabled`.

**predefined_agents** `object[]`  |  Predefined members

`multiagent.subagents.predefined_agents`

List of predefined agents and self.

**type** `string`  |  Sub\-agent source

`multiagent.subagents.predefined_agents.type`

The source of the sub\-agent.`agent` references an existing agent. `self` reuses the agent configuration that owns the current member list, but does not inherit its member list. Self can be used for `multiagent_20261001.predefined_agents` and `coordinator.agents`. When using self, do not provide `id` or `version`.

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

Tool type. The meanings of the values are as follows:

* `agent_toolset_20260916`: Use ModelArk's built\-in tools, including `bash`, `edit`, `read`, `write`, `glob`, `grep`, `web_fetch`, and `web_search`.

* `mcp_toolset`: Use tools provided by the specified MCP service.

* `custom`: Use a custom tool executed by the client.

   Options: `agent_toolset_20260401`, `agent_toolset_20260701`, `agent_toolset_20260916`, `mcp_toolset`, `custom`.

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

**enabled** `boolean`  |  Whether enabled

`tools.default_config.enabled`

Whether to enable by default tools that are not separately configured in `configs`.

* `true`: enabled by default.

* `false`: disabled by default.

**permission_policy** `object`  |  Permission policy

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
