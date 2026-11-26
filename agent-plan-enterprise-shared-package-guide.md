# Shared package guide

The Agent Plan Team shared package is a shared usage package that ModelArk Agent Plan Team provides for enterprise teams. It consists of an AFP quota pool shared by all team members. Administrators can purchase and manage shared packages centrally, while members use the shared AFP quota as needed. Unlike Team seat plans, shared packages do not require seats to be purchased and assigned on a one\-to\-one basis for each member. Administrators can purchase shared packages and seats separately or together based on how their team uses the service.

## About the Agent Plan Team shared package

### Key benefits

The Agent Plan Team shared package offers the core benefits of [Team seat plans](agent-plan-enterprise-plan-overview.md), along with the following additional benefits:

* **Flexible usage and sharing**: Team members share an AFP quota pool. This suits teams with multiple users, large differences in usage among members, or fluctuating demand, and reduces unused quota caused by allocating fixed quotas to individual seats. You can purchase multiple shared packages at once based on your business scale and estimated usage.

* **Purchases on demand**: You can purchase shared packages, seat plans, or both as needed, with no requirement to purchase them together.

* **Centralized quota management**: Administrators can view the team's shared\-package AFP usage and consumption details for models and harness services in the Agent Plan Team console. Shared and dedicated quota allocation, as well as detailed usage controls for each member, will be supported in the future.

* **Easier integration**: After purchasing a shared package, administrators can obtain a team\-specific API Key without purchasing and assigning a seat for each member.

### Shared packages and seat plans

Enterprise administrators can purchase shared packages and seat plans separately or together as needed. The following table compares the two options:

