# Create dream

`POST https://ark.ap-southeast.bytepluses.com/api/v3/dreams`

Create an asynchronous memory organization task. Each task reads an existing memory store and 1 to 100 historical sessions, generates a new memory store, and does not modify the input memory store.

The output memory store is created together with the task, but it cannot be mounted, read, or modified while the task is in the `pending` or `running` state. A task can run for up to 48 hours.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Body parameters

**inputs** `object[]` `Required`  |  Task input

Task input, which must contain exactly one `memory_store` input and one `sessions` input.

Source memory store input `object`

Specifies a source memory store.

**memory_store_id** `string` `Required`  |  Source memory store ID

`inputs.memory_store_id`

Source memory store ID, which cannot contain only whitespace characters.

**type** `string` `Required`  |  Input type

`inputs.type`

Input type, fixed as `memory_store`.

Historical session input `object`

Specifies a set of historical sessions.

**session_ids** `string[]` `Required`  |  Historical session IDs

`inputs.session_ids`

List of historical session IDs, which must contain 1 to 100 unique IDs, none of which can contain only whitespace characters.

**type** `string` `Required`  |  Input type

`inputs.type`

Input type, fixed as `sessions`.

**model** `string` `Required`  |  Model ID

The model ID used to perform the memory organization task. The model must have been activated in the current project.

**output_memory_store_name** `string` `Required`  |  Output memory store name

The name of the output memory store. The length is 1 to 64 Unicode characters, and it cannot contain only whitespace characters.

**instructions** `string`  |  Organization instructions

Memory organization requirements, such as topic filtering, removal of private information, and archiving rules. Contains up to 4096 Unicode characters.

**output_memory_store_description** `string`  |  Output memory store description

The description of the output memory store, containing up to 300 Unicode characters. If omitted, an empty description is used.

## Response parameters

**created_at** `string`  |  Creation time

The task creation time, in RFC 3339 format.

**id** `string`  |  Dream ID

The unique identifier of the dream task, in the format of a string that starts with `drm-`.

**inputs** `object[]`  |  Task input

The input snapshot saved when the task was created.

Source memory store input `object`

The source memory store input saved when the task was created.

**memory_store_id** `string`  |  Source memory store ID

`inputs.memory_store_id`

Source memory store ID.

**type** `string`  |  Input type

`inputs.type`

Input type, fixed as `memory_store`.

Historical session input `object`

The historical session input saved when the task was created.

**session_ids** `string[]`  |  Historical session IDs

`inputs.session_ids`

The list of historical session IDs saved when the task was created.

**type** `string`  |  Input type

`inputs.type`

Input type, fixed as `sessions`.

**model** `object`  |  Model information

Information about the model used to perform the memory organization task.

**id** `string`  |  Model ID

`model.id`

The model ID used to perform the memory organization task.

**outputs** `object[]`  |  Task output

The output memory store created by the dream. The access timing and content integrity are indicated by `status`.

**memory_store_id** `string`  |  Output memory store ID

`outputs.memory_store_id`

The output memory store ID created by the dream.

**type** `string`  |  Output type

`outputs.type`

Output type, fixed as `memory_store`.

**status** `string`  |  Task status

Dream task status. It is usually `pending` after successful creation.

* `pending`: The task has been accepted and is waiting to run. The output memory store is not yet accessible.

* `running`: The task is running. The output memory store is not yet accessible.

* `completed`: The task is completed. The output memory store is available.

* `failed`: The task failed to run. The output memory store is accessible, but its content may be empty or incomplete; see `error` for the reason.

* `canceled`: The task has been canceled. The output memory store is accessible, but its content may be empty or incomplete.

**type** `string`  |  Object type

Object type, fixed as `dream`.

**ended_at** `string`  |  End time

The time when the task enters a terminal state, in RFC 3339 format.`null` if the task has not ended.

**error** `object`  |  Failure information

Information about the asynchronous execution failure of the task.`null` if the task has not failed.

**message** `string`  |  Error information

`error.message`

The reason why dream execution failed, used to troubleshoot task failure.

**type** `string`  |  Error type

`error.type`

Error type.`timeout` indicates that the task timed out; `internal_error` indicates that an internal error occurred during task execution.

**instructions** `string`  |  Organization instructions

The memory organization requirements passed in when the task was created.`null` if not passed in.

**running_time** `number`  |  Cumulative running time

The cumulative running time of dream excluding idle intervals, in seconds.`null` if it has not been obtained yet.

**usage** `object`  |  Usage statistics

Token usage of the task. A value of `null` indicates that the usage is unknown and must not be interpreted as zero consumption.

**cache_creation_input_tokens** `integer`  |  Input tokens written to the cache

`usage.cache_creation_input_tokens`

Number of input tokens written to the cache.

**cache_read_input_tokens** `integer`  |  Cache read tokens

`usage.cache_read_input_tokens`

The number of input tokens read from the cache.

**input_tokens** `integer`  |  Total input tokens

`usage.input_tokens`

The number of input tokens consumed by this dream execution.

**output_tokens** `integer`  |  Total output tokens

`usage.output_tokens`

The number of output tokens generated by this dream execution.
