# Tool call events

Streaming events related to supported tool calls.

## Tool call (general)

### response.agent_tool_call.in_progress

> Event triggered when an agent tool call is in progress.

**item_id** `string`  |  Call item ID

`response.agent_tool_call.in_progress.item_id`

The ID of the agent tool call item being processed.

**name** `string`  |  Tool name

`response.agent_tool_call.in_progress.name`

The name of the agent tool.

**output_index** `integer`  |  Output item index

`response.agent_tool_call.in_progress.output_index`

The index of the output item it belongs to.

**sequence_number** `integer`  |  Sequence number

`response.agent_tool_call.in_progress.sequence_number`

Sequence number of the event.

**type** `string`  |  Event type

`response.agent_tool_call.in_progress.type`

The event type, fixed as `response.agent_tool_call.in_progress`.

### response.agent_tool_call.completed

> An event triggered when an agent tool call is completed.

**item_id** `string`  |  Call item ID

`response.agent_tool_call.completed.item_id`

The ID of the completed agent tool call item.

**name** `string`  |  Tool name

`response.agent_tool_call.completed.name`

The name of the agent tool.

**output_index** `integer`  |  Output item index

`response.agent_tool_call.completed.output_index`

The index of the output item it belongs to.

**sequence_number** `integer`  |  Sequence number

`response.agent_tool_call.completed.sequence_number`

Sequence number of the event.

**type** `string`  |  Event type

`response.agent_tool_call.completed.type`

The event type, fixed as `response.agent_tool_call.completed`.

### response.function_call_arguments.delta

> Triggered when a fragment of function call arguments exists.

**item_id** `string`  |  Output item ID

`response.function_call_arguments.delta.item_id`

The unique ID of the output item it belongs to.

**output_index** `integer`  |  Output item index

`response.function_call_arguments.delta.output_index`

The list index of the output item it belongs to.

**sequence_number** `integer`  |  Sequence number

`response.function_call_arguments.delta.sequence_number`

Sequence number of the event.

**type** `string`  |  Event type

`response.function_call_arguments.delta.type`

The event type, fixed as `response.function_call_arguments.delta`.

**delta** `string`  |  Argument delta

`response.function_call_arguments.delta.delta`

The newly added delta fragment of function call arguments this time.

### response.function_call_arguments.done

> Triggered when function call arguments are completed.

**item_id** `string`  |  Output item ID

`response.function_call_arguments.done.item_id`

The unique ID of the output item it belongs to.

**output_index** `integer`  |  Output item index

`response.function_call_arguments.done.output_index`

The list index of the output item it belongs to.

**sequence_number** `integer`  |  Sequence number

`response.function_call_arguments.done.sequence_number`

Sequence number of the event.

**type** `string`  |  Event type

`response.function_call_arguments.done.type`

The event type, fixed as `response.function_call_arguments.done`.

**arguments** `string`  |  Function arguments

`response.function_call_arguments.done.arguments`

Complete parameters of the function call.

## Tool call (MCP)

### response.mcp_approval_request

> Event triggered when an MCP tool call requires user approval.

**function_mcp_approval_request** `object`  |  Approval request

`response.mcp_approval_request.function_mcp_approval_request`

The MCP tool call request that requires user approval.

**arguments** `string`  |  Call arguments

`response.mcp_approval_request.function_mcp_approval_request.arguments`

The JSON string of the tool call parameters.

**name** `string`  |  Tool name

`response.mcp_approval_request.function_mcp_approval_request.name`

Name of the tool to be called.

**server_label** `string`  |  MCP server label

`response.mcp_approval_request.function_mcp_approval_request.server_label`

The label of the MCP server that initiated the request.

**type** `string`  |  Item type

`response.mcp_approval_request.function_mcp_approval_request.type`

The item's type identifier, used to distinguish different types of output items.

Value is fixed to `mcp_approval_request`.

**id** `string`  |  Request ID

`response.mcp_approval_request.function_mcp_approval_request.id`

Unique identifier of the approval request.

**output_index** `integer`  |  Output item index

`response.mcp_approval_request.output_index`

The index of the output item to which the approval request belongs.

**sequence_number** `integer`  |  Sequence number

`response.mcp_approval_request.sequence_number`

Sequence number of the event.

**type** `string`  |  Event type

`response.mcp_approval_request.type`

The event type, fixed as `response.mcp_approval_request`.

### response.mcp_call.in_progress

> Event triggered when an MCP tool call is in progress.

**item_id** `string`  |  Call item ID

`response.mcp_call.in_progress.item_id`

The unique identifier of the MCP tool call item being processed.

**output_index** `integer`  |  Output item index

`response.mcp_call.in_progress.output_index`

The index in the output item array where the MCP call is located.

**sequence_number** `integer`  |  Sequence number

