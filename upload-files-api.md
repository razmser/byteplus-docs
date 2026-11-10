# Upload file

`POST https://ark.ap-southeast.bytepluses.com/api/v3/files`

Upload files to ModelArk as input for subsequent model calls, such as images, videos, and documents. Files expire and are deleted after a certain period of time.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Body parameters

**file** `object`  |  Uploaded file

The file to upload. It must be a binary file. For specific limits, see the Files API tutorial.

> **Note**

> File upload supports two input methods: binary file and file URL. `file` and `url` are mutually exclusive required parameters and cannot be passed at the same time.

**purpose** `string` `Required`  |  The file purpose

The file purpose.

* `user_data`: A file that can be used for any purpose flexibly.

* `agent`: Files used by agents.

**url** `string`  |  File URL

File URL. The following two URL types are supported:

* HTTP/HTTPS URL: A file URL that can be directly accessed over the public internet. Make sure the file URL is accessible.

* TOS URI: A resource location identifier specific to TOS, in the format `tos://<bucket>/<prefix>/<file_name>`. Here, `<bucket>`, `<prefix>`, and `<file_name>` represent the bucket name, storage path, and file name, respectively. For detailed usage instructions, see the [Upload via TOS URI](file-api.md#tos-uri-upload) section. You can also go to [BytePlus TOS bucket](https://console.byteplus.com/tos/bucket?projectName=default) to manage buckets.

> **Note**

* > File upload supports two input methods: binary file and file URL. `file` and `url` are mutually exclusive required parameters and cannot be passed at the same time.

* > When passing a TOS URI, you must store the file in a BytePlus TOS bucket. In this case, `tos.bucket` and `tos.prefix` are required parameters.

**tos** `object`  |  TOS storage configuration

The TOS information where the file is stored. TOS is BytePlus Object Storage. After this parameter is configured, uploaded files are stored in the user\-specified BytePlus TOS bucket, instead of the default storage space of ModelArk.

> **Note**

> If the `tos` parameter is not passed: The file is stored in the default storage space managed by ModelArk, with a maximum size of 512 MB per file. Historical files already stored in the managed storage space of ModelArk are not automatically migrated to other storage locations.

* > If the `tos` parameter is passed: The file is stored in the user\-specified BytePlus TOS bucket. Video files can be up to 2 GB, and other file types can be up to 512 MB. For supported formats, see [File types](file-api.md#81920512). Files that are authorized to be stored in user\-specified TOS buckets cannot be deleted or modified on the TOS console. To delete files, call the Files API. See [Delete files](delete-files-api.md) for more information.

**bucket** `string`  |  Bucket name

`tos.bucket`

The name of the TOS bucket to which the target file will be stored in. The bucket must be created in advance. See [Creating a bucket](https://docs.byteplus.com/en/docs/tos/docs-create-a-bucket).

**prefix** `string`  |  Storage path prefix

`tos.prefix`

The prefix path where the file is stored in the target TOS bucket. Enter a relative path as input. After upload, the file will be stored in this prefix directory. Example: `arkfiles/`.

**preprocess_configs** `object`  |  Preprocessing configuration

Used to set preprocessing rules for different file types.

**video** `object`  |  Video preprocessing configuration

`preprocess_configs.video`

Configurations related to video preprocessing.

> **Note**

> After the `model` parameter is configured, the system automatically fills in the default values of the `max_video_tokens`, `min_frame_tokens`, `max_frame_tokens`, and `min_frames` parameters based on the frame sampling strategy of the corresponding model. For specific values, see [Frame sampling strategy](video-understanding.md#203baa92).

**fps** `number` `Default 1`  |  Frame sampling rate

`preprocess_configs.video.fps`

The number of frames to be extracted from the video per second. The higher the value, the more sensitive it is to frame changes in the video. The lower the value, the less sensitive it is to frame changes in the video, but it consumes fewer tokens and runs faster.

> **Note**

> Value range: `[0.2, 5]`. The token usage range for a single video is `[10k, 80k]`.

**Range**: `[0.2, 5]`

**model** `string`  |  Preprocessing model

`preprocess_configs.video.model`

The video understanding model ID or endpoint ID used for inference with this file.

> **Note**

> The model ID set in the Files API is not strongly coupled with the model ID used for inference. It only affects the preprocessing frame sampling strategy when uploading video files. For the preprocessing frame sampling strategy, see [Frame sampling strategy](video-understanding.md#203baa92).

* > Pass a model ID: Different model IDs use different frame sampling strategies.

* > Pass an endpoint ID: Frames are sampled based on the frame sampling strategy of the model mapped to the endpoint ID at upload time.

* > If this parameter is not passed: By default, the frame sampling strategy for models earlier than `seed-1-8` is used.

> **Note**

> To process audio content in videos, set this parameter to a model that supports audio understanding. For supported models, see [Model list](model-list.md#9619c0ba).

**max_video_tokens** `integer`  |  Maximum tokens of the video

`preprocess_configs.video.max_video_tokens`

The maximum number of video tokens. A larger value preserves more video information.

**Range**: `[10240, 204800]`

**min_frame_tokens** `integer`  |  Minimum tokens per frame

`preprocess_configs.video.min_frame_tokens`

The minimum number of tokens for a single frame, used to control the lower limit of single\-frame compression. A smaller value allows greater compression for a single frame, but reduces single\-frame details.

**Range**: `[16, 128]`

**max_frame_tokens** `integer`  |  Maximum tokens per frame

`preprocess_configs.video.max_frame_tokens`

The maximum number of tokens for a single frame, used to control the upper limit of single\-frame quality. A larger value allows more details to be retained for a single frame.

**Range**: `[128, 640]`

**min_frames** `integer`  |  Minimum number of sampled frames

`preprocess_configs.video.min_frames`

The minimum number of frames to sample, used to ensure that short videos have enough frames for processing. If the total number of frames sampled by the `fps` parameter is less than `min_frames`, frame sampling is performed first according to the `min_frames` configuration.

**Range**: `[5, 16]`

**expire_at** `integer`  |  File expiration time

Set the storage validity period. You must pass a UTC Unix timestamp (unit: seconds). Retained for at least 1 day and at most 30 days. If this parameter is not passed, files are retained for 7 days by default (current time + 604800).

> **Note**

> Value range: `[current time + 86400, current time + 2592000]`, meaning the file is retained for at least 1 day and at most 30 days. If this parameter is not passed, files are retained for 7 days by default (current time + 604800).

## Response parameters

**object** `string`  |  Object type

Object type. Fixed to `file`.

**id** `string`  |  File ID

The unique identifier of the file.

**purpose** `string`  |  The file purpose

The file purpose.

* `user_data`: A file that can be used for any purpose flexibly.

* `agent`: Files used by agents.

**scope** `object`  |  Information about the scope to which the file belongs

The scope information of the file, used to identify the session context that the file belonged to when it was created.

**type** `string`  |  Scope type

`scope.type`

The scope type, which is currently fixed to `session`.

**id** `string`  |  Scope ID

`scope.id`

The scope ID, which returns the corresponding session ID.

**filename** `string`  |  Filename

Filename.

**tos** `object`  |  The TOS information where the file is stored

The TOS information where the file is stored. TOS is BytePlus Torch Object Storage and is returned only when the user passes the `tos` parameter.

**bucket** `string`  |  The name of the TOS bucket in which the target file is to be stored

`tos.bucket`

The name of the TOS bucket to which the target file is to be stored in.

**object_key** `string`  |  The path information of the file in TOS

`tos.object_key`

The path information of the file in TOS.

**bytes** `integer`  |  File size

The file size in bytes.

**created_at** `integer`  |  Creation time

The Unix timestamp (in seconds) when the file was uploaded in this request.

**expire_at** `integer`  |  Expiration time

The Unix timestamp (in seconds) of the file expiration time.

**mime_type** `string`  |  MIME type

The MIME type of the file, such as `application/pdf`.

**status** `string`  |  The file processing status

The file processing status.

* `processing`: The file is being preprocessed and cannot be used.

* `active`: The file has been processed and can be used.

* `failed`: The file upload failed. See the `error` parameter for error details.

**download_url** `string`  |  Download URL

A temporary presigned URL generated by the server for downloading the original uploaded file.

* When `purpose` is `agent`, `download_url` is returned by default.

* `download_url` is valid for 24 hours and can be used for up to 100 downloads.

* The validity period of `download_url` is independent of the file storage validity period `expire_at`. Even if the file is still within its storage validity period, `download_url` may become unavailable because the link has expired or the download limit has been reached.

> **Note**

> This URL contains temporary access credentials. Provide it only to authorized users, and avoid writing it to logs, caching it long term, or sharing it publicly.

**error** `object`  |  Error information

The error object returned when a file upload fails. This parameter is returned only when `status` is `failed`.

**code** `string`  |  Error code

`error.code`

Error code.

**message** `string`  |  Error description

`error.message`

Error description.

**preprocess_configs** `object`  |  Preprocessing configuration

Used to set preprocessing rules for different file types.

**video** `object`  |  Video preprocessing configuration

`preprocess_configs.video`

Configurations related to video preprocessing.

**fps** `number`  |  Frame sampling rate

`preprocess_configs.video.fps`

The number of frames to be extracted from the video per second. The higher the value, the more sensitive it is to frame changes in the video. The lower the value, the less sensitive it is to frame changes in the video, but it consumes fewer tokens and runs faster.

> **Note**

> Value range: `[0.2, 5]`. The token usage range for a single video is `[10k, 80k]`.

**Range**: `[0.2, 5]`

**model** `string`  |  Preprocessing model

`preprocess_configs.video.model`

The video understanding model ID or endpoint ID used for inference with this file.

> **Note**

> The model ID set in the Files API is not strongly coupled with the model ID used for inference. It only affects the preprocessing frame sampling strategy when uploading video files. For the preprocessing frame sampling strategy, see [Frame sampling strategy](video-understanding.md#203baa92).

* > Pass a model ID: Different model IDs use different frame sampling strategies.

* > Pass an endpoint ID: Frames are sampled based on the frame sampling strategy of the model mapped to the endpoint ID at upload time.

* > If this parameter is not passed: By default, the frame sampling strategy for models earlier than `seed-1-8` is used.

> **Note**

> To process audio content in videos, set this parameter to a model that supports audio understanding. For supported models, see [Model list](model-list.md#9619c0ba).

**max_video_tokens** `integer`  |  Maximum tokens of the video

`preprocess_configs.video.max_video_tokens`

The maximum number of video tokens. A larger value preserves more video information.

**Range**: `[10240, 204800]`

**min_frame_tokens** `integer`  |  Minimum tokens per frame

`preprocess_configs.video.min_frame_tokens`

The minimum number of tokens for a single frame, used to control the lower limit of single\-frame compression. A smaller value allows greater compression for a single frame, but reduces single\-frame details.

**Range**: `[16, 128]`

**max_frame_tokens** `integer`  |  Maximum tokens per frame

`preprocess_configs.video.max_frame_tokens`

The maximum number of tokens for a single frame, used to control the upper limit of single\-frame quality. A larger value allows more details to be retained for a single frame.

**Range**: `[128, 640]`

**min_frames** `integer`  |  Minimum number of sampled frames

`preprocess_configs.video.min_frames`

The minimum number of frames to sample, used to ensure that short videos have enough frames for processing. If the total number of frames sampled by the `fps` parameter is less than `min_frames`, frame sampling is performed first according to the `min_frames` configuration.

**Range**: `[5, 16]`
