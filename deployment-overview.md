# Inference methods overview

ModelArk supports multiple model inference methods for different business scenarios and resource priorities. This topic describes the features of each method to help you select an appropriate option.

> **tip**

> If you are new to ModelArk or want to quickly evaluate a model, call the model directly by model ID. ModelArk automatically matches the model to a preset inference service, so no additional deployment is required. For details, see [Quick start](quick-start.md).

# Inference method comparison

|Inference method |Online inference (standard) |Online inference (Flex) |Model Unit |Batch inference |
|---|---|---|---|---|
|Overview |Runs in a public resource pool and is billed by model usage. Performance is affected by platform resource availability. |Uses low\-priority resources and token\-based billing to provide best\-effort inference at a lower price. |Uses dedicated computing resources to provide resource\-guaranteed online inference for fine\-tuned models. |Does not require real\-time responses and costs half as much as Online inference (standard). |
|Key specifications |* General\-purpose inference method<br><br>* Postpaid token\-based billing<br><br>* Low integration cost<br><br>* Can be combined with other online inference methods |* Independent TPM quota<br><br>* Postpaid token\-based billing<br><br>* Higher TPM quota available during off\-peak hours upon request<br><br>* Can be combined with other online inference methods |* Dedicated computing resources for more stable and reliable performance<br><br>* High flexibility for complex scenarios and special latency or concurrency requirements |* High quota for offline processing of large data volumes<br><br>* Lower cost |
|Supported models |* Platform foundation models<br><br>* Models fine\-tuned with LoRA |Selected platform foundation models |* Platform foundation models<br><br>* Fully fine\-tuned models and models fine\-tuned with LoRA<br><br>* Uploaded custom models |* Platform foundation models<br><br>* Fully fine\-tuned models and models fine\-tuned with LoRA |
|Use cases |* Cost\-sensitive workloads without strict latency or concurrency requirements<br><br>* Workloads with unpredictable traffic that can tolerate occasional errors caused by limited resource availability |* Batch or background tasks that are latency\-insensitive, retryable, and can wait<br><br>* Non\-critical online paths |* Large\-scale inference workloads that use fully fine\-tuned models<br><br>* Production workloads with high SLA requirements that need maximum flexibility and cost efficiency |Large data workloads such as model evaluation and batch regression, where individual request execution does not need to be tracked |
|Billing method |Token usage<br><br>No charges when not invoked |Token usage<br><br>No charges when not invoked |Number of Model Units purchased<br><br><br>* Monthly prepaid billing<br><br>* Postpaid billing based on purchase duration |Token usage<br><br>No charges when not invoked |
|Price |Low |Lower; input and output token unit prices are approximately half those of Online inference (standard) |High |Lower; input and output token unit prices are approximately half those of Online inference (standard) |

# Recommendations

* For general\-purpose workloads, use [Online inference (standard)](online-inference-standard.md). You are not charged when you do not invoke the service.

* For latency\-insensitive batch or background tasks that can wait or retry after failure, use [Online inference (Flex)](online-inference-flex.md) to reduce costs.

* For enterprise workloads that require dedicated computing resources for cost\-efficient, high\-performance inference at scale, use Model Unit. For details, see [Model Unit](model-unit-overview.md).

* To let the platform manage and process an entire batch when longer completion times are acceptable, use [Batch inference](batch-inference.md#075b8a07). This method is designed for processing data at very large scale and for workloads that require task status tracking, result files, and failure recovery.

# About inference endpoints

An inference endpoint is the entry point for invoking a model. Inference endpoints are divided into the following types:

* **Preset inference endpoint**: When you invoke a ModelArk model by model ID, the system automatically matches the model to a preset inference service. If no matching service exists, the system creates one. This type is designed for new users, feature testing, and lightweight workloads.

* **Custom inference endpoint**: You create this type of endpoint. All online inference methods can be accessed through custom inference endpoints. This type provides more features and supports a wider range of business scenarios.

|Category |Feature |Preset inference endpoint |Custom inference endpoint |
|---|---|---|---|
|Inference method |Online inference (standard) |Supported |Supported |
||Online inference (Flex) |Supported |Supported |
||Model Unit |Not\-supported |Supported |
||Batch inference |Not\-supported |Not\-supported |
|Online inference |API invocation |By model ID or endpoint ID |By endpoint ID |
||Supported models |ModelArk foundation models |ModelArk foundation models and fine\-tuned models |
||Seamless model version switching |Not\-supported |Supported |
||Endpoint rate limit configuration |Supported |Supported |
||Endpoint activation and deactivation |Not\-supported |Supported |
||Monitoring |Supported |Supported |
||Security audit for sessions and transport encryption |Supported |Supported |
||Fine\-grained permission management by project and tag |Supported |Supported |
|AI App Lab |No\-code applications |Supported |Supported |
||High\-code applications |Supported |Supported |
|Model evaluation |Model evaluation |Not\-supported |Supported |