`response.mcp_call.in_progress.sequence_number`

Sequence number of the event.

**type** `string`  |  Event type

`response.mcp_call.in_progress.type`

The event type, fixed as `response.mcp_call.in_progress`.

### response.mcp_call.completed

> Triggered when the MCP tool call is completed.

**item_id** `string`  |  Call item ID

`response.mcp_call.completed.item_id`

ID of the completed MCP tool call item.

**output_index** `integer`  |  Output item index

`response.mcp_call.completed.output_index`

Index of the completed output item.

**sequence_number** `integer`  |  Sequence number

`response.mcp_call.completed.sequence_number`

Sequence number of the event.

**type** `string`  |  Event type

`response.mcp_call.completed.type`

The event type, fixed as `response.mcp_call.completed`.

### response.mcp_call.failed

> An event triggered when an MCP tool call fails.

**item_id** `string`  |  Call item ID

`response.mcp_call.failed.item_id`

The ID of the failed MCP tool call item.

**output_index** `integer`  |  Output item index

`response.mcp_call.failed.output_index`

The index of the failed output item.

**sequence_number** `integer`  |  Sequence number

`response.mcp_call.failed.sequence_number`

Sequence number of the event.

**type** `string`  |  Event type

`response.mcp_call.failed.type`

The event type, fixed as `response.mcp_call.failed`.

### response.mcp_call_arguments.delta

> Triggered when an MCP tool call argument fragment is added.

**delta** `string`  |  Argument delta

`response.mcp_call_arguments.delta.delta`

The newly added JSON string fragment of the MCP tool call arguments.

**item_id** `string`  |  Call item ID

`response.mcp_call_arguments.delta.item_id`

The unique identifier of the MCP tool call item being processed.

**output_index** `integer`  |  Output item index

`response.mcp_call_arguments.delta.output_index`

The index in the output item array where the MCP call is located.

**sequence_number** `integer`  |  Sequence number

`response.mcp_call_arguments.delta.sequence_number`

Sequence number of the event.

**type** `string`  |  Event type

`response.mcp_call_arguments.delta.type`

The event type, fixed as `response.mcp_call_arguments.delta`.

### response.mcp_call_arguments.done

> Event triggered when MCP tool call arguments are completed.

**arguments** `string`  |  Call arguments

`response.mcp_call_arguments.done.arguments`

Complete arguments JSON string for the MCP tool call.

**item_id** `string`  |  Call item ID

`response.mcp_call_arguments.done.item_id`

The unique identifier of the MCP tool call item being processed.

**output_index** `integer`  |  Output item index

`response.mcp_call_arguments.done.output_index`

The index in the output item array where the MCP call is located.

**sequence_number** `integer`  |  Sequence number

`response.mcp_call_arguments.done.sequence_number`

Sequence number of the event.

**type** `string`  |  Event type

`response.mcp_call_arguments.done.type`

The event type, fixed as `response.mcp_call_arguments.done`.

### response.mcp_list_tools.in_progress

> Event triggered when an MCP tool list query is in progress.

**item_id** `string`  |  Call item ID

`response.mcp_list_tools.in_progress.item_id`

ID of the MCP tool list item being processed.

**output_index** `integer`  |  Output item index

`response.mcp_list_tools.in_progress.output_index`

Index of the output item that is being processed.

**sequence_number** `integer`  |  Sequence number

`response.mcp_list_tools.in_progress.sequence_number`

Sequence number of the event.

**type** `string`  |  Event type

`response.mcp_list_tools.in_progress.type`

The event type, fixed as `response.mcp_list_tools.in_progress`.

### response.mcp_list_tools.completed

> Triggered when the MCP tool list query is completed.

**item_id** `string`  |  Call item ID

`response.mcp_list_tools.completed.item_id`

ID of the MCP tool list item that produced this output.

**output_index** `integer`  |  Output item index

`response.mcp_list_tools.completed.output_index`

Index of the processed output item.

**sequence_number** `integer`  |  Sequence number

`response.mcp_list_tools.completed.sequence_number`

Sequence number of the event.

**type** `string`  |  Event type

`response.mcp_list_tools.completed.type`

The event type, fixed as `response.mcp_list_tools.completed`.

### response.mcp_list_tools.failed

> Triggered when an MCP tool list query fails.

**item_id** `string`  |  Call item ID

`response.mcp_list_tools.failed.item_id`

The ID of the failed MCP tool list item.

**output_index** `integer`  |  Output item index

`response.mcp_list_tools.failed.output_index`

The index of the failed output item.

**sequence_number** `integer`  |  Sequence number

`response.mcp_list_tools.failed.sequence_number`

Sequence number of the event.

**type** `string`  |  Event type

`response.mcp_list_tools.failed.type`

The event type, fixed as `response.mcp_list_tools.failed`.
