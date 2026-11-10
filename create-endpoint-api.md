# Create an inference endpoint

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=CreateEndpoint&Version=2024-01-01`

Creates an inference endpoint for invoking model services. An endpoint is the entry resource for model invocation. After binding a model, you can use the Endpoint ID to send inference requests.

> **Note**

> Endpoint creation is asynchronous. After the API returns *Id*, the endpoint enters the `Scheduling` state and changes to `Running` when it is ready. Billing starts when the endpoint enters the `Running` state. To validate parameters without creating an endpoint, set *DryRun* to `true`.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**ModelReference** `object` `Required`  |  Model configuration

The model reference used by the endpoint. Specify either a base model (*FoundationModel*) or a custom model (*CustomModelId*).

**CustomModelId** `string`  |  Custom model ID

`ModelReference.CustomModelId`

The custom model ID, which starts with `cm-`. Specify either this field or *FoundationModel*.

**FoundationModel** `object`  |  Foundation model reference

`ModelReference.FoundationModel`

The foundation model reference. Specify either this field or *CustomModelId*. This field is required when you specify a foundation model.

**ModelVersion** `string` `Required`  |  Foundation model version

`ModelReference.FoundationModel.ModelVersion`

The foundation model version. You can query it by calling *ListFoundationModels*.

**Name** `string` `Required`  |  Foundation model name

`ModelReference.FoundationModel.Name`

The foundation model name. You can query it by calling *ListFoundationModels*.

**ConfigurationId** `string`  |  Model configuration ID

`ModelReference.FoundationModel.ConfigurationId`

The model configuration ID. If empty, the default model configuration is used.

**Name** `string` `Required`  |  Endpoint name

The endpoint name, which identifies the endpoint in the BytePlus console and list APIs. For easier identification, consider including the model and intended use in the name.

**AllowDataCollected** `boolean`  |  Allow data collection

Specifies whether invocation data can be collected for model training or data collaboration.

**BatchOnly** `boolean` `Default false`  |  Batch inference only

Specifies whether the endpoint is used for batch inference. A value of `true` indicates batch inference; `false` indicates real\-time inference.

**ContentGeneration** `object`  |  Image and video generation configuration

Configuration specific to image and video generation models. Use it to set concurrency and tasks\-per\-minute limits.

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

**DataDelivery** `boolean` `Default false`  |  Data collaboration

Specifies whether to enable inference data collaboration. A value of `true` allows endpoint requests and responses to be fed back for data collection; `false` disables the feature.

**DedicatedToPTU** `boolean` `Default false`  |  Dedicated model unit

Specifies whether the endpoint is dedicated to the bound model unit (PTU). A value of `true` means endpoint traffic has exclusive use of the bound model unit; `false` means the endpoint and model unit are non\-exclusive.

**Description** `string`  |  Endpoint description

The endpoint description, which helps distinguish its purpose in the BytePlus console or a list. The default value is empty.

**DryRun** `boolean` `Default false`  |  Dry\-run mode

Specifies whether to perform validation only. A value of `true` validates the request without creating an endpoint; `false` creates the endpoint.

**InferenceDefaultParam** `object`  |  Default inference parameters

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

**IntelligentRouter** `object`  |  Intelligent routing

Intelligent routing configuration used to distribute requests among multiple candidate models based on a routing strategy.

**Mode** `string` `Required`  |  Routing mode

`IntelligentRouter.Mode`

The routing mode. Valid values are `Automatic` (automatic), `CandidateSet` (candidate set), and `Ordered` (in order).

**Strategy** `string` `Required`  |  Routing strategy

`IntelligentRouter.Strategy`

The routing strategy. Valid values are `EffectFirst` (quality first), `CostFirst` (cost first), and `Balanced` (balanced).

**BaselineModel** `object`  |  Baseline model

`IntelligentRouter.BaselineModel`

**CustomModelId** `string`  |  Custom model ID

`IntelligentRouter.BaselineModel.CustomModelId`

The custom model ID, which starts with `cm-`. Specify either this field or *FoundationModel*.

**FoundationModel** `object`  |  Foundation model reference

`IntelligentRouter.BaselineModel.FoundationModel`

The foundation model reference. Specify either this field or *CustomModelId*. This field is required when you specify a foundation model.

**ModelVersion** `string` `Required`  |  Foundation model version

`IntelligentRouter.BaselineModel.FoundationModel.ModelVersion`

The foundation model version. You can query it by calling *ListFoundationModels*.

**Name** `string` `Required`  |  Foundation model name

`IntelligentRouter.BaselineModel.FoundationModel.Name`

The foundation model name. You can query it by calling *ListFoundationModels*.

**ConfigurationId** `string`  |  Model configuration ID

`IntelligentRouter.BaselineModel.FoundationModel.ConfigurationId`

The model configuration ID. If empty, the default model configuration is used.

**CandidateModels** `object[]`  |  Candidate model list.

`IntelligentRouter.CandidateModels`

A list of candidate models.

**CustomModelId** `string`  |  Custom model ID

`IntelligentRouter.CandidateModels.CustomModelId`

The custom model ID, which starts with `cm-`. Specify either this field or *FoundationModel*.

**FoundationModel** `object`  |  Foundation model reference

`IntelligentRouter.CandidateModels.FoundationModel`

The foundation model reference. Specify either this field or *CustomModelId*. This field is required when you specify a foundation model.

**ModelVersion** `string` `Required`  |  Foundation model version

`IntelligentRouter.CandidateModels.FoundationModel.ModelVersion`

The foundation model version. You can query it by calling *ListFoundationModels*.

**Name** `string` `Required`  |  Foundation model name

`IntelligentRouter.CandidateModels.FoundationModel.Name`

The foundation model name. You can query it by calling *ListFoundationModels*.

**ConfigurationId** `string`  |  Model configuration ID

`IntelligentRouter.CandidateModels.FoundationModel.ConfigurationId`

The model configuration ID. If empty, the default model configuration is used.

**IsIntelligent** `boolean`  |  Enable intelligent routing

Specifies whether to enable intelligent routing.

**Moderation** `object`  |  Content safety guardrail

The content guardrail configuration. *Strategy* is required. Specify *CustomizedModerationInfos* only when *Strategy* is `Customized`.

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

**NeedWatermark** `boolean`  |  Enable watermarking

Specifies whether to add a watermark to generated content. If enabled, also specify *WatermarkInfo* to provide information about the watermark subject.

**ProjectName** `string` `Default default`  |  Project name

The name of the project to which the resource belongs. If the resource is not in the default project (`default`), specify the actual project name.

**RateLimit** `object`  |  Rate limit configuration

The endpoint rate limit configuration. When empty, the model's default rate limits apply. *Rpm* and *Tpm* are required. Specify *Ipm* and *FastTpm* as needed.

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

The maximum number of images generated per minute (Images Per Minute). This field applies only to image generation models.

**OffPeakFlexTpm** `integer`  |  Off\-peak Flex TPM limit

`RateLimit.OffPeakFlexTpm`

**Tags** `object[]`  |  Tag list

A list of tags bound to the endpoint for resource classification and retrieval. Each tag consists of *Key* (required) and *Value* (optional).

**Key** `string` `Required`  |  Tag key

`Tags.Key`

The tag key. The length must be 1\-64 characters.

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

**Id** `string`  |  Endpoint ID

The ID of the created inference endpoint. It starts with `ep-` and can be used as the *Id* parameter in subsequent APIs such as *GetEndpoint*, *UpdateEndpoint*, and *DeleteEndpoint*.
