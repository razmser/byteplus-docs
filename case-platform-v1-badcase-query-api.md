# CasePlatformV1BadcaseQuery — BytePlus ModelArk Seedance 2.x Generations Quality Diagnostics Result Query API

`POST https://prompt-pilot.ap-southeast.bytepluses.com/case-platform?Action=CasePlatformV1BadcaseQuery&Version=2024-01-01`

## Usage

After reporting a badcase with CasePlatformV1BadcaseReport, you can use this API to query how it was processed and any applicable incentive result.

The report API retrieves the original task data from upstream using `task_id`, so submit the report within the **3\-day (72\-hour)**  window. BytePlus cleans up task logs on the hour; plan to submit within **72 hours**. Reported cases are retained long term, as described below; the reporting window is not a deadline for querying a reported case.

If `Status` is `processing`, the investigation is still in progress and `ProblemLabel` and `SolutionNote` are not yet available. Query again later. Attachment URLs are re\-signed on each query; use the URLs from the latest response and query again if a link expires.

> **Note**

> BytePlus does not store the detailed logs of your inference tasks by default. To let BytePlus ModelArk investigate a quality issue from a task ID alone, the corresponding request logs must be retained for a short period, and that requires your explicit consent.

> **Enable Model Generations Quality Diagnostics before using this API.** In the ModelArk console, open **Model Generations Quality Diagnostics** from the console home page, turn the feature on, and accept the Model Generations Quality Diagnostics agreement. Only users holding the arkfullaccess permission can change this setting. Querying from an account that has not opted in returns error code 70001.

> What the agreement covers:

* > **Scope** — asynchronous inference models only (Seedance 2.x, models and similar). Synchronous inference models such as Seed and Seedream are **not** included.

* > **Retention** — all asynchronous task logs under your primary account are stored for **3 days (72 hours)** in Johor, and dropped afterwards.

* > **Opting out** — ingestion stops immediately. Data already stored is not deleted on the spot; it clears naturally within 3 days.

* > **Reported cases** — a task you submit through the report API is retained long term and transferred across borders. It is used for model evaluation, regression and analysis. **It is not used to train models.**

   > For the full terms, see the [Model Generations Quality Diagnostics agreement](model-diagnostic-service-acknowledgement.md).

   > **This API and its documentation apply to the BytePlus ModelArk service.**

### URL validity

* `FeedbackImages[].Url` is a presigned screenshot URL valid for approximately **10 minutes**. See `ExpiresAt` for the exact expiry time. Each query generates a new URL. Query again after it expires.

* `SolutionNote.Attachments[].Url` is a solution attachment URL that is re\-signed on each query. Use the URL from the latest response and query again if it expires.

These durations describe the validity of the returned URLs, not the retention period of the case records.

### Request headers

For ModelArk API key authentication, set `Authorization: Bearer <API_KEY>` and `Content-Type: application/json`. The service resolves the account identity from the API key.

For AK/SK authentication, set the `Authorization`, `X-Date`, and `X-Content-Sha256` headers required for V4 signing. Use `ark` for `Service` and `ap-southeast-1` for `Region`.

### Request Instructions

### Calling with a BytePlus ModelArk API Key

