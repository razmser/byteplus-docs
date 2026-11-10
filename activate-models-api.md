# ActivateModels - Batch activation of foundation models

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=ActivateModels&Version=2024-01-01`

Activates foundation model services for the current account in batches. This operation corresponds to activating foundation models, such as Dola foundation models, on the [Model activation page](https://console.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=model). Specify the models to activate through `FoundationModelNames`. You can confirm the activation results by calling [ListModelActivations - List activated models](list-model-activations-api.md).

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**FoundationModelNames** `string[]` `Required`  |  Foundation model name list

A non\-empty list of foundation model names to activate. Specify model identifiers rather than display names, Endpoint IDs, or product names. You can obtain the foundation model names from `Items[].FoundationModelName` returned by [ListModelActivations - List activated models](list-model-activations-api.md).

## Response parameters

This API has no response\-specific parameters. On success the HTTP status is `200` and the response `Result` is an empty object `{}`.
