# Batch inference (via console)

<span id="c34d231e"></span>
# Overview

Batch inference is suitable for scenarios that do not require real\-time response, allowing large amounts of data to be processed offline at a time. Compared with online inference, batch inference provides more access quotas. You do not need to pay attention to the execution of a single request. Applicable scenarios include model evaluation and batch regression.

<span id="7cb4662b"></span>
# Supported models

See [Supported models](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1399517#1cbb9a13).

> If you want to increase the total quota for batch inference, or if you have other language models that require batch inference, you can submit [a ticket](https://console.byteplus.com/workorder/create) to apply.


<span id="ba8acbff"></span>
# Batch inference options

ModelArk offers you two ways to use the batch inference service.


* Batch inference tasks: A standard offline batch processing pattern that needs to be used together with Torch Object Storage (TOS).

* Batch inference endpoint: Without relying on storage, directly request the ModelArk model interface. It is similar to the current use of online inference.


The recommended selection information is as follows.


| |Batch Inference Task |Batch Inference Endpoint |
|---|---|---|
|Scenarios |* Data has been statically stored in Torch Object Storage or other cloud vendors' object storage, HDFS, ES.<br><br>* The volume of data is large, processing more than 100 Billion Tokens or more per day.<br><br>* Batch inference scenarios with high demands on network bandwidth. |* Data is stored non\-statically in object storage, and the organization and transformation of data is complex.<br><br>* The upstream and downstream links of the ModelArk model are online and cannot be changed to offline links, or calling a single model is just a link in the entire application link. |
|Advantages |* It is more suitable for hyperscale data processing or scenarios that require large network bandwidth.<br><br>* The platform hosts the resource scheduling of all requests with optimal throughput. |* The amount of engineering renovation is minimal. |
|Limitations |* Data uploading, sharding, and catalog segmentation bring about a certain amount of engineering transformation. |* You will receive more `ServerOverloaded` errors during peak hours.<br><br>* Daily throughput is determined by platform resource constraints and user concurrency control, and daily throughput is relatively uncontrollable. |


<span id="workflow"></span>
# Create a batch inference task

Industry\-standard batch, offline usage model that needs to be used together with Torch Object Storage (TOS).

<span id="upload-file"></span>
## 1. Prepare and upload files

Please refer to [Input/Output format](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1305505#batch-inference-file) to prepare the input file containing the request you want to inference for and make sure your account has TOS enabled, then upload the prepared input file to your TOS.

<span id="batch-inference-create"></span>
## 2. Create batch inference tasks


1. On the Batch inference page, click the **Start Batch inference** button to jump to the create page.

2. Follow the console prompts to fill out the batch inference task form.

   In the invocation mode field, select **Create batch inference task**.


<span id="batch-inference-view"></span>
## 3. View and manage batch inference tasks

You can enter the task details page from the batch inference task list page or by clicking the name of the batch inference task. Then, you can view the task information, including the basic information, status, progress (including the number of processed requests/total number of requests and the number of failed requests), and configuration.

The statuses of the batch inference task are described as follows:


|**Status** |**Description** |
|---|---|
|Queueing |The task needs to wait in line due to the upper limit of the number of concurrent tasks under the account. |
|Running |The task is running. |
|Completed |All requests have been processed and the task has been completed. |
|Stopping |The task is currently in the cancel state due to system reasons such as expiration or manual termination. |
|Has stopped |The task has been cancelled. |
|Failed |Input file validation failed or the task failed due to other reasons. |
|Restart |For failed or stopped tasks, you can restart to continue processing outstanding inference requests, and completed requests will be automatically skipped. |


<span id="view-results"></span>
## 4. View output files

After the batch inference task is completed, you can click the **view result** button or click the "usage file information" in the details page to jump to the TOS page view and download the result file.

The TOS storage path of the output file is as follows:

• Output file: `{OutputDirectory}/{batch_job_id}/output/results.jsonl`

• Error file: `{OutputDirectory}/{batch_job_id}/error/errors.jsonl`

<span id="75759432"></span>
## 5. Subscribe to notifications

You can use BytePlus' [Message Notification Service (SNS)](https://console.byteplus.com/sns/) to keep track of the status of batch inference tasks.

<span id="13bc8fe9"></span>
#### Subscription process


1. Submit a support ticket to enable SNS and synchronize the application for the corresponding message event:

   * `BatchJobFinished`: Batch inference task completed.

   * `BatchJobFailed`: Batch inference task failed.

2. Create a topic on the **Topic** page.

   * Publisher: Specify the publishers

   * Service that can publish messages: `ark`

   <span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/0c0886aeb99a4b2aa344a580755504f8~tplv-goo7wpa0wc-image.image) </span>

3. Create an event subscription on the **Cloud event sub** page.

   * Product service: `ark`

   * Topic TRN: Select the topic just created

   * Event type: Select `BatchJobFinished` and `BatchJobFailed`

   <span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/02f4ff9ccfd0473595d247c1b81b0a2e~tplv-goo7wpa0wc-image.image) </span>

4. Create a subscription to subscribe to the topic you just created.

   * Push type: HTTP or HTTPS

   * Receiver address: The address that receives notifications.

   <span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/f626f8d796494a17b5ead2dff1a935a9~tplv-goo7wpa0wc-image.image) </span>


