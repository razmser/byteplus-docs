# StopBatchInferenceJob

Stops a batch inference task.

<span id=".6LCD6K-V"></span>
## Debugging

<span id=".6K-35rGC5Y-C5pWw"></span>
## Request parameters

The following table describes only the request parameters specific to this API and some common parameters. For more information, see [Common parameters](https://docs.byteplus.com/en/docs/byteplus-platform/reference-common-parameters).


|Parameter |Type |Required |Example Value |Description |
|---|---|---|---|---|
|Action |String |Yes |StopBatchInferenceJob |The operation to be performed, value: StopBatchInferenceJob. |
|Version |String |Yes |2024\-01\-01 |The version of the API, value: 2024\-01\-01. |
|Id |String |Yes |bi\-202409181\-r2\*\*\* |The ID of the batch inference task. |
|DryRun |Boolean |No |false |Whether to only perform a pre\-check for this request.<br><br>Values:<br><br>\* true: Send a check request.<br><br>\* false (default): Send a normal request. |


<span id=".6L-U5Zue5Y-C5pWw"></span>
## Response parameters

This API has no specific response parameters. For more information, see [Return Structure](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/StopBatchInferenceJob).

<span id=".6K-35rGC56S65L6L"></span>
## Sample request

```text
POST /?Action=StopBatchInferenceJob&Version=2024-01-01 HTTP/1.1
Host: ark.ap-southeast-1.byteplusapi.com
Content-Type: application/json; charset=UTF-8
X-Date: 20241101T132518Z
X-Content-Sha256: 287e874e******d653b44d21e
Authorization: HMAC-SHA256 Credential=Adfks******wekfwe/20241101/ap-southeast/ark/request, SignedHeaders=host;x-content-sha256;x-date, Signature=47a7d934ff7b37c03938******cd7b8278a40a1057690c401e92246a0e41085f
{
  "Id": "WT8B0KFw"
}
```


<span id=".6L-U5Zue56S65L6L"></span>
## Sample response

```JSON
{  "ResponseMetadata": {    "RequestId": "202411012125322342121442116F88A9",    "Action": "StopBatchInferenceJob",    "Version": "2024-01-01",    "Service": "ark",    "Region": "ap-southeast"  },  "Result": {}
}
```


<span id=".6ZSZ6K-v56CB"></span>
## Error codes

For a list of error codes, see [Common Error Codes](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1299023).
