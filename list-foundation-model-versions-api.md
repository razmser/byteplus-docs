# ListFoundationModelVersions

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=ListFoundationModelVersions&Version=2024-01-01`

Lists base model versions.

> **Note**

> This API returns only metadata for published or retiring base model versions. Visibility of unpublished versions is controlled by account permissions.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**FoundationModelName** `string` `Required`  |  Base model name

The name of the base model whose versions to query. You can obtain it by calling the ListFoundationModels API.

**Filter** `object`  |  Filter conditions

The filter conditions for the version list.

**Description** `string`  |  Description matching

`Filter.Description`

Matches the version description using fuzzy matching.

**ModelVersions** `string[]`  |  Version number matching

`Filter.ModelVersions`

Matches version numbers exactly.

**Statuses** `string[]`  |  Status matching

`Filter.Statuses`

Matches version statuses exactly. Valid values are `Unpublished`, `Published`, and `Retiring`.

**PageNumber** `integer` `Default 1`  |  Starting page number

The page number for paginated queries, starting from 1.

**PageSize** `integer` `Default 10`  |  Records per page

The number of records returned per page in a paginated query.

**Range**: `[1, 100]`

**ProjectName** `string`  |  Project name

The name of the project to which the resource belongs. Specify the actual project name when the resource is not in the default project.

**SortBy** `string` `Default CreateTime`  |  Sorting field

The field to sort by. Valid values:

* `CreateTime`: Sort by creation time

* `UpdateTime`: Sort by update time.

**SortOrder** `string` `Default Desc`  |  Sort direction

The sort direction. Valid values:

* `Asc`: Ascending order

* `Desc`: Descending order.

## Response parameters

**Items** `object[]`  |  Version list

The list of base model versions on the current page.

**CreateTime** `string`  |  Creation time

`Items.CreateTime`

The creation time of the version, in RFC3339 format.

**FoundationModelName** `string`  |  Base model name

`Items.FoundationModelName`

The name of the base model to which the version belongs.

**ModelVersion** `string`  |  Version number

`Items.ModelVersion`

The base model version number.

**Status** `string`  |  Version status

`Items.Status`

The lifecycle status of the version. Valid values:

* `Unpublished`: Unpublished

* `Published`: Published

* `Retiring`: Being retired

**UpdateTime** `string`  |  Update time

`Items.UpdateTime`

The last update time of the version, in RFC3339 format.

**ActiveConfigurationId** `string`  |  Active configuration ID

`Items.ActiveConfigurationId`

The ID of the currently active version configuration.

**Description** `string`  |  Version description

`Items.Description`

The description of the version.

**PublishTime** `string`  |  Publication time

`Items.PublishTime`

The publication time of the version, in RFC3339 format.

**PageNumber** `integer`  |  Current page number

The page number currently returned.

**PageSize** `integer`  |  Records per page

The number of records returned per page in a paginated query.

**TotalCount** `integer`  |  Total count

The total number of base model versions that meet the query conditions.
