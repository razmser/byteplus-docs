# Report 3D generation quality issues

`POST https://prompt-pilot.ap-southeast.bytepluses.com/case-platform?Action=CasePlatformV1BadcaseReport&Version=2024-01-01`

Use this API to report Seed3D 2.0 generation quality issues, such as missing geometry, broken surfaces, misaligned textures, or differences from the reference image. The service retrieves the input reference images, generation parameters, and output model for the specified `task_id`, creates a badcase record, and saves your description and optional screenshots. It supports access key and ModelArk API key authentication.

This page describes the parameters for reporting 3D issues. After a report is accepted, use [Query 3D generation quality issue results](seed3d-badcase-query.md) to check the investigation status, conclusions, and screenshot download results. For video tasks, see [Report video generation quality issues](case-platform-v1-badcase-report-api.md).

### Prerequisites

* The task uses Seed3D 2.0 and has completed successfully with a generated 3D model.

* Report the task within **7 days**. This API uses `task_id` to retrieve the original task data from upstream, which may be unavailable after this reporting window. Submit the report as soon as possible after generation completes.

* The credentials and task belong to the same BytePlus account.

* Each task can be reported only once. Prepare the complete description and screenshots before submitting.

Screenshot source URLs must remain accessible for at least **24 hours** after submission so that the service can download them asynchronously. This period is separate from the reporting window for the 3D task.

For tasks that failed, were blocked, or produced no model, contact BytePlus technical support. For tasks still generating, wait until generation completes before reporting.

### Request headers

For ModelArk API key authentication, set `Authorization: Bearer <API_KEY>` and `Content-Type: application/json`. You can omit `AccountId` from the request body.

For AK/SK authentication, set the `Authorization`, `X-Date`, and `X-Content-Sha256` headers required for V4 signing. Use `ark` for `Service` and `ap-southeast-1` for `Region`, and include `AccountId` in the request body.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* [Recommended] API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Query parameters

> Passed as URL query string.

**Action** `string` `Required`  |    The API action

Set to `CasePlatformV1BadcaseReport`.

**Version** `string` `Required`  |  The API version

Set to `2024-01-01`.

### Body parameters

**Feedback** `string` `Required`  |  Feedback description

A description of the 3D model quality issue, containing 1 to 2000 characters and not consisting only of whitespace. All tasks in the request share this description. Include the expected result, the actual issue, the viewing angle, and the viewer used. This field is required for 3D tasks and cannot be replaced by `TimelineFeedbacks`.

**Id** `string / string[]` `Required`  |  Task ID list

The task IDs to report, provided as a string or an array of strings. The server splits strings on half\-width or full\-width commas, newlines, spaces, or tabs, trims leading and trailing whitespace, and removes empty entries.

After splitting, the list must contain 1 to 50 tasks. Otherwise, the API returns HTTP `422` with error code `40011`. All tasks in the request share `Feedback` and `FeedbackImages`.

**AccountId** `string`  |  Account ID

The BytePlus account ID that owns the tasks in `Id`. Required for AK/SK authentication; optional for API key authentication, which resolves the account from the API key.

**ContactInfo** `string`  |  Contact information

The submitter's email address or phone number, up to 255 characters. Providing this information is recommended so that support staff can contact you for more details.

**FeedbackImages** `object[]`  |  Issue screenshots

Optional screenshots of the issue, up to 5 images, shared by all tasks in the request. The service downloads them asynchronously after accepting the report. A download failure does not prevent case creation and is not retried automatically. Use [Query 3D generation quality issue results](seed3d-badcase-query.md) to check each screenshot's download status and failure reason.

**Url** `string` `Required`  |  Screenshot URL

`FeedbackImages.Url`

A direct HTTPS image URL that is publicly accessible without login, does not point to an internal or private address, and uses no more than 3 redirects. Do not submit a web page, 3D model file, `tos://` path, or `data:` inline image.

PNG, JPEG, and WebP are supported, as identified from the actual file contents. Each image must be no larger than 10 MB. Keep the URL accessible for at least 24 hours after reporting. Each image download has a 60\-second timeout.

### Error code

