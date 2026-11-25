# CasePlatformV1BadcaseReport - BytePlus ModelArk Seedance 2.x Generations Quality Diagnostics API

`POST https://prompt-pilot.ap-southeast.bytepluses.com/case-platform?Action=CasePlatformV1BadcaseReport&Version=2024-01-01`

## Usage

After generating a video with Seedance 2.x, you can use this API to report the corresponding task_id as a badcase.

This API retrieves the original task data from upstream using `task_id`, so reports must be submitted within the **3\-day (72\-hour)**  window. BytePlus cleans up task logs on the hour; plan to submit within **72 hours**. Once the logs expire, ModelArk may no longer be able to retrieve the task data needed to accept and investigate the report. Enable Model Generations Quality Diagnostics before generating the task, as described below.

> **Note**

> BytePlus does not store the detailed logs of your inference tasks by default. To let BytePlus ModelArk investigate a quality issue from a task ID alone, the corresponding request logs must be retained for a short period, and that requires your explicit consent.

> **Enable** [Model Generations Quality Diagnostics](model-diagnostic-service-acknowledgement.md) **before using this API.** In the ModelArk console, open **Model Generations Quality Diagnostics** from the console home page, turn the feature on, and accept the Model Generations Quality Diagnostics agreement. Only users holding the arkfullaccess permission can change this setting. Reporting for an account that has not opted in returns error code 70001.

> What the agreement covers:

* > **Scope** — asynchronous inference models only (Seedance 2.x, and similar). Synchronous inference models such as Seed and Seedream are **not** included.

* > **Retention** — all asynchronous task logs under your primary account are stored for **3 days (72 hours)** in Johor, and dropped afterwards.

* > **Opting out** — ingestion stops immediately. Data already stored is not deleted on the spot; it clears naturally within 3 days.

* > **Reported cases** — a task you submit through this API is retained long term and transferred across borders. It is used for model evaluation, regression and analysis. **It is not used to train models.**

   > For the full terms, see the [Model Generations Quality Diagnostics agreement](model-diagnostic-service-acknowledgement.md).

   > **This API and its documentation apply to the BytePlus ModelArk service.**

### Case types and the incentive program

Choose `CaseType` according to the purpose of your feedback and decide whether to participate in the incentive program.

|Case type |Purpose |Model requirements |Incentive program |
|---|---|---|---|
|`Badcase` |Report a quality issue for investigation. |Supported Seedance models. |Participates when `JoinIncentive=true` for Seedance 2.5 or later; earlier models do not participate. |
|`Goodcase` |Report a good\-quality video without investigation. |Seedance 2.5 or later only. |All Goodcases participate, regardless of `JoinIncentive`. |

When reporting a Goodcase or participating in the incentive program, `Feedback` is required and the task must belong to the caller's account.

### Report by Session

You can combine multiple tasks from a continuous creation session into a single Session case, with each task representing one turn. Each request can report only one Session containing 1 to 50 tasks.

**Before reporting**

* All tasks belong to the same account and use the same model.

* Every task successfully generated a video and is neither a draft nor a final\-video task.

* Task IDs are unique and have not previously been reported individually or as a turn in another Session.

* All tasks satisfy the reporting window, account, and other validation requirements.

**Build the request**

1. Set `IdType` to `session`.

2. Populate `Id` in generation order. The first task is turn 1.

3. Provide feedback. Tasks in the same Session share `Feedback`, `FeedbackImages`, `BadcaseType`, `ContactInfo`, `Source`, `AccountId`, `CaseType`, and `JoinIncentive`. For timeline feedback, use `TimelineFeedbacks[].TaskId` to identify the turn. Times are relative to that turn's video.

**Check acceptance**

The service creates a single case only after all tasks pass validation. Check the response as follows:

|Result |Response and resolution |
|---|---|
|Report accepted |Each task has its own entry in `Accepted`. All entries share the same `CaseId`, and `TurnNo` starts at 1 in `Id` order. |
|Task validation failed |`Accepted` is empty and all tasks appear in `Rejected`. Tasks that failed validation return their specific error codes; the others return `40040`. Match results by `TaskId`; do not rely on the order of `Rejected`. |
|Save failed |Returns `40046`. The entire Session was not saved. Retry later. |
|Session reporting is unavailable in this environment |Returns `40038`. Use `task` reporting instead. |

