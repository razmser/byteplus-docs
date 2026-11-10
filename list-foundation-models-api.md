# ListFoundationModels

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=ListFoundationModels&Version=2024-01-01`

Lists base models.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Filter** `object`  |  Filter conditions

The filter conditions for the base model list.

**AccessTypes** `string[]`  |  Access types

`Filter.AccessTypes`

Matches base model access types in the list exactly. Valid values are `Public` and `Private`.

**Description** `string`  |  Description matching

`Filter.Description`

Matches base model descriptions using fuzzy matching.

**DisplayName** `string`  |  Display name matching

`Filter.DisplayName`

Matches base model display names using fuzzy matching.

**FoundationModelTag** `object`  |  Capability tags

`Filter.FoundationModelTag`

Filters by base model capability tags.

**Domains** `string[]`  |  Capability domains

`Filter.FoundationModelTag.Domains`

The list of capability domains covered by the base model. Valid values:

* `LLM`: Large language models

* `Audio`: Speech models

* `ComputerVision`: Computer vision models

* `MultiModal`: Multimodal

* `Embedding`: Embedding models

* `VLM`: Vision language models

**Languages** `string[]`  |  Supported languages

`Filter.FoundationModelTag.Languages`

The list of natural languages supported by the base model.

**TaskTypes** `string[]`  |  Supported task types

`Filter.FoundationModelTag.TaskTypes`

The list of task types supported by the foundation model. Valid values:

* `Chat`: Chat

* `TextGeneration`: Text generation

* `PreTrain`: Pre\-training

* `VisualQuestionAnswering`: Image content understanding

* `TextToImage`: Text\-to\-image generation

* `ImageToImage`: Image\-to\-image generation

* `TextToVideo`: Text\-to\-video generation

* `ImageToVideo`: Image\-to\-video generation

* `TextTo3D`: Text\-to\-3D generation

* `ImageTo3D`: Image\-to\-3D generation

* `TextToSpeech`: Text\-to\-speech

* `SpeechToText`: Speech\-to\-text

* `SpeechToSpeech`: Speech\-to\-speech

* `TextEmbedding`: Text embedding

* `ImageEmbedding`: Image embedding

* `MultimodalEmbedding`: Multimodal embedding

* `Translation`: Translation

**UsedLibraries** `string[]`  |  Dependencies

`Filter.FoundationModelTag.UsedLibraries`

The list of libraries on which the base model depends.

**Introduction** `string`  |  Introduction matching

`Filter.Introduction`

Matches base model introductions using fuzzy matching.

**Name** `string`  |  Name matching

`Filter.Name`

Matches base model names using fuzzy matching.

**Names** `string[]`  |  Name list

`Filter.Names`

Matches base model names in the list exactly.

**SupportedCustomizationTypes** `string[]`  |  Supported fine\-tuning types

`Filter.SupportedCustomizationTypes`

Matches the fine\-tuning types supported by base models exactly. Valid values are `Sft`, `Lora`, `ContinuePretrain`, `FinetuneSft`, `FinetuneLoRA`, `DPOLoRA`, and `Pretrain`.

**PageNumber** `integer` `Default 1`  |  Starting page number

The starting page number for paginated queries, beginning at 1.

**PageSize** `integer` `Default 10`  |  Records per page

The number of records returned per page in a paginated query.

**Range**: `[1, 100]`

**ProjectName** `string`  |  Project name

The name of the project to which the resource belongs. The default value is `default`.

**SortBy** `string` `Default CreateTime`  |  Sorting field

The field to sort by. Valid values:

* `CreateTime`: Sort by creation time

* `UpdateTime`: Sort by update time.

**SortOrder** `string` `Default Desc`  |  Sort direction

The sort direction. Valid values:

* `Asc`: Ascending order

* `Desc`: Descending order.

**TagFilters** `object[]`  |  Tag filters

The list of filter conditions based on attached tags.

**Key** `string` `Required`  |  Key

`TagFilters.Key`

The tag key used for filtering.

**Values** `string[]`  |  Value list

`TagFilters.Values`

The list of tag values used for filtering.

## Response parameters

**Items** `object[]`  |  Model list

The list of base models on the current page.

**CreateTime** `string`  |  Creation time

`Items.CreateTime`

The creation time of the base model, in RFC3339 format.

**Name** `string`  |  Model name

`Items.Name`

The base model name, which uniquely identifies the base model globally.

**UpdateTime** `string`  |  Update time

`Items.UpdateTime`

The last update time of the base model, in RFC3339 format.

**VendorName** `string`  |  Provider

`Items.VendorName`

The name of the base model provider.

**AccessType** `string`  |  Visibility

`Items.AccessType`

The visibility of the base model. Valid values:

* `Public`: Public model

* `Private`: Private model

**Description** `string`  |  Internal description

`Items.Description`

The internal description of the base model.

**DisplayName** `string`  |  Display name

`Items.DisplayName`

The base model name displayed in the console.

**FeaturedImage** `object`  |  Display image

`Items.FeaturedImage`

The image displayed for the base model in the console, stored in TOS.

**BucketName** `string`  |  Bucket name

`Items.FeaturedImage.BucketName`

The TOS bucket name. Use a bucket in the Asia Pacific (Johor) region (`ap-southeast-1`).

**ObjectKey** `string`  |  Object path

`Items.FeaturedImage.ObjectKey`

The storage path of the TOS object.

**FoundationModelTag** `object`  |  Capability tags

`Items.FoundationModelTag`

The capability classification tags of the base model.

**Domains** `string[]`  |  Capability domains

`Items.FoundationModelTag.Domains`

The list of capability domains covered by the base model. Valid values:

* `LLM`: Large language models

* `Audio`: Speech models

* `ComputerVision`: Computer vision models

* `MultiModal`: Multimodal

* `Embedding`: Embedding models

* `VLM`: Vision language models

**Languages** `string[]`  |  Supported languages

`Items.FoundationModelTag.Languages`

The list of natural languages supported by the base model.

**TaskTypes** `string[]`  |  Supported task types

`Items.FoundationModelTag.TaskTypes`

The list of task types supported by the foundation model. Valid values:

* `Chat`: Chat

* `TextGeneration`: Text generation

* `PreTrain`: Pre\-training

* `VisualQuestionAnswering`: Image content understanding

* `TextToImage`: Text\-to\-image generation

* `ImageToImage`: Image\-to\-image generation

* `TextToVideo`: Text\-to\-video generation

* `ImageToVideo`: Image\-to\-video generation

* `TextTo3D`: Text\-to\-3D generation

* `ImageTo3D`: Image\-to\-3D generation

* `TextToSpeech`: Text\-to\-speech

* `SpeechToText`: Speech\-to\-text

* `SpeechToSpeech`: Speech\-to\-speech

* `TextEmbedding`: Text embedding

* `ImageEmbedding`: Image embedding

* `MultimodalEmbedding`: Multimodal embedding

* `Translation`: Translation

**UsedLibraries** `string[]`  |  Dependencies

`Items.FoundationModelTag.UsedLibraries`

The list of libraries on which the base model depends.

**Introduction** `string`  |  User\-facing introduction

`Items.Introduction`

The introduction to the base model. Due to response size, this field is not returned by the *ListFoundationModels* API.

**PrimaryVersion** `string`  |  Primary version

`Items.PrimaryVersion`

The currently promoted version number of the base model.

**ProjectName** `string`  |  Project

`Items.ProjectName`

The name of the project to which the base model belongs.

**Tags** `object[]`  |  Custom tags

`Items.Tags`

The list of user\-defined key\-value tags attached to the base model.

**Key** `string`  |  Key

`Items.Tags.Key`

The tag key.

**Value** `string`  |  Value

`Items.Tags.Value`

The tag value.

**PageNumber** `integer`  |  Current page number

The page number currently returned.

**PageSize** `integer`  |  Records per page

The number of records per page currently returned.

**TotalCount** `integer`  |  Total count

The total number of base models that match the filter conditions.
