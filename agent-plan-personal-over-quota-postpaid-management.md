# Manage overage billing

When your plan quota is insufficient but you still need to use models or harness services supported by the current plan, you can enable overage postpaid billing. This topic describes how to enable or disable overage postpaid billing for models and harness services in Agent Plan, along with related notes.

> **warning**

> You must complete real\-name verification before enabling overage postpaid billing. For details, see [Real\-name verification](https://docs.byteplus.com/en/docs/Account/real-name-verification).

# Model overage postpaid billing management

Prerequisite: Model service has been enabled on the [Model activation page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=model).

1. In the plan information area of [Agent Plan](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=agentPlan), click **Overpaid**.

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/bp-sub-page.png)

2. On the **Model** tab of **Post\-payment management**, enable or disable the model's overage postpaid switch as needed.

   > **warning**

   * > If model service is disabled on the [Model activation page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=model), you can no longer use overage postpaid billing for models in Agent Plan.

   * > For models that support overage postpaid billing and their postpaid rules, see [Model overage postpaid rules](agent-plan-personal-over-quota-postpaid-rules.md#model-overage-postpaid-rules).

# Harness overage postpaid billing management

## Deduction settings

* Enable deduction: After it is enabled, the corresponding harness consumes AFP in the plan first when sufficient quota remains. Use either of the following methods to enable deduction:

   * When purchasing a plan, select the relevant agreement. Deduction is enabled for supported harness services.

   * In the **Harness** area of [Agent Plan](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=agentPlan), enable deduction on the corresponding harness card.

* Disable deduction: After it is disabled, the harness service in the plan cannot be used. The corresponding harness no longer consumes AFP from the plan, and its overage postpaid billing is also disabled. To disable deduction:

   Disable it on the corresponding harness card in the Harness area.

## Overage postpaid billing settings

After you enable overage postpaid billing, usage is deducted from your plan quota first while quota remains in the current billing cycle. When the quota is exhausted, the system automatically switches to overage postpaid billing for the corresponding harness service and generates a postpaid bill. You can continue using the service without changing any configuration, such as the Base URL, API Key, or model name. If you disable overage postpaid billing, you can no longer use the corresponding harness service through the current invocation method once the quota is exhausted during the billing cycle.

You can use either of the following methods to enable or disable harness overage postpaid billing:

* In the plan information area of [Agent Plan](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=agentPlan), click **Overpaid**, then enable or disable the corresponding overage postpaid switch on the **Harness** tab as needed.

* In the **Harness** area, enable or disable the overage postpaid switch for the corresponding harness card as needed.

> **warning**

> For harness services that support overage postpaid billing and their postpaid rules, see [Harness overage postpaid rules](agent-plan-personal-over-quota-postpaid-rules.md#harness-overage-postpaid-rules).
