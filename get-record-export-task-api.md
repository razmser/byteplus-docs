# Query a usage record export task

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=GetRecordExportTask&Version=2024-01-01`

Queries the status of a usage record export task and retrieves the file download URL.

> **Important**

> The presentation of billing fields in CSV files returned by `GetRecordExportTask` has been switched in phases based on user invocation activity and the export task query window end time, `EndTime`:

* > For users with invocations before 2026\-09\-02, the cutover time is 2026\-09\-15.

* > For users whose first invocation is on or after 2026\-09\-02, the cutover time is 2026\-09\-02.

> For both groups:

* > If `EndTime` is **later than** the applicable cutover time, the CSV no longer returns the `input_token_charge_item`, `output_token_charge_item`, and `cache_hit_token_charge_item` columns separately. The related billing information is consolidated in the `charge_value_list` field.

* > If `EndTime` is **earlier than or equal to** the applicable cutover time, the CSV continues to return these three columns separately.

> After a task is submitted, the CSV field presentation for that task does not change. Adapt your application based on the actual response.

> **Note**

* > Pass the task ID returned by `CreateRecordExportTask` as the request parameter.

* > After submitting a `CreateRecordExportTask` task, wait 5 seconds before polling with `GetRecordExportTask`:

   * > Polling interval: At least 5 seconds.

   * > Total polling duration for one task: No more than 5 minutes.

   * > When the API returns the `Succeeded` status and includes `DownloadUrl`, download the CSV file immediately to avoid URL expiration. The URL is valid for 12 hours.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Id** `string` `Required`  |  Export task ID

The unique task ID returned by *CreateRecordExportTask*, in the format `uet-YYYYMMDDhhmmss-<5 alphanumeric characters>`, where the suffix consists of five alphanumeric characters.

## Response parameters

**Task** `object`  |  Complete task status

Complete task status, including task progress, matched and exported row counts, and the result file download URL, which is returned only when the task succeeds.

**CreateTime** `number`  |  Creation time

`Task.CreateTime`

Task creation time as a UTC Unix timestamp in seconds, when the task was submitted and created.

**Id** `string`  |  Task ID

`Task.Id`

Task ID, matching the *Id* request parameter, which helps distinguish results when querying tasks concurrently.

**Status** `string`  |  Task status

`Task.Status`

Task state. Valid values:

* `Pending`: The task has been submitted and is awaiting scheduling.

* `Running`: The task is running.

* `Succeeded`: The task succeeded. Download the result from *DownloadUrl*.

* `Failed`: The task failed. See *ErrorMessage* for the cause.

**DownloadUrl** `string`  |  Download URL

`Task.DownloadUrl`

A single\-use download URL issued by TOS. It has a valid value only when *Status* is `Succeeded`.

> **Note**

* > The download URL is valid for 12 hours. After the URL expires, *FileExpired* is `true`. Submit a new task to obtain a new URL.

* > The presentation of billing fields in the CSV file downloaded from *DownloadUrl* has changed. For change details and field descriptions, see [Best practices for parsing usage details CSV files](usage-csv-charge-value-list-best-practice.md).

**DownloadUrlExpireTime** `number`  |  URL expiration time

`Task.DownloadUrlExpireTime`

Expiration time of the download URL as a UTC Unix timestamp in seconds. The URL is valid for 12 hours, after which the corresponding TOS file is deleted.

**EndTime** `number`  |  End time

`Task.EndTime`

Task end time as a UTC Unix timestamp in seconds, when the task entered the `Succeeded` or `Failed` terminal state. This field is not returned while the task is being processed.

**ErrorMessage** `string`  |  Failure reason

`Task.ErrorMessage`

Guidance returned when the task fails. It has a valid value only when *Status* is `Failed`.

**ExportCount** `number`  |  Exported row count

`Task.ExportCount`

Number of rows written to the CSV file, up to `100000`. If *TotalCount* is greater than *ExportCount*, the export result was truncated because it reached the per\-task row limit.

**Max**: `100000`

**FileExpired** `number`  |  File expiration indicator

`Task.FileExpired`

Indicates whether the file has expired. The value is `true` when the current time is greater than or equal to *DownloadUrlExpireTime*.

**QueryParams** `string`  |  Original request parameters

`Task.QueryParams`

JSON string containing the original request parameters, used for task replay or auditing.

**StartTime** `number`  |  Start time

`Task.StartTime`

Task start time as a UTC Unix timestamp in seconds, corresponding to the time when the task status changed to `Running`. This field is not returned before the task is scheduled. If a system retry is triggered, this field always retains the initial start time.

**TotalCount** `number`  |  Total matched rows

`Task.TotalCount`

Total number of matched rows. Use it to determine whether *ExportCount* was truncated by the per\-task limit of `100000` rows.
