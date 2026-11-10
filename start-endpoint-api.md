# StartEndpoint

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=StartEndpoint&Version=2024-01-01`

Starts a specified inference endpoint and restores it from the stopped state to an available state.

> **Note**

> You can start only an endpoint in the `Stopped` state. Starting an endpoint that is already in the `Running` state returns an error. The start operation is asynchronous. After the API call succeeds, the endpoint enters the `Starting` state and changes to `Running` when ready. For postpaid endpoints, billing starts when the endpoint enters the `Running` state.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Id** `string` `Required`  |  Inference endpoint ID

The ID of the inference endpoint to start. It starts with `ep-`. View it on the Inference endpoints page in the BytePlus console, or obtain it by calling *ListEndpoints*.

## Response parameters

This API has no response\-specific parameters. On success the HTTP status is `200` and the response `Result` is an empty object `{}`.
