# CasePlatformV1BadcaseReport - BytePlus ModelArk Seedance 2.x Generations Quality Diagnostics API

## Usage

After generating a video with Seedance 2.x, you can use this API to report the corresponding task_id as a badcase.

This API retrieves the original task data from upstream using `task_id`, so reports must be submitted within the **3\-day (72\-hour)**  window. BytePlus cleans up task logs on the hour; plan to submit within **72 hours**. Once the logs expire, ModelArk may no longer be able to retrieve the task data needed to accept and investigate the report. Enable Model Generations Quality Diagnostics before generating the task, as described below.

## Notes

BytePlus does not store the detailed logs of your inference tasks by default. To let BytePlus ModelArk investigate a quality issue from a task ID alone, the corresponding request logs must be retained for a short period, and that requires your explicit consent.

**Enable** [Model Generations Quality Diagnostics](model-diagnostic-service-acknowledgement.md) **before using this API.**  In the ModelArk console, open **Model Generations Quality Diagnostics** from the console home page, turn the feature on, and accept the Model Generations Quality Diagnostics agreement. Only users holding the arkfullaccess permission can change this setting. Reporting for an account that has not opted in returns error code 70001.

What the agreement covers:

* **Scope** — asynchronous inference models only (Seedance 2.x, and similar). Synchronous inference models such as Seed and Seedream are **not** included.

* **Retention** — all asynchronous task logs under your primary account are stored for **3 days (72 hours)**  in Johor, and dropped afterwards.

* **Opting out** — ingestion stops immediately. Data already stored is not deleted on the spot; it clears naturally within 3 days.

* **Reported cases** — a task you submit through this API is retained long term and transferred across borders. It is used for model evaluation, regression and analysis. **It is not used to train models.**

   For the full terms, see the Model Generations Quality Diagnostics agreement (link TBD).

   **This API and its documentation apply to the BytePlus ModelArk service. They do not apply to Volcengine Ark (mainland China).**

## Request Instructions

### Calling with a BytePlus ModelArk API Key

