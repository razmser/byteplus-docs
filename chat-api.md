# Chat API

`POST https://ark.ap-southeast.bytepluses.com/api/v3/chat/completions`

Send a list of messages containing modalities such as text, image, video, and audio, and the model will generate the next message in the conversation.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* [Recommended] API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Body parameters

**messages** `object[]` `Required`  |  Message list

Different models support different types of messages, such as text, image, video, audio, etc.

System message `object`

The system message, instructions that the model must follow, including the role to play, background information, etc.

**content** `string / object[]` `Required`  |  Message content

`messages.content`

Content of the system message. Supports plain text or multimodal content.

Plain text `string`

Plain text content.

Multimodal content `object[]`

Multimodal content such as text, image, video, and audio.

Text information `object`

The text part of a multimodal message.

**text** `string` `Required`  |  Text content

`messages.content.text`

Content of the text modality.

**type** `string` `Required`  |  Type

`messages.content.type`

Content modality type. Fixed to `text`.

Image information `object`

Images.

**image_url** `object` `Required`  |  Image content

`messages.content.image_url`

Content of the image modality. Image input supports the **file_id / url** parameters. You must pass one of them. For usage details, see [Image understanding](image-understanding.md).

**detail** `string`  |  Image parsing fineness

`messages.content.image_url.detail`

Image parsing fineness.

