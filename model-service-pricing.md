# Model service billing

# **Billing cycle**

BytePlus ModelArk model services support hourly billing and near real\-time billing. Different features support different billing cycle modes. This section describes the differences between these modes and the billing cycle supported by each model service feature.

> **Tip**

> This service upgrade will start in batches **from** **March 6, 2026 to March 31, 2026 (UTC+8)** and will gradually cover all online customer accounts. During the upgrade process, your service will not be affected. The billing time will be based on the actual billing time of the system.

## Comparison of billing cycle modes

|**Hourly billing** |**Near real\-time billing** |
|---|---|
|Issue a bill for the previous hour every hour. |Issue a bill for the previous billing cycle every 5 minutes. |
|The billing time is usually around 1\-2 hours after the end of the current billing cycle, subject to the actual billing time specified by the system. |The billing time is usually around 5\-10 minutes after the end of the current billing cycle, subject to the actual billing time specified by the system. |
|For example, the bill for the period from 16:00 to 17:00 is expected to be issued between 18:00 and 19:00. |For example, the bill for the period from 16:00 to 16:05 is expected to be issued between 16:10 and 16:15.<br><br>> **warning**<br><br><br>> To further improve billing settlement timeliness and user experience, starting July 1, 2026, **the billing cycle for Seedance 2.0 and 2.5 model series, including later versions, is shortened from 5 minutes to 30 seconds**.<br> |

> **Tip**

> BytePlus offers you two billing modes: hourly billing and near real\-time billing. These billing modes are designed to help you view your billing information more efficiently and promptly.

> We apologize for any delay in billing time due to technical reasons. The billing time and cycle are subject to the actual execution of the platform. BytePlus shall not be liable for any losses you incur from using this billing mode.

## Billing cycle for different functions of the model service

|**Model service** |**Billing method** |**Billing item** |**Billing cycle mode** |
|---|---|---|---|
|Online inference (including post\-fine\-tuning inference) |Pay\-as\-you\-go by token usage |Inference input |Near real\-time billing |
|||Inference output |Near real\-time billing |
|||Cache hit |Near real\-time billing |
|||Cache storage |Hourly billing |
||Model unit |See Model unit \> [Model Unit](model-unit-overview.md) |See Model unit \> [Model Unit](model-unit-overview.md) |
|Batch inference |Pay\-as\-you\-go by token usage |Inference input |Near real\-time billing |
|||Inference output |Near real\-time billing |
|||Cache hit |Near real\-time billing |
|Fine\-tuning |Paid by computing power |Paid by computing power |Hourly billing |
