# ListBatchInferenceJobs

Obtains a list of batch inference tasks.

<span id=".6LCD6K-V"></span>
## Debugging

<span id=".6K-35rGC5Y-C5pWw"></span>
## Request parameters

The following table describes only the request parameters specific to this API and some common parameters. For more information, see [Common parameters](https://docs.byteplus.com/en/docs/byteplus-platform/reference-common-parameters).


|Parameter |Type |Required |Example Value |Description |
|---|---|---|---|---|
|Action |String |Yes |ListBatchInferenceJobs |The operation to be performed. Value: ListBatchInferenceJobs. |
|Version |String |Yes |2024\-01\-01 |The version of the API. Value: 2024\-01\-01. |
|ProjectName |String |No |\- |Project name |
|PageNumber |Integer |No |\- |The starting page number for paginated query, starting from 1, with a default value of 1 |
|PageSize |Integer |No |10 |The number of records displayed per page for paginated query. Values: \- Minimum: 1 \- Maximum: 100 \- Default: 10 |
|SortOrder |String |No |\- |Specify the sorting order. Allowed values: \- Asc: Ascending order \- Desc: Descending order |
|SortBy |String |No |CreateTime |Specify the sorting indicator. Allowed values: \* CreateTime: Creation time \* UpdateTime: Update time |
|TagFilters |Array of Object |No |\- |Filter conditions based on the bound tags |
|Filter |Object |No |\- |Filter conditions for querying batch inference jobs |


<span id=".6L-U5Zue5Y-C5pWw"></span>
## Response parameters

The following table describes only the response parameters specific to this API. For more information, see [Return Structure](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/ListBatchInferenceJobs).


|Parameter |Type |Example Value |Description |
|---|---|---|---|
|TotalCount |Integer |\- |The number of batch inference jobs |
|PageNumber |Integer |1 |The starting page number for paginated query, starting from 1, with a default value of 1. |
|PageSize |Integer |10 |The number of records displayed per page for paginated query. Values: \- Minimum: 1 \- Maximum: 100 \- Default: 10 |
|Items |Array of Object |\- |Array of batch inference jobs |


<span id=".6K-35rGC56S65L6L"></span>
## Sample request

```text
{
 "PageNumber": 1,
 "PageSize": 10,
 "ProjectName":"default",
 "Filter": {
 "Ids": [
 "bi-20240918194641-r2nwq"
 ]
 },
 "TagFilters": [
 {
 "Key": "sys:ark:createdBy",
 "Values": [
 "IAMUser/12345678/test.user@xxxxxx.com"
 ]
 }
 ]
}
```


<span id=".6L-U5Zue56S65L6L"></span>
## Sample response

```JSON
{ "ResponseMetadata": { "RequestId": "2024091819503335D2321703C1A3CCA381", "Action": "ListBatchInferenceJobs", "Version": "2024-01-01", "Service": "ark", "Region": "ap-southeast-1" }, "Result": { "TotalCount": 1, "PageNumber": 1, "PageSize": 10, "Items": [ { "Id": "bi-20240918194641-r2nwq", "Name": "Batch inference task", "Description": "This is a batch inference task", "InputFileTosLocation": { "BucketName": "my-bucket-name", "ObjectKey": "batch-inference-job/dataset/my-job.jsonl" }, "OutputDirTosLocation": { "BucketName": "my-bucket-name", "ObjectKey": "batch-inference-job/output/" }, "CompletionWindow": "1d", "Status": { "Phase": "Completed", "PhaseTime": "2024-09-18T11:50:00Z", "Message": "Batch inference task completed" }, "ModelReference": { "FoundationModel": { "Name": "doubao-pro-32k", "ModelVersion": "240615" } }, "RequestCounts": { "Total": 2, "Completed": 2, "Failed": 0 }, "Tags": [ { "Key": "sys:ark:createdBy", "Value": "IAMUser/30805480/test@xxxxxx.com" }, { "Key": "test_key", "Value": "test_value" } ], "ProjectName": "default", "CreateTime": "2024-09-18T11:46:41Z", "UpdateTime": "2024-09-18T11:50:00Z", "ExpireTime": "2024-09-19T11:46:41Z" } ] }
}
```


<span id=".6ZSZ6K-v56CB"></span>
## Error codes

For a list of error codes, see [Common Error Codes](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1299023).
