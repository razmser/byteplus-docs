# GetBatchInferenceJob

Obtains a batch inference task.

<span id=".6LCD6K-V"></span>
## Debugging

<span id=".6K-35rGC5Y-C5pWw"></span>
## Request parameters

The following table describes only the request parameters specific to this API and some common parameters. For more information, see [Common parameters](https://docs.byteplus.com/en/docs/byteplus-platform/reference-common-parameters).


|Parameter |Type |Required |Example Value |Description |
|---|---|---|---|---|
|Action |String |Yes |GetBatchInferenceJob |The operation to be performed, value: GetBatchInferenceJob. |
|Version |String |Yes |2024\-01\-01 |The version of the API, value: 2024\-01\-01. |
|Id |String |Yes |\- |The ID of the batch inference task. |


<span id=".6L-U5Zue5Y-C5pWw"></span>
## Response parameters

The following table describes only the response parameters specific to this API. For more information, see [Return Structure](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/GetBatchInferenceJob).


|Parameter |Type |Example Value |Description |
|---|---|---|---|
|Id |String |\- |The ID of the batch inference task |
|Name |String |\- |The name of the batch inference task |
|Description |String |\- |The description of the batch inference task |
|InputFileTosLocation |Object |\- |The TOS address of the file passed into the batch inference task. Sub\-fields: `BucketName` (String, TOS bucket name), `ObjectKey` (String, object key path within the bucket). |
|OutputDirTosLocation |Object |\- |The TOS address for the result output of the batch inference task. Sub\-fields: `BucketName` (String, TOS bucket name), `ObjectKey` (String, object key prefix). |
|ErrorFileTosLocation |Object |\- |The TOS address of the error information output by the batch inference task. Sub\-fields: `BucketName` (String, TOS bucket name), `ObjectKey` (String, object key path). |
|CompletionWindow |String |\- |The maximum waiting time for the batch inference task |
|Status |Object |\- |The status meta \- information of the batch inference task |
|ModelReference |Object |\- |The model selected for the batch inference task |
|RequestCounts |Object |\- |The number of requests for the batch inference task |
|TokenUsage |Object |\- |The token usage of the batch inference task |
|Tags |Array of Object |\- |The tags bound to the batch inference task |
|ProjectName |String |\- |The project name |
|CreateTime |String |\- |The creation time of the batch inference task, in RFC3339 format |
|UpdateTime |String |\- |The update time of the batch inference task, in RFC3339 format |
|ExpireTime |String |\- |The expiration time of the batch inference task, in RFC3339 format |


<span id=".6K-35rGC56S65L6L"></span>
## Sample request

```text
{
    "Id": "7WtYtkPG9x"
}
```


<span id=".6L-U5Zue56S65L6L"></span>
## Sample response

```JSON
{ "ResponseMetadata": { "RequestId": "2024091910512025A35A851624E4541879", "Action": "GetBatchInferenceJob", "Version": "2024-01-01", "Service": "ark", "Region": "ap-southeast" }, "Result": { "Id": "bi-20240918194641-r2nwq", "Name": "Batch Inference Task", "Description": "This is a batch inference task", "InputFileTosLocation": { "BucketName": "my-bucket-name", "ObjectKey": "batch-inference-job/dataset/my-job.jsonl" }, "OutputDirTosLocation": { "BucketName": "my-bucket-name", "ObjectKey": "batch-inference-job/output/" }, "CompletionWindow": "1d", "Status": { "Phase": "Completed", "PhaseTime": "2024-09-18T11:50:00Z", "Message": "The batch inference task has been completed" }, "ModelReference": { "FoundationModel": { "Name": "doubao-pro-32k", "ModelVersion": "240615" } }, "RequestCounts": { "Total": 2, "Completed": 2, "Failed": 0 }, "Tags": [ { "Key": "sys:ark:createdBy", "Value": "IAMUser/30805480/test@xxxxxx.com" }, { "Key": "test_key", "Value": "test_value" } ], "ProjectName": "default", "CreateTime": "2024-09-18T11:46:41Z", "UpdateTime": "2024-09-18T11:50:00Z", "ExpireTime": "2024-09-19T11:46:41Z" }
}
```


<span id=".6ZSZ6K-v56CB"></span>
## Error codes

For a list of error codes, see [Common Error Codes](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1299023).
