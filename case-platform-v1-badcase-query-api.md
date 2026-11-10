# CasePlatformV1BadcaseQuery — BytePlus ModelArk Seedance 2.x Generations Quality Diagnostics Result Query API

## Usage

After reporting a badcase with CasePlatformV1BadcaseReport, you can use this API to query how it was processed.

The report API retrieves the original task data from upstream using `task_id`, so submit the report within the **3\-day (72\-hour)**  window. BytePlus cleans up task logs on the hour; plan to submit within **72 hours**. Reported cases are retained long term, as described below; the reporting window is not a deadline for querying a reported case.

If `Status` is `processing`, the investigation is still in progress and `ProblemLabel` and `SolutionNote` are not yet available. Query again later. Attachment URLs are re\-signed on each query; use the URLs from the latest response and query again if a link expires.

## Notes

BytePlus does not store the detailed logs of your inference tasks by default. To let BytePlus ModelArk investigate a quality issue from a task ID alone, the corresponding request logs must be retained for a short period, and that requires your explicit consent.

**Enable Model Generations Quality Diagnostics before using this API.**  In the ModelArk console, open **Model Generations Quality Diagnostics** from the console home page, turn the feature on, and accept the Model Generations Quality Diagnostics agreement. Only users holding the arkfullaccess permission can change this setting. Querying from an account that has not opted in returns error code 70001.

What the agreement covers:

* **Scope** — asynchronous inference models only (Seedance 2.x, models and similar). Synchronous inference models such as Seed and Seedream are **not** included.

* **Retention** — all asynchronous task logs under your primary account are stored for **3 days (72 hours)**  in Johor, and dropped afterwards.

* **Opting out** — ingestion stops immediately. Data already stored is not deleted on the spot; it clears naturally within 3 days.

* **Reported cases** — a task you submit through the report API is retained long term and transferred across borders. It is used for model evaluation, regression and analysis. **It is not used to train models.**

   For the full terms, see the Model Generations Quality Diagnostics agreement (link TBD).

   **This API and its documentation apply to the BytePlus ModelArk service. They do not apply to Volcengine Ark (mainland China).**

## Request Instructions

### Calling with a BytePlus ModelArk API Key