Obtain the API Key from [API Key Management](https://console.byteplus.com/ark/region:ap-southeast-1/apiKey). **Calling with a ModelArk API Key uses a different request scheme than AK/SK: the request URL is not https://open.byteplusapi.com but the Case Platform route below, and you must add the /case\-platform path.**

```Bash
curl --location 'https://prompt-pilot.ap-southeast.bytepluses.com/case-platform?Action=CasePlatformV1BadcaseReport&Version=2024-01-01' \
--header 'accept: application/json, text/plain, */*' \
--header 'content-type: application/json' \
--header 'Authorization: Bearer ModelArk_API_KEY' \
--header 'Region: ap-southeast-1' \
--header 'ServiceName: ark' \
--header 'Version: 2024-01-01' \
--data-raw '{
  "Id": "cgt-20260501132104-tfm9v",
  "Feedback": "Character hands are distorted, and lip movement does not match the dubbed audio",
  "AccountId": "2000000000",
  "BadcaseType": "character_id_drift",
  "ContactInfo": "user@example.com"
}'
```

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

## Request Parameters

The table below lists only the parameters specific to this interface plus a subset of common parameters. See the BytePlus ModelArk Common Parameters reference (link TBD) for more information.

|**Parameter** |**Type** |**Required** |**Description** |
|---|---|---|---|
|Action |String |Yes |The operation to perform. Value: CasePlatformV1BadcaseReport. |
|Version |String |Yes |API version. Value: 2024\-01\-01. |
|Id |String |Yes |Example: cgt\-20260501132104\-tfm9v. A single task_id, or a list of task_ids. At least 1, at most 50 — exceeding the limit returns 422 directly. The interface normalizes the submitted task_ids: it accepts either a single string or a list of strings, splits on comma, newline, space, tab and similar separators into a unified task_id list, and trims leading/trailing whitespace and empty fragments. |
|Feedback |String |Yes |Example: "Character hands are distorted, and lip movement does not match the dubbed audio". Problem description, 1–2000 characters; whitespace\-only strings are rejected. Persisted as a feedback annotation. |
|AccountId |String |No |Example: 2000000000. The BytePlus account ID the Id (task_id) belongs to. If omitted, the account ID is resolved from the AK/SK or ModelArk API Key. |
|BadcaseType |String |No |Example: character_id_drift. Must be one of the enum values listed below; an invalid value returns a 422 parameter validation error for the whole request. Note that task_id\-based import currently supports Seedance 2.0 / Seedance 2.0 fast tasks only; if the model behind the task_id is out of scope, the backend returns a business error (for example 60002). |
|ContactInfo |String |No |Example: user@example.com. Contact info (email or phone), up to 255 characters. |

## Response Parameters

The table below lists only the response parameters specific to this interface. See the BytePlus ModelArk Response Structure reference (link TBD) for more information.

|**Parameter** |**Type** |**Description** |
|---|---|---|
|SubmissionId |String |Example: badcase\-sub\-7c2e1f88\-…. The ID of this batch submission, prefixed badcase\-sub\-, usable for log troubleshooting. |
|Total |Integer |Example: 1. Total number of submitted task_ids (the length of Id before deduplication). |
|Accepted |Object[] |task_ids that passed validation and have been pre\-written as cases.<br><br>· **TaskId** (String) — the task_id as submitted.<br><br>· **CaseId** (String, e.g. instance\-11111111\-…) — the pre\-allocated case_id, prefixed instance\-. The case is in processing state on return; the backend asynchronously transfers the video and input frames and fills in conversation/message, becoming completed when done, or failed on error.<br><br>· **Code** (Integer, always 0).<br><br>· **Message** (String, always "Success"). |
|Rejected |Object[] |task_ids that failed validation and were not written.<br><br>· **TaskId** (String) — the rejected task_id.<br><br>· **Code** (Integer, e.g. 40002) — business error code.<br><br>· **Message** (String) — human\-readable rejection reason. |

## Request Example

```plaintext
POST /?Action=CasePlatformV1BadcaseReport&Version=2024-01-01 HTTP/1.1
Host: open.byteplusapi.com
Content-Type: application/json; charset=UTF-8
X-Date: 20260508T071922Z
X-Content-Sha256: 287e874e******d653b44d21e
Authorization: HMAC-SHA256 Credential=Adfks******wekfwe/20260508/ap-southeast-1/ark/request, SignedHeaders=host;x-content-sha256;x-date, Signature=47a7d934ff7b37c03938******cd7b8278a40a1057690c401e92246a0e41085f

{
  "Id": "cgt-20260501132104-tfm9v",
  "Feedback": "Character hands are distorted, and lip movement does not match the dubbed audio",
  "AccountId": "2000000000",
  "BadcaseType": "character_id_drift",
  "ContactInfo": "user@example.com"
}
```

## Response Example

```Plain Text
{
  "ResponseMetadata": {
    "RequestId": "2026050800200011803712817624A398",
    "Action": "CasePlatformV1BadcaseReport",
    "Version": "2024-01-01",
    "Service": "ark",
    "Region": "ap-southeast-1"
  },
  "Result": {
    "SubmissionId": "badcase-sub-7c2e1f88-…",
    "Total": 2,
    "Accepted": [
      {
        "TaskId": "cgt-20260501132104-tfm9v",
        "CaseId": "instance-11111111-…",
        "Code": 0,
        "Message": "Success"
      }
    ],
    "Rejected": [
      {
        "TaskId": "cgt-20260502aaaaa-bbbbb",
        "Code": 40002,
        "Message": "task_id does not exist or does not belong to the current account_id"
      }
    ]
  }
}
```

## Error Codes

The table below lists the business\-logic error codes for this interface. See the BytePlus ModelArk Common Error Codes reference (link TBD) for common error codes.

|**HTTP status** |**Error code** |**Error message** |**Description** |
|---|---|---|---|
|200 |40002 |task_id does not exist or does not belong to the current account_id |task_id does not exist, or exists but belongs to a different AccountId / the account tied to the API Key |
|200 |40003 |task_id has already been submitted as a badcase |task_id was submitted as a badcase previously |
|200 |40004 |task_id is still being generated; please wait for it to complete before submitting a badcase |The task behind task_id has not finished generating |
|200 |40005 |task_id has already been submitted as a badcase |The same task_id appeared more than once in this request |
|200 |70001 |This Account did not opt\-in data Diagnostic agreement yet. Please opt\-in ModelArk Data Diagnostic Agreement. |The account that owns this task has not enabled Model Generations Quality Diagnostics, so its inference data was never retained. **Evaluated per task_id and returned inside Rejected** — tasks owned by opted\-in accounts in the same batch are still accepted. Enable it as described under Notes. |
|200 |60002 |"The ModelId in the request is not a supported Seedance 2.0 series model; only Seedance 2.0 and Seedance 2.0 fast are supported" or "The model behind task_id is not a Seedance 2.0 series model; badcase import currently only supports Seedance 2.0" |The ModelId in the request is not a supported Seedance 2.0 series model, or the model resolved from task_id is not in the supported series |
|200 |60003 |The actual model behind task_id does not match the ModelId in the request; the correct ModelId should be … |The ModelId in the request does not match the model resolved from task_id |
|400 |40006 |AccountId is required when not authenticating with a ModelArk API key |Not authenticated with a ModelArk API Key, and AccountId was not provided in the request body |
|422 |40007 |account_id must not be empty or whitespace |AccountId was provided but is empty or all whitespace |
|422 |40008 |model_id must not be empty or whitespace |ModelId was provided but is empty or all whitespace |
|422 |40009 |feedback must not be empty or whitespace |Feedback is empty or all whitespace |
|422 |40010 |"badcase_type list must contain at least one value" or "badcase_type contains invalid value ''. Allowed values:" |BadcaseType was passed as an empty list, or with an invalid enum value |
|422 |40011 |"id must contain at least one task_id" or "id may contain at most 50 task_ids" |After splitting, Id contains no valid task_id, or more than 50 task_ids |

## Enum Value Descriptions

### BadcaseType

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
