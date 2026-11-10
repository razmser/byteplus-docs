# GetEndpoint

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=GetEndpoint&Version=2024-01-01`

Gets details about a specified inference endpoint.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Id** `string` `Required`  |  Endpoint ID

The inference endpoint ID, which starts with `ep-`. Obtain it by calling *ListEndpoints* or from the BytePlus console.

## Response parameters

**BatchOnly** `boolean`  |  Batch inference

Indicates whether this is a batch inference endpoint. A value of `true` indicates that the endpoint supports only batch inference; `false` indicates real\-time inference.

**ContentGeneration** `object`  |  Generation configuration

Configuration specific to image or video generation models. Use it to set concurrency and tasks\-per\-minute limits.

**AudioConcurrentRequests** `integer`  |  Concurrent audio requests

`ContentGeneration.AudioConcurrentRequests`

**AudioRpm** `integer`  |  Audio request RPM limit

`ContentGeneration.AudioRpm`

**ConcurrentRequests** `number`  |  Concurrent requests

`ContentGeneration.ConcurrentRequests`

**ConcurrentRequestsFor4K** `integer`  |  Concurrent 4K requests

`ContentGeneration.ConcurrentRequestsFor4K`

**CreateTaskRpm** `number`  |  Tasks per minute

`ContentGeneration.CreateTaskRpm`

**CreateTaskRpmFor4K** `integer`  |  4K tasks per minute

`ContentGeneration.CreateTaskRpmFor4K`

**CreateTime** `string`  |  Creation time

The time when the endpoint was created, in RFC 3339 format.

**Description** `string`  |  Description

The inference endpoint description. The maximum length is 200 characters.

**EndpointModelType** `string`  |  Model type

The type of model used by the endpoint. Options: `FoundationModel`, `CustomModel`, `ServiceModel`.

**Id** `string`  |  Endpoint ID

The inference endpoint ID, which starts with `ep-` and uniquely identifies the endpoint.

**ModelReference** `object`  |  Model reference

The model reference used by the inference endpoint. The response returns either a base model (*FoundationModel*) or a custom model (*CustomModelId*).

**CustomModelId** `string`  |  Custom model ID

`ModelReference.CustomModelId`

The custom model ID, which starts with `cm-`. Specify either this field or *FoundationModel*.

**FoundationModel** `object`  |  Foundation model reference

`ModelReference.FoundationModel`

The foundation model reference. Specify either this field or *CustomModelId*. This field is required when you specify a foundation model.

**ModelVersion** `string`  |  Foundation model version

`ModelReference.FoundationModel.ModelVersion`

The foundation model version. You can query it by calling *ListFoundationModels*.

**Name** `string`  |  Foundation model name

`ModelReference.FoundationModel.Name`

The foundation model name. You can query it by calling *ListFoundationModels*.

**ConfigurationId** `string`  |  Model configuration ID

`ModelReference.FoundationModel.ConfigurationId`

The model configuration ID. If empty, the default model configuration is used.

**ModelUnitId** `string`  |  Model unit ID

The ID of the model unit (PTU) bound to the endpoint. It starts with `ptu-`. An empty value indicates postpaid billing.

**Moderation** `object`  |  Content guardrail configuration

The content guardrail configuration of the endpoint.

**Strategy** `string`  |  Content safety guardrail configuration

`Moderation.Strategy`

The content safety guardrail configuration. Valid values:

* `Default` (default): Provides lower latency and broad coverage for common content safety requirements.

* `Basic`: The basic option (in public beta). Supports returning matched risk categories in the API response. Currently, only two risk categories are included: severe violations and extreme behavior.

* `Skip`: Skips content moderation.

* `Customized`: Uses custom content moderation.

> **warning**

> Using `Basic` for streaming calls is not recommended because it may increase response latency.

**CustomizedModerationInfos** `object[]`  |  Custom moderation configuration

`Moderation.CustomizedModerationInfos`

**AppID** `integer`  |  Application ID

`Moderation.CustomizedModerationInfos.AppID`

**BizType** `string`  |  Business type

`Moderation.CustomizedModerationInfos.BizType`

**RiskType** `string`  |  Risk type

`Moderation.CustomizedModerationInfos.RiskType`

**Scene** `string`  |  Moderation scenario

`Moderation.CustomizedModerationInfos.Scene`

**Name** `string`  |  Name

The inference endpoint name specified during creation. The length is 1\-64 characters.

**ProjectName** `string`  |  Project

The name of the project to which the inference endpoint belongs.

**RateLimit** `object`  |  Rate limit configuration

The rate limit configuration of the inference endpoint.

> **tip**

> An empty value indicates that the model's default rate limits are used.

**Rpm** `integer`  |  Requests per minute

`RateLimit.Rpm`

The maximum number of requests per minute (Requests Per Minute).

**Tpm** `integer`  |  Request tokens per minute

`RateLimit.Tpm`

The maximum number of tokens per minute (Tokens Per Minute).

**FastTpm** `integer`  |  Low\-latency TPM limit

`RateLimit.FastTpm`

**FlexTpm** `integer`  |  Flex TPM limit

`RateLimit.FlexTpm`

**Ipm** `integer`  |  Images generated per minute

`RateLimit.Ipm`

The maximum number of images generated per minute (Images Per Minute). This field applies only to image generation models.

**OffPeakFlexTpm** `integer`  |  Off\-peak Flex TPM limit

`RateLimit.OffPeakFlexTpm`

**RollingId** `string`  |  Version switch ID

The model version switch task ID. This value is present only while the endpoint is switching model versions.

**ScaleTierId** `string`  |  TPM guarantee package ID

The ID of the TPM guarantee package associated with the endpoint. If the endpoint is not associated with a TPM guarantee package, this value is empty.

**Status** `string`  |  Status

The status of the inference endpoint. Options: `Running`, `Scheduling`, `Abnormal`, `Stopped`.

**StatusReason** `string`  |  Status details

Additional information about the current endpoint status, such as the reason for an abnormal status. This value is empty when the endpoint is running normally.

**SupportRolling** `boolean`  |  Version switching support

Indicates whether the endpoint supports model version switching through a rolling update. A value of `true` indicates support; `false` indicates no support.

**SupportScaleTier** `boolean`  |  TPM guarantee package supported

Indicates whether the endpoint is a TPM guarantee package endpoint. A value of `true` indicates that it is; `false` indicates that it is not.

**Tags** `object[]`  |  Tags

A list of tags bound to the inference endpoint.

**Key** `string`  |  Tag key

`Tags.Key`

The tag key. The length must be 1\-64 characters.

**Value** `string`  |  Tag value

`Tags.Value`

The tag value. The maximum length is 256 characters.

**UpdateTime** `string`  |  Update time

The time when the endpoint was updated, in RFC 3339 format.
