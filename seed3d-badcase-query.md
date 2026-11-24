# Query 3D generation quality issue results

`POST https://prompt-pilot.ap-southeast.bytepluses.com/case-platform?Action=CasePlatformV1BadcaseQuery&Version=2024-01-01`

Use this API to query the processing status, issue labels, solutions, and screenshot download results for Seed3D 2.0 generation quality issues. First submit feedback using [Report 3D generation quality issues](seed3d-badcase-report.md), then query using the generation task's `task_id`. You can query only tasks already reported by the current account. Access key and ModelArk API key authentication are supported.

`Status` is `processing` while the investigation is in progress, and `done` after it completes. Screenshot download status is independent of investigation status. For video tasks, see [Query video generation quality issue results](case-platform-v1-badcase-query-api.md).

### Reporting window and when to query

First submit 3D task feedback for BytePlus ModelArk within **7 days** using [Report 3D generation quality issues](seed3d-badcase-report.md). The report API retrieves the original task data from upstream using `task_id`, which is why a reporting window applies.

The 7\-day limit is a reporting window. This API queries the status and results of cases already reported. When `Status` is `processing`, the investigation is incomplete and `ProblemLabel` and `SolutionNote` are not yet available. Query again later. When it is `done`, you can read the conclusions. Screenshot download status is independent of investigation status.

### URL validity

* `FeedbackImages[].Url` is a presigned screenshot URL valid for approximately **10 minutes**. See `ExpiresAt` for the exact expiry time. Each query generates a new URL. Query again after it expires.

* `SolutionNote.Attachments[].Url` is a solution attachment URL that is re\-signed on each query. Use the URL from the latest response and query again if it expires.

These durations describe the validity of the returned URLs, not the retention period of the case records.

### Request headers

For ModelArk API key authentication, set `Authorization: Bearer <API_KEY>` and `Content-Type: application/json`. The service resolves the account identity from the API key.

For AK/SK authentication, set the `Authorization`, `X-Date`, and `X-Content-Sha256` headers required for V4 signing. Use `ark` for `Service` and `ap-southeast-1` for `Region`.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* [Recommended] API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Query parameters

> Passed as URL query string.

**Action** `string` `Required`  |    The API action

Set to `CasePlatformV1BadcaseQuery`.

**Version** `string` `Required`  |  The API version

Set to `2024-01-01`.

### Body parameters

**Id** `string / string[]` `Required`  |  Task ID list

The task IDs to query, provided as a string or an array of strings. Strings are split on half\-width or full\-width commas, newlines, spaces, or tabs. Leading and trailing whitespace and empty entries are removed, and duplicate task IDs are deduplicated.

After normalization, there must be 1 to 50 tasks. Otherwise, the API returns HTTP `422` with error code `40011`. You can query only tasks already reported by the current account.

### Video compatibility fields in 3D responses

Video and 3D tasks share the same Action, Version, authentication methods, and common response structure. In `Accepted` entries, `BadcaseType` and `TimelineFeedbacks` apply only to video tasks. For 3D tasks, they are empty arrays and can be ignored.

### Screenshot download failure reasons

A screenshot download failure does not prevent case creation and is not retried automatically. The same task cannot be reported again. To provide additional screenshots, contact technical support with `SubmissionId` and the task ID. Use the following table to diagnose the failure and check screenshot sources for future reports.

|`ErrorCode` |`ErrorReason` |Meaning and resolution |
|---|---|---|
|`40020` |`blocked_address` |The URL or redirect target is not a public address. Check the screenshot source. |
|`40020` |`timeout` |The download exceeded 60 seconds. Check the image size and source server response time. |
|`40020` |`http_error` |The source returned an error status such as 403 or 404. Check that the URL has not expired and requires no login. |
|`40020` |`fetch_failed` |Connection failure, empty response, or more than 3 redirects. Check the direct image URL. |
|`40020` |`not_image` |The content is not PNG, JPEG, or WebP. Check whether you submitted a web page or another file type. |
|`40023` |`too_large` |The image exceeds 10 MB. Compress images before future submissions. |
|`50010` |`storage_failed` |Platform storage failure. Contact technical support with the task ID. |

### Error codes

For HTTP 4xx or 5xx, errors appear in `ResponseMetadata.Error`. Even with HTTP 200, check `Result.Accepted` and `Result.Rejected` to confirm the result for each task.

|HTTP |Error code |Meaning and resolution |
|---|---|---|
|422 |`40011` |After normalization, `Id` is empty or contains more than 50 tasks. Correct the task list. |
|400 |`100023` |Request validation failed. Check the JSON and field types according to `Message`. |
|503 |`100050` |The upstream task query is busy or timed out. Retry later. |
|200 |`40002` |The task does not exist or does not belong to the current account. Check the task ID and the account used for authentication. |
|200 |`40012` |No badcase has been reported for this task. Call the report API first. |

In the query API, `40012` means that no report has been submitted. It differs from `40012` in the report API, which means the task ID format is invalid. Error messages may be in Chinese or English. Your application should use error codes to determine the cause.