For the image understanding fineness, default values for different models and corresponding specific pixel ranges, see [Precision control of image understanding](image-understanding.md#bf4d9224). Options: `low`, `high`, `xhigh`.

**file_id** `string`  |  File ID

`messages.content.image_url.file_id`

File ID.

* The file ID is the ID returned after you upload a file through the [Files API](upload-files-api.md).

* The file type corresponding to **file_id** must be consistent with **type**, and the file status must be **active**.

* The project that the **API key** belongs to must be the same as the project used to upload **file_id**.

**image_pixel_limit** `object` `Default null`  |  Image pixel limit

`messages.content.image_url.image_pixel_limit`

Pixel range of the image input to the model. If it is not within this range, the image will be scaled proportionally to fit this range.

* Priority: It has higher priority than **detail**. That is, when both **detail** and **image_pixel_limit** are configured, **image_pixel_limit** takes effect.

* Default rule: If **image_pixel_limit** is not set, the **min_pixels** / **max_pixels** value corresponding to the value configured in **detail** will be used.

> **Note**

> The image pixel value must fall in the range of [196, 36000000] px; otherwise, an error will be reported.

**max_pixels** `integer`  |  Maximum pixels

`messages.content.image_url.image_pixel_limit.max_pixels`

Maximum pixel limit for uploaded images. If the image pixel count exceeds this value, it will be scaled down proportionally to below the value of `max_pixels`. If not set, the value will be the `max_pixels` value corresponding to the configured `detail` value.

**Max**: `9031680`

**min_pixels** `integer`  |  Minimum pixels

`messages.content.image_url.image_pixel_limit.min_pixels`

Minimum pixel limit for uploaded images. If the image pixel count is lower than this value, it will be scaled up proportionally to above the value of the `min_pixels`. If not set, the value will be the `min_pixels` value corresponding to the configured `detail` value.

**Min**: `1764`

**url** `string`  |  Resource URL

`messages.content.image_url.url`

The image URL to send to the model. Supported formats are as follows:

* Image link

* Base64 string of the image

**type** `string` `Required`  |  Type

`messages.content.type`

Content type. Fixed to `image_url`.

Video information `object`

Videos.

**type** `string` `Required`  |  Type

`messages.content.type`

Content type. Fixed to `video_url`.

**video_url** `object` `Required`  |  Video content

`messages.content.video_url`

Content of the video message. Video input supports the **file_id / url** parameters. You must pass one of them. For usage details, see [Video understanding](video-understanding.md).

**file_id** `string`  |  File ID

`messages.content.video_url.file_id`

File ID.

* The file ID is the ID returned after you upload a file through the [Files API](upload-files-api.md).

* The file type corresponding to **file_id** must be consistent with **type**, and the file status must be **active**.

* The project that the **API key** belongs to must be the same as the project used to upload **file_id**.

**fps** `number` `Default 1.0`  |  Video frame rate

`messages.content.video_url.fps`

Frame sampling frequency. For details, see [Video understanding](video-understanding.md).

* The higher the value, the more sensitive it is to frame changes in the video.

* The lower the value, the less sensitive it is to frame changes in the video, but it consumes fewer tokens and runs faster.

**Range**: `[0.2, 5]`

**url** `string`  |  Resource URL

`messages.content.video_url.url`

Video URL to send to the model. Supported formats are as follows:

* Video link

* Base64 string of the video

Audio information `object`

Audios.

**input_audio** `object` `Required`  |  Audio content

`messages.content.input_audio`

Content of the audio modality. Audio input supports three parameters: **file_id / url / data**. You must pass one of them. For specific usage, see [Audio understanding](audio-understanding.md).

> **Note**

* > File size cannot exceed 25 MB.

* > The total audio duration per request cannot exceed 120 minutes. Only pure audio duration is counted, while embedded audio in video is not included.

* > For details, see [Audio understanding](audio-understanding.md).

**data** `string`  |  Audio data

`messages.content.input_audio.data`

Base64 string of the audio content.

**file_id** `string`  |  File ID

`messages.content.input_audio.file_id`

File ID.

* The file ID is the ID returned after you upload a file through the [Files API](upload-files-api.md).

* The file type corresponding to **file_id** must be consistent with **type**, and the file status must be **active**.

* The project that the **API key** belongs to must be the same as the project used to upload **file_id**.

**format** `string`  |  Audio format

`messages.content.input_audio.format`

Audio format. This parameter is required when **data** is used. The MIME types of supported audio formats are as follows: Pure audio formats: \*.mp3: `audio/mpeg` \*.wav: `audio/wav` \*.aac: `audio/aac` \*.m4a: `audio/mp4` Embedded audio formats in video: \*.mp3: `audio/mpeg` \*.wav: `audio/wav` \*.aac: `audio/aac` \*.m4a: `audio/mp4` \*.pcm: `audio/L16` \*.ac3: `audio/ac3` \*.alac: `audio/mp4`

**url** `string`  |  Resource URL

`messages.content.input_audio.url`

URL of the audio content.

**type** `string` `Required`  |  Type

`messages.content.type`

Content type. Fixed to `input_audio`.

File information `object`

Files.

**file** `object` `Required`  |  File content

`messages.content.file`

Content of the file modality. Currently, only PDF files are supported. File input supports the **file_id / file_data / file_url** parameters. You must pass one of them. For multimodal understanding examples, see [Document understanding](document-understanding.md).

> **Tip**

> For details about document understanding, see [Document understanding](document-understanding.md).

**file_data** `string`  |  File data

`messages.content.file.file_data`

Base64 string of the file content. The file size of a single file cannot exceed 50 MB.

**file_id** `string`  |  File ID

`messages.content.file.file_id`

File ID.

* The file ID is the ID returned after you upload a file through the [Files API](upload-files-api.md).

* The file type corresponding to **file_id** must be consistent with **type**, and the file status must be **active**.

* The project that the **API key** belongs to must be the same as the project used to upload **file_id**.

**file_url** `string`  |  Resource URL

`messages.content.file.file_url`

Accessible URL of the file. The file size cannot exceed 50 MB.

**filename** `string`  |  Filename

`messages.content.file.filename`

Filename. This parameter is required when **file_data** is used.

**type** `string` `Required`  |  Type

`messages.content.type`

Modality of message. Fixed to `file`.

**role** `string` `Required`  |  Role

`messages.role`

Role that sends the message.

Value is fixed to `system`.

**name** `string`  |  Participant name

`messages.name`

Participant name, which helps the model distinguish different participants.

User message `object`

User message sent by the user role. Supported parameter types vary by model.

**role** `string` `Required`  |  Role

`messages.role`

Role that sends the message.

Value is fixed to `user`.

**content** `string / object[]`  |  Message content

`messages.content`

Content of the user message. Plain text or multimodal content is supported. If this parameter is omitted, `null` is passed, or empty content is passed, the server treats it as an empty string.

Plain text `string`

Plain text content.

Multimodal content `object[]`

Multimodal content such as text, image, video, and audio.

Text information `object`

The text part of a multimodal message.

**text** `string` `Required`  |  Text content

`messages.content.text`

Content of the text modality.

**type** `string` `Required`  |  Type

`messages.content.type`

Content modality type. Fixed to `text`.

Image information `object`

Images.

**image_url** `object` `Required`  |  Image content

`messages.content.image_url`

Content of the image modality. Image input supports the **file_id / url** parameters. You must pass one of them. For usage details, see [Image understanding](image-understanding.md).

**detail** `string`  |  Image parsing fineness

`messages.content.image_url.detail`

Image parsing fineness.

For the image understanding fineness, default values for different models and corresponding specific pixel ranges, see [Precision control of image understanding](image-understanding.md#bf4d9224). Options: `low`, `high`, `xhigh`.

**file_id** `string`  |  File ID

`messages.content.image_url.file_id`

File ID.

* The file ID is the ID returned after you upload a file through the [Files API](upload-files-api.md).

* The file type corresponding to **file_id** must be consistent with **type**, and the file status must be **active**.

* The project that the **API key** belongs to must be the same as the project used to upload **file_id**.

**image_pixel_limit** `object` `Default null`  |  Image pixel limit

`messages.content.image_url.image_pixel_limit`

Pixel range of the image input to the model. If it is not within this range, the image will be scaled proportionally to fit this range.

* Priority: It has higher priority than **detail**. That is, when both **detail** and **image_pixel_limit** are configured, **image_pixel_limit** takes effect.

* Default rule: If **image_pixel_limit** is not set, the **min_pixels** / **max_pixels** value corresponding to the value configured in **detail** will be used.

> **Note**

> The image pixel value must fall in the range of [196, 36000000] px; otherwise, an error will be reported.

**max_pixels** `integer`  |  Maximum pixels

`messages.content.image_url.image_pixel_limit.max_pixels`

Maximum pixel limit for uploaded images. If the image pixel count exceeds this value, it will be scaled down proportionally to below the value of `max_pixels`. If not set, the value will be the `max_pixels` value corresponding to the configured `detail` value.

**Max**: `9031680`

**min_pixels** `integer`  |  Minimum pixels

`messages.content.image_url.image_pixel_limit.min_pixels`

Minimum pixel limit for uploaded images. If the image pixel count is lower than this value, it will be scaled up proportionally to above the value of the `min_pixels`. If not set, the value will be the `min_pixels` value corresponding to the configured `detail` value.

**Min**: `1764`

**url** `string`  |  Resource URL

`messages.content.image_url.url`

The image URL to send to the model. Supported formats are as follows:

* Image link

* Base64 string of the image

**type** `string` `Required`  |  Type

`messages.content.type`

Content type. Fixed to `image_url`.

Video information `object`

Videos.

**type** `string` `Required`  |  Type

`messages.content.type`

Content type. Fixed to `video_url`.

**video_url** `object` `Required`  |  Video content

`messages.content.video_url`

Content of the video message. Video input supports the **file_id / url** parameters. You must pass one of them. For usage details, see [Video understanding](video-understanding.md).

**file_id** `string`  |  File ID

`messages.content.video_url.file_id`

File ID.

* The file ID is the ID returned after you upload a file through the [Files API](upload-files-api.md).

* The file type corresponding to **file_id** must be consistent with **type**, and the file status must be **active**.

* The project that the **API key** belongs to must be the same as the project used to upload **file_id**.

**fps** `number` `Default 1.0`  |  Video frame rate

`messages.content.video_url.fps`

Frame sampling frequency. For details, see [Video understanding](video-understanding.md).

* The higher the value, the more sensitive it is to frame changes in the video.

* The lower the value, the less sensitive it is to frame changes in the video, but it consumes fewer tokens and runs faster.

**Range**: `[0.2, 5]`

**url** `string`  |  Resource URL

`messages.content.video_url.url`

Video URL to send to the model. Supported formats are as follows:

* Video link

* Base64 string of the video

Audio information `object`

Audios.

**input_audio** `object` `Required`  |  Audio content

`messages.content.input_audio`

Content of the audio modality. Audio input supports three parameters: **file_id / url / data**. You must pass one of them. For specific usage, see [Audio understanding](audio-understanding.md).

> **Note**

* > File size cannot exceed 25 MB.

* > The total audio duration per request cannot exceed 120 minutes. Only pure audio duration is counted, while embedded audio in video is not included.

* > For details, see [Audio understanding](audio-understanding.md).

**data** `string`  |  Audio data

`messages.content.input_audio.data`

Base64 string of the audio content.

**file_id** `string`  |  File ID

`messages.content.input_audio.file_id`

File ID.

* The file ID is the ID returned after you upload a file through the [Files API](upload-files-api.md).

* The file type corresponding to **file_id** must be consistent with **type**, and the file status must be **active**.

* The project that the **API key** belongs to must be the same as the project used to upload **file_id**.

**format** `string`  |  Audio format

`messages.content.input_audio.format`

Audio format. This parameter is required when **data** is used. The MIME types of supported audio formats are as follows: Pure audio formats: \*.mp3: `audio/mpeg` \*.wav: `audio/wav` \*.aac: `audio/aac` \*.m4a: `audio/mp4` Embedded audio formats in video: \*.mp3: `audio/mpeg` \*.wav: `audio/wav` \*.aac: `audio/aac` \*.m4a: `audio/mp4` \*.pcm: `audio/L16` \*.ac3: `audio/ac3` \*.alac: `audio/mp4`

**url** `string`  |  Resource URL

`messages.content.input_audio.url`

URL of the audio content.

**type** `string` `Required`  |  Type

`messages.content.type`

Content type. Fixed to `input_audio`.

File information `object`

Files.

**file** `object` `Required`  |  File content

`messages.content.file`

Content of the file modality. Currently, only PDF files are supported. File input supports the **file_id / file_data / file_url** parameters. You must pass one of them. For multimodal understanding examples, see [Document understanding](document-understanding.md).

> **Tip**

> For details about document understanding, see [Document understanding](document-understanding.md).

**file_data** `string`  |  File data

`messages.content.file.file_data`

Base64 string of the file content. The file size of a single file cannot exceed 50 MB.

**file_id** `string`  |  File ID

`messages.content.file.file_id`

File ID.

* The file ID is the ID returned after you upload a file through the [Files API](upload-files-api.md).

* The file type corresponding to **file_id** must be consistent with **type**, and the file status must be **active**.

* The project that the **API key** belongs to must be the same as the project used to upload **file_id**.

**file_url** `string`  |  Resource URL

`messages.content.file.file_url`

Accessible URL of the file. The file size cannot exceed 50 MB.

**filename** `string`  |  Filename

`messages.content.file.filename`

Filename. This parameter is required when **file_data** is used.

**type** `string` `Required`  |  Type

`messages.content.type`

Modality of message. Fixed to `file`.

**name** `string`  |  Participant name

`messages.name`

Participant name, which helps the model distinguish different participants.

Model message `object`

Messages returned by the model in historical conversations. Used to maintain conversation consistency, and often used in [multi-turn conversations](text-generation.md#f6222fec) and [prefill-based response](continuation-mode.md).

**role** `string` `Required`  |  Role

`messages.role`

Role that sends the message.

Value is fixed to `assistant`.

**content** `string`  |  Message content

`messages.content`

Content of the model message. If this parameter is omitted, `null` is passed, or empty content is passed, the server treats it as an empty string.

**encrypted_content** `string`  |  Encrypted reasoning content

`messages.encrypted_content`

Encrypted reasoning content. Reasoning content that has been encrypted and compressed.

When calling supported models, you must return the encrypted reasoning content to ensure model reasoning performance.

Related documents:

* [Key rules](agent-model-invocation-practice.md#key-rules)

* [Examples of returning encrypted reasoning content blocks](agent-model-invocation-practice.md#encrypted-thinking-block-examples)

> **Note**

* > The returned `encrypted_content` must be valid. If it is tampered with or cannot be restored, the error `Invalid signature` is returned.

* > `encrypted_content` has higher priority than `reasoning_content`. When `encrypted_content` is returned, the `reasoning_content` content will be ignored.

**Supported models**:

* ``seed\-2\-0\-lite\-260228`and later versions`

**name** `string`  |  Participant name

`messages.name`

Participant name, which helps the model distinguish different participants.

**reasoning_content** `string`  |  Chain\-of\-thought content

`messages.reasoning_content`

Chain\-of\-thought content in the model message.

**Supported models**:

* `Seed series models`

* `deepseek-v4-pro`

* `deepseek-v4-flash`

**tool_calls** `object[]`  |  Tool calling

`messages.tool_calls`

The tool call part in the model message.

**function** `object` `Required`  |  Function information

`messages.tool_calls.function`

Information about the tool call function generated by the model.

**arguments** `string` `Required`  |  Function arguments

`messages.tool_calls.function.arguments`

Parameters generated by the model for calling the function in JSON format. The model does not always generate valid JSON, and may fabricate some parameters that are not defined in your function parameter specifications. Before calling the function, please verify the validity of these parameters in your code.

**name** `string` `Required`  |  Function name

`messages.tool_calls.function.name`

Name of the function called by the model.

**type** `string` `Required`  |  Type

`messages.tool_calls.type`

Tool type. Currently only `function` is supported.

**id** `string`  |  Tool call ID

`messages.tool_calls.id`

The tool call ID in the historical model message, used to associate it with the `tool_call_id` of a subsequent tool message. If omitted or set to `null`, the server processes it as an empty string.

Tool message `object`

Messages returned by the called tools in historical conversations. Used in tool call scenarios.

**role** `string` `Required`  |  Role

`messages.role`

Role that sends the message.

Value is fixed to `tool`.

**content** `string`  |  Message content

`messages.content`

Message returned by the tool. If omitted or set to `null`, the server processes it as an empty string.

**name** `string`  |  Participant name

`messages.name`

Participant name, which helps the model distinguish different participants.

**tool_call_id** `string`  |  Tool call ID

`messages.tool_call_id`

Tool call ID associated with the tool result. The value corresponds to the value of `tool_calls.id` in the historical model message, and is used to let the server match the tool result to the corresponding tool call. If omitted or set to `null`, the server processes it as an empty string.

**model** `string` `Required`  |  Model ID

ID of the called model. After you [activate model services](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenTokenDrawer=false), you can [query the model ID](model-list.md).

For scenarios with multiple applications and fine\-grained management, we recommend calling by inference endpoint ID. For details, see [Get endpoint ID](online-inference-standard.md).

**frequency_penalty** `number` `Default 0.0`  |  Frequency penalty coefficient

If the value is positive, new tokens are penalized based on their frequency of occurrence in the text, reducing the possibility of the model repeating word for word.

> **Note**

* > Seed 2.0 and later series, and Deepseek V4.1 Flash do not support this parameter.

* > Deepseek V4 Pro and Deepseek V4 Flash also do not support this parameter in reasoning mode.

* > Glm 5.3 Flash supports this parameter.

**Range**: `[-2.0, 2.0]`

**logit_bias** `object` `Default null`  |  Token bias

Adjusts the probability of specified tokens appearing in the model's output, making the generated content better align with specific preferences.

`logit_bias` accepts a map. Each key is a token ID in the vocabulary, which can be obtained through the Tokenization API, and each value is the bias value for that token:

* A negative value decreases the likelihood of selecting the token. `-100` completely prevents the token from being selected.

* A positive value increases the likelihood of selecting the token. `100` causes only this token to be selected.

Example: `{"<Token_ID>": -100}`. The actual effect of this parameter may vary by model.

> **Note**

> Models with deep reasoning capabilities do not support this parameter. For the list of these models, see [Model list](model-list.md#32e9e053) and find the models with "Deep reasoning" in the Capabilities column.

**Range**: `[-100, 100]`

**logprobs** `boolean` `Default false`  |  Return log probabilities

Whether to return the log probabilities of output tokens.

* `false`: Does not return log probability information.

* `true`: Returns the log probability of each output token in the message content.

> Most [models with deep reasoning capabilities](model-list.md) do not support this parameter, except the following ones:`deepseek-v4-1-flash-261017`, `deepseek-v4-1-flash-260910`, `deepseek-v4-1-flash-economy-261017`, `deepseek-v4-pro-ga-260813`, and `deepseek-v4-flash-ga-260731`.

**max_completion_tokens** `integer`  |  Maximum output length

Controls the maximum length of the model's output (including the length of the model's answer and model chain\-of\-thought content, unit: token).

After configuring this parameter, the model can output ultra\-long content, the default value of `max_tokens` becomes invalid, and the model outputs content (answer and chain\-of\-thought) as needed until the `max_completion_tokens` value is reached.

Cannot be set while `max_tokens` parameter is set.

> **Note**

> For the `deepseek-v4-1-flash-261017`, `deepseek-v4-1-flash-260910` and `deepseek-v4-1-flash-economy-261017` models, the default value of this parameter is 128k. For models that support this parameter and usage instructions, see [Deep reasoning](deep-thinking.md).

**Range**: `[1, 65536]`

**max_tokens** `integer` `Default 4096`  |  Maximum response length

Maximum length of the model's answer (unit: token). The value range varies by model. For details, see [Model list](model-list.md).

> **Tip**

* > `deepseek-v4-1-flash-261017`, `deepseek-v4-1-flash-260910` and `deepseek-v4-1-flash-economy-261017`: `max_tokens` controls the length of the model's answer and chain\-of\-thought content. No error is reported if the length is exceeded. The default value is 128k.

* > Models other than the preceding model: `max_tokens` controls the length of the model's answer and does not include chain\-of\-thought content. Model answer = model output \- model chain\-of\-thought (if any).

* > The total length of output tokens is also limited by the model's context length.

**parallel_tool_calls** `boolean` `Default true`  |  Parallel tool calling

Specifies whether the model is allowed to return multiple tools to be called for this request.

* `true`: Allows returning multiple tools to be called.

* `false`: Allows returning no more than one tool to be called.

**presence_penalty** `number` `Default 0.0`  |  Presence penalty coefficient

If the value is positive, new tokens are penalized based on whether they have appeared in the text so far, increasing the possibility of the model discussing new topics.

> **Note**

* > Seed 2.0 and later series, and Deepseek V4.1 Flash do not support this parameter.

* > Deepseek V4 Pro and Deepseek V4 Flash also do not support this parameter in reasoning mode.

* > Glm 5.3 Flash supports this parameter.

**Range**: `[-2.0, 2.0]`

**prompt_cache_key** `string`  |  Cache routing identifier

A cache routing identifier used to improve the implicit cache hit rate. Requests with the same value and the same endpoint and model are preferentially routed to a service that is more likely to reuse the same prefix cache.

* This parameter is an optional routing optimization, not an implicit cache switch; omitting this parameter does not disable the implicit cache.

* Requests that continue the same context should use the same value; different users or parallel sessions should use different values.

* For requests with the same value, we recommend keeping total traffic to no more than 15 requests per minute. If this range is exceeded, some requests may fall back to regular routing.

* Use an unreadable internal session ID, and avoid passing phone numbers, email addresses, access credentials, or the original prompt text.

* Requests may be rerouted when service scaling, releases, failover, mapping expiration, or protection policies are triggered, so this parameter cannot guarantee a cache hit for every request.

**reasoning_effort** `string`  |  Reasoning effort

Limits the amount of reasoning work. Reducing the reasoning depth can increase speed and reduce token consumption.

* `none`: Turn off reasoning.

* `minimal`: Turns off reasoning and answers directly.

* `low`: Light reasoning, focused on fast responses.

* `medium`: Balanced mode, considering both speed and depth.

* `high`: Deep analysis for handling complex problems.

* `xhigh`: Higher level of reasoning.

* `max`: Maximum level of reasoning, suitable for highly difficult reasoning tasks.

> **Note**

> For models that support this parameter, default values, parameter compatibility mapping rules, and the relationship with the `thinking.type` parameter, see [Deep reasoning](deep-thinking.md).

**response_format** `object`  |  Response format

Specifies the format of the model's answer. Default: `{"type": "text"}`

> **Note**

* > For models that support this parameter, see [documentation](structured-output-beta.md#3aae5325).

* > This capability is still in beta stage. Please use it with caution in production environments.

**json_schema** `object`  |  Schema configuration

`response_format.json_schema`

Definition of the JSON structure.

**name** `string` `Required`  |  Schema name

`response_format.json_schema.name`

The name of the JSON Schema, used to identify the response format.

**schema** `object` `Required`  |  Schema definition

`response_format.json_schema.schema`

The JSON Schema definition of the answer format.

**description** `string`  |  Description

`response_format.json_schema.description`

Description of the purpose of the answer, which the model uses to determine how to generate the answer in this format.

**strict** `boolean` `Default false`  |  Strict mode

`response_format.json_schema.strict`

Whether to enable strict schema adherence when generating output.

* `true`: The model always strictly follows the format defined in the `schema` parameter.

* `false`: The model follows the structure defined in the `schema` parameter as much as possible.

**type** `string`  |  Type

`response_format.type`

Answer format type. Options: `text`, `json_schema`, `json_object`.

**service_tier** `string`  |  Inference mode

Controls the online inference mode used.

* `default`: This request only uses the [Online inference (standard)](online-inference-standard.md) mode, maintaining the standard service tier.

* `flex`: This request uses the [online inference (flex)](online-inference-flex.md) mode.

**stop** `string / string[]` `Default null`  |  Stop words

When the model encounters the string specified by the `stop` parameter, it will stop generating further content, and the string itself will not be output.

* By default, up to `4` stop words are supported.

* DeepSeek V4 series models support up to `16` stop words.

* Kimi series (not available for BytePlus users) models support up to `5` stop words.

Example: `["hello", "weather"]`.

> [Deep reasoning models](model-list.md#32e9e053) do not support this parameter by default, except for DeepSeek V4 models mentioned above.

**stream** `boolean` `Default false`  |  Streaming response

Whether the response content is returned in streaming mode.

* `false`: The model returns the result all at once after generating all content.

* `true`: Returns the content generated by the model chunk by chunk according to the SSE protocol, and ends with a `data: [DONE]` message. When `stream` is `true`, you can set the `stream_options` parameter to obtain token usage statistics.

**stream_options** `object` `Default null`  |  Streaming options

Options for streaming output. This parameter can be set when `stream` is `true`.

**chunk_include_usage** `boolean` `Default false`  |  Per\-chunk usage information

`stream_options.chunk_include_usage`

When the model outputs in streaming mode, whether to output the cumulative token usage information of the request up to the output time of this chunk in each output chunk.

* `true`: The returned `usage` parameter outputs the cumulative token usage of the request up to the output time of this chunk.

* `false`: Token usage information is not returned in every chunk.

**include_usage** `boolean` `Default false`  |  Output usage information

`stream_options.include_usage`

Whether to output the token usage information of this request before the end of output when the model outputs in streaming mode.

* `true`: An additional chunk is returned before the `data: [DONE]` message. In this chunk, the `usage` parameter outputs the token usage of the entire request, and the `choices` parameter is an empty array.

* `false`: No chunk is used to return token usage information before the end of output.

> **Note**

> `deepseek-v4-1-flash-261017` and `deepseek-v4-1-flash-economy-261017` models do not support using this parameter to control how usage information is returned. The models above return the `usage` information for this request in the chunk that contains `finish_reason`, and do not return an additional standalone usage information chunk.

**temperature** `number` `Default 1.0`  |  Sampling temperature

Controls the degree of smoothing applied to the probability distribution of each candidate word when generating text. When the value is 0, the model only considers the token with the highest log probability.

Higher values (e.g., 0.8) make the output more random, while lower values (e.g., 0.2) make the output more focused and deterministic.

It is generally recommended to adjust either `temperature` or `top_p`, while modifying both is not advised.

> **Note**

* > For the `seed-2-0-pro-260328` and `seed-2-0-lite-260228` models, the **temperature** parameter is fixed as `1.0`, and manually specified parameter values are ignored.

**Range**: `[0, 2]`

**thinking** `object`  |  Deep reasoning switch

Whether deep reasoning is enabled for the model.

> **Note**

> Support and default values vary by model. For details, see [documentation](deep-thinking.md#14b5c6db).

**type** `string`  |  Type

`thinking.type`

Whether reasoning is enabled for the model.

* `enabled`: Enables reasoning. The model always reasons before answering.

* `disabled`: Disables reasoning. The model answers the question directly without reasoning.

**tool_choice** `string / object`  |  Tool call control

Controls whether tools to be called are included in the model's response for this request.

* `none`: The model does not call tools and directly generates a message. When no tools are specified, `none` is the default value.

* `auto`: The model can choose to generate a message or call tools. If tools are specified, `auto` is the default value.

* `required`: The model must call one or more tools.

You can also set `tool_choice` to the object type to force the model to call a specified tool.

Enum values `string`

Controls whether the model's response includes tools to be called.

* `none`: The model's response must not contain any tools to be called.

* `required`: The model's response must contain tools to be called. When selecting this option, please confirm that suitable tools are available to reduce the occurrence of model hallucinations.

* `auto`: The model independently determines whether to include tools to be called in the response.

Specified tool `object`

Specifies the scope of tools that can be called. The model's response is only allowed to include information of the following specified tool.

When selecting this option, please confirm that the tool meets user requirements to reduce the occurrence of model hallucinations.

**function** `object` `Required`  |  Function information

`tool_choice.function`

Information of the called tool.

**name** `string` `Required`  |  Function name

`tool_choice.function.name`

Name of the function to be called.

**type** `string` `Required`  |  Type

`tool_choice.type`

Type of the call. In this case, set it to `function`.

**tools** `object[]` `Default null`  |  Tool list

List of tools available for calling, which may be included in the model's response.

This structure needs to be configured when you require the model to return tools to be called. For models that support this parameter, see [documentation](model-list.md#f44ceef7).

**function** `object` `Required`  |  Function information

`tools.function`

The model's response may include tools to be called.

**name** `string` `Required`  |  Function name

`tools.function.name`

Tool (function) name for the model to identify and call.

**description** `string`  |  Description

`tools.function.description`

Description of the function to be called. The LLM uses it to determine whether to call this tool.

**parameters** `object`  |  Parameter definition

`tools.function.parameters`

Function request parameters, described in JSON Schema format. For the specific format, see the [JSON Schema](https://json-schema.org/understanding-json-schema) documentation. The format is as follows:

The formats are as follows:

```JSON
{ "type": "object", "properties": { "parameter_name": { "type": "string | number | boolean | object | array", "description": "Parameter description" } }, "required": ["required_parameter"] }
```

Tips: All parameter names are case\-sensitive; `parameters` must be a valid JSON Schema object; English parameter names are recommended.

**strict** `boolean`  |  Strict mode

`tools.function.strict`

Whether to enable strict schema validation to ensure that the function call parameters generated by the model strictly conform to the `parameters` definition.

**type** `string` `Required`  |  Type

`tools.type`

Type of the tool. In this case, set it to `function`.

**top_logprobs** `integer` `Default 0`  |  Number of returned probabilities

Specifies the most probable number of tokens to return for each output token position, each with an associated log probability.

> **Note**

* > This parameter can be set only when `logprobs=true`.

* > Most [models with deep reasoning capabilities](model-list.md) do not support this parameter, except the following ones:`deepseek-v4-1-flash-261017`, `deepseek-v4-1-flash-260910`, `deepseek-v4-1-flash-economy-261017`, `deepseek-v4-pro-ga-260813`, and `deepseek-v4-flash-ga-260731`.

**Range**: `[0, 20]`

**top_p** `number` `Default 0.7`  |  Nucleus sampling threshold

Nucleus sampling probability threshold. The model considers token results whose cumulative probability mass falls within the `top_p` threshold.

When the value is 0, the model only considers the token with the highest log probability. A value of 0.1 means only the top 10% of tokens with the highest probability mass are considered. The higher the value, the higher the randomness of generation; the lower the value, the higher the determinism of generation.

It is generally recommended to adjust either `temperature` or `top_p`, while modifying both is not advised.

> **Note**

* > For the `seed-2-0-pro-260328` and `seed-2-0-lite-260228` models, the top_p parameter is fixed as `0.95`, and manually specified parameter values are ignored.

* > For the `glm-5-3-flash-260828` model, the default value is `0.95`.

* > For the `deepseek-v4-1-flash-261017`, `deepseek-v4-1-flash-260910`, and `deepseek-v4-1-flash-economy-261017` models, the rules for `top_p` parameter values are as follows:

   * > Reasoning mode: The parameter takes effect. If this parameter is not passed, the default value is 0.95 when using a tool, and 1 in other cases.

      * <div data-tips="true" data-tips-type="warning">Passed value < 0.95: It is automatically raised to 0.95.</div>

      * > Passed value ≥ 0.95: The actual passed value is used.

   * > Non\-reasoning mode: The parameter value is fixed at 1.0, and the passed value is ignored.

**Range**: `[0, 1]`

## Response parameters

### Non\-streaming output

**choices** `object[]`  |  Generated results

Model output content of this request.

**finish_reason** `string`  |  Finish reason

`choices.finish_reason`

The reason why the model stopped generating tokens.

* `stop`: The model output ends naturally, or is truncated because it hits the parameter specified in the request parameter `stop`.

* `length`: The model output is truncated because it reaches the model output limit, for the following reasons:

   * The `max_tokens` limit is triggered (length limit of the answer content).

   * The `max_completion_tokens` limit is triggered (length limit of chain\-of\-thought content + answer content).

   * The `context_window` limit is triggered (length limit of input content + chain\-of\-thought content + answer content).

* `content_filter`: The model output is intercepted by content moderation.

* `tool_calls`: The model called tools.

**index** `integer`  |  Index

`choices.index`

The index of the current element in the **choices** list.

**logprobs** `object`  |  Choice log probability details

`choices.logprobs`

Log probability information of the current content.

**content** `object[]`  |  Message content

`choices.logprobs.content`

Log probability information of tokens in each content element in the message list.

**bytes** `integer[]`  |  Bytes

`choices.logprobs.content.bytes`

UTF\-8 value of the current token, in the format of an integer list. It can be used for character encoding and decoding when a character consists of multiple tokens (such as emojis or special characters). It is empty if the token has no UTF\-8 value.

**logprob** `number`  |  Log probability value

`choices.logprobs.content.logprob`

Log probability of the current token.

**token** `string`  |  Token

`choices.logprobs.content.token`

Current token.

**top_logprobs** `object[]`  |  Candidate probabilities

`choices.logprobs.content.top_logprobs`

A list of the most probable tokens and their log probabilities at the current token position. In some cases, the number of returned results may be less than the number specified by the request parameter top_logprobs.

**bytes** `integer[]`  |  Bytes

`choices.logprobs.content.top_logprobs.bytes`

UTF\-8 value of the current token, in the format of an integer list. It can be used for character encoding and decoding when a character consists of multiple tokens (such as emojis or special characters). It is empty if the token has no UTF\-8 value.

**logprob** `number`  |  Log probability value

`choices.logprobs.content.top_logprobs.logprob`

Log probability of the current token.

**token** `string`  |  Token

`choices.logprobs.content.top_logprobs.token`

Current token.

**message** `object`  |  message

`choices.message`

Content output by the model.

**content** `string`  |  Message content

`choices.message.content`

Message content generated by the model.

**role** `string`  |  Role

`choices.message.role`

Role of the output information. Fixed to `assistant`.

**encrypted_content** `string`  |  Encrypted reasoning content

`choices.message.encrypted_content`

Encrypted reasoning content. Original reasoning content that has been encrypted and compressed.

Related documents:

* [Key rules](agent-model-invocation-practice.md#key-rules)

* [Examples of returning encrypted reasoning content blocks](agent-model-invocation-practice.md#encrypted-thinking-block-examples)

**Supported models**:

* ``seed\-2\-0\-lite\-260228`and later versions`

**reasoning_content** `string`  |  Chain\-of\-thought content

`choices.message.reasoning_content`

Chain\-of\-thought content of the model processing the problem.

**Supported models**:

* `Seed series models`

* `deepseek-v4-pro`

* `deepseek-v4-flash`

**tool_calls** `object[]`  |  Tool calling

`choices.message.tool_calls`

Tool calls generated by the model.

**function** `object`  |  Function information

`choices.message.tool_calls.function`

The function called by the model.

**arguments** `string`  |  Function arguments

`choices.message.tool_calls.function.arguments`

Parameters generated by the model for calling the function in JSON format. The model does not always generate valid JSON, and may fabricate some parameters that are not defined in your function parameter specifications. Before calling the function, please verify the validity of these parameters in your code.

**name** `string`  |  Function name

`choices.message.tool_calls.function.name`

Name of the function called by the model.

**id** `string`  |  ID

`choices.message.tool_calls.id`

Tool call ID generated by the model. After calling the tool, the developer must return this value in `tool_call_id` of the subsequent tool message to associate the tool result with the corresponding tool call. This parameter is always returned in the response.

**type** `string`  |  Type

`choices.message.tool_calls.type`

Tool type. Currently only `function` is supported.

**moderation_hit_type** `string`  |  Moderation hit type

`choices.moderation_hit_type`

When the model output text contains sensitive information, the risk classification tag hit by the model output text will be returned.

* `severe_violation`: The model output text involves serious violations.

* `violence`: The model output text involves radical behavior.

> **Note**

> Note: Currently only [visual understanding models](image-understanding.md#f8d6cc48) support returning this parameter. The risk classification tag will only be returned when ModerationStrategy is set to basic in the [endpoint creation page](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint/create?customModelId=) of the ModelArk console or the CreateEndpoint API.

**created** `integer`  |  Creation time

UNIX timestamp (in seconds) of the request creation time.

**id** `string`  |  Request ID

Unique identifier of the request.

**model** `string`  |  Model name

The name and version of the model actually used for this request.

**object** `string`  |  Object type

Fixed to `chat.completion`.

**service_status** `object`  |  Service status

Service status information attached to this request, such as whether model fallback was triggered.

**model_fallback** `object`  |  Model fallback status

`service_status.model_fallback`

Status information related to model fallback.

**fallback_triggered** `boolean`  |  Whether fallback is triggered

`service_status.model_fallback.fallback_triggered`

Whether model fallback was triggered for this request.

**original_model** `string`  |  Original model

`service_status.model_fallback.original_model`

The model name originally specified in the request before fallback was triggered.

**service_tier** `string`  |  Inference mode

The inference mode actually used for this request.

* `default`: This request uses the [Online inference (standard)](online-inference-standard.md) mode.

* `flex`: This request uses the [online inference (flex)](online-inference-flex.md) mode.

**usage** `object`  |  Token usage

Token usage of this request.

**completion_tokens** `integer`  |  Total output tokens

`usage.completion_tokens`

Number of tokens consumed by the model output.

**completion_tokens_details** `object`  |  Output token details

`usage.completion_tokens_details`

Details of the number of tokens consumed by the model output.

**reasoning_tokens** `integer`  |  Chain\-of\-thought tokens

`usage.completion_tokens_details.reasoning_tokens`

Number of tokens consumed by the output chain\-of\-thought content. For models that support outputting chain\-of\-thought, see [documentation](deep-thinking.md#14b5c6db).

**prompt_tokens** `integer`  |  Total input tokens

`usage.prompt_tokens`

Number of tokens of the content input to the model for processing.

**prompt_tokens_details** `object`  |  Input token details

`usage.prompt_tokens_details`

Details of the number of tokens of the content input to the model for processing.

**cached_tokens** `integer`  |  Cache hit tokens

`usage.prompt_tokens_details.cached_tokens`

Total number of tokens consumed by the cached input content (including all types such as text, audio, etc.).

**audio_cached_tokens** `integer`  |  Audio cached tokens

`usage.prompt_tokens_details.audio_cached_tokens`

Number of tokens consumed by the cached audio input content. Returned only in implicit cache mode when the audio cache is hit; this parameter is not returned when explicit cache is enabled.

**audio_tokens** `integer`  |  Audio tokens

`usage.prompt_tokens_details.audio_tokens`

Number of tokens consumed by audio input content.

**total_tokens** `integer`  |  Total tokens

`usage.total_tokens`

Total number of tokens consumed by this request (input + output).

### Streaming output

**choices** `object[]`  |  Generated results

Model output content of this request.

**delta** `object`  |  Incremental message

`choices.delta`

Incremental content output by the model.

**content** `string`  |  Message content

`choices.delta.content`

Message content generated by the model.

**encrypted_content** `string`  |  Encrypted reasoning content

`choices.delta.encrypted_content`

Encrypted reasoning content. Original reasoning content that has been encrypted and compressed.

Related documents:

* [Key rules](agent-model-invocation-practice.md#key-rules)

* [Examples of returning encrypted reasoning content blocks](agent-model-invocation-practice.md#encrypted-thinking-block-examples)

> **Tip**

> In streaming output scenarios, data containing the complete `encrypted_content` will be output after the reasoning content is completely output and before the official response content is output. In this case, both the `content` and `reasoning_content` parameters are empty.

**Supported models**:

* ``seed\-2\-0\-lite\-260228`and later versions`

**reasoning_content** `string`  |  Chain\-of\-thought content

`choices.delta.reasoning_content`

Original text of the reasoning content.

Starting from version `seed-2-0-lite-260428`, this parameter is used to return the summary of reasoning content.

> **Note**

> For time\-consuming scenarios such as long text generation and deep reasoning, it is recommended to appropriately increase the first token timeout (TTFT) and per\-token generation timeout (TPOT) to prevent requests from being interrupted due to timeout.

**Supported models**:

* `Seed series models`

* `deepseek-v4-pro`

* `deepseek-v4-flash`

**role** `string`  |  Role

`choices.delta.role`

Role of the output information. Fixed to `assistant`.

**tool_calls** `object[]`  |  Tool calling

`choices.delta.tool_calls`

Tool calls generated by the model.

**index** `integer`  |  Index

`choices.delta.tool_calls.index`

Index position of the tool call in the array.

**function** `object`  |  Function information

`choices.delta.tool_calls.function`

The function called by the model.

**arguments** `string`  |  Function arguments

`choices.delta.tool_calls.function.arguments`

Parameters generated by the model for calling the function in JSON format. The model does not always generate valid JSON, and may fabricate some parameters that are not defined in your function parameter specifications. Before calling the function, please verify the validity of these parameters in your code.

**name** `string`  |  Function name

`choices.delta.tool_calls.function.name`

Name of the function called by the model.

**id** `string`  |  ID

`choices.delta.tool_calls.id`

ID of the called tool.

**type** `string`  |  Type

`choices.delta.tool_calls.type`

Tool type. Currently only `function` is supported.

**index** `integer`  |  Index

`choices.index`

The index of the current element in the **choices** list.

**finish_reason** `string`  |  Finish reason

`choices.finish_reason`

The reason why the model stopped generating tokens.

* `stop`: The model output ends naturally, or is truncated because it hits the parameter specified in the request parameter `stop`.

* `length`: The model output is truncated because it reaches the model output limit, for the following reasons:

   * The `max_tokens` limit is triggered (length limit of the answer content).

   * The `max_completion_tokens` limit is triggered (length limit of chain\-of\-thought content + answer content).

   * The `context_window` limit is triggered (length limit of input content + chain\-of\-thought content + answer content).

* `content_filter`: The model output is intercepted by content moderation.

* `tool_calls`: The model called tools.

**logprobs** `object`  |  Choice log probability details

`choices.logprobs`

Log probability information of the current content.

**content** `object[]`  |  Message content

`choices.logprobs.content`

Log probability information of tokens in each content element in the message list.

**bytes** `integer[]`  |  Bytes

`choices.logprobs.content.bytes`

UTF\-8 value of the current token, in the format of an integer list. It can be used for character encoding and decoding when a character consists of multiple tokens (such as emojis or special characters). It is empty if the token has no UTF\-8 value.

**logprob** `number`  |  Log probability value

`choices.logprobs.content.logprob`

Log probability of the current token.

**token** `string`  |  Token

`choices.logprobs.content.token`

Current token.

**top_logprobs** `object[]`  |  Candidate probabilities

`choices.logprobs.content.top_logprobs`

A list of the most probable tokens and their log probabilities at the current token position. In some cases, the number of returned results may be less than the number specified by the request parameter top_logprobs.

**bytes** `integer[]`  |  Bytes

`choices.logprobs.content.top_logprobs.bytes`

UTF\-8 value of the current token, in the format of an integer list. It can be used for character encoding and decoding when a character consists of multiple tokens (such as emojis or special characters). It is empty if the token has no UTF\-8 value.

**logprob** `number`  |  Log probability value

`choices.logprobs.content.top_logprobs.logprob`

Log probability of the current token.

**token** `string`  |  Token

`choices.logprobs.content.top_logprobs.token`

Current token.

**moderation_hit_type** `string`  |  Moderation hit type

`choices.moderation_hit_type`

When the model output text contains sensitive information, the risk classification tag hit by the model output text will be returned.

* `severe_violation`: The model output text involves serious violations.

* `violence`: The model output text involves radical behavior.

> **Note**

> Note: Currently only [visual understanding models](image-understanding.md#f8d6cc48) support returning this parameter. The risk classification tag will only be returned when ModerationStrategy is set to basic in the [endpoint creation page](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint/create?customModelId=) of the ModelArk console or the CreateEndpoint API.

**created** `integer`  |  Creation time

UNIX timestamp (in seconds) of the request creation time.

**id** `string`  |  Request ID

Unique identifier of the request.

**model** `string`  |  Model name

The name and version of the model actually used for this request.

**object** `string`  |  Object type

Fixed to `chat.completion.chunk`.

**service_status** `object`  |  Service status

Service status information attached to this request, such as whether model fallback was triggered.

**model_fallback** `object`  |  Model fallback status

`service_status.model_fallback`

Status information related to model fallback.

**fallback_triggered** `boolean`  |  Whether fallback is triggered

`service_status.model_fallback.fallback_triggered`

Whether model fallback was triggered for this request.

**original_model** `string`  |  Original model

`service_status.model_fallback.original_model`

The model name originally specified in the request before fallback was triggered.

**service_tier** `string`  |  Inference mode

The inference mode actually used for this request.

* `default`: This request uses the [Online inference (standard)](online-inference-standard.md) mode.

* `flex`: This request uses the [online inference (flex)](online-inference-flex.md) mode.

**usage** `object`  |  Token usage

Token usage of this request.

Default behavior:

* Some models return the token usage information for this request by default before the output ends. Applicable models: `glm-5-3-flash-260828`, `glm-5-2-260617`, `deepseek-v4-1-flash-261017`, `deepseek-v4-1-flash-260910`, `deepseek-v4-1-flash-economy-260910`, `deepseek-v4-1-flash-economy-261017`, `deepseek-v4-pro-ga-260813`, and `deepseek-v4-flash-ga-260731`. Among them, `deepseek-v4-1-flash-261017` and `deepseek-v4-1-flash-economy-261017` return the token usage information for this request by default in the chunk that contains `finish_reason`.

* Other models: By default, token usage information is not calculated, and the return value is `null`. If you need to count token usage, set `stream_options.include_usage` to `true`.

**completion_tokens** `integer`  |  Total output tokens

`usage.completion_tokens`

Number of tokens consumed by the model output.

**completion_tokens_details** `object`  |  Output token details

`usage.completion_tokens_details`

Details of the number of tokens consumed by the model output.

**reasoning_tokens** `integer`  |  Chain\-of\-thought tokens

`usage.completion_tokens_details.reasoning_tokens`

Number of tokens consumed by the output chain\-of\-thought content. For models that support outputting chain\-of\-thought, see [documentation](deep-thinking.md#14b5c6db).

**prompt_tokens** `integer`  |  Total input tokens

`usage.prompt_tokens`

Number of tokens of the content input to the model for processing.

**prompt_tokens_details** `object`  |  Input token details

`usage.prompt_tokens_details`

Details of the number of tokens of the content input to the model for processing.

**cached_tokens** `integer`  |  Cache hit tokens

`usage.prompt_tokens_details.cached_tokens`

Total number of tokens consumed by the cached input content (including all types such as text, audio, etc.).

**audio_cached_tokens** `integer`  |  Audio cached tokens

`usage.prompt_tokens_details.audio_cached_tokens`

Number of tokens consumed by the cached audio input content. Returned only in implicit cache mode when the audio cache is hit; this parameter is not returned when explicit cache is enabled.

**audio_tokens** `integer`  |  Audio tokens

`usage.prompt_tokens_details.audio_tokens`

Number of tokens consumed by audio input content.

**total_tokens** `integer`  |  Total tokens

`usage.total_tokens`

Total number of tokens consumed by this request (input + output).