After a successful report, tasks cannot be reported individually or added to another Session. Repeated reports return `40003`.

### Request headers

For ModelArk API key authentication, set `Authorization: Bearer <API_KEY>` and `Content-Type: application/json`. You can omit `AccountId` from the request body.

For AK/SK authentication, set the `Authorization`, `X-Date`, and `X-Content-Sha256` headers required for V4 signing. Use `ark` for `Service` and `ap-southeast-1` for `Region`, and include `AccountId` in the request body.

### Request Instructions

### Calling with a BytePlus ModelArk API Key

Obtain the API Key from [API Key Management](https://console.byteplus.com/ark/region:ap-southeast-1/apiKey). **Calling with a ModelArk API Key uses a different request scheme than AK/SK: the request URL is not https://open.byteplusapi.com but the Case Platform route below, and you must add the /case\-platform path.**

The host is bytepluses.com — note the trailing s.

**The API Key must match the entry you call it from.**  ModelArk API Keys and Volcengine Ark API Keys are registered in separate control planes (ap\-southeast\-1 and cn\-beijing). The backend verifies a key against the control plane of the entry the request arrived through, so a ModelArk API Key is not resolvable from the Volcengine entry and authentication fails outright — and the reverse is equally true. This is the most common cause of an authentication failure on this API.

**This entry exposes three Actions only** — CasePlatformV1BadcaseReport, CasePlatformV1BadcaseQuery and CasePlatformV1AuthCheckAccess. Any other Action, and any request that omits the Action parameter, is rejected at the gateway with a plain HTTP 404 that is **not** a TOP response envelope.

Requests arriving through this entry are resolved to the BytePlus data source automatically — you do not need to specify a site.

### Calling via AK/SK

See [Access Key](https://docs.byteplus.com/en/docs/byteplus-platform/docs-managing-keys) to obtain your BytePlus AK/SK. The corresponding sub\-account must have the CasePlatformAccess policy enabled. Sign the request with service ark and region ap\-southeast\-1, and send it to https://open.byteplusapi.com.

## Connectivity check

CasePlatformV1AuthCheckAccess confirms that your entry and API Key work end to end without creating any data. Result.Success: true means the path is healthy, and ResponseMetadata.Region echoes the region the request was resolved to.

```Bash
curl --location 'https://prompt-pilot.ap-southeast.bytepluses.com/case-platform?Action=CasePlatformV1AuthCheckAccess' \
--header 'content-type: application/json' \
--header 'Authorization: Bearer ModelArk_API_KEY' \
--data-raw '{}'
# => {"ResponseMetadata":{...,"Region":"ap-southeast-1","Error":null},"Result":{"Success":true}}
```

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* [Recommended] API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Query parameters

> Passed as URL query string.

**Action** `string` `Required`  |    The operation to perform

Value: CasePlatformV1BadcaseReport.

**Version** `string` `Required`  |  API version

Value: 2024\-01\-01.

**ProjectName** `string` `Default default`  |  Project name

The project name. If the resource is not in the default project, provide its actual project name.

### Body parameters

**Feedback** `string`  |  Feedback description

Example: "Character hands are distorted, and lip movement does not match the dubbed audio". Problem description, 1–2000 characters; whitespace\-only strings are rejected. Persisted as a feedback annotation.

Provide at least one of `Feedback` and `TimelineFeedbacks`; otherwise, error code `40014` is returned. When reporting a Goodcase or participating in the incentive program, `Feedback` is required; timeline feedback alone is insufficient.

**Id** `string / string[]` `Required`  |  Task ID list

Example: cgt\-20260501132104\-tfm9v. A single task_id, or a list of task_ids. At least 1, at most 50 — exceeding the limit returns 422 directly. The interface normalizes the submitted task_ids: it accepts either a single string or a list of strings, splits on comma, newline, space, tab and similar separators into a unified task_id list, and trims leading/trailing whitespace and empty fragments.

When `IdType` is `session`, provide the task IDs for one Session in turn order, starting with turn 1. Each request can report only one Session, also containing 1 to 50 tasks.

**AccountId** `string`  |  Account ID

Example: 2000000000. The BytePlus account ID the Id (task_id) belongs to. Required when using AK/SK. If omitted when using a ModelArk API Key, the account ID is resolved from the API Key.

**BadcaseType** `string / string[]`  |  Badcase types

    Example: character_id_drift. Must be one of the enum values listed below; an invalid value returns a 422 parameter validation error for the whole request. Note that task_id\-based import currently supports Seedance 2.x tasks; if the model behind the task_id is out of scope, the backend returns a business error (for example 60002).

BadcaseType is accepted only when the task's model ID is one of the following:

* dreamina\-seedance\-2\-5\-260628

* dreamina\-seedance\-2\-0\-260128

* dreamina\-seedance\-2\-0\-fast\-260128

* dreamina\-seedance\-2\-0\-mini\-260615

|**Enum value** |**Meaning** |
|---|---|
|character_id_drift |Character ID drift |
|subtitle_generation_error |Subtitle generation error |
|logo_watermark_generated |Logo/watermark generated |
|style_drift |Style drift |
|regular_flickering |Regular/periodic flickering |
|extended_video_joint_jump |Jump\-cut artifact at the extended\-video splice point |
|duplicate_character_appearance |Duplicate character ("twin") appearance |
|audio_end_noise |Noise at the end of the audio |
|chinese_pronunciation_error |Inaccurate pronunciation in the generated speech |
|voice_tone_reference_error |Inaccurate voice/tone reference |
|other |Other |

Provide a single enum value or an array of enum values. Options: `character_id_drift`, `subtitle_generation_error`, `logo_watermark_generated`, `style_drift`, `regular_flickering`, `extended_video_joint_jump`, `duplicate_character_appearance`, `audio_end_noise`, `chinese_pronunciation_error`, `voice_tone_reference_error`, `other`.

**Supported models**:

* `dreamina-seedance-2-5-260628`

* `dreamina-seedance-2-0-260128`

* `dreamina-seedance-2-0-fast-260128`

* `dreamina-seedance-2-0-mini-260615`

**CaseType** `string` `Default Badcase`  |  Case type

The type of case. Values:

* **`Badcase`**: Report a quality issue for investigation.

* **`Goodcase`**: Report a good\-quality video without investigation. Supported only for Seedance 2.5 or later. All Goodcases participate in the incentive program, and `Feedback` is required.

Defaults to `Badcase` if omitted or `null`.

**ContactInfo** `string`  |  Contact information

Example: user@example.com. Contact info (email or phone), up to 255 characters.

**FeedbackImages** `object[]`  |  Issue screenshots

Optional screenshots of the output, up to 5 images, shared by all tasks in the request. If an entry format, image count, or image reference is invalid, the entire request is rejected with error code `40015`.

The service downloads HTTPS images asynchronously after acceptance. Download failures do not prevent case creation. Use [Query video generation quality issue results](case-platform-v1-badcase-query-api.md) to check each screenshot's download status and failure reason.

**Url** `string` `Required`  |  Screenshot URL

`FeedbackImages.Url`

Image references support the following forms:

* An object uploaded to the platform bucket through the presigned upload API: use a tos:// path or a presigned GET URL.

* Another direct, publicly accessible HTTPS image URL: PNG, JPEG, and WebP are supported, as identified from the actual file contents. Each image must be no larger than 10 MB. data: inline images are not supported.

HTTPS image URLs must remain accessible for at least 24 hours after reporting.

**IdType** `string` `Default task`  |  Reporting mode

How tasks are organized. Values:

* **`task`**: Create a separate case for each task.

* **`session`**: Save multiple tasks as one multi\-turn case, in `Id` order. The entire Session is accepted only after every task passes validation.

Defaults to `task` if omitted or `null`.

**JoinIncentive** `boolean`  |  Incentive participation

Whether to participate in the case incentive program. Applies only to Seedance 2.5 or later.

* **`Badcase`**: Participates when set to `true`.

* **`Goodcase`**: All Goodcases participate regardless of this field.

Tasks from earlier models can still be reported but do not participate in the incentive program. Participation requires `Feedback`, and the task must belong to the caller's account.

**Source** `string` `Default customer`  |  Feedback source

The source of the generation quality feedback. Values:

* `huosu`: Huosu assistant

* `huoshan`: Huoshan assistant

* `qianzhi`: Qianzhi

* `customer`: Customer submission

Defaults to `customer` if omitted or `null`.

**TimelineFeedbacks** `object[]`  |  Video timeline feedback

Video timeline feedback. Each entry identifies its task using `TaskId`. Up to 20 entries are retained per task. Entries that exceed the limit or fail validation are returned in `RejectedTimelineFeedbacks`, without affecting creation of the main case record.

Provide at least one of `Feedback` and `TimelineFeedbacks`. When reporting by Session, each entry refers to the task for its turn, and times are relative to that turn's video.

**EndTime** `number` `Required`  |  The end of the time range, in seconds

`TimelineFeedbacks.EndTime`

Must be greater than or equal to `StartTime`. Equal values represent a single point in time. Whether it exceeds the video duration is not validated.

**Feedback** `string` `Required`  |  The quality issue description for this time range

`TimelineFeedbacks.Feedback`

containing 1 to 2000 characters and not consisting only of whitespace.

**StartTime** `number` `Required`  |  The start of the time range, in seconds

`TimelineFeedbacks.StartTime`

Must be non\-negative, with a precision of 0.01 seconds.

**TaskId** `string` `Required`  |  The associated video task ID

`TimelineFeedbacks.TaskId`

Must appear in `Id` in this request.

**BadcaseType** `string / string[]`  |  The video quality issue types for this time range

`TimelineFeedbacks.BadcaseType`

Optional. See the top\-level `BadcaseType` for values.

### Error Codes

The table below lists the business\-logic error codes for this interface.

|**HTTP status** |**Error code** |**Error message** |**Description** |
|---|---|---|---|
|200 |40002 |task_id does not exist or does not belong to the current account_id |task_id does not exist, or exists but belongs to a different AccountId / the account tied to the API Key |
|200 |40003 |task_id has already been submitted as a badcase |task_id was submitted as a badcase previously |
|200 |40004 |task_id is still being generated; please wait for it to complete before submitting a badcase |The task behind task_id has not finished generating |
|200 |40005 |task_id has already been submitted as a badcase |The same task_id appeared more than once in this request |
|200 |70001 |This Account did not opt\-in data Diagnostic agreement yet. Please opt\-in ModelArk Data Diagnostic Agreement. |The account that owns this task has not enabled Model Generations Quality Diagnostics, so its inference data was never retained. **Evaluated per task_id and returned inside Rejected** — tasks owned by opted\-in accounts in the same batch are still accepted. Enable it as described under Notes. |
|200 |60002 |See the error message returned by the API. |The requested `ModelId` or the model actually used by the task is not supported. Check the supported model range. |
|200 |60003 |The actual model behind task_id does not match the ModelId in the request; the correct ModelId should be … |The ModelId in the request does not match the model resolved from task_id |
|400 |40006 |AccountId is required when not authenticating with a ModelArk API key |Not authenticated with a ModelArk API Key, and AccountId was not provided in the request body |
|422 |40007 |account_id must not be empty or whitespace |AccountId was provided but is empty or all whitespace |
|422 |40008 |model_id must not be empty or whitespace |ModelId was provided but is empty or all whitespace |
|422 |40009 |feedback must not be empty or whitespace |Feedback is empty or all whitespace |
|422 |40010 |"badcase_type list must contain at least one value" or "badcase_type contains invalid value ''. Allowed values:" |BadcaseType was passed as an empty list, or with an invalid enum value |
|422 |40011 |"id must contain at least one task_id" or "id may contain at most 50 task_ids" |After splitting, Id contains no valid task_id, or more than 50 task_ids |
|422 |40014 |at least one of feedback and timeline_feedbacks must be provided |Neither `Feedback` nor `TimelineFeedbacks` is provided. Provide at least one of them. |
|200 |40037 |See the error message returned by the API. |`CaseType` is `Goodcase`, but the task uses a model earlier than Seedance 2.5. Check the supported model range. |
|400 |40038 |See the error message returned by the API. |`IdType` is `session`, but Session reporting is not yet available in this environment. The request is rejected before validation and writing. Use `task` reporting instead. |
|200 |40039 |See the error message returned by the API. |When reporting by Session, the task count is outside the range of 1 to 50. Adjust the count. Parameter validation usually catches this first and returns `40011`. |
|200 |40040 |See the error message returned by the API. |This task passed validation, but another task in the same Session did not, so the entire Session was not saved. Check the other tasks' error codes. |
|200 |40041 |See the error message returned by the API. |This task belongs to a different account from the first turn. Ensure that all tasks in the Session belong to the same account. |
|200 |40042 |See the error message returned by the API. |This task uses a different model from the first turn. Ensure that all tasks in the Session use the same model. |
|200 |40043 |See the error message returned by the API. |This task is a draft or final\-video task and cannot be added to a Session. Report it individually using `task`. |
|200 |40044 |See the error message returned by the API. |This task did not successfully generate a video and cannot be added to a Session. |
|200 |40045 |See the error message returned by the API. |The task content cannot be read, for example because it was not found or did not pass content moderation. See the error message for details. |
|200 |40046 |See the error message returned by the API. |An error occurred while saving the Session. The entire Session was not saved. Retry later. |

## Response parameters

**SubmissionId** `string`  |  Submission ID

  Example: badcase\-sub\-7c2e1f88\-…. The ID of this batch submission, prefixed badcase\-sub\-, usable for log troubleshooting.

**Total** `integer`  |  Total submissions

Example: 1. Total number of submitted task_ids (the length of Id before deduplication).

**Accepted** `object[]`  |  Accepted task list

task_ids that passed validation and have been pre\-written as cases.

After a successful Session report, each task has its own entry. All entries share the same `CaseId`, and `TurnNo` starts at 1 in the order of `Id` in the request. An empty array is returned if the entire Session was not accepted.

**CaseId** `string`  |  Case ID

`Accepted.CaseId`

the pre\-allocated case_id, prefixed instance\-. The case is in processing state on return; the backend asynchronously transfers the video and input frames and fills in conversation/message, becoming completed when done, or failed on error.

**Code** `integer`  |  Return code

`Accepted.Code`

Always 0.

**Message** `string`  |  Return message

`Accepted.Message`

Always "Success".

**TaskId** `string`  |  Task ID

`Accepted.TaskId`

the task_id as submitted.

**CaseType** `string`  |  Case type

`Accepted.CaseType`

The type of this case. Options: `Badcase`, `Goodcase`.

**JoinIncentive** `boolean`  |  Incentive participation

`Accepted.JoinIncentive`

Whether this case participates in the incentive program. Always `false` for models earlier than Seedance 2.5.

**TurnNo** `integer`  |  Session turn

`Accepted.TurnNo`

The task's turn in the Session. Returned only when `IdType` is `session`. Starts at 1, following the order of `Id` in the request.

**Min**: `1`

**Rejected** `object[]`  |  Rejected task list

task_ids that failed validation and were not written.

When reporting by Session, if any task fails validation, every task appears in this list. Tasks that failed validation return their specific error codes; the others return `40040`. The response order is not guaranteed to match `Id` in the request. Match results by `TaskId`.

**Code** `integer`  |  Error code

`Rejected.Code`

business error code.

**Message** `string`  |  Rejection reason

`Rejected.Message`

human\-readable rejection reason.

**TaskId** `string`  |  Rejected task ID

`Rejected.TaskId`

the rejected task_id.

**RejectedTimelineFeedbacks** `object[]`  |  Rejected timeline feedback

Timeline feedback entries that were not saved. Failure of an individual entry does not affect the main case record. An empty array if the request omitted `TimelineFeedbacks`.

**Message** `string`  |  A human\-readable explanation of the rejection.

`RejectedTimelineFeedbacks.Message`

**Reason** `string`  |  Rejection reasons:

`RejectedTimelineFeedbacks.Reason`

* `TASK_ID_NOT_MATCHED`: The task ID is missing or is not in the request's `Id` list.

* `INVALID_TIME_RANGE`: A time is missing or negative, or the start exceeds the end.

* `FEEDBACK_INVALID`: The text is missing, contains only whitespace, or exceeds the length limit.

* `BADCASE_TYPE_INVALID`: An issue type enum value is invalid.

* `EXCEEDED_LIMIT`: The limit of 20 entries per task was exceeded.

* `TASK_REJECTED`: The associated task was not accepted.

**EndTime** `number / null`  |  The submitted end time

`RejectedTimelineFeedbacks.EndTime`

echoed unchanged. May be `null`.

**Feedback** `string / null`  |  The submitted issue description

`RejectedTimelineFeedbacks.Feedback`

echoed unchanged. May be `null`.

**StartTime** `number / null`  |  The submitted start time

`RejectedTimelineFeedbacks.StartTime`

echoed unchanged. May be `null`.

**TaskId** `string`  |  The originally associated task ID

`RejectedTimelineFeedbacks.TaskId`

or an empty string if omitted.
