# GetFoundationModelVersion

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=GetFoundationModelVersion&Version=2024-01-01`

Gets information about a base model version.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**FoundationModelName** `string` `Required`  |  Base model name

The name of the base model to which the version to query belongs.

**ModelVersion** `string` `Required`  |  Version number

The base model version number to query.

## Response parameters

**ActiveConfigurationId** `string`  |  Active configuration ID

The ID of the currently active version configuration.

**Configuration** `object`  |  Active configuration

The details of the currently active configuration.

**AppSettings** `object`  |  Application settings

`Configuration.AppSettings`

Settings for the model playground application.

**Greeting** `string`  |  Greeting

`Configuration.AppSettings.Greeting`

The greeting text for the application's first interaction.

**MaxInputTokenLength** `integer`  |  Maximum input length

`Configuration.AppSettings.MaxInputTokenLength`

The maximum permitted length of user input, in tokens.

**Parameters** `object[]`  |  Parameter settings

`Configuration.AppSettings.Parameters`

The list of model parameters configurable in the playground.

**Name** `string`  |  Parameter name

`Configuration.AppSettings.Parameters.Name`

The unique parameter name.

**Type** `string`  |  Parameter type

`Configuration.AppSettings.Parameters.Type`

The parameter data type. Valid values are `Int`, `Bool`, `Float`, and `String`.

**DefaultValue** `string`  |  Default value

`Configuration.AppSettings.Parameters.DefaultValue`

The default value of the parameter.

**Description** `string`  |  Description

`Configuration.AppSettings.Parameters.Description`

The description of the parameter.

**DisplayName** `string`  |  Display name

`Configuration.AppSettings.Parameters.DisplayName`

The parameter name displayed in the console.

**Max** `string`  |  Maximum value

`Configuration.AppSettings.Parameters.Max`

The maximum permitted value of the parameter.

**Min** `string`  |  Minimum value

`Configuration.AppSettings.Parameters.Min`

The minimum permitted value of the parameter.

**Options** `string[]`  |  Options

`Configuration.AppSettings.Parameters.Options`

The list of enumerated options for the parameter.

**Prompts** `object[]`  |  Preset prompts

`Configuration.AppSettings.Prompts`

The list of preset prompts for the playground application.

**Message** `string`  |  Prompt content

`Configuration.AppSettings.Prompts.Message`

The text of the preset prompt.

**Scenario** `string`  |  Applicable scenario

`Configuration.AppSettings.Prompts.Scenario`

The identifier of the scenario to which the preset prompt applies.

**SystemPrompt** `object`  |  System prompt

`Configuration.AppSettings.SystemPrompt`

Settings for the system prompt.

**Message** `string`  |  Prompt content

`Configuration.AppSettings.SystemPrompt.Message`

The text of the system prompt.

**MaxInputTokenLength** `number`  |  Maximum token length

`Configuration.AppSettings.SystemPrompt.MaxInputTokenLength`

The maximum permitted length of the system prompt, in tokens.

**ModificationAllowed** `boolean`  |  Modification allowed

`Configuration.AppSettings.SystemPrompt.ModificationAllowed`

Whether users can modify the system prompt in the playground.

**CustomizationJobSettings** `object`  |  Fine\-tuning settings

`Configuration.CustomizationJobSettings`

Settings for model fine\-tuning jobs.

**ContinuePretrainSettings** `object`  |  Continued pretraining

`Configuration.CustomizationJobSettings.ContinuePretrainSettings`

Settings for continued pretraining jobs.

**Enabled** `boolean`  |  Enabled

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.Enabled`

Whether the current training type is enabled.

**Parameters** `object[]`  |  Training parameters

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.Parameters`

The list of configurable parameters for the training job.

**Name** `string`  |  Parameter name

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.Parameters.Name`

The unique parameter name.

**Type** `string`  |  Parameter type

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.Parameters.Type`

The parameter data type. Valid values are `Bool`, `Int`, `Float`, and `String`.

**DefaultValue** `string`  |  Default value

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.Parameters.DefaultValue`

The default value of the parameter.

**Description** `string`  |  Description

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.Parameters.Description`

The description of the parameter.

**DisplayName** `string`  |  Display name

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.Parameters.DisplayName`

The parameter name displayed in the console.

**IncrementalLearningLocked** `boolean`  |  Incremental training lock

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.Parameters.IncrementalLearningLocked`

Whether the parameter value is locked against modification during incremental training.

**Max** `string`  |  Maximum value

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.Parameters.Max`

The maximum permitted value of the parameter.

**Min** `string`  |  Minimum value

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.Parameters.Min`

The minimum permitted value of the parameter.

**Options** `string[]`  |  Options

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.Parameters.Options`

The list of enumerated options for the parameter.

**SupportPresetDatasets** `object[]`  |  Preset datasets

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.SupportPresetDatasets`

The list of preset datasets that can be mixed in.

**Description** `string`  |  Description

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.SupportPresetDatasets.Description`

