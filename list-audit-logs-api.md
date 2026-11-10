# Retrieve Security audit logs

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=ListAuditLogs&Version=2024-01-01`

Queries and audits security\-related events. Currently supported events include secure sandbox login, outbound connections from secure sandboxes, secure sandbox container escapes, vArmor protection, KMS access, and TKS access.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Filter** `object` `Required`  |  Filter conditions

The filter conditions for audit logs. You can specify a time range, risk levels, a log type, and custom conditions.

**LogType** `string` `Required`  |  Log type

`Filter.LogType`

The audit log type to filter by. `SshLogin` indicates SSH login to a secure sandbox, `ContainerLogin` indicates local login to a secure sandbox, `Connection` indicates outbound network connections from a secure sandbox, `ContainerBreakout` indicates a secure sandbox container escape, `VarmorDefence` indicates vArmor protection, `KMSAccess` indicates KMS access, and `TksAccess` indicates Trusted Key Service (TKS) key access. Options: `SshLogin` / `ContainerLogin` / `Connection` / `ContainerBreakout` / `VarmorDefence` / `KMSAccess` / `TksAccess` / `AuthStatistic` / `Auth`.

> **Note**

> `TksAccess` is currently a paid feature in private beta. Contact your sales manager to enable it if needed.

**Conditions** `object[]`  |  Custom filter conditions

`Filter.Conditions`

The list of custom filter conditions, which filter audit logs using combinations of fields, operators, and values.

**Field** `string` `Required`  |  Field name

`Filter.Conditions.Field`

The name of the field to filter by.

**Operator** `string` `Required`  |  Operator

`Filter.Conditions.Operator`

The comparison operator used in the filter condition.

**Value** `string` `Required`  |  Filter value

`Filter.Conditions.Value`

The target value to compare against in the filter condition.

**EndTime** `integer`  |  End time

`Filter.EndTime`

The end of the query time range, as a Unix timestamp in seconds.

**RiskLevels** `string[]`  |  Risk level filter

`Filter.RiskLevels`

The list of risk levels to filter by. Logs matching any level in the list are returned. `Info` indicates a normal event, `Low` indicates low risk, `Medium` indicates medium risk, `High` indicates high risk, and `Critical` indicates critical risk.

**StartTime** `integer`  |  Start time

`Filter.StartTime`

The start of the query time range, as a Unix timestamp in seconds.

**ResourceId** `string` `Required`  |  Resource ID

The ID of the audited resource, used together with *ResourceType*. When *ResourceType* is `endpoint`, specify an inference endpoint ID (such as `ep-**************-*****`). When *ResourceType* is `modelcustomizationjob`, specify a model fine\-tuning job ID (such as `mcj-**************-*****`).

**ResourceType** `string` `Required`  |  Resource type

The type of the audited resource. `endpoint` indicates an inference endpoint, and `modelcustomizationjob` indicates a model fine\-tuning job.

**PageNumber** `integer` `Default 1`  |  Starting page number

The starting page number for pagination.

**Min**: `1`

**PageSize** `integer` `Default 10`  |  Records per page

The number of records returned per page for pagination.

**Range**: `[1, 100]`

**ProjectName** `string` `Default default`  |  Project name

The name of the project to which the resource belongs. If the resource is not in the default project, specify the correct project name.

**SortBy** `string` `Default Timestamp`  |  Sorting criterion

The sort criterion. Currently, only sorting by `Timestamp` (the time of the audited activity) is supported.

**SortOrder** `string` `Default Desc`  |  Sorting order

The sort direction. Valid values:

* `Asc`: Sort in ascending order.

* `Desc`: Sort in descending order.

## Response parameters

**Items** `object[]`  |  Log list

The list of audit log entries. Each entry contains the details of one audit log.

**LogContents** `object[]`  |  Log field contents

`Items.LogContents`

The list of audit log field contents. Each element is a key\-value pair containing a structured log field.

**Key** `string`  |  Field name

`Items.LogContents.Key`

The log field key, which identifies the content of the key\-value pair. Common values include `Arguments` (the full command with arguments), `Command` (the command), `Process` (the process name), `Operation` (the hook command in vArmor protection logs), `SSH` (the four\-tuple of source/destination IP addresses and ports), and `Tag` (the log tag).

**Value** `string`  |  Field value

`Items.LogContents.Value`

The log field value paired with *Key*. For example, when *Key* is `Arguments`, the value has the form `curl 192.168.0.1`; for `Command`, `curl`; for `Process`, `/usr/bin/curl`; for `Operation`, `cat`; for `SSH`, `192.168.0.1 8122 192.168.0.2 22`; and for `Tag`, `Docker`.

**LogDetail** `string`  |  Log details

`Items.LogDetail`

The audit log details. The content varies by *LogType*. `SshLogin` shows the login method, source/destination IP addresses and ports, and process ID; `ContainerLogin` shows the login method, login command, and source/destination IP addresses and ports; `Connection` shows the connection type, connection status, source/destination IP addresses and ports, process name, and process ID; `ContainerBreakout` shows the specific escape behavior, command, process name, and process ID; `VarmorDefence` shows the command, process name, process ID, and the hook that triggered protection; `KMSAccess` shows the user ID, key name, encryption/decryption target, and operation type; `TksAccess` shows the TKS key access method (such as `CreateTksKey`, `GetTksKeyinfo`, `ExportTksPubKey`, and `ExportTksKey`).

**LogType** `string`  |  Log type

`Items.LogType`

The audit log type. `SshLogin` indicates SSH login to a secure sandbox, `ContainerLogin` indicates local login to a secure sandbox, `Connection` indicates outbound network connections from a secure sandbox, `ContainerBreakout` indicates a secure sandbox container escape, `VarmorDefence` indicates vArmor protection, `KMSAccess` indicates KMS access, and `TksAccess` indicates Trusted Key Service (TKS) key access. Options: `SshLogin` / `ContainerLogin` / `Connection` / `ContainerBreakout` / `VarmorDefence` / `KMSAcess` / `TksAccess` / `AuthStatistic` / `Auth`.

> **Note**

> `TksAccess` is currently a paid feature in private beta. Contact your sales manager to enable it if needed.

**ResourceId** `string`  |  Resource ID

`Items.ResourceId`

The ID of the audited resource, which identifies the resource together with *ResourceType*. An inference endpoint ID has the form `ep-**************-*****`; a model fine\-tuning job ID has the form `mcj-**************-*****`.

**ResourceType** `string`  |  Resource type

`Items.ResourceType`

The type of the audited resource. `endpoint` indicates an inference endpoint, and `modelcustomizationjob` indicates a model fine\-tuning job.

**RiskLevel** `string`  |  Risk level

`Items.RiskLevel`

The risk level of the audited event. `Info` indicates a normal event, `Low` indicates low risk, `Medium` indicates medium risk, `High` indicates high risk, and `Critical` indicates critical risk.

**Timestamp** `string`  |  Event time

`Items.Timestamp`

The time when the audited activity occurred, in ISO 8601 format.

**PageNumber** `integer`  |  Current page number

The page number of the current results.

**PageSize** `integer`  |  Records per page

The number of records returned per page in a paginated query.

**TotalCount** `integer`  |  Total log count

The total number of audit logs that match the filter conditions.
