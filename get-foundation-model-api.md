# GetFoundationModel

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=GetFoundationModel&Version=2024-01-01`

Gets detailed information about a base model.

> **Note**

> This API returns only the base model's own metadata, excluding specific version configurations. To query the version list, use ListFoundationModelVersions.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Name** `string` `Required`  |  Base model name

The name of the base model to query, used as its unique identifier.

## Response parameters

**AccessType** `string`  |  Visibility

The visibility of the base model. Valid values:

* `Public`: A public model accessible to all tenants.

* `Private`: A non\-public model accessible only to authorized tenants.

**CreateTime** `string`  |  Creation time

The creation time of the base model, in RFC3339 format.

**Description** `string`  |  Internal description

The internal description of the base model, intended for operations and management.

**DisplayDescription** `string`  |  Display description

The brief description displayed in the console.

**DisplayName** `string`  |  Display name

The base model name displayed in the console.

**FeaturedImage** `object`  |  Display image

The image displayed for the base model in the console, stored in TOS.

**BucketName** `string`  |  Bucket name

`FeaturedImage.BucketName`

The TOS bucket name. Use a bucket in the Asia Pacific (Johor) region (`ap-southeast-1`).

**ObjectKey** `string`  |  Object path

`FeaturedImage.ObjectKey`

The storage path of the TOS object.

**FoundationModelTag** `object`  |  Capability tags

The capability classification tags of the base model, covering dimensions such as languages, task types, and domains.

**Domains** `string[]`  |  Capability domains

`FoundationModelTag.Domains`

The list of capability domains covered by the base model. Valid values:

* `LLM`: Large language models

* `Audio`: Speech models

* `ComputerVision`: Computer vision models

* `MultiModal`: Multimodal

* `Embedding`: Embedding models

* `VLM`: Vision language models

**Languages** `string[]`  |  Supported languages

`FoundationModelTag.Languages`

The list of natural languages supported by the base model.

**TaskTypes** `string[]`  |  Supported task types

`FoundationModelTag.TaskTypes`

The list of task types supported by the base model. Valid values:

* `TextGeneration`: Text generation

* `VisualQuestionAnswering`: Image content understanding

* `TextToImage`: Text\-to\-image generation

* `ImageToImage`: Image\-to\-image generation

* `TextToVideo`: Text\-to\-video generation

* `ImageToVideo`: Image\-to\-video generation

* `TextTo3D`: Text\-to\-3D generation

* `ImageTo3D`: Image\-to\-3D generation

* `VoiceClone`: Voice cloning

* `TextToSpeech`: Text\-to\-speech

* `SpeechToText`: Speech\-to\-text

* `SpeechToSpeech`: Simultaneous interpretation

* `TextEmbedding`: Text embedding

* `ImageEmbedding`: Image embedding

* `MultimodalEmbedding`: Multimodal embedding

**UsedLibraries** `string[]`  |  Dependencies

`FoundationModelTag.UsedLibraries`

The list of libraries on which the base model depends.

**Introduction** `string`  |  User\-facing introduction

The introduction to the base model displayed to users.

**Name** `string`  |  Model name

The unique name of the base model, which globally identifies a base model.

**PrimaryVersion** `string`  |  Primary version

The currently promoted version number of the base model.

**ProjectName** `string`  |  Project

The name of the project to which the base model belongs.

**RegionWhiteList** `string[]`  |  Available regions

The allowlist of regions in which the base model is available.

**ResourceOrigin** `string`  |  Resource origin

The origin identifier of the base model resource.

**ShortName** `string`  |  Short name

The short name of the base model for compact display.

**Tags** `object[]`  |  Custom tags

The list of user\-defined key\-value tags attached to the base model.

**Key** `string`  |  Key

`Tags.Key`

The tag key.

**Value** `string`  |  Value

`Tags.Value`

The tag value.

**UpdateTime** `string`  |  Update time

The last update time of the base model, in RFC3339 format.

**VendorName** `string`  |  Provider

The name of the base model provider.
