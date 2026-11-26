# Console guide

This topic is for administrators and seat users of ModelArk Agent Plan Team. It introduces the core console workflows and restrictions to help you efficiently configure, manage, and use Team.

# Administrator operations

Administrators have the ArkFullAccess permission. Administrators must purchase a Agent Plan Team plan and assign seats before assigned users can use the corresponding plan in coding tools. Administrators can perform the following operations in the Agent Plan Team console to ensure appropriate seat allocation and normal usage.

## Subscription management

Administrators can click [Agent Plan Team](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise?agentMode=close) to access the console and manage plans.

* **Purchase a plan**: Select and purchase an appropriate plan and number of seats based on actual enterprise requirements to ensure that enterprise users can use coding tools normally.

   * **Add seats**: If the purchased number of seats is insufficient, administrators can add seats in the console to meet growing usage needs.

   * **Notes**:

      * Purchase limits: See [Subscription rules](agent-plan-enterprise-plan-overview.md#purchase-limits).

      * Effective date: A plan takes effect immediately after purchase. Administrators are advised to assign seats and begin using the plan promptly. For details, see [Subscription rules](agent-plan-enterprise-plan-overview.md#purchase-limits).

* **Renew a plan**: You can renew an individual seat or renew multiple seats in batches.

   * **Auto\-renewal**: Select auto\-renewal **when placing an order**, or enable auto\-renewal for one or more seats on the **Seat management** page. When auto\-renewal is enabled, the plan fee for the corresponding seat for the next month is automatically charged when the seat plan expires.

   * **Manual renewal**: If auto\-renewal is not enabled, you can renew one or more seats on the **Seat management** page.

* **Change a plan**: The currently subscribed plan **does not support upgrades or downgrades**. Purchase an appropriate plan based on enterprise user requirements.

* **Cancel a plan**: A purchased plan **cannot be canceled**. You can use the service during the current cycle. Select an appropriate plan and cycle based on your usage needs.

## Seat management

Administrators must manage seats on the [Agent Plan Team - Seat management](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise?agentMode=close) page. For seat assignment limits, see [Seat management](agent-plan-enterprise-plan-overview.md#upgrade-rules).

* **Assign seats**: You can assign an individual seat or assign multiple seats in batches.

* **Unassign seats**: Enterprise users can unassign seats when they no longer need the resources associated with them. You can unassign an individual seat or unassign multiple seats in batches.

   > **warning**

   > Each seat can be reassigned up to three times in a subscription month. After this limit is exceeded, the seat cannot be assigned to a user for the rest of that month.

* **Add seats**: If the purchased number of seats is insufficient, administrators can click **Add seats** in the **Actions** column for the plan.

## Administrator authorization

Administrators can grant or revoke the ArkFullAccess administrator permission for subaccounts on the [Access control - Permission policies](https://console.byteplus.com/iam/policymanage) page. For details, see [Agent Plan Team - Administrator authorization](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise?agentMode=close).

Users with administrator permissions can manage seats and view usage.

## Quota management

Administrators can configure seat quotas for each project in [Project configuration - Quota management - AgentPlan](https://ai.byteplus.com/ark/region:ap-southeast-1/projectManagement?quota=%7B%22business%22%3A%22AgentPlan%22%2C%22quota%22%3A%22RealAgentPlanEnterprise%22%2C%22table%22%3A%7B%7D%7D&tab=Quota). If no project quota is configured, all projects share the quota.

> **tip**

> To adjust the quota under the primary account, contact sales.

* **Allocate quotas**: You can allocate seat quotas to different projects. Their total must satisfy the upper and lower quota limits of the primary account.

* **Quota usage statistics**: Administrators can view the seat quota and current number of seats for each project.

* **Purchase limits after seat quota configuration**:

   * Existing seats under the primary account + seats to purchase ≥ 2

   * Existing seats under a project + seats to purchase under that project ≤ the project's seat quota

## Usage information

Administrators can view the usage of all seats on the [Agent Plan Team - Usage details](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/usage) page, which provides a basis for appropriate seat allocation and resource optimization.

# Seat user operations

After an administrator assigns seats, assigned users can click [Agent Plan Team](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise?agentMode=close) to access the console and perform the following operations:

* **View plan information**: View the plan name and subscription status.

* **View plan usage**: View AFP usage for the past five hours, week, and month, along with model invocation details and harness usage details. Model invocation details have a delay of 0.5 to 1 day.

   If daily usage is high and the usage limit is reached, contact an administrator to **change the seat plan**. For example, if the current seat is a Agent Plan Team Small plan, unassign it and then assign a seat from another plan that supports more usage. After changing seats, you **must use the dedicated API Key for the new seat** and configure it again in the tool.

   > **warning**

   > Each seat can be reassigned up to three times in a subscription month. After this limit is exceeded, the seat cannot be assigned to a user for the rest of that month.

* **View core configuration information**:

   * **View the dedicated API Key**: Used to configure the tool.

   * **View supported models and the Base URL**: Used to configure the tool.
