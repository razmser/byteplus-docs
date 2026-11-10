# ListEndpoints

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=ListEndpoints&Version=2024-01-01`

Lists inference endpoints with pagination and supports filtering by project, tag, model, status, and other criteria.

> **Note**

> This API returns only endpoints in projects that the caller has permission to view. For cross\-project queries, switch projects or explicitly specify *ProjectName*. The response contains summary endpoint information (*ListEndpointItem*). To retrieve the complete configuration, call *GetEndpoint* with the endpoint ID.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Filter** `object`  |  Endpoint filter criteria

Endpoint filter criteria. Subfields use AND logic. Format: `{ "Statuses": ["Running"], "FoundationModelName": "seed-2-0-pro-260328" }`.

**CustomModelIds** `string[]`  |  Custom model ID filter

`Filter.CustomModelIds`

Filters for exact matches against a list of custom model IDs.

**Domain** `string`  |  Endpoint type filter

`Filter.Domain`

Filters by endpoint type. Valid values:

* `Ark`

* `Mlp`

**EndpointModelTypes** `string[]`  |  Model type filter

`Filter.EndpointModelTypes`

Filters by the type of model used by the endpoint. Valid values:

* `FoundationModel`

* `CustomModel`

* `ServiceModel`

**FoundationModelName** `string`  |  Base model name filter

`Filter.FoundationModelName`

Performs a fuzzy match by base model name.

**Ids** `string[]`  |  Endpoint ID filter

`Filter.Ids`

Filters for exact matches against a list of endpoint IDs.

**ModelOrServiceName** `string`  |  Model or service name filter

`Filter.ModelOrServiceName`

Performs a fuzzy match by model name or MLP service name.

**ModelVersions** `string[]`  |  Model version filter

`Filter.ModelVersions`

Filters for exact matches against a list of base model versions.

**Name** `string`  |  Endpoint name filter

`Filter.Name`

Performs a fuzzy match by endpoint name.

**PaymentType** `string`  |  Billing method filter

`Filter.PaymentType`

Filters by billing method. Valid values:

* `ModelUnit`: Model unit.

* `ScaleTier`: TPM guarantee package.

* Omitted or empty: Does not filter by billing method.

**ServiceInfoIds** `string[]`  |  MLP service ID filter

`Filter.ServiceInfoIds`

Filters by a list of MLP inference service IDs. This field applies only when *Domain* is `Mlp`.

**Statuses** `string[]`  |  Status filter

`Filter.Statuses`

Filters by endpoint status. Valid values:

* `Running`

* `Scheduling`

* `Abnormal`

* `Stopped`

**PageNumber** `integer` `Default 1`  |  Starting page number

The page number for pagination. The value starts from 1.

**PageSize** `integer` `Default 10`  |  Items per page

The number of items displayed per page. The value ranges from 1 to 100.

**ProjectName** `string` `Default default`  |  Project name

Filters endpoints by exact project name.

**SortBy** `string` `Default CreateTime`  |  Sorting field

The field used for sorting. Valid values:

* `CreateTime` (default): Creation time.

* `UpdateTime`: Update time.

**SortOrder** `string` `Default Desc`  |  Sort order

The sort order. Valid values:

* `Asc`: Ascending order.

* `Desc` (default): Descending order.

**TagFilters** `object[]`  |  Tag filter criteria

Filters endpoints by tags. Multiple filters use AND logic, while multiple *Values* for the same *Key* use OR logic. Format: `[ { "Key": "env", "Values": ["stg", "boe"] } ]`.

**Key** `string` `Required`  |  Tag key

`TagFilters.Key`

The tag key. The length must be 1 to 64 characters.

**Values** `string[]`  |  Tag value list

`TagFilters.Values`

A list of tag values. If empty, all tag values under the specified *Key* match.

## Response parameters

**Items** `object[]`  |  Endpoint list

A list of endpoints. Each array item contains summary information about one endpoint. Format: `[ { "Id": "ep-20260507230659-skvgn", "Name": "curl_bot_test", "Status": "Running" } ]`.

**CreateTime** `string`  |  Creation time

`Items.CreateTime`

The creation time in RFC 3339 format.

**EndpointModelType** `string`  |  Model type

`Items.EndpointModelType`

The type of model used by the endpoint. Valid values:

* `FoundationModel`

* `CustomModel`

* `ServiceModel`

**Id** `string`  |  Endpoint ID

`Items.Id`

The inference endpoint ID.

**ModelReference** `object`  |  Model reference

`Items.ModelReference`

The model reference used by the endpoint. Format: `{ "FoundationModel": { "Name": "seed-2-0-pro-260328", "ModelVersion": "250615" } }`.

**CustomModelId** `string`  |  Custom model ID

`Items.ModelReference.CustomModelId`

The custom model ID, which starts with `cm-`. Specify either this field or *FoundationModel*.

**FoundationModel** `object`  |  Foundation model reference

`Items.ModelReference.FoundationModel`

The foundation model reference. Specify either this field or *CustomModelId*. This field is required when you specify a foundation model.

**ModelVersion** `string`  |  Foundation model version

`Items.ModelReference.FoundationModel.ModelVersion`

The foundation model version. You can query it by calling *ListFoundationModels*.

**Name** `string`  |  Foundation model name

`Items.ModelReference.FoundationModel.Name`

The foundation model name. You can query it by calling *ListFoundationModels*.

**ConfigurationId** `string`  |  Model configuration ID

`Items.ModelReference.FoundationModel.ConfigurationId`

The model configuration ID. If empty, the default model configuration is used.

**Name** `string`  |  Endpoint name

`Items.Name`

The endpoint name.

**ProjectName** `string`  |  Project name

`Items.ProjectName`

The project name.

**RateLimit** `object`  |  Rate limit configuration

`Items.RateLimit`

The rate limit configuration. Format: `{ "Rpm": 600, "Tpm": 100000 }`.

**Rpm** `integer`  |  Requests per minute

`Items.RateLimit.Rpm`

The maximum number of requests per minute (Requests Per Minute).

**Tpm** `integer`  |  Request tokens per minute

`Items.RateLimit.Tpm`

The maximum number of tokens per minute (Tokens Per Minute).

**FastTpm** `integer`  |  Low\-latency TPM limit

`Items.RateLimit.FastTpm`

**FlexTpm** `integer`  |  Flex TPM limit

`Items.RateLimit.FlexTpm`

**Ipm** `integer`  |  Images generated per minute

`Items.RateLimit.Ipm`

The maximum number of images generated per minute (Images Per Minute). This field applies only to image generation models.

**OffPeakFlexTpm** `integer`  |  Off\-peak Flex TPM limit

`Items.RateLimit.OffPeakFlexTpm`

**Status** `string`  |  Endpoint status

`Items.Status`

The endpoint status. Valid values:

* `Running`

* `Scheduling`

* `Abnormal`

* `Stopped`

**Tags** `object[]`  |  Tag list

`Items.Tags`

A list of tags bound to the endpoint.

**Key** `string`  |  Tag key

`Items.Tags.Key`

The tag key.

**Value** `string`  |  Tag value

`Items.Tags.Value`

The tag value.

**UpdateTime** `string`  |  Update time

`Items.UpdateTime`

The update time in RFC 3339 format.

**ContentGeneration** `object`  |  Image and video generation configuration

`Items.ContentGeneration`

Configuration specific to image and video generation models. Format: `{ "ConcurrentRequests": 10, "CreateTaskRpm": 60 }`.

**AudioConcurrentRequests** `integer`  |  Concurrent audio requests

`Items.ContentGeneration.AudioConcurrentRequests`

**AudioRpm** `integer`  |  Audio request RPM limit

`Items.ContentGeneration.AudioRpm`

**ConcurrentRequests** `number`  |  Concurrent requests

`Items.ContentGeneration.ConcurrentRequests`

**ConcurrentRequestsFor4K** `integer`  |  Concurrent 4K requests

`Items.ContentGeneration.ConcurrentRequestsFor4K`

**CreateTaskRpm** `number`  |  Tasks per minute

`Items.ContentGeneration.CreateTaskRpm`

**CreateTaskRpmFor4K** `integer`  |  4K tasks per minute

`Items.ContentGeneration.CreateTaskRpmFor4K`

**Description** `string`  |  Endpoint description

`Items.Description`

The endpoint description.

**ModelUnitId** `string`  |  Model unit ID

`Items.ModelUnitId`

The model unit (PTU) ID, which starts with `ptu-`.

**PaymentType** `string`  |  Billing method

`Items.PaymentType`

The billing method. Valid values:

* `ModelUnit`: Model unit.

* `ScaleTier`: TPM guarantee package.

* Omitted or empty: Pay\-as\-you\-go token billing.

**StatusReason** `string`  |  Status details

`Items.StatusReason`

Additional status information.

**PageNumber** `integer`  |  Current page number

The page number returned, which matches the *PageNumber* request parameter.

**PageSize** `integer`  |  Items per page

The number of items per page returned, which matches the *PageSize* request parameter.

**TotalCount** `integer`  |  Total number of endpoints

The total number of endpoints that match the filter criteria.