|Dimension |Shared package |Seat plan |
|---|---|---|
|Purpose |A shared quota pool purchased for the team, used by members as needed, and managed centrally by administrators. |Seat plans purchased and assigned on a one\-to\-one basis for each team member, with dedicated quotas. |
|Use cases |* **Flexible shared quota pool**: Teams with multiple users and variable usage, where members use the service at different frequencies and want to reduce unused fixed seat quotas.<br><br>* **Supplementary seat quota**: When an individual seat reaches its plan limit, a shared usage package can supplement its quota as part of a combined usage arrangement. |* Members with stable, long\-term usage who need individual plans and fixed quotas. Quotas are bound to seats and assigned to specific members on a one\-to\-one basis. |
|Integration |* Members access the service using the team's shared\-package API Key (shared packages and seat plans are independent in the current version; shared\-package quota is not automatically deducted when seat quota runs out).<br><br>* Base URL: The dedicated Agent Plan Team Base URL supports the Chat/Messages/Responses API |* After a seat is assigned, each member uses their own dedicated API Key.<br><br>* Base URL: The dedicated Agent Plan Team Base URL supports the Chat/Messages/Responses API |
|Pricing and billing |* USD 5000/month<br><br>* Usage is deducted in Agent Fuel Points (AFP), following the same deduction rules as Agent Plan Team seat plans. See [Deduction coefficients](agent-plan-enterprise-afp-credits-billing-rules.md#deduction-coefficient). |* For seat plan pricing, see [Subscription overview](agent-plan-enterprise-plan-overview.md).<br><br>* Usage is deducted in Agent Fuel Points (AFP). See [AFP deduction rules for plans](agent-plan-enterprise-afp-credits-billing-rules.md). |
|Usage management |* View the team's shared\-package AFP usage and consumption details for models and harness services. |* View AFP quota consumption and consumption details for models and harness services by seat. |
|Limits |* Monthly limit only |* 5\-hour, weekly, and monthly limits |
|Plan benefits |* Shared packages support the same models and harness services as Agent Plan Team seat plans. See [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness). |* [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness) |

## Plan overview

### Supported models and harness services

Shared packages support the same models and harness services as Agent Plan Team seat plans. See [Supported models and harness](agent-plan-enterprise-plan-overview.md#supported-models-harness).

### Usage details

|Plan |Team shared package |
|---|---|
|Price |USD 5000/month |
|Monthly quota (AFP) |5,000,000 |
|Weekly quota (AFP) |/ |
|5\-hour quota (AFP) |/ |

### Validity period

Shared packages are monthly subscriptions. Each subscription cycle lasts 1 month. Any unused quota for the current cycle is cleared when the cycle expires.

## Subscription rules

* **Purchase restrictions**

   * **Eligibility**: Accounts that have completed enterprise verification and have administrator permissions can purchase shared packages.

   * **Quantity**: The minimum purchase is 1 package. You can purchase multiple packages at once, and their quotas are added to the shared pool. **Additional purchases are not allowed during the validity period. If the quota runs out, you can enable overage postpaid billing.**

* **Renewal**: Monthly renewal and auto\-renewal are supported.

* **Cancellation**: Cancellation without a reason is not supported after purchase. You can continue using the service during the current cycle. Purchase based on your usage needs.

## AFP deduction rules for shared packages

Shared packages follow the same AFP deduction rules as Agent Plan Team seat plans. See [Deduction coefficients](agent-plan-enterprise-afp-credits-billing-rules.md#deduction-coefficient).

## Quick start

> **warning**

* > Shared packages can only be centrally managed and configured by enterprise administrators with Arkfullaccess permissions.

* > Team members who need access must contact their administrator for the team's shared\-package API Key or request administrator permissions.

### Shared package management and configuration

The following steps are for enterprise administrators. Team members who need access must contact their administrator for an API Key.

1. Purchase shared packages

   Enterprise administrators can visit the [Agent Plan Team console](https://ai.byteplus.com/ark/region:ap-southeast-1) to subscribe to shared packages for their team as needed. Multiple shared packages can be purchased at once.

2. View subscription management

   Enterprise administrators can view the subscription status and details of packages or seats in **Subscriptions**.

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/screenshot-20261119-211447.png)

3. Manage and configure shared packages

   On the **Shared packages** page, enterprise administrators can view the models and harness services covered by shared packages, obtain the team's shared\-package API Key, and distribute it to members who need access.

> **Note**: Team members cannot currently view shared\-package configuration. Contact your administrator for an API Key or request administrator permissions.

4. Manage usage

   Enterprise administrators can view shared\-package AFP quota information and go to **Usage details** to view detailed consumption for models and harness services.

5. (Optional) Enable overage postpaid billing

   Additional quota cannot currently be purchased for shared packages during their validity period. If the shared\-package quota runs out, administrators can enable overage postpaid billing for models or harness services.

### Shared package usage

Shared packages differ from seat plans in that **you must use the dedicated shared\-package API Key** (shared package API Keys and seat\-plan API Keys are different and cannot be used interchangeably). For configuration instructions, see [Quick start guide](agent-plan-enterprise-get-started.md).

## FAQ

### Access and usage

* **Q: Can shared\-package quota be deducted directly after seat quota runs out?**

   This is not supported in the current version. Seat API Keys and shared\-package API Keys are independent. To use shared\-package quota, contact your administrator for an API Key that consumes the team's shared quota.

* **Q: Is the Base URL dedicated?**

   Agent Plan Team uses a unified, dedicated Base URL. If you do not use the correct API Base URL, you cannot use Agent Plan Team quota.

* **Q: How do I use the shared\-package API Key?**

   Each shared package uses a team\-specific API Key that administrators can manage or distribute to members. Shared\-package quota is not automatically deducted when seat quota runs out. Switch to the team's shared\-package API Key to use it.

### Subscriptions

* **Q: Can I purchase multiple shared packages at once?**

   Yes. Enterprise administrators can purchase multiple shared packages at once based on estimated usage to obtain a larger shared quota for their team. Additional shared packages cannot currently be purchased mid\-cycle. If the quota runs out, you can enable overage postpaid billing.

* **Q: Do multiple shared packages have the same validity period and API Key?**

   Within a Project, multiple shared packages form one instance and have the same lifecycle. In the current version, they share one API Key.

* **Q: Can I cancel a shared package?**

   A subscription purchase is considered confirmed and is non\-refundable. Fees cannot be refunded even if you have not used up the package. Choose a subscription plan and cycle based on your usage needs.

* **Q: Can I subscribe to shared packages under different Projects?**

   Yes. Enterprise administrators can purchase shared packages under different Projects. The shared packages under each Project have an independent API Key.

### Quota management

* **Q: What should I do if shared\-package quota runs out during the subscription cycle?**

   Additional shared packages cannot currently be purchased mid\-cycle. If the quota runs out, administrators can enable overage postpaid billing.

   Options after the package quota runs out:

   * Enable overage postpaid billing

   * Purchase shared packages under a different Project

   * Purchase more shared packages next month after the instance expires

* **Q: Can I set a separate shared\-package quota limit for each member?**

   Quota allocation at the member level is not currently supported.
