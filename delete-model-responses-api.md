# Delete a model response

`DELETE https://ark.ap-southeast.bytepluses.com/api/v3/responses/{responseId}`

This topic describes how to delete a response with a specified ID.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**responseId** `string` `Required`  |  Response ID

The ID of the response to delete.

## Response parameters

**deleted** `boolean`  |  Whether the deletion was successful

Value range:

* `true`: Deleted successfully.

* `false`: Not deleted successfully.

**id** `string`  |  Response ID

The deleted response ID.

**object** `string`  |  Object type

It is always `response`.
