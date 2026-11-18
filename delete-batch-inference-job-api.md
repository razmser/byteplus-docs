# DeleteBatchInferenceJob

Deletes a batch inference task.

## Debugging

## Request parameters

The following table describes only the request parameters specific to this API and some common parameters. For more information, see [Common parameters](https://docs.byteplus.com/en/docs/byteplus-platform/reference-common-parameters).

|Parameter |Type |Required |Example Value |Description |
|---|---|---|---|---|
|Action |String |Yes |DeleteBatchInferenceJob |The operation to be performed, value: DeleteBatchInferenceJob. |
|Version |String |Yes |2024\-01\-01 |The version of the API, value: 2024\-01\-01. |
|DryRun |Boolean |No |true |Whether to only perform a pre\-check for this request. Values: \- true: Send a check request. \- false (default): Send a normal request |
|Id |String |Yes |\- |The ID of the batch inference task. |

## Response parameters

This API has no specific response parameters. For more information, see [Return Structure](delete-batch-inference-job-api.md).

## Sample request

```json
{
    "Id": "IS4VOHMRBj",
    "DryRun": false
}
```

## Sample response

```Plain Text
 {
  "ResponseMetadata": {
    "RequestId": "202411012044302531090851016F6034",
    "Action": "DeleteBatchInferenceJob",
    "Version": "2024-01-01",
    "Service": "ark",
    "Region": "ap-southeast"
  },
  "Result": {}
}
```

## Error codes

For a list of error codes, see [Common Error Codes](error-codes.md).
