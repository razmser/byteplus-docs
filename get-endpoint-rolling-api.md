# GetEndpointRolling - Query inference endpoint rolling upgrade details

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=GetEndpointRolling&Version=2024-01-01`

Gets details about a specified rolling update task, including its status, canary traffic ratio, rolling update strategy, and execution logs.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Id** `string` `Required`  |  Rolling update task ID

The ID of the rolling update task to query.

## Response parameters

**RollingInfo** `object`  |  Rolling update details

Detailed information about the rolling update task.

**CreateTime** `string`  |  Creation time

`RollingInfo.CreateTime`

The time when the rolling update task was created.

**EndpointId** `string`  |  Endpoint ID

`RollingInfo.EndpointId`

The ID of the inference endpoint associated with the rolling update.

**Id** `string`  |  Rolling update task ID

`RollingInfo.Id`

The rolling update task ID.

**RollingGray** `number`  |  Canary traffic ratio

`RollingInfo.RollingGray`

The current canary traffic ratio for the rolling update. The value ranges from 0 to 1.

**RollingIn** `object`  |  Target model snapshot

`RollingInfo.RollingIn`

The target model snapshot for the rolling update, representing the version to switch to.

**ModelVersion** `string`  |  Model version

`RollingInfo.RollingIn.ModelVersion`

**Name** `string`  |  Model name

`RollingInfo.RollingIn.Name`

The rolling update task name.

**RouterInfo** `object`  |  Routing information

`RollingInfo.RollingIn.RouterInfo`

The intelligent routing configuration associated with this snapshot.

**Mode** `string`  |  Routing mode

`RollingInfo.RollingIn.RouterInfo.Mode`

The routing mode. Valid values:

* `Automatic`: Select automatically.

* `CandidateSet`: Select from the candidate model set.

* `Ordered`: Select in the specified order.

**Strategy** `string`  |  Routing strategy

`RollingInfo.RollingIn.RouterInfo.Strategy`

The routing strategy. Valid values:

* `EffectFirst`: Quality first.

* `CostFirst`: Cost first.

* `Balanced`: Balanced quality and cost.

**BaselineModel** `object`  |  Baseline model

`RollingInfo.RollingIn.RouterInfo.BaselineModel`

**CustomModelId** `string`  |  Custom model ID

`RollingInfo.RollingIn.RouterInfo.BaselineModel.CustomModelId`

The custom model ID, which starts with `cm-`. Specify either this field or *FoundationModel*.

**FoundationModel** `object`  |  Foundation model reference

`RollingInfo.RollingIn.RouterInfo.BaselineModel.FoundationModel`

The foundation model reference. Specify either this field or *CustomModelId*. This field is required when you specify a foundation model.

**ModelVersion** `string`  |  Foundation model version

`RollingInfo.RollingIn.RouterInfo.BaselineModel.FoundationModel.ModelVersion`

The foundation model version. You can query it by calling *ListFoundationModels*.

**Name** `string`  |  Foundation model name

`RollingInfo.RollingIn.RouterInfo.BaselineModel.FoundationModel.Name`

The foundation model name. You can query it by calling *ListFoundationModels*.

**ConfigurationId** `string`  |  Model configuration ID

`RollingInfo.RollingIn.RouterInfo.BaselineModel.FoundationModel.ConfigurationId`

The model configuration ID. If empty, the default model configuration is used.

**CandidateModels** `object[]`  |  Candidate model list.

`RollingInfo.RollingIn.RouterInfo.CandidateModels`

A list of candidate models.

**CustomModelId** `string`  |  Custom model ID

`RollingInfo.RollingIn.RouterInfo.CandidateModels.CustomModelId`

The custom model ID, which starts with `cm-`. Specify either this field or *FoundationModel*.

**FoundationModel** `object`  |  Foundation model reference

`RollingInfo.RollingIn.RouterInfo.CandidateModels.FoundationModel`

The foundation model reference. Specify either this field or *CustomModelId*. This field is required when you specify a foundation model.

**ModelVersion** `string`  |  Foundation model version

`RollingInfo.RollingIn.RouterInfo.CandidateModels.FoundationModel.ModelVersion`

The foundation model version. You can query it by calling *ListFoundationModels*.

**Name** `string`  |  Foundation model name

`RollingInfo.RollingIn.RouterInfo.CandidateModels.FoundationModel.Name`

The foundation model name. You can query it by calling *ListFoundationModels*.

**ConfigurationId** `string`  |  Model configuration ID

`RollingInfo.RollingIn.RouterInfo.CandidateModels.FoundationModel.ConfigurationId`

The model configuration ID. If empty, the default model configuration is used.

**RollingOut** `object`  |  Source model snapshot

`RollingInfo.RollingOut`

The original model snapshot for the rolling update, representing the version to switch from.

**ModelVersion** `string`  |  Model version

`RollingInfo.RollingOut.ModelVersion`

**Name** `string`  |  Model name

`RollingInfo.RollingOut.Name`

The rolling update task name.

**RouterInfo** `object`  |  Routing information

`RollingInfo.RollingOut.RouterInfo`

The intelligent routing configuration associated with this snapshot.

**Mode** `string`  |  Routing mode

`RollingInfo.RollingOut.RouterInfo.Mode`

The routing mode. Valid values:

* `Automatic`: Select automatically.

* `CandidateSet`: Select from the candidate model set.

* `Ordered`: Select in the specified order.

**Strategy** `string`  |  Routing strategy

`RollingInfo.RollingOut.RouterInfo.Strategy`

The routing strategy. Valid values:

* `EffectFirst`: Quality first.

* `CostFirst`: Cost first.

* `Balanced`: Balanced quality and cost.

**BaselineModel** `object`  |  Baseline model

`RollingInfo.RollingOut.RouterInfo.BaselineModel`

**CustomModelId** `string`  |  Custom model ID

`RollingInfo.RollingOut.RouterInfo.BaselineModel.CustomModelId`

The custom model ID, which starts with `cm-`. Specify either this field or *FoundationModel*.

**FoundationModel** `object`  |  Foundation model reference

`RollingInfo.RollingOut.RouterInfo.BaselineModel.FoundationModel`

The foundation model reference. Specify either this field or *CustomModelId*. This field is required when you specify a foundation model.

**ModelVersion** `string`  |  Foundation model version

`RollingInfo.RollingOut.RouterInfo.BaselineModel.FoundationModel.ModelVersion`

The foundation model version. You can query it by calling *ListFoundationModels*.

**Name** `string`  |  Foundation model name

`RollingInfo.RollingOut.RouterInfo.BaselineModel.FoundationModel.Name`

The foundation model name. You can query it by calling *ListFoundationModels*.

**ConfigurationId** `string`  |  Model configuration ID

`RollingInfo.RollingOut.RouterInfo.BaselineModel.FoundationModel.ConfigurationId`

The model configuration ID. If empty, the default model configuration is used.

**CandidateModels** `object[]`  |  Candidate model list.

`RollingInfo.RollingOut.RouterInfo.CandidateModels`

A list of candidate models.

**CustomModelId** `string`  |  Custom model ID

`RollingInfo.RollingOut.RouterInfo.CandidateModels.CustomModelId`

The custom model ID, which starts with `cm-`. Specify either this field or *FoundationModel*.

**FoundationModel** `object`  |  Foundation model reference

`RollingInfo.RollingOut.RouterInfo.CandidateModels.FoundationModel`

The foundation model reference. Specify either this field or *CustomModelId*. This field is required when you specify a foundation model.

**ModelVersion** `string`  |  Foundation model version

`RollingInfo.RollingOut.RouterInfo.CandidateModels.FoundationModel.ModelVersion`

The foundation model version. You can query it by calling *ListFoundationModels*.

**Name** `string`  |  Foundation model name

`RollingInfo.RollingOut.RouterInfo.CandidateModels.FoundationModel.Name`

The foundation model name. You can query it by calling *ListFoundationModels*.

**ConfigurationId** `string`  |  Model configuration ID

`RollingInfo.RollingOut.RouterInfo.CandidateModels.FoundationModel.ConfigurationId`

The model configuration ID. If empty, the default model configuration is used.

**RollingStrategy** `object`  |  Rolling update strategy

`RollingInfo.RollingStrategy`

The execution strategy configuration for the rolling update.

**Step** `integer`  |  Canary step percentage

`RollingInfo.RollingStrategy.Step`

The percentage by which canary traffic increases at each step.

**WaitDuration** `integer`  |  Wait duration

`RollingInfo.RollingStrategy.WaitDuration`

The wait time, in seconds, between consecutive batches.

**RollingTraceList** `object[]`  |  Execution log list

`RollingInfo.RollingTraceList`

A list of execution logs for the rolling update. Each log records a change to the canary traffic ratio.

**CreateTime** `string`  |  Log time

`RollingInfo.RollingTraceList.CreateTime`

The time when the log entry was created.

**Message** `string`  |  Log message

`RollingInfo.RollingTraceList.Message`

The status description or error message.

**Status** `string`  |  Task status

`RollingInfo.Status`

The current status of the rolling update task.

**UpdateTime** `string`  |  Update time

`RollingInfo.UpdateTime`

The most recent update time of the rolling update task.

**GrayWhenRevert** `number`  |  Canary ratio at rollback

`RollingInfo.GrayWhenRevert`

The canary traffic ratio when a rollback is triggered.

**InferenceDefaultParam** `object`  |  Default inference parameters

`RollingInfo.InferenceDefaultParam`

The default inference parameter configuration.

**ThinkingConfig** `object`  |  Thinking configuration

`RollingInfo.InferenceDefaultParam.ThinkingConfig`

**Enabled** `boolean`  |  Enable Thinking

`RollingInfo.InferenceDefaultParam.ThinkingConfig.Enabled`

Specifies whether to enable Thinking.

**Message** `string`  |  Additional message

`RollingInfo.Message`

Additional information about the rolling update task, such as the reason for a failure.

**RevertTime** `string`  |  Rollback time

`RollingInfo.RevertTime`

The time when the rolling update was rolled back. This value is empty if no rollback occurred.
