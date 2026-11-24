# List dreams

`GET https://ark.ap-southeast.bytepluses.com/api/v3/dreams`

Query dream tasks under the current account and project with pagination. Supports filtering by task status and input memory store, and returns results in ascending or descending order by creation time.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Query parameters

> Passed as URL query string.

**limit** `integer` `Default 20`  |  Items per page

The number of dreams returned per page. The value range is 1–100.

**Range**: `[1, 100]`

**order** `string` `Default desc`  |  Sort order

The sort order for the dream creation time.`asc` indicates ascending order, and `desc` indicates descending order.

**page** `string`  |  Next page identifier

Omit this parameter for the first request.

For subsequent requests, pass in the `next_page` value from the previous response as is.

Do not parse, generate, or modify this value; it must match the current account, project, status, input memory store filter, and sort order.

**source_memory_store_query** `string`  |  Input memory store filter

Filter dreams by input memory store. When a full ID is passed in, an exact match is performed; when a name is passed in, substring matching is performed, where `%`, `_`, and backslashes are all treated as ordinary characters.

**status** `string[]`  |  Status filter

Filter by dream status. You can pass this query parameter multiple times to match any specified status; if it is omitted, all statuses are returned. Options: `pending`, `running`, `completed`, `failed`, `canceled`.

## Response parameters

**data** `object[]`  |  Dream list

The list of dreams on the current page, returned in the creation\-time order specified by `order`.

**created_at** `string`  |  Creation time

`data.created_at`

The task creation time, in RFC 3339 format.

**id** `string`  |  Dream ID

`data.id`

The unique identifier of the dream task, in the format of a string that starts with `drm-`.

**inputs** `object[]`  |  Task input

`data.inputs`

The input snapshot saved when the task was created.

Source memory store input `object`

The source memory store input saved when the task was created.

**memory_store_id** `string`  |  Source memory store ID

`data.inputs.memory_store_id`

Source memory store ID.

**type** `string`  |  Input type

`data.inputs.type`

Input type, fixed as `memory_store`.

**memory_store_name** `string`  |  Input memory store name

`data.inputs.memory_store_name`

The current name of the input memory store. Omitted when the associated memory store has been deleted.

Historical session input `object`

The historical session input saved when the task was created.

**session_ids** `string[]`  |  Historical session IDs

`data.inputs.session_ids`

The list of historical session IDs saved when the task was created.

**type** `string`  |  Input type

`data.inputs.type`

Input type, fixed as `sessions`.

**model** `object`  |  Model information

`data.model`

Information about the model used to perform the memory organization task.

**id** `string`  |  Model ID

`data.model.id`

The model ID used to perform the memory organization task.

**outputs** `object[]`  |  Task output

`data.outputs`

The output memory store created by the dream. The access timing and content integrity are indicated by `status`.

**memory_store_id** `string`  |  Output memory store ID

`data.outputs.memory_store_id`

The output memory store ID created by the dream.

**type** `string`  |  Output type

`data.outputs.type`

Output type, fixed as `memory_store`.

**memory_store_name** `string`  |  Output memory store name

`data.outputs.memory_store_name`

The current name of the output memory store. Omitted when the associated memory store has been deleted.

**status** `string`  |  Task status

`data.status`

Dream task status.

* `pending`: The task has been accepted and is waiting to run. The output memory store is not yet accessible.

* `running`: The task is running. The output memory store is not yet accessible.

* `completed`: The task is completed. The output memory store is available.

* `failed`: The task failed to run. The output memory store is accessible, but its content may be empty or incomplete; see `error` for the reason.

* `canceled`: The task has been canceled. The output memory store is accessible, but its content may be empty or incomplete.

**type** `string`  |  Object type

`data.type`

Object type, fixed as `dream`.

**ended_at** `string`  |  End time

`data.ended_at`

The time when the task enters a terminal state, in RFC 3339 format.`null` if the task has not ended.

**error** `object`  |  Failure information

`data.error`

Information about the asynchronous execution failure of the task.`null` if the task has not failed.

**message** `string`  |  Error information

`data.error.message`

The reason why dream execution failed, used to troubleshoot task failure.

**type** `string`  |  Error type

`data.error.type`

Error type.`timeout` indicates that the task timed out; `internal_error` indicates that an internal error occurred during task execution.

**instructions** `string`  |  Organization instructions

`data.instructions`

The memory organization requirements passed in when the task was created.`null` if not passed in.

**running_time** `number`  |  Cumulative running time

`data.running_time`

The cumulative running time of dream excluding idle intervals, in seconds.`null` if it has not been obtained yet.

**usage** `object`  |  Usage statistics

`data.usage`

Token usage of the task. A value of `null` indicates that the usage is unknown and must not be interpreted as zero consumption.

**cache_creation_input_tokens** `integer`  |  Input tokens written to the cache

`data.usage.cache_creation_input_tokens`

Number of input tokens written to the cache.

**cache_read_input_tokens** `integer`  |  Cache read tokens

`data.usage.cache_read_input_tokens`

The number of input tokens read from the cache.

**input_tokens** `integer`  |  Total input tokens

`data.usage.input_tokens`

The number of input tokens consumed by this dream execution.

**output_tokens** `integer`  |  Total output tokens

`data.usage.output_tokens`

The number of output tokens generated by this dream execution.

**next_page** `string`  |  Next page identifier

Next page identifier. If the response does not return `next_page`, there is no more data and you should stop paginated queries.

**total_count** `integer`  |  Total number of dreams

The total number of dreams that match the current filter conditions, unaffected by pagination.