**Note**: After the subscription configuration, SNS will send a confirmation link to the receiver. You need to call back the `SubscribeURL` to confirm.

> You can also subscribe to information using [function services](https://console.byteplus.com/vefaas).

> In **Push type**, select function service.


5. After the callback is successful, the status of the subscription will become `confirmed`, indicating that the subscription has been completed.


<span id="b0b60819"></span>
#### Events


|Event |Field |
|---|---|
|`BatchJobFinished` |* EventID: notification ID<br><br>* Project: Project name<br><br>* EventName: BatchJobFinished<br><br>* EventTime: Batch inference task completion time<br><br>* AccountID: Root account ID<br><br>* JobInfo<br><br>   * JobID: Batch inference task ID<br><br>   * JobName: Batch Inference Task ID<br><br>   * Message: Task information.<br><br>   * Failure count: The number of requests that were not successfully executed when this batch inference task was completed.<br><br>   * TotalNum: The total number of tasks submitted by this batch inference task.<br><br>   * SuccessNum: The number of requests that were successfully executed when this batch inference task was completed.<br><br>   * FailFileTOSPath: failed task TOS file path<br><br>      * BucketName: Bucket name<br><br>      * ObjectKey: file name<br><br>   * SuccessFileTOSPath: success task TOS file path<br><br>      * BucketName: Bucket name<br><br>   * ObjectKey: file name |
|`BatchJobFailed` |* EventID: notification ID<br><br>* Project: Project name<br><br>* EventName: BatchJobFailed<br><br>* EventTime: Batch inference task completion time<br><br>* AccountID: Root account ID.<br><br>* JobInfo<br><br>   * JobID: Batch inference task ID<br><br>   * JobName: Batch Inference Task ID<br><br>   * Message: Cause of failure |


<span id="batch-inference-file"></span>
## Input/output format

<span id="input-file"></span>
### Input

The input file is a `.jsonl` file where each line contains the details of a single request to the API.


* The default maximum file size for enterprise users is 5 GB; the default maximum file size for individual users is 2 GB.

* Each request must contain a unique `custom_id` value (unique in a `jsonl` file) that you can use to find the corresponding request in the resulting data.

* Each request is an independent request, sent independently, and received independently. If multiple requests have the same prompt, you need to add the prompt to each request.

* The parameters in the `body` field of each line are the same as those in the request body of the API called by the underlying model, which are a valid `JSON object`.


Below is a sample input file for batch inference in the chat model. It contains two requests:

```json
{"custom_id": "request-1", "body": {"messages": [{"role": "user", "content": "Why is the sky so blue?"}], "max_tokens": 1000, "top_p": 1}}
{"custom_id": "request-2", "body": {"messages": [{"role": "system", "content": "You are an unhelpful assistant."}, {"role": "user", "content": "Why is the sky so blue?"}], "max_tokens": 1000}}
```


> In order to avoid the failure of batch inference task due to wrong file format, a script is provided for verification. Please configure your file before detection.


<Attachment link="https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/a8d1609de90e4026be06f17ec14714b6~tplv-goo7wpa0wc-image.image" name="jsonl_linter.py">jsonl_linter.py</Attachment>


<span id="output-file"></span>
### Output


* The result file is `results.jsonl`, and each successful request line in the input file corresponds to a response line in the result file, as follows:

   ```json
   {"id": "batch-req-123", "custom_id": "request-1", "response": {"status_code": 200, "request_id": "req1", "body": {"id": "chatcmpl-9P3WtvGUhW5Ty91CdlcBJPgqh1rGG", "object": "chat.completion", "created": 1711111111, "model": "Skylark-pro-32k-seed-stg-240515", "choices": [{"index": 0, "message": {"role": "assistant", "content": "The sky appears blue because of the phenomenon of \"Rayleigh scattering\". In simple terms, when sunlight enters the Earth's atmosphere, red, orange, and yellow light with longer wavelengths can penetrate the atmosphere and reach the ground directly. However, blue, purple, indigo and other colored lights with shorter wavelengths are easily blocked by particles suspended in the air, causing the light to scatter in all directions, making the sky appear blue."}, "logprobs": null, "finish_reason": "stop"}], "usage": {"prompt_tokens": 20, "completion_tokens": 9, "total_tokens": 29}}}, "error": null}
   ```
   

* The error message of any failed request will be written into the error file `errors.jsonl`, and each failed request line corresponds to a response line in the error file, as follows:

   ```json
   {"id":"batch-req-456","custom_id":"request-2","response":null,"error":{"code":"AccessDenied","type":"Forbidden","message":"The request failed because you do not have access to the requested resource."}}
   ```
   


<span id="cef7adc4"></span>
# Invoke batch inference endpoints

Without relying on storage, the ModelArk model interface is directly requested, which is close to the current use of online inference.

<span id="076e7470"></span>
## Procedure

<span id="d581da34"></span>
### 1. Create batch inference endpoint


1. Open the Batch Inference creation page .

2. Follow the page for configuration, in the invoke mode configuration item, select create batch inference endpoint.

3. Go back to the Batch inference page and click the endpoint tab on the right to view your Batch inference endpoint

> Batch inference endpoints are in the format: `ep-bi-***`.


<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/60f6239f3b9c4d1a87a7ac27fcddf2fb~tplv-goo7wpa0wc-image.image) </span>

