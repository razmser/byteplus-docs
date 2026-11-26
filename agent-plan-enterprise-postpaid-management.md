# Manage postpaid billing

When your plan quota is insufficient but you still need to use models or harness services supported by the plan, you can enable overage postpaid billing. This topic describes how to enable or disable overage postpaid billing for models and harness services in Agent Plan Team, along with related notes.

# Model overage postpaid billing management

Prerequisite: Model service has been enabled on the [Model activation page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=model).

1. On the [Model management](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/settings) page, an administrator configures whether seats can enable model overage postpaid billing as needed.

   * When enabled: Seat users under the account can enable overage postpaid billing for the corresponding models.

   * When disabled: Seat users under the account cannot enable overage postpaid billing for the corresponding models.

2. After the administrator allows seats to enable overage postpaid billing, a seat user clicks **Overpaid** in the plan information area, then enables or disables overage postpaid billing for the corresponding model on the **Model** tab as needed.

   * When enabled: If the plan quota is insufficient, the seat user can continue using the corresponding model through overage postpaid billing.

   * When disabled: If the plan quota is insufficient, the seat user cannot continue using the corresponding model.

> **warning**

* > Seat users cannot enable overage postpaid billing for models that the administrator has not allowed.

* > For models that support overage postpaid billing and their postpaid rules, see [Model overage postpaid rules](agent-plan-enterprise-over-quota-postpaid-rules.md#model-overage-postpaid-rules).

# Harness overage postpaid billing management

## Deduction settings

1. On [Agent Plan Team](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise?agentMode=close), an administrator allows or disallows plan quota deductions for harness services as needed.

   * Allow deductions: Select the relevant agreement when purchasing the plan to allow supported harness services to consume AFP from the plan. On the **Harness management** tab, allow plan quota deductions for the corresponding harness service.

   * Disallow deductions: On the **Harness management** tab, disallow plan quota deductions for the corresponding harness service. The service can no longer be used through the plan and no longer consumes AFP from the plan. Its overage postpaid billing is also disabled.

2. After the administrator allows deductions, seat users can turn the deduction switch for the corresponding harness card on or off as needed in the **Harness** area.

   * When enabled: When using the corresponding harness, seat users consume AFP in the plan first when sufficient quota remains.

   * When disabled: Seat users cannot use the harness service in the plan. The service no longer consumes AFP from the plan, and its overage postpaid billing is also disabled.

## Overage postpaid billing settings

After you enable overage postpaid billing, usage is deducted from your plan quota first while quota remains in the current billing cycle. When the quota is exhausted, the system automatically switches to overage postpaid billing for the corresponding harness service and generates a postpaid bill. You can continue using the service without changing any configuration, such as the Base URL, API Key, or model name. If you disable overage postpaid billing, you can no longer use the corresponding harness service once the quota is exhausted during the billing cycle.

1. On [Agent Plan Team](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise?agentMode=close), an administrator enables or disables the Allow seats to enable overage postpaid billing switch for the corresponding harness as needed on the **Harness management** tab.

   * When enabled: Seat users can enable overage postpaid billing for the corresponding harness.

   * When disabled: Seat users cannot enable overage postpaid billing for the corresponding harness.

2. After the administrator allows seats to enable overage postpaid billing, seat users can use either of the following methods to enable or disable overage postpaid billing for the corresponding harness:

   * In the plan information area, click Overage postpaid management, then enable or disable the corresponding overage postpaid switch on the **Harness** tab as needed.

   * In the **Harness** area, enable or disable the overage postpaid switch for the corresponding harness card as needed.

> **warning**

> For harness services that support overage postpaid billing and their postpaid rules, see [Harness overage postpaid rules](agent-plan-enterprise-over-quota-postpaid-rules.md#harness-overage-postpaid-rules).
