# StopEndpoint

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=StopEndpoint&Version=2024-01-01`

Stops a specified inference endpoint. After it is stopped, the endpoint no longer accepts inference requests. Call *StartEndpoint* to restart it.

> **Note**

> You can stop only an endpoint in the `Running` state. Stopping an endpoint that is already in the `Stopped` state returns an error. The stop operation is asynchronous. After the API call succeeds, the endpoint enters the `Stopping` state and changes to `Stopped` when the operation is complete. The endpoint does not accept inference requests while it is stopping. For postpaid endpoints, billing stops after the endpoint is stopped. Call *StartEndpoint* to restart it.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* Access Key authentication. Go to the [Access Key management](https://console.byteplus.com/iam/keymanage) page. Obtain an Access Key. This API supports only Access Key authentication.

---

## Request parameters

### Body parameters

**Id** `string` `Required`  |  Inference endpoint ID

The ID of the inference endpoint to stop. It starts with `ep-`, and the endpoint must be in the `Running` state.

## Response parameters

This API has no response\-specific parameters. On success the HTTP status is `200` and the response `Result` is an empty object `{}`.