<span id="363a0966"></span>
### 2. Batch inference using the Batch Inference endpoint SDK

See the specific call method [sample code](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1399517#01826852).

<span id="quota"></span>
# Quota

<span id="task"></span>
## TPD quota

10B TPD per model


* Quota Unit: It is TPD (Tokens Per Day), which refers to the token quota that can be consumed within 24 hours.

* Quota sharing: This quota is set based on the model, and all batch inference tasks under the account that use the same model share this quota.

   * There is no distinction between sub\-accounts, that is, all sub\-accounts under the account share the quota.

   * Do not distinguish between versions, such as 241215 and 240815 versions under Skylark\-pro\-32k share quotas.

* Quota segregation: Quotas are isolated from the rate limits of the online inference model (see [Enabling Management](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenTokenDrawer=false)[ ](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&OpenTokenDrawer=false)page). Running batch inference tasks does not consume the rate limits of online inference models.

* Overquota task: When a user submits a task that exceeds the TPD quota, it will try to continue execution when the platform resources are idle.


If you wish to increase the total batch inference quota, you can submit [a ticket](https://console.byteplus.com/workorder/create) to apply.

<span id="quota-task"></span>
## Other quotas

The account dimension also has the following quotas:


* Limit on the number of submitted tasks: The maximum number of batch inference tasks submitted within 7 days under a single project is 500, and new tasks cannot be submitted temporarily after the time limit.

* Limit on the number of tasks running at the same time: The maximum number of batch inference tasks in the "running" state under a single project is 3, and the remaining tasks will be in the "queued" state, waiting until a running slot becomes available.

> The actual number of tasks running concurrently under the account will be affected by the overall resource limitations of the platform and the task scheduling policy.


<span id="billing"></span>
# Billing

Batch inference is billed by token, which means you only need to pay for the amount of tokens consumed by processed requests. For more information, see Token usage on the batch inference task details page.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">Batch inference does not support the free trial quota.</div>


<span id="33eddf81"></span>
# Related documents


* [Batch inference (via API)](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1305505): Tutorial on implementing batch inference through APIs.

* [CreateBatchInferenceJob](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1339603): Use API to create batch inference tasks.

* [ListBatchInferenceJobs](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1339606): View batch inference tasks and task status.


<span id="0381cde3"></span>
# FAQs


* [When the task status is Running, Stopping, Stopped, or Failed, will there be output of partially successful results?](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1359411#48fce2af)

* [Based on what criteria is the "Failed" status determined? Is it based on the success/error ratio, or does any error trigger a failure?](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1359411#c7e33a93)

* [For a large number of sub-tasks where the processing time cannot be estimated, is there an upper limit for the maximum waiting time?](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1359411#544b65f6)

* [If the batch inference task is Stopped due to timeout, are the processing results before the stoppage retained?](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1359411#26324f4b)

* [Is the output length 4K, 6K, or something else?](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1359411#71cf8a1b)

* [Batch inference is billed by token usage. Is it based on input tokens, output tokens, or the sum of both?](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1359411#b4b42090)

* [How are the failed results, that is, the results in errors.jsonl, billed?](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1359411#1e98eaca)