The description of the preset dataset.

**Labelled** `boolean`  |  Labeled

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.SupportPresetDatasets.Labelled`

Whether the preset dataset contains labels.

**Name** `string`  |  Dataset name

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.SupportPresetDatasets.Name`

The preset dataset name.

**SampleCount** `integer`  |  Sample count

`Configuration.CustomizationJobSettings.ContinuePretrainSettings.SupportPresetDatasets.SampleCount`

The number of samples in the preset dataset.

**LoraDPOSettings** `object`  |  DPO LoRA

`Configuration.CustomizationJobSettings.LoraDPOSettings`

Settings for DPO LoRA training jobs.

**Enabled** `boolean`  |  Enabled

`Configuration.CustomizationJobSettings.LoraDPOSettings.Enabled`

Whether the current training type is enabled.

**Parameters** `object[]`  |  Training parameters

`Configuration.CustomizationJobSettings.LoraDPOSettings.Parameters`

The list of configurable parameters for the training job.

**Name** `string`  |  Parameter name

`Configuration.CustomizationJobSettings.LoraDPOSettings.Parameters.Name`

The unique parameter name.

**Type** `string`  |  Parameter type

`Configuration.CustomizationJobSettings.LoraDPOSettings.Parameters.Type`

The parameter data type. Valid values are `Bool`, `Int`, `Float`, and `String`.

**DefaultValue** `string`  |  Default value

`Configuration.CustomizationJobSettings.LoraDPOSettings.Parameters.DefaultValue`

The default value of the parameter.

**Description** `string`  |  Description

`Configuration.CustomizationJobSettings.LoraDPOSettings.Parameters.Description`

The description of the parameter.

**DisplayName** `string`  |  Display name

`Configuration.CustomizationJobSettings.LoraDPOSettings.Parameters.DisplayName`

The parameter name displayed in the console.

**IncrementalLearningLocked** `boolean`  |  Incremental training lock

`Configuration.CustomizationJobSettings.LoraDPOSettings.Parameters.IncrementalLearningLocked`

Whether the parameter value is locked against modification during incremental training.

**Max** `string`  |  Maximum value

`Configuration.CustomizationJobSettings.LoraDPOSettings.Parameters.Max`

The maximum permitted value of the parameter.

**Min** `string`  |  Minimum value

`Configuration.CustomizationJobSettings.LoraDPOSettings.Parameters.Min`

The minimum permitted value of the parameter.

**Options** `string[]`  |  Options

`Configuration.CustomizationJobSettings.LoraDPOSettings.Parameters.Options`

The list of enumerated options for the parameter.

**SupportPresetDatasets** `object[]`  |  Preset datasets

`Configuration.CustomizationJobSettings.LoraDPOSettings.SupportPresetDatasets`

The list of preset datasets that can be mixed in.

**Description** `string`  |  Description

`Configuration.CustomizationJobSettings.LoraDPOSettings.SupportPresetDatasets.Description`

The description of the preset dataset.

**Labelled** `boolean`  |  Labeled

`Configuration.CustomizationJobSettings.LoraDPOSettings.SupportPresetDatasets.Labelled`

Whether the preset dataset contains labels.

**Name** `string`  |  Dataset name

`Configuration.CustomizationJobSettings.LoraDPOSettings.SupportPresetDatasets.Name`

The preset dataset name.

**SampleCount** `integer`  |  Sample count

`Configuration.CustomizationJobSettings.LoraDPOSettings.SupportPresetDatasets.SampleCount`

The number of samples in the preset dataset.

**LoraSettings** `object`  |  LoRA settings

`Configuration.CustomizationJobSettings.LoraSettings`

Settings for LoRA training jobs.

**Enabled** `boolean`  |  Enabled

`Configuration.CustomizationJobSettings.LoraSettings.Enabled`

Whether the current training type is enabled.

**Parameters** `object[]`  |  Training parameters

`Configuration.CustomizationJobSettings.LoraSettings.Parameters`

The list of configurable parameters for the training job.

**Name** `string`  |  Parameter name

`Configuration.CustomizationJobSettings.LoraSettings.Parameters.Name`

The unique parameter name.

**Type** `string`  |  Parameter type

`Configuration.CustomizationJobSettings.LoraSettings.Parameters.Type`

The parameter data type. Valid values are `Bool`, `Int`, `Float`, and `String`.

**DefaultValue** `string`  |  Default value

`Configuration.CustomizationJobSettings.LoraSettings.Parameters.DefaultValue`

The default value of the parameter.

**Description** `string`  |  Description

`Configuration.CustomizationJobSettings.LoraSettings.Parameters.Description`

The description of the parameter.

**DisplayName** `string`  |  Display name

`Configuration.CustomizationJobSettings.LoraSettings.Parameters.DisplayName`

The parameter name displayed in the console.

**IncrementalLearningLocked** `boolean`  |  Incremental training lock

`Configuration.CustomizationJobSettings.LoraSettings.Parameters.IncrementalLearningLocked`

