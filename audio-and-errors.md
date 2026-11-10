# Speech transcription and error events

Streaming transcription and error events.

## Error events

### error

> Triggered when an error occurs.

**message** `string`  |  Error reason

`error.message`

Error reason.

**sequence_number** `integer`  |  Sequence number

`error.sequence_number`

Sequence number of the event.

**type** `string`  |  Event type

`error.type`

The event type, fixed as `error`.

**code** `string`  |  Error code

`error.code`

Error code.

**param** `string`  |  Error parameter

`error.param`

The name of the parameter that caused the error.

## Speech transcription

### response.transcription_part.added

> Event triggered when new transcription content is added.

**item_id** `string`  |  Output item ID

`response.transcription_part.added.item_id`

The unique ID of the output item it belongs to.

**output_index** `integer`  |  Output item index

`response.transcription_part.added.output_index`

The list index of the output item it belongs to.

**part** `object`  |  Transcription part

`response.transcription_part.added.part`

The added transcription part.

**type** `string`  |  Type

`response.transcription_part.added.part.type`

Transcription segment type.

Value is fixed to `transcription_text`.

**chunks** `object[]`  |  Audio chunk

`response.transcription_part.added.part.chunks`

List of audio chunks corresponding to the current segment.

**end_time** `integer`  |  End time

`response.transcription_part.added.part.chunks.end_time`

Chunk end time (milliseconds).

**start_time** `integer`  |  Start time

`response.transcription_part.added.part.chunks.start_time`

Chunk start time (milliseconds).

**text** `string`  |  Text content

`response.transcription_part.added.part.chunks.text`

Text content corresponding to the current chunk.

**text** `string`  |  Transcription text

`response.transcription_part.added.part.text`

Transcription text of the current chunk.

**sequence_number** `integer`  |  Sequence number

`response.transcription_part.added.sequence_number`

Sequence number of the event.

**transcription_index** `integer`  |  Transcription index

`response.transcription_part.added.transcription_index`

The index of the transcription segment it belongs to.

**type** `string`  |  Event type

`response.transcription_part.added.type`

The event type, fixed as `response.transcription_part.added`.

### response.transcription_part.done

> Event triggered when the transcription part is completed.

**item_id** `string`  |  Output item ID

`response.transcription_part.done.item_id`

The unique ID of the output item it belongs to.

**output_index** `integer`  |  Output item index

`response.transcription_part.done.output_index`

The list index of the output item it belongs to.

**part** `object`  |  Transcription part

`response.transcription_part.done.part`

The completed transcription part.

**type** `string`  |  Type

`response.transcription_part.done.part.type`

Transcription segment type.

Value is fixed to `transcription_text`.

**chunks** `object[]`  |  Audio chunk

`response.transcription_part.done.part.chunks`

List of audio chunks corresponding to the current segment.

**end_time** `integer`  |  End time

`response.transcription_part.done.part.chunks.end_time`

Chunk end time (milliseconds).

**start_time** `integer`  |  Start time

`response.transcription_part.done.part.chunks.start_time`

Chunk start time (milliseconds).

**text** `string`  |  Text content

`response.transcription_part.done.part.chunks.text`

Text content corresponding to the current chunk.

**text** `string`  |  Transcription text

`response.transcription_part.done.part.text`

Transcription text of the current chunk.

**sequence_number** `integer`  |  Sequence number

`response.transcription_part.done.sequence_number`

Sequence number of the event.

**transcription_index** `integer`  |  Transcription index

`response.transcription_part.done.transcription_index`

The index of the transcription segment it belongs to.

**type** `string`  |  Event type

`response.transcription_part.done.type`

The event type, fixed as `response.transcription_part.done`.

### response.transcription_text.delta

> Triggered when a transcription text segment is added.

**end_time** `integer`  |  End time

`response.transcription_text.delta.end_time`

The end time of this transcription segment, in milliseconds.

**item_id** `string`  |  Output item ID

`response.transcription_text.delta.item_id`

The unique ID of the output item it belongs to.

**output_index** `integer`  |  Output item index

`response.transcription_text.delta.output_index`

The list index of the output item it belongs to.

**sequence_number** `integer`  |  Sequence number

`response.transcription_text.delta.sequence_number`

Sequence number of the event.

**start_time** `integer`  |  Start time

`response.transcription_text.delta.start_time`

The start time of this transcription segment, in milliseconds.

**transcription_index** `integer`  |  Transcription index

`response.transcription_text.delta.transcription_index`

The index of the transcription segment it belongs to.

**type** `string`  |  Event type

`response.transcription_text.delta.type`

The event type, fixed as `response.transcription_text.delta`.

**delta** `string`  |  Transcription delta

`response.transcription_text.delta.delta`

The newly added transcription text segment.

### response.transcription_text.done

> Triggered when the transcription text content is complete.

**chunks** `object[]`  |  Audio segment

`response.transcription_text.done.chunks`

Audio chunks associated with the complete transcription of this segment.

**end_time** `integer`  |  End time

`response.transcription_text.done.chunks.end_time`

Chunk end time (milliseconds).

**start_time** `integer`  |  Start time

`response.transcription_text.done.chunks.start_time`

Chunk start time (milliseconds).

**text** `string`  |  Text content

`response.transcription_text.done.chunks.text`

Text content corresponding to the current chunk.

**end_time** `integer`  |  End time

`response.transcription_text.done.end_time`

The end time of this transcription segment, in milliseconds.

**item_id** `string`  |  Output item ID

`response.transcription_text.done.item_id`

The unique ID of the output item it belongs to.

**output_index** `integer`  |  Output item index

`response.transcription_text.done.output_index`

The list index of the output item it belongs to.

**sequence_number** `integer`  |  Sequence number

`response.transcription_text.done.sequence_number`

Sequence number of the event.

**start_time** `integer`  |  Start time

`response.transcription_text.done.start_time`

The start time of this transcription segment, in milliseconds.

**transcription_index** `integer`  |  Transcription index

`response.transcription_text.done.transcription_index`

The index of the transcription segment it belongs to.

**type** `string`  |  Event type

`response.transcription_text.done.type`

The event type, fixed as `response.transcription_text.done`.

**text** `string`  |  Transcription text

`response.transcription_text.done.text`

The complete transcription text content.