Obtain the API Key from [API Key Management](https://console.byteplus.com/ark/region:ap-southeast-1/apiKey). **Calling with a ModelArk API Key uses a different request scheme than AK/SK: the request URL is not https://open.byteplusapi.com but the Case Platform route below, and you must add the /case\-platform path.**

The host is bytepluses.com — note the trailing s.

**The API Key must match the entry you call it from.**  ModelArk API Keys and Volcengine Ark API Keys are registered in separate control planes (ap\-southeast\-1 and cn\-beijing). The backend verifies a key against the control plane of the entry the request arrived through, so a ModelArk API Key is not resolvable from the Volcengine entry and authentication fails outright — and the reverse is equally true. This is the most common cause of an authentication failure on this API.

**This entry exposes three Actions only** — CasePlatformV1BadcaseReport, CasePlatformV1BadcaseQuery and CasePlatformV1AuthCheckAccess. Any other Action, and any request that omits the Action parameter, is rejected at the gateway with a plain HTTP 404 that is **not** a TOP response envelope.

Requests arriving through this entry are resolved to the BytePlus data source automatically — you do not need to specify a site.

This API takes no AccountId. The badcases you can read are determined entirely by the account resolved from your own credentials, so query with your own API Key; you cannot query on another account's behalf.

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

Value: CasePlatformV1BadcaseQuery.

**Version** `string` `Required`  |  API version

Value: 2024\-01\-01.

### Body parameters

**Id** `string / string[]` `Required`  |  Task ID list

Example: cgt\-20260501132104\-tfm9v. A single task_id, or a list of task_ids. 1–50 after splitting and removing empty entries, before deduplication; exceeding the limit returns 422. Duplicate task_ids within one request are deduplicated and reported once. Split on comma, newline, space, tab and similar separators, with leading/trailing whitespace and empty fragments trimmed.

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

### Error Codes

The table below lists the business\-logic error codes for this interface.

|**HTTP status** |**Error code** |**Error message** |**Description** |
|---|---|---|---|
|200 |40002 |task_id does not exist or does not belong to the current account_id |task_id does not exist, or exists but belongs to a different account. Returned inside Rejected. |
|200 |40012 |task_id was never submitted as a badcase |task_id is valid and belongs to the current account, but was never submitted through the report API. Returned inside Rejected. |
|422 |40011 |"id must contain at least one task_id" or "id may contain at most 50 task_ids" |After splitting, Id is empty or contains more than 50 task_ids |
|403 |70001 |This Account did not opt\-in data Diagnostic agreement yet. Please opt\-in ModelArk Data Diagnostic Agreement. |The calling account has not enabled Model Generations Quality Diagnostics. **The whole request is refused** — no Accepted or Rejected is returned. Enable it as described under Notes. |

**70001 behaves differently here than on the report API.**  Reporting may cover several accounts in one batch, so it evaluates the agreement per task_id and rejects only the tasks whose owner has not opted in. Querying is self\-service: it takes no AccountId and only ever reads back your own badcases, so an account that has not opted in has nothing it may read and the request is refused outright — including badcases it submitted before opting out.

### Incentive result

Read `Result.Accepted[].Incentive.Status` for the eligibility decision and `TokenAmount` for the quantity in tokens. `Accepted[].Status` tracks the investigation separately.

|Case outcome |`Incentive.Status` |`Incentive.TokenAmount` |
|---|---|---|
|Did not join the program |`Incentive` is `null` |— |
|Joined, result pending |`unknown` |`null` |
|Joined, did not qualify |`miss` |`0` |
|Joined, qualified |`hit` |Non\-negative integer, which can be `0` |

Use `Status` to determine eligibility, including when `TokenAmount` is `0`. Query again later if the result is `unknown`.

The first task qualifies for an incentive while the investigation is in progress. The second investigation is complete and its incentive result is still pending.

```json
{
  "ResponseMetadata": {
    "RequestId": "example-request-id",
    "Action": "CasePlatformV1BadcaseQuery",
    "Version": "2024-01-01",
    "Service": "ark",
    "Region": "ap-southeast-1"
  },
  "Result": {
    "Total": 2,
    "Accepted": [
      {
        "TaskId": "cgt-20261124101500-abc12",
        "Status": "processing",
        "BadcaseType": [],
        "Feedback": "The subject changes between shots.",
        "FeedbackImages": [],
        "TimelineFeedbacks": [],
        "SubmitTime": "2026-11-24T10:45:00Z",
        "ProblemLabel": "",
        "SolutionNote": null,
        "Incentive": {
          "Status": "hit",
          "TokenAmount": 1500
        }
      },
      {
        "TaskId": "cgt-20261124103000-def34",
        "Status": "done",
        "BadcaseType": [],
        "Feedback": "The last shot flickers.",
        "FeedbackImages": [],
        "TimelineFeedbacks": [],
        "SubmitTime": "2026-11-24T11:00:00Z",
        "ProblemLabel": "Flicker",
        "SolutionNote": {
          "Text": "Shorten the final shot and smooth the lighting transition.",
          "Attachments": null
        },
        "Incentive": {
          "Status": "unknown",
          "TokenAmount": null
        }
      }
    ],
    "Rejected": []
  }
}
```

## Response parameters

**Total** `integer`  |  Total tasks

  Example: 1. Number of deduplicated task_ids queried (Accepted + Rejected).

**Accepted** `object[]`  |  Accepted task list

task_ids that match a badcase belonging to the current account. Returns the submission\-time snapshot plus the processing result.

Note that the two "empty" shapes differ: while Status is processing, ProblemLabel is an empty string and SolutionNote is null.

**Status** `string`  |  Processing status

`Accepted.Status`

outward processing status, processing or done. Options: `processing`, `done`.

**TaskId** `string`  |  Task ID

`Accepted.TaskId`

task ID.

**BadcaseType** `string[]`  |  Issue categories

`Accepted.BadcaseType`

the categories selected at submission time; same enum as the report API.

**Feedback** `string`  |  Issue description

`Accepted.Feedback`

the feedback text entered at submission time.

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

**Incentive** `object / null`  |  Incentive result

`Accepted.Incentive`

The incentive result for the reported case. Returns `null` if the case did not join the program; otherwise returns an object. The incentive decision is independent of the investigation status in `Accepted[].Status`.

**Status** `string`  |  Incentive decision

`Accepted.Incentive.Status`

* `unknown`: Joined the program; the result is pending. Query again later.

* `miss`: Joined the program but did not qualify.

* `hit`: Joined the program and qualified.

Use this field to determine eligibility. A `TokenAmount` of `0` can still indicate a qualifying case.

**TokenAmount** `integer / null`  |  Incentive token amount

`Accepted.Incentive.TokenAmount`

The quantity in tokens: `null` when `Status` is `unknown`, `0` when it is `miss`, and a non\-negative integer (possibly `0`) when it is `hit`.

**Min**: `0`

**ProblemLabel** `string`  |  Issue label

`Accepted.ProblemLabel`

issue label assigned by support staff. An empty string while processing.

**SolutionNote** `object / null`  |  Solution

`Accepted.SolutionNote`

The solution note: text plus attachments. Null while processing or when no note is available.

**Attachments** `object[] / null`  |  image/file attachments

`Accepted.SolutionNote.Attachments`

each {Type, Url, Name, MimeType}. The file URL is re\-signed when read, so use it promptly rather than storing it. May be `null`. Attachments can also be videos or audio.

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

**Text** `string / null`  |  the note text

`Accepted.SolutionNote.Text`

May be `null`.

**SubmitTime** `string / null`  |  Submission time

`Accepted.SubmitTime`

submission time, ISO 8601 UTC. May be `null` if unavailable.

**TimelineFeedbacks** `object[]`  |  Timeline feedback

`Accepted.TimelineFeedbacks`

Video timeline feedback included in the report, returned in submission order.

**EndTime** `number`  |  The end of the time range in seconds

`Accepted.TimelineFeedbacks.EndTime`

If equal to the start, it represents a single point in time.

**StartTime** `number`  |  The start of the time range in seconds.

`Accepted.TimelineFeedbacks.StartTime`

**BadcaseType** `string[] / null`  |  The list of video issue categories for this time range

`Accepted.TimelineFeedbacks.BadcaseType`

May be `null`.

**Feedback** `string`  |  The issue description submitted for this time range.

`Accepted.TimelineFeedbacks.Feedback`

**Rejected** `object[]`  |  Rejected task list

task_ids that could not be returned.

**Code** `integer`  |  Error code

`Rejected.Code`

business error code.

**Message** `string`  |  Rejection reason

`Rejected.Message`

human\-readable reason.

**TaskId** `string`  |  Task ID

`Rejected.TaskId`

task ID.
