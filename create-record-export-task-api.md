# Create a usage record export task

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=CreateRecordExportTask&Version=2024-01-01`

Creates an asynchronous task to export user usage records. A successful submission returns a task ID. Use `GetRecordExportTask` to query the task status and export results.

> **Note**

* > An account can have no more than 3 active tasks.

* > An account can submit at most 100 export tasks in a rolling 24\-hour period. New submission requests fail after this limit is reached.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**EndTime** `number` `Required`  |  Task end time

Task end time as a UTC Unix timestamp in seconds. *EndTime* minus *StartTime* must be less than or equal to `10800` (a maximum time span of 3 hours).

> **Note**

> A single export can cover at most `10800` seconds (3 hours). To export a longer period, submit multiple tasks in 3\-hour windows.

**StartTime** `number` `Required`  |  Task start time

Task start time as a UTC Unix timestamp in seconds. The minimum value is `1777593600`, corresponding to `2026-05-01 00:00:00` UTC. Use with *EndTime* to define the time window covered by this export.

**Min**: `1777593600`

**Filters** `object[]`  |  Usage filters

An array of filters. Each element is a *Key*/*Values* pair. Multiple filters are combined with AND. A filter whose *Key* is `model_name` is required.

**Key** `string` `Required`  |  Filter field name

`Filters.Key`

Filter field name. Valid values:

* `model_name`: Filter by model name. Required.

* `model_endpoint`: Filter by model endpoint.

* `batch_job_id`: Filter by batch inference job ID.

**Values** `string[]`  |  Filter values

`Filters.Values`

An array of filter values to use with *Key*. When *Key* is `model_name`, example values include `seed-2-0-pro`, `seed-2-0-lite`, `seed-2-0-mini`, `seed-2-0-code`, `dreamina-seedance-2-0`, and `dreamina-seedance-2-0-fast`.

## Response parameters

**Task** `object`  |  New task information

Brief information about the new task, including the task ID for subsequent task status queries.

**Id** `string`  |  Task ID

`Task.Id`

The unique task ID returned by *CreateRecordExportTask*, in the format `uet-YYYYMMDDhhmmss-<5 alphanumeric characters>`, where the suffix consists of five alphanumeric characters. Use it as input to *GetRecordExportTask* to query task status and download links.