Obtain the API Key from [API Key Management](https://console.byteplus.com/ark/region:ap-southeast-1/apiKey). **Calling with a ModelArk API Key uses a different request scheme than AK/SK: the request URL is not https://open.byteplusapi.com but the Case Platform route below, and you must add the /case\-platform path.**

```Bash
curl --location 'https://prompt-pilot.ap-southeast.bytepluses.com/case-platform?Action=CasePlatformV1BadcaseQuery&Version=2024-01-01' \
--header 'accept: application/json, text/plain, */*' \
--header 'content-type: application/json' \
--header 'Authorization: Bearer ModelArk_API_KEY' \
--header 'Region: ap-southeast-1' \
--header 'ServiceName: ark' \
--header 'Version: 2024-01-01' \
--data-raw '{
  "Id": "cgt-20260501132104-tfm9v"
}'
```

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

## Request Parameters

The table below lists only the parameters specific to this interface plus a subset of common parameters. See the BytePlus ModelArk Common Parameters reference (link TBD) for more information.

|**Parameter** |**Type** |**Required** |**Description** |
|---|---|---|---|
|Action |String |Yes |The operation to perform. Value: CasePlatformV1BadcaseQuery. |
|Version |String |Yes |API version. Value: 2024\-01\-01. |
|Id |String |Yes |Example: cgt\-20260501132104\-tfm9v. A single task_id, or a list of task_ids. 1–50; exceeding the limit returns 422. Duplicate task_ids within one request are deduplicated and reported once. Split on comma, newline, space, tab and similar separators, with leading/trailing whitespace and empty fragments trimmed. |

## Response Parameters

The table below lists only the response parameters specific to this interface. See the BytePlus ModelArk Response Structure reference (link TBD) for more information.

|**Parameter** |**Type** |**Description** |
|---|---|---|
|Total |Integer |Example: 1. Number of deduplicated task_ids queried (Accepted + Rejected). |
|Accepted |Object[] |task_ids that match a badcase belonging to the current account. Returns the submission\-time snapshot plus the processing result.<br><br>· **TaskId** (String) — task ID.<br><br>· **BadcaseType** (Array of String) — the categories selected at submission time; same enum as the report API.<br><br>· **Feedback** (String) — the feedback text entered at submission time.<br><br>· **SubmitTime** (String) — submission time, ISO 8601 UTC.<br><br>· **Status** (String) — outward processing status, processing or done.<br><br>· **ProblemLabel** (String) — issue label assigned by support staff. An empty string while processing.<br><br>· **SolutionNote** (Object) — the solution note written for you: text plus attachments. null while processing.<br><br>· **Text** (String) — the note text.<br><br>· **Attachments** (Array of Object) — image/file attachments, each {Type, URL, Name, MimeType}. The file URL is re\-signed when read, so use it promptly rather than storing it. |
|Rejected |Object[] |task_ids that could not be returned.<br><br>· **TaskId** (String) — task ID.<br><br>· **Code** (Integer) — business error code.<br><br>· **Message** (String) — human\-readable reason. |

Note that the two "empty" shapes differ: while Status is processing, ProblemLabel is an empty string and SolutionNote is null.

## Request Example

```plaintext
POST /?Action=CasePlatformV1BadcaseQuery&Version=2024-01-01 HTTP/1.1
Host: open.byteplusapi.com
Content-Type: application/json; charset=UTF-8
X-Date: 20260625T001714Z
X-Content-Sha256: 287e874e******d653b44d21e
Authorization: HMAC-SHA256 Credential=Adfks******wekfwe/20260625/ap-southeast-1/ark/request, SignedHeaders=host;x-content-sha256;x-date, Signature=47a7d934ff7b37c03938******cd7b8278a40a1057690c401e92246a0e41085f

{
  "Id": "cgt-20260501132104-tfm9v"
}
```

## Response Example

```Plain Text
{
  "ResponseMetadata": {
    "RequestId": "202606241717181540900962155418EE",
    "Action": "CasePlatformV1BadcaseQuery",
    "Version": "2024-01-01",
    "Service": "ark",
    "Region": "ap-southeast-1"
  },
  "Result": {
    "Total": 2,
    "Accepted": [
      {
        "TaskId": "cgt-20260501132104-tfm9v",
        "BadcaseType": [
          "character_id_drift"
        ],
        "Feedback": "Character hands are distorted, and lip movement does not match the dubbed audio",
        "SubmitTime": "2026-05-08T07:19:22Z",
        "Status": "done",
        "ProblemLabel": "Character consistency",
        "SolutionNote": {
          "Text": "Confirmed to be a LoRA load-order issue; recommend explicitly specifying the style tag in the prompt",
          "Attachments": [
            {
              "Type": "image",
              "Url": "https://case-platform-files-cn-beijing.tos-cn-beijing.volces.com/.../repro.png?X-Tos-Algorithm=...",
              "Name": "repro.png",
              "MimeType": "image/png"
            }
          ]
        }
      }
    ],
    "Rejected": [
      {
        "TaskId": "cgt-20260502aaaaa-bbbbb",
        "Code": 40012,
        "Message": "task_id was never submitted as a badcase"
      }
    ]
  }
}
```

## Error Codes

The table below lists the business\-logic error codes for this interface. See the BytePlus ModelArk Common Error Codes reference (link TBD) for common error codes.

|**HTTP status** |**Error code** |**Error message** |**Description** |
|---|---|---|---|
|200 |40002 |task_id does not exist or does not belong to the current account_id |task_id does not exist, or exists but belongs to a different account. Returned inside Rejected. |
|200 |40012 |task_id was never submitted as a badcase |task_id is valid and belongs to the current account, but was never submitted through the report API. Returned inside Rejected. |
|422 |40011 |"id must contain at least one task_id" or "id may contain at most 50 task_ids" |After splitting, Id is empty or contains more than 50 task_ids |
|403 |70001 |This Account did not opt\-in data Diagnostic agreement yet. Please opt\-in ModelArk Data Diagnostic Agreement. |The calling account has not enabled Model Generations Quality Diagnostics. **The whole request is refused** — no Accepted or Rejected is returned. Enable it as described under Notes. |

**70001 behaves differently here than on the report API.**  Reporting may cover several accounts in one batch, so it evaluates the agreement per task_id and rejects only the tasks whose owner has not opted in. Querying is self\-service: it takes no AccountId and only ever reads back your own badcases, so an account that has not opted in has nothing it may read and the request is refused outright — including badcases it submitted before opting out.
