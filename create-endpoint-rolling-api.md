# CreateEndpointRolling - Create an inference endpoint rolling upgrade task

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=CreateEndpointRolling&Version=2024-01-01`

Creates a rolling update task for the model version of a specified inference endpoint, allowing a canary switch of the underlying model without interrupting service.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**EndpointId** `string` `Required`  |  Endpoint ID

The ID of the inference endpoint for which to create a rolling update.

**ModelReference** `object` `Required`  |  Target model configuration

The target model configuration for the rolling update. It specifies the new model version to switch to.

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

**Description** `string`  |  Rolling update description

The description of the rolling update task.

**TargetRouter** `object`  |  Target routing configuration

The target intelligent routing configuration to use after the rolling update is complete.

**Mode** `string` `Required`  |  Routing mode

`TargetRouter.Mode`

The routing mode. Valid values are `Automatic` (automatic), `CandidateSet` (candidate set), and `Ordered` (in order).

**Strategy** `string` `Required`  |  Routing strategy

`TargetRouter.Strategy`

The routing strategy. Valid values are `EffectFirst` (quality first), `CostFirst` (cost first), and `Balanced` (balanced).

**BaselineModel** `object`  |  Baseline model

`TargetRouter.BaselineModel`

**CustomModelId** `string`  |  Custom model ID

`TargetRouter.BaselineModel.CustomModelId`

The custom model ID, which starts with `cm-`. Specify either this field or *FoundationModel*.

**FoundationModel** `object`  |  Foundation model reference

`TargetRouter.BaselineModel.FoundationModel`

The foundation model reference. Specify either this field or *CustomModelId*. This field is required when you specify a foundation model.

**ModelVersion** `string` `Required`  |  Foundation model version

`TargetRouter.BaselineModel.FoundationModel.ModelVersion`

The foundation model version. You can query it by calling *ListFoundationModels*.

**Name** `string` `Required`  |  Foundation model name

`TargetRouter.BaselineModel.FoundationModel.Name`

The foundation model name. You can query it by calling *ListFoundationModels*.

**ConfigurationId** `string`  |  Model configuration ID

`TargetRouter.BaselineModel.FoundationModel.ConfigurationId`

The model configuration ID. If empty, the default model configuration is used.

**CandidateModels** `object[]`  |  Candidate model list.

`TargetRouter.CandidateModels`

A list of candidate models.

**CustomModelId** `string`  |  Custom model ID

`TargetRouter.CandidateModels.CustomModelId`

The custom model ID, which starts with `cm-`. Specify either this field or *FoundationModel*.

**FoundationModel** `object`  |  Foundation model reference

`TargetRouter.CandidateModels.FoundationModel`

The foundation model reference. Specify either this field or *CustomModelId*. This field is required when you specify a foundation model.

**ModelVersion** `string` `Required`  |  Foundation model version

`TargetRouter.CandidateModels.FoundationModel.ModelVersion`

The foundation model version. You can query it by calling *ListFoundationModels*.

**Name** `string` `Required`  |  Foundation model name

`TargetRouter.CandidateModels.FoundationModel.Name`

The foundation model name. You can query it by calling *ListFoundationModels*.

**ConfigurationId** `string`  |  Model configuration ID

`TargetRouter.CandidateModels.FoundationModel.ConfigurationId`

The model configuration ID. If empty, the default model configuration is used.

## Response parameters

**Id** `string`  |  Rolling update task ID

The ID of the newly created rolling update task. Use it to query the task status or perform a rollback.