## Response parameters

**Total** `integer`  |  Total tasks

  The number of task IDs in this query, counting each duplicate ID only once. This differs from `Total` in the report API, which counts IDs before deduplication.

**Accepted** `object[]`  |  Accepted task list

The list of matching `task_id` values belonging to the current account, including submission snapshots and processing results.

**Status** `string`  |  Processing status

`Accepted.Status`

The badcase processing status. Values: `processing` (in progress) or `done` (completed).

**TaskId** `string`  |  Task ID

`Accepted.TaskId`

A validated `task_id` belonging to the current account.

**Feedback** `string`  |  Issue description

`Accepted.Feedback`

The quality issue description provided when the user submitted the badcase.

**FeedbackImages** `object[]`  |  Issue screenshots

`Accepted.FeedbackImages`

Screenshots attached to the report, returned in submission order. An empty array if no screenshots were submitted. Screenshot download status is independent of case investigation status. See the child fields for URLs and failure reasons.

**ErrorCode** `integer / null`  |  Screenshot error code

`Accepted.FeedbackImages.ErrorCode`

The error code for a failed download: `40020` for a download failure, `40023` for an image exceeding 10 MB, or `50010` for a platform storage failure. `null` if no error occurred.

**ErrorMessage** `string / null`  |  Screenshot error message

`Accepted.FeedbackImages.ErrorMessage`

An English description of the download failure, such as the HTTP status returned by the source server. `null` if no error occurred.

**ErrorReason** `string / null`  |  Screenshot failure reason

`Accepted.FeedbackImages.ErrorReason`

The specific download failure reason. See "Screenshot download failure reasons" for values and resolutions. `null` if there is no failure or this field was not recorded for an older screenshot.

**ExpiresAt** `string / null`  |  URL expiry time

`Accepted.FeedbackImages.ExpiresAt`

The expiry time of `Url` in ISO 8601 UTC format. `null` if no URL is available.

**MediaKey** `string / null`  |  Internal media asset identifier

`Accepted.FeedbackImages.MediaKey`

An internal identifier used to renew URLs. Can be ignored and may be `null`.

**MimeType** `string / null`  |  Image type

`Accepted.FeedbackImages.MimeType`

The MIME type identified from the actual file contents, such as `image/png`. `null` if not yet available.

**Status** `string`  |  Screenshot download status

`Accepted.FeedbackImages.Status`

* `ok`: The screenshot has been saved and can be viewed using `Url`.

* `pending`: The service is still downloading the screenshot.

* `failed`: The download failed. Check `ErrorCode`, `ErrorReason`, and `ErrorMessage`.

This status is independent of the investigation status in `Accepted[].Status`.

**Url** `string / null`  |  Screenshot URL

`Accepted.FeedbackImages.Url`

A presigned screenshot URL, regenerated on each query and valid for approximately 10 minutes. `null` while the download is pending or if it failed. Query again to obtain a new URL after it expires.

**ProblemLabel** `string`  |  Issue label

`Accepted.ProblemLabel`

The issue label assigned by support staff based on the case analysis after processing completes. It is not yet available while `Status` is `processing` and is usually an empty string.

**SolutionNote** `object / null`  |  Solution

`Accepted.SolutionNote`

The solution, including explanatory text and attachments. It is not yet available while `Status` is `processing`, so the value is `null`. An object is returned after processing completes.

object `object`

**Attachments** `object[] / null`  |  The list of solution attachments

`Accepted.SolutionNote.Attachments`

May be `null`. Attachments can be images, videos, or audio. URLs are regenerated on each query. Retrieve them from the latest response rather than storing them long term.

Attachment[] `object[]`

**Type** `string`  |  File type

`Accepted.SolutionNote.Attachments.Type`

The file type. Options: `image`, `video`, `audio`.

**Url** `string`  |  File URL

`Accepted.SolutionNote.Attachments.Url`

The file URL. TOS URLs are automatically re\-signed when read.

**MimeType** `string / null`  |  MIME type

`Accepted.SolutionNote.Attachments.MimeType`

The Multipurpose Internet Mail Extensions (MIME) type. May be `null`.

**Name** `string / null`  |  File name

`Accepted.SolutionNote.Attachments.Name`

The file name. May be `null`.

null `null`

**Text** `string / null`  |  The solution text

`Accepted.SolutionNote.Text`

May be `null`.

null `null`

**SubmitTime** `string / null`  |  Submission time

`Accepted.SubmitTime`

The badcase submission time in ISO 8601 UTC format. May be `null` if unavailable.

**Rejected** `object[]`  |  Rejected task list

The list of `task_id` values that were not found, belong to another account, or have no badcase report.

**Code** `integer`  |  Error code

`Rejected.Code`

The business error code for rejecting this `task_id`.

**Message** `string`  |  Rejection reason

`Rejected.Message`

The reason this `task_id` was rejected.

**TaskId** `string`  |  Task ID

`Rejected.TaskId`

The rejected `task_id`.
