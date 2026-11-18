# UpdateBatchInferenceJob

Updates a batch inference task.

## Debugging

## Request parameters

The following table describes only the request parameters specific to this API and some common parameters. For more information, see [Common parameters](https://docs.byteplus.com/en/docs/byteplus-platform/reference-common-parameters).

|Parameter |Type |Required |Example Value |Description |
|---|---|---|---|---|
|Action |String |Yes |UpdateBatchInferenceJob |The operation to be performed, value: UpdateBatchInferenceJob. |
|Version |String |Yes |2024\-01\-01 |The version of the API, value: 2024\-01\-01. |
|Name |String |No |\- |Batch inference task name |
|Description |String |No |\- |Batch task description |
|DryRun |Boolean |No |true |Whether to only perform a pre\-check for this request. Values: \- true: Send a check request. \- false (default): Send a normal request |
|Id |String |Yes |\- |The ID of the batch inference task. |

## Response parameters

This API has no specific response parameters. For more information, see [Return Structure](update-batch-inference-job-api.md).

## Sample request

```json
{
    "Id": "jTcq2HdZCY",
    "Name": "2izb27TqbM",
    "Description": "lup3fM4Mu5",
    "DryRun": true
}
```

## Sample response

```Plain Text
{  "ResponseMetadata": {    "RequestId": "20241101204820216194191074D4638D",    "Action": "UpdateBatchInferenceJob",    "Version": "2024-01-01",    "Service": "ark",    "Region": "ap-southeast"  },  "Result": {}
}
```

## Error codes

For a list of error codes, see [Common Error Codes](error-codes.md).