Request\-level errors appear in `ResponseMetadata.Error`. Even with HTTP 200, check `Result.Accepted` and `Result.Rejected`. The HTTP status alone does not indicate that every task was reported successfully.

|HTTP |`ResponseMetadata.Error.CodeN` |Meaning and resolution |
|---|---|---|
|400 |`100023` |Invalid JSON, field, or type; a description exceeds 2000 characters; more than 5 screenshots; or contact information exceeds 255 characters. Correct the request according to `Message`. |
|401 |May be empty |Check the Bearer format and API key validity. |
|400 |`40006` |`AccountId` is missing when using AK/SK. |
|422 |`40007` |`AccountId` is empty or contains only whitespace. |
|422 |`40011` |After splitting, `Id` must contain 1 to 50 tasks. |
|422 |`40014` |The 3D `Feedback` is empty or contains only whitespace. |
|422 |`40015` |Invalid screenshot entry. Use the zero\-based index in `Message` to check `Url`, which must be a direct, public HTTPS image URL. |
|503 |`100050` |The upstream task query is busy or timed out. Retry later. |
|500 |`100000` |Server error. Keep `RequestId` and contact technical support. Before resubmitting, query whether the task was already accepted. |

|`Result.Rejected[].Code` |Meaning and resolution |
|---|---|
|`40012` |Invalid task ID format. Use the exact `id` returned when creating the 3D task. |
|`40002` |The task does not exist, belongs to another account, has exceeded its retention period, or produced no successful output. |
|`40004` |The task is still generating. Wait until generation succeeds before reporting. |
|`40003` |The task was already reported. Query its processing results; contact technical support to provide additional information. |
|`40005` |Duplicate task IDs in the same request. Deduplicate them before sending. |
|`60002` |The model is not supported. Ensure that the task uses Seed3D 2.0. |

Screenshots are downloaded asynchronously after acceptance. Download failure does not prevent case creation. Use [Query 3D generation quality issue results](seed3d-badcase-query.md) to inspect `FeedbackImages[].Status`: `pending` means downloading, `ok` means available, and `failed` means failure. On failure, check `ErrorCode`, `ErrorReason`, and `ErrorMessage`: `40020` indicates download failure, `40023` indicates an image exceeding 10 MB, and `50010` indicates platform storage failure. Screenshot downloads are not retried automatically, and the same task cannot be reported again. To provide additional information, contact technical support with `SubmissionId` and the task ID.

## Response parameters

**SubmissionId** `string`  |  Submission ID

  The unique identifier of this submission, in the format `badcase-sub-<uuid>`. Use it to retrieve related logs.

**Total** `integer`  |  Total submissions

The total number of `task_id` values in this submission, counted from the original list after splitting `Id`, including duplicates.

**Accepted** `object[]`  |  Accepted task list

The list of `task_id` values that passed validation and have a case record.

**CaseId** `string`  |  Case ID

`Accepted.CaseId`

The case ID preallocated by the service, in the format `instance-<uuid>`. After acceptance, the service asynchronously copies the 3D model and input reference images and downloads issue screenshots. Use `CasePlatformV1BadcaseQuery` to query the investigation status and conclusions. That API returns `processing` or `done` for `Status`, independently of screenshot download status.

**Code** `integer`  |  Return code

`Accepted.Code`

The result code for this task. Always `0`.

**Message** `string`  |  Return message

`Accepted.Message`

The result message for this task. Always `Success`.

**TaskId** `string`  |  Task ID

`Accepted.TaskId`

The `task_id` that was accepted and has a case record.

**Rejected** `object[]`  |  Rejected task list

The list of `task_id` values that failed validation and have no case record.

**Code** `integer`  |  Error code

`Rejected.Code`

The business error code for rejecting this task. See the error codes section for details.

**Message** `string`  |  Rejection reason

`Rejected.Message`

The reason this task was rejected.

**TaskId** `string`  |  Rejected task ID

`Rejected.TaskId`

The `task_id` that failed validation.

**RejectedTimelineFeedbacks** `object[]`  |  Rejected timeline feedback

3D requests omit `TimelineFeedbacks`, so this list is an empty array.

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
