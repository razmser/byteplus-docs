# Report large language model quality issues

`POST https://ark.ap-southeast-1.byteplusapi.com/?Action=CasePlatformV1SeedBadcaseReport&Version=2024-01-01`

Use this API to report quality issues with Seed large language models. Each case must include the original model request and response. An Instance ID is returned after acceptance and can be used to query processing results. This API supports access key and ModelArk API key authentication.

### Reporting time and asset availability

This API has **no reporting time limit**. Include the original `Request` and either the non\-streaming `Response` or `StreamChunks` in arrival order directly in the request body. The service creates the case from these contents without retrieving the original request or response from upstream. Save this data when you call the model.

Images, videos, and other assets referenced in the request are fetched and copied asynchronously after the report is accepted. Report while asset URLs are still valid and keep assets available until copying completes. When using `file_id`, ensure that the file exists and has not expired. An expired asset URL does not prevent case acceptance, but causes asset copying to fail. Use [Query large language model quality issue results](case-platform-v1-seed-badcase-query-api.md) to inspect `AssetIssues[].Code`. URL fetch failures return error code `40020`.

Small images can also be provided inline as base64. The size limit for a single case is **2 MB**, including the original request, response, and encoded assets, so base64 is suitable only for small images.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* [Recommended] API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Body parameters

**Cases** `object[]` `Required`  |  Case array

The list of badcases to report.

Each item must include the original request body for the Chat or Responses API.

Also provide either `Response` (the original non\-streaming response body) or `StreamChunks` (the array of streaming response chunks). They are mutually exclusive.

> **warning**

* > Cases array length: 1 to 50 entries.

* > Maximum size per case: 2 MB.

* > Maximum total request body size: 20 MB.

**ApiType** `string` `Required`  |  API type

`Cases.ApiType`

The API type for this case.

* `chat`: Chat Completions API.

* `responses`: Responses API.

**Feedback** `string` `Required`  |  Issue description

`Cases.Feedback`

A text description of the quality issue.

**Request** `object` `Required`  |  Original request body

`Cases.Request`

The original request body sent to the model, preserved exactly according to the Chat or Responses API specification.

**BadcaseType** `string[]`  |  Issue types

`Cases.BadcaseType`

The list of issue type identifiers. Available values are maintained by `CasePlatformV1TccGetBadcaseTypeList`.

**ContactInfo** `string`  |  Contact information

`Cases.ContactInfo`

The submitter's email address or phone number.

**Response** `object`  |  Original non\-streaming response

`Cases.Response`

The original non\-streaming response body. Mutually exclusive with `StreamChunks`; provide exactly one of them.

**StreamChunks** `object[]`  |  Streaming response chunks

`Cases.StreamChunks`

The original streaming response chunks in arrival order. Mutually exclusive with `Response`; provide exactly one of them.

**Source** `string` `Default customer`  |  Feedback source

The feedback source identifier. If omitted or `null`, the default source is used.

* `huosu`: Huosu assistant

* `huoshan`: Huoshan assistant

* `qianzhi`: Qianzhi

* `customer`: Customer submission

Other values are rejected.

## Response parameters

**Total** `integer`  |  Total cases

The total number of cases in this submission.

**Accepted** `object[]`  |  Accepted cases

The list of cases that passed validation and were accepted.

**ApiType** `string`  |  API type

`Accepted.ApiType`

The API type for this case.

* `chat`: Chat Completions API.

* `responses`: Responses API.

**Index** `integer`  |  Index

`Accepted.Index`

The zero\-based index in the submitted Cases array.

**InstanceId** `string`  |  Case ID

`Accepted.InstanceId`

The instance ID assigned by the service. Use it to query the processing result.

**Model** `string`  |  Model name

`Accepted.Model`

The model name parsed from the response body.

**SubmitTime** `string`  |  Acceptance time

`Accepted.SubmitTime`

The acceptance time in ISO 8601 UTC format.

**Rejected** `object[]`  |  Rejected cases

The list of cases that failed validation and were not accepted.

**Code** `integer`  |  Error code

`Rejected.Code`

The error code for this case. See the error codes section for details.

**Index** `integer`  |  Index

`Rejected.Index`

The zero\-based index in the submitted Cases array.

**Message** `string`  |  Rejection reason

`Rejected.Message`

A human\-readable explanation of the rejection.
