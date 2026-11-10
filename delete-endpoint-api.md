# DeleteEndpoint

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=DeleteEndpoint&Version=2024-01-01`

Deletes a specified inference endpoint. The endpoint cannot be recovered after deletion.

> **Note**

> After an endpoint is deleted, it **cannot be recovered**. Before deletion, the endpoint must be in the `Stopped` state and must not have any active bound Agents. For an endpoint bound to a prepaid model unit, you can delete it only when the corresponding TPM resource has been reclaimed or its creation failed. Otherwise, unsubscribe from the resource first.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Id** `string` `Required`  |  Endpoint ID

The ID of the inference endpoint to delete. It starts with `ep-`. View it on the Inference endpoints page in the BytePlus console, or obtain it by calling *ListEndpoints*.

## Response parameters

This API has no response\-specific parameters. On success the HTTP status is `200` and the response `Result` is an empty object `{}`.
