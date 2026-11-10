# CreateBatchInferenceJob

Creates a batch inference task.

<span id=".6LCD6K-V"></span>
## Debugging

<span id=".6K-35rGC5Y-C5pWw"></span>
## Request parameters

The following table describes only the request parameters specific to this API and some common parameters. For more information, see [Common parameters](https://docs.byteplus.com/en/docs/byteplus-platform/reference-common-parameters).


|Parameter |Type |Required |Example Value |Description |
|---|---|---|---|---|
|Action |String |Yes |CreateBatchInferenceJob |The operation to be performed. Value: CreateBatchInferenceJob. |
|Version |String |Yes |2024 \- 01 \- 01 |The version of the API. Value: 2024 \- 01 \- 01. |
|ProjectName |String |No |my \- project |Project name |
|Name |String |Yes |my \- batch \- Inference \- job |Name of the batch inference task |
|Description |String |No |my \- batch \- Inference \- job |Description of the batch inference task |
|ModelReference |Object |Yes |\- |The model selected for the batch inference task. One of the two parameters, CustomModelId (customized model) or FoundationModel (foundation model), needs to be specified. |
|InputFileTosLocation |Object |Yes |\- |File for batch inference input |
|OutputDirTosLocation |Object |Yes |\- |Folder for the output results of the batch inference task |
|CompletionWindow |String |No |\- |Maximum waiting time for the batch inference task |
|Tags |Array of Object |No |\- |Tags bound to the batch inference task |
|DryRun |Boolean |No |true |Whether to perform only a pre \- check for this request.<br><br>Values:<br><br>\* true: Send a check request.<br><br>\* false (default): Send a normal request. |


<span id=".6L-U5Zue5Y-C5pWw"></span>
## Response parameters

The following table describes only the response parameters specific to this API. For more information, see [Return Structure](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/CreateBatchInferenceJob).


|Parameter |Type |Example Value |Description |
|---|---|---|---|
|Id |String |bi\-20240918\-r2\*\*\* |The Id of the batch inference task |


<span id=".6K-35rGC56S65L6L"></span>
## Sample request

```text
POST /?Action=CreateBatchInferenceJob&Version=2024-01-01 HTTP/1.1
Host: ark.ap-southeast-1.byteplusapi.com
Content-Type: application/json; charset=UTF-8
X-Date: 20240514T132743Z
X-Content-Sha256: 287e874e******d653b44d21e
Authorization: HMAC-SHA256 Credential=Adfks******wekfwe/20240514/ap-southeast/ark/request, SignedHeaders=host;x-content-sha256;x-date, Signature=47a7d934ff7b37c03938******cd7b8278a40a1057690c401e92246a0e41085f

{
 "Name": "Batch Inference Task",
 "Description": "This is a batch inference task",
 "ModelReference": {
 "FoundationModel": {
 "Name": "doubao-pro-32k",
 "ModelVersion": "240615"
 }
 },
 "InputFileTosLocation": {
 "BucketName": "my-bucket-name",
 "ObjectKey": "batch-inference-job/dataset/my-job.jsonl"
 },
 "OutputDirTosLocation": {
 "ObjectKey": "batch-inference-job/output/",
 "BucketName": "my-bucket-name"
 },
 "ProjectName": "default",
 "CompletionWindow": "1d",
 "Tags": [
 {
 "Key": "test_key",
 "Value": "test_value"
 }
 ]
}
```


<span id=".6L-U5Zue56S65L6L"></span>
## Sample response

```JSON
{ "ResponseMetadata": { "RequestId": "20240918194640ECB302768CB287CEDAC6", "Action": "CreateBatchInferenceJob", "Version": "2024-01-01", "Service": "ark", "Region": "ap-southeast" }, "Result": { "Id": "bi-2024091****-****" }
}
```


<span id=".6ZSZ6K-v56CB"></span>
## Error codes

For a list of error codes, see [Common Error Codes](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1299023).
