# UpdateEndpoint

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=UpdateEndpoint&Version=2024-01-01`

Updates the mutable configuration of an existing inference endpoint, including its name, description, rate limits, tags, default model inference parameters, watermark, and content safety guardrail. Include only the fields to change. Omitted fields retain their existing values.

> **Note**

> The model used by the endpoint (*ModelReference*) cannot be changed after the endpoint is created. To use a different model, create a new endpoint. This API updates only the fields included in the request; omitted fields retain their existing values. To validate parameters without applying changes, set *DryRun* to `true`.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Id** `string` `Required`  |  Endpoint ID

The ID of the inference endpoint to update. It starts with `ep-`. Call *ListEndpoints* to obtain it.

**AllowDataCollected** `boolean`  |  Allow data collection

Specifies whether invocation data can be collected for model training or data collaboration.

**ContentGeneration** `object`  |  Image and video generation configuration

Configuration specific to image and video generation models. Omit this parameter to leave the configuration unchanged.

**AudioConcurrentRequests** `integer`  |  Concurrent audio requests

`ContentGeneration.AudioConcurrentRequests`

**AudioRpm** `integer`  |  Audio request RPM limit

`ContentGeneration.AudioRpm`

**ConcurrentRequests** `number`  |  Concurrent requests

`ContentGeneration.ConcurrentRequests`

The maximum number of concurrent requests.

**ConcurrentRequestsFor4K** `integer`  |  Concurrent 4K requests

`ContentGeneration.ConcurrentRequestsFor4K`

**CreateTaskRpm** `number`  |  Tasks per minute

`ContentGeneration.CreateTaskRpm`

The maximum number of tasks created per minute.

**CreateTaskRpmFor4K** `integer`  |  4K tasks per minute

`ContentGeneration.CreateTaskRpmFor4K`

**DataDelivery** `boolean`  |  Data collaboration

Specifies whether to enable data collaboration.

**Description** `string`  |  Endpoint description

The new endpoint description. The maximum length is 200 characters. Omit this parameter to leave the description unchanged.

**DryRun** `boolean` `Default false`  |  Dry\-run mode

Specifies whether to perform validation only. A value of `true` validates the request without updating the endpoint; `false` updates the endpoint.

**InferenceDefaultParam** `object`  |  Default inference parameters

The default inference parameter configuration. Parameters not explicitly specified when invoking the endpoint use the values configured here.

**Detail** `object`  |  Detail parameter

`InferenceDefaultParam.Detail`

**Value** `string`  |  Value

`InferenceDefaultParam.Detail.Value`

**ReasoningEffort** `object`  |  Reasoning effort

`InferenceDefaultParam.ReasoningEffort`

**Value** `string`  |  Value

`InferenceDefaultParam.ReasoningEffort.Value`

**ServiceTier** `object`  |  Service tier

`InferenceDefaultParam.ServiceTier`

**Value** `string`  |  Value

`InferenceDefaultParam.ServiceTier.Value`

**ThinkingConfig** `object`  |  Deep thinking configuration

`InferenceDefaultParam.ThinkingConfig`

**IsSupported** `boolean`  |  Whether supported

`InferenceDefaultParam.ThinkingConfig.IsSupported`

**Value** `string`  |  Value

`InferenceDefaultParam.ThinkingConfig.Value`

**Moderation** `object`  |  Content safety guardrail

The content guardrail configuration. Omit this parameter to leave the configuration unchanged. *Strategy* is required. Specify *CustomizedModerationInfos* only when *Strategy* is `Customized`.

**Strategy** `string` `Required`  |  Content safety guardrail configuration

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

**AppID** `integer` `Required`  |  Application ID

`Moderation.CustomizedModerationInfos.AppID`

**BizType** `string` `Required`  |  Business type

`Moderation.CustomizedModerationInfos.BizType`

**RiskType** `string` `Required`  |  Risk type

`Moderation.CustomizedModerationInfos.RiskType`

**Scene** `string` `Required`  |  Moderation scenario

`Moderation.CustomizedModerationInfos.Scene`

**Name** `string`  |  Endpoint name

The new endpoint name. The length must be 1 to 64 characters. Omit this parameter to leave the name unchanged.

**NeedWatermark** `boolean`  |  Enable watermarking

Specifies whether to add a watermark to generated content. If enabled, also specify *WatermarkInfo*.

**RateLimit** `object`  |  Rate limit configuration

The endpoint rate limit configuration. Omit this parameter to leave the configuration unchanged. *Rpm* and *Tpm* are required. Specify *Ipm* and *FastTpm* as needed.

**Rpm** `integer` `Required`  |  Requests per minute

`RateLimit.Rpm`

The maximum number of requests per minute (Requests Per Minute).

**Tpm** `integer` `Required`  |  Request tokens per minute

`RateLimit.Tpm`

The maximum number of tokens per minute (Tokens Per Minute).

**FastTpm** `integer`  |  Low\-latency TPM limit

`RateLimit.FastTpm`

**FlexTpm** `integer`  |  Flex TPM limit

`RateLimit.FlexTpm`

**Ipm** `integer`  |  Images generated per minute

`RateLimit.Ipm`

The maximum number of images generated per minute. This parameter applies only to image generation models.

**OffPeakFlexTpm** `integer`  |  Off\-peak Flex TPM limit

`RateLimit.OffPeakFlexTpm`

**Tags** `object[]`  |  Tag list

A list of tags bound to the endpoint. During an update, this list replaces all existing tags.

**Key** `string` `Required`  |  Tag key

`Tags.Key`

The tag key. The length must be 1 to 64 characters.

**Value** `string` `Required`  |  Tag value

`Tags.Value`

The tag value. The maximum length is 256 characters.

**UsageLimit** `object`  |  Usage limit

**Enabled** `boolean` `Required`  |  Whether enabled

`UsageLimit.Enabled`

**Limit** `integer`  |  Usage limit

`UsageLimit.Limit`

**WatermarkInfo** `object`  |  Watermark subject information

Information about the watermark subject. Specify this parameter when *NeedWatermark* is `true`.

**SocialUnifiedCreditCode** `string` `Required`  |  Unified Social Credit Code

`WatermarkInfo.SocialUnifiedCreditCode`

**SubjectName** `string` `Required`  |  Watermark subject name

`WatermarkInfo.SubjectName`

**IsContentDissemination** `number`  |  Allow content distribution

`WatermarkInfo.IsContentDissemination`

Specifies whether content distribution is allowed.

## Response parameters

This API has no response\-specific parameters. On success the HTTP status is `200` and the response `Result` is an empty object `{}`.
