# Query large language model quality issue results

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=CasePlatformV1SeedBadcaseQuery&Version=2024-01-01`

Query the processing status and conclusions for ModelArk large language model quality issues (Seed badcases) by Instance ID. This API supports access key and ModelArk API key authentication.

> **About AssetIssues**

* > Multimodal assets, such as images, audio, and video, are copied asynchronously after acceptance. Failures during copying are recorded in `AssetIssues[]` and returned by this API.

* > 40020/40021/40022/40023 indicate issues with the submitted data, such as URL fetch failure, base64 decoding failure, a missing or expired file_id, or an oversized asset. Correct the data and resubmit.

* > 50010 indicates a platform storage failure, not an issue with the submitted data. Resubmit later.

### Processing time and URL validity

[Report large language model quality issues](case-platform-v1-seed-badcase-report-api.md) has no reporting time limit. The caller submits the original request and response directly; the service does not retrieve them from upstream. After acceptance, use the returned `InstanceId` to query the case.

When `Status` is `processing`, the investigation is in progress and `ProblemLabel` and `SolutionNote` are not yet available. Query again later. When it is `done`, you can read the conclusions.

Multimodal assets are copied asynchronously after acceptance, and `AssetIssues` is updated as copying proceeds. An expired asset URL does not prevent case acceptance but causes copying to fail. URL fetch failures return `40020` in `AssetIssues[].Code`. Report while URLs are still valid and keep assets available until copying completes. When using `file_id`, also ensure that the file has not expired.

`SolutionNote.Attachments[].Url` is a presigned URL regenerated on each query. Use the URL from the latest response promptly and query again if it expires. Attachment URL validity does not represent the retention period of the case records.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* [Recommended] API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Body parameters

**InstanceId** `string[]` `Required`  |  Case ID list

The instance IDs to query, obtained from the report API response. The former name `CaseId` remains supported.

> **warning**

> Each request must contain 1 to 50 InstanceId values after deduplication.

## Response parameters

**Total** `integer`  |  Total cases

The total number of submitted case IDs, equal to the number of parsed `CaseId` values before deduplication.

**Accepted** `object[]`  |  Accepted cases

The list of matching case IDs belonging to the current account, including reporting snapshots and processing results.

**InstanceId** `string`  |  Case ID

`Accepted.InstanceId`

The case ID being queried.

**Status** `string`  |  Processing status

`Accepted.Status`

The processing status.

* `processing`: Investigation in progress.

* `done`: Completed.

> **tip**

> While the status is `processing`, `ProblemLabel` and `SolutionNote` are not yet available.

**ApiType** `string`  |  API type

`Accepted.ApiType`

The API type for this case.

* `chat`: Chat Completions API.

* `responses`: Responses API.

**AssetIssues** `object[]`  |  Asset issues

`Accepted.AssetIssues`

The list of multimodal assets that could not be copied to platform storage.

The original asset references are retained. This list is produced asynchronously. See the error codes section for details.

> **tip**

> Entries with error code `50010` can be retried.

**Code** `integer`  |  Error code

`Accepted.AssetIssues.Code`

The asset processing error code. See the error codes section for details.

**Message** `string`  |  Error description

`Accepted.AssetIssues.Message`

The reason the asset could not be copied.

**BadcaseType** `string[]`  |  Issue types

`Accepted.BadcaseType`

The list of badcase type identifiers selected when reporting.

**Feedback** `string`  |  Issue description

`Accepted.Feedback`

The quality issue description provided when reporting.

**Model** `string`  |  Model name

`Accepted.Model`

The model name recorded when reporting.

**ProblemLabel** `string`  |  Issue label

`Accepted.ProblemLabel`

The issue label assigned after investigation, returned when processing completes.

**SolutionNote** `object`  |  Solution

`Accepted.SolutionNote`

The solution, returned when processing completes, including explanatory text and attachments.

**Attachments** `object[]`  |  Attachment list

`Accepted.SolutionNote.Attachments`

The solution attachments, which can be images or videos.

> **warning**

> Attachment URLs are presigned and regenerated on each query. Do not store them long term.

**MimeType** `string`  |  MIME type

`Accepted.SolutionNote.Attachments.MimeType`

The MIME type of the attachment.

**Name** `string`  |  Attachment name

`Accepted.SolutionNote.Attachments.Name`

The attachment file name.

**Type** `string`  |  Attachment type

`Accepted.SolutionNote.Attachments.Type`

The media type of the attachment.

**Url** `string`  |  Attachment URL

`Accepted.SolutionNote.Attachments.Url`

The presigned download URL for the attachment.

**Text** `string`  |  Explanatory text

`Accepted.SolutionNote.Text`

The text of the solution.

**SubmitTime** `string`  |  Report time

`Accepted.SubmitTime`

The report time in ISO 8601 UTC format.

**Rejected** `object[]`  |  Rejected cases

The list of case IDs that were not found or belong to another account.

**Code** `integer`  |  Error code

`Rejected.Code`

The business error code. See the error codes section for details.

**InstanceId** `string`  |  Case ID

`Rejected.InstanceId`

The case ID being queried.

**Message** `string`  |  Rejection reason

`Rejected.Message`

The reason no result was returned.