Whether the parameter value is locked against modification during incremental training.

**Max** `string`  |  Maximum value

`Configuration.CustomizationJobSettings.LoraSettings.Parameters.Max`

The maximum permitted value of the parameter.

**Min** `string`  |  Minimum value

`Configuration.CustomizationJobSettings.LoraSettings.Parameters.Min`

The minimum permitted value of the parameter.

**Options** `string[]`  |  Options

`Configuration.CustomizationJobSettings.LoraSettings.Parameters.Options`

The list of enumerated options for the parameter.

**SupportPresetDatasets** `object[]`  |  Preset datasets

`Configuration.CustomizationJobSettings.LoraSettings.SupportPresetDatasets`

The list of preset datasets that can be mixed in.

**Description** `string`  |  Description

`Configuration.CustomizationJobSettings.LoraSettings.SupportPresetDatasets.Description`

The description of the preset dataset.

**Labelled** `boolean`  |  Labeled

`Configuration.CustomizationJobSettings.LoraSettings.SupportPresetDatasets.Labelled`

Whether the preset dataset contains labels.

**Name** `string`  |  Dataset name

`Configuration.CustomizationJobSettings.LoraSettings.SupportPresetDatasets.Name`

The preset dataset name.

**SampleCount** `integer`  |  Sample count

`Configuration.CustomizationJobSettings.LoraSettings.SupportPresetDatasets.SampleCount`

The number of samples in the preset dataset.

**SftSettings** `object`  |  SFT settings

`Configuration.CustomizationJobSettings.SftSettings`

Settings for SFT training jobs.

**Enabled** `boolean`  |  Enabled

`Configuration.CustomizationJobSettings.SftSettings.Enabled`

Whether the current training type is enabled.

**Parameters** `object[]`  |  Training parameters

`Configuration.CustomizationJobSettings.SftSettings.Parameters`

The list of configurable parameters for the training job.

**Name** `string`  |  Parameter name

`Configuration.CustomizationJobSettings.SftSettings.Parameters.Name`

The unique parameter name.

**Type** `string`  |  Parameter type

`Configuration.CustomizationJobSettings.SftSettings.Parameters.Type`

The parameter data type. Valid values are `Bool`, `Int`, `Float`, and `String`.

**DefaultValue** `string`  |  Default value

`Configuration.CustomizationJobSettings.SftSettings.Parameters.DefaultValue`

The default value of the parameter.

**Description** `string`  |  Description

`Configuration.CustomizationJobSettings.SftSettings.Parameters.Description`

The description of the parameter.

**DisplayName** `string`  |  Display name

`Configuration.CustomizationJobSettings.SftSettings.Parameters.DisplayName`

The parameter name displayed in the console.

**IncrementalLearningLocked** `boolean`  |  Incremental training lock

`Configuration.CustomizationJobSettings.SftSettings.Parameters.IncrementalLearningLocked`

Whether the parameter value is locked against modification during incremental training.

**Max** `string`  |  Maximum value

`Configuration.CustomizationJobSettings.SftSettings.Parameters.Max`

The maximum permitted value of the parameter.

**Min** `string`  |  Minimum value

`Configuration.CustomizationJobSettings.SftSettings.Parameters.Min`

The minimum permitted value of the parameter.

**Options** `string[]`  |  Options

`Configuration.CustomizationJobSettings.SftSettings.Parameters.Options`

The list of enumerated options for the parameter.

**SupportPresetDatasets** `object[]`  |  Preset datasets

`Configuration.CustomizationJobSettings.SftSettings.SupportPresetDatasets`

The list of preset datasets that can be mixed in.

**Description** `string`  |  Description

`Configuration.CustomizationJobSettings.SftSettings.SupportPresetDatasets.Description`

The description of the preset dataset.

**Labelled** `boolean`  |  Labeled

`Configuration.CustomizationJobSettings.SftSettings.SupportPresetDatasets.Labelled`

Whether the preset dataset contains labels.

**Name** `string`  |  Dataset name

`Configuration.CustomizationJobSettings.SftSettings.SupportPresetDatasets.Name`

The preset dataset name.

**SampleCount** `integer`  |  Sample count

`Configuration.CustomizationJobSettings.SftSettings.SupportPresetDatasets.SampleCount`

The number of samples in the preset dataset.

**CreateTime** `string`  |  Creation time

The creation time of the version, in RFC3339 format.

**Description** `string`  |  Version description

The description of this version.

**FoundationModelName** `string`  |  Base model name

The name of the base model to which the version belongs.

**ModelVersion** `string`  |  Version number

The base model version number.

**PublishTime** `string`  |  Publication time

The publication time of the version, in RFC3339 format.

**Status** `string`  |  Version status

The lifecycle status of the version. Valid values:

* `Unpublished`: Unpublished

* `Published`: Published

* `Retiring`: Being retired

**UpdateTime** `string`  |  Update time

The last update time of the version, in RFC3339 format.
