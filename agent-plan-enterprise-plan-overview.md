# Plan overview

ModelArk Agent Plan Team is a subscription plan for enterprise users that supports models across modalities and dedicated harness capabilities. It uses granular credit\-based billing to support new agent experiences.

## Key benefits

* **Multimodal models and harness upgrades**: Provides language, image, video, and embedding models, with dedicated harness capabilities such as BytePlus Searchinfinity ready to use.

* **Compatibility with popular coding and agent tools**: Integrates with multiple ecosystems and popular tools such as OpenClaw, Hermes Agent, Claude Code, and OpenCode.

* **Cost\-effective**: Four tiers—Small, Medium, Large, and Max—offer flexible capacity and value, with greater discounts than postpaid usage.

* **Monthly subscriptions**: Reduces the burden of budget management and simplifies enterprise budgets through centralized purchasing.

* **Clear usage tracking**: Uses Agent Fuel Point (AFP) as a unified usage unit, making quota consumption easy to track.

* **Higher quotas and multiple seats**: Enterprise accounts can purchase Agent Plan Team by seat and assign seats to employee subaccounts. A minimum purchase of 2 seats supports team use at scale.

* **Team management**: Supports bulk seat assignment, permission management, and usage statistics for enterprise teams.

* **Data security**: Does not record user requests or model responses, supporting enterprise data privacy requirements.

## Quick start

[Quick start guide](agent-plan-enterprise-get-started.md): If you are familiar with Agent Plan Team, visit [Agent Plan Team](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise?agentMode=close) to purchase a plan as needed, then follow this guide to start using AI tools.

## Plan overview

> **warning**

> Small and Medium plans are intended for lightweight trials and do not support video generation. Large and Max plans are recommended if you need video generation.

### Supported models and harness

Models and harness availability for each plan are listed below:

|Category |Domain |Model name |Length limits (tokens) |Small |Medium |Large |Max |
|---|---|---|---|---|---|---|---|
|Model |Text generation (fast) |seed\-2.0\-mini |* Context window: 256K<br><br>* Maximum output: 128K |✓ |✓ |✓ |✓ |
||Text generation (standard) |seed\-2.0\-lite |* Context window: 256K<br><br>* Maximum output: 128K |✓ |✓ |✓ |✓ |
||Text generation (advanced) |dola\-seed\-2.1\-turbo |* Context window: 256K<br><br>* Maximum output: 256K |✓ |✓ |✓ |✓ |
||Text generation (advanced) |deepseek\-v4.1\-flash\-economy |* Context window: 1024K<br><br>* Maximum output: 384K |✓ |✓ |✓ |✓ |
||Text generation (advanced) |deepseek\-v4.1\-flash |* Context window: 1024K<br><br>* Maximum output: 384K |✓ |✓ |✓ |✓ |
||Text generation (advanced) |deepseek\-v4\-pro |* Context window: 1024K<br><br>* Maximum output: 384K |✓ |✓ |✓ |✓ |
||Embedding |skylark\-embedding\-vision |* Context window: 128K |✓ |✓ |✓ |✓ |
||Image generation |dola\-seedream\-5.5 |\- |✓ |✓ |✓ |✓ |
||Image generation |dola\-seedream\-5.0\-flash |\- |✓ |✓ |✓ |✓ |
||Image generation |dola\-seedream\-5.0\-pro |\- |✓ |✓ |✓ |✓ |
||Video generation |dreamina\-seedance\-2.5\-flash |\- |✗ |✗ |✓ |✓ |
||Video generation |dreamina\-seedance\-2.5 |\- |✗ |✗ |✓ |✓ |
|Harness |BytePlus Searchinfinity |\- |\- |✓ |✓ |✓ |✓ |

### Usage notes

Usage is deducted in credits (AFP). For detailed deduction rules, see [AFP credit deduction rules](agent-plan-enterprise-afp-credits-billing-rules.md).

> **warning**

* > Image generation models, video generation models, and harness have no 5\-hour or weekly quota limits. They are subject only to model daily quotas and plan monthly quotas.

* > All daily quota limits are half of the plan's monthly quota.

|Plan |Small |Medium |Large |Max |
|---|---|---|---|---|
|Price |20 USD/month |100 USD/month |300 USD/month |1000 USD/month |
|Monthly quota (AFP) |20,000 |100,000 |300,000 |1,000,000 |
|Weekly quota (AFP) |7,000 |35,000 |105,000 |350,000 |
|5\-hour quota (AFP) |2,000 |10,000 |30,000 |100,000 |
|Daily quota (AFP)<br><br>> Supported only for selected models and harness |10,000 |50,000 |150,000 |500,000 |

View model usage details and harness usage details in [Agent Plan Team](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise?agentMode=close). Model usage details are delayed by 0.5 \- 1 day. Postpaid overage usage is an estimate; the bill is authoritative.

### Validity period

Plan validity is measured in calendar months from the date of successful purchase. For example, a 1\-month plan purchased on 01.31 expires at 23:59 on 02.28 in a non\-leap year.

### Quota refresh rules

* **Postpaid overage usage disabled**: If the plan quota is exhausted within a period, wait until the next period for it to refresh automatically. Other resource packages and account balances are not consumed. For deduction rules, see [AFP credit deduction rules](agent-plan-enterprise-afp-credits-billing-rules.md).

   * 5\-hour limit: Refreshes every 5 hours from the time of the first request.

   * Weekly limit: Resets at 00:00:00 every Monday.

   * Daily limit: Resets at 00:00:00 each day (visual models, and harness only).

   * Monthly limit: Resets at 00:00:00 on day 1 of each subscription month.

* **Postpaid overage usage enabled**: Plan quota is deducted first while quota remains in the period. Once the quota is exhausted, the system automatically switches to postpaid overage usage and generates a postpaid bill. No configuration changes (such as Base URL, API Key, or model name) are required. For rules and operations, see [Overage billing rules](agent-plan-enterprise-over-quota-postpaid-rules.md) and [Manage postpaid billing](agent-plan-enterprise-postpaid-management.md).

   > **warning**

   * > Text generation and embedding models: With postpaid overage usage enabled, usage automatically switches to postpaid billing when the 5\-hour, weekly, or monthly limit is reached. When the plan quota refreshes, usage automatically switches back to plan quota deduction.

   * > Harness: Have no 5\-hour or weekly limits. With postpaid overage usage enabled, usage automatically switches to postpaid billing only when the monthly limit is reached.

### TPM limits

Agent Plan Team plans provide sufficient TPM for normal development. For exceptionally high TPM requirements, use postpaid APIs. The recommended numbers of projects are:

* Small: Develop a single project at a time

* Medium: Develop 1\-2 projects concurrently

* Large and Max: Develop 2+ projects concurrently

### Quota usage restrictions

* A subscription plan can be used in all supported tools, with shared quota.

* Text generation and embedding models cannot be used for API calls. Using the Base URL and API Key for Agent Plan Team benefits outside AI tools may be identified as abuse or a violation, resulting in subscription suspension or account banning.

## Subscription and seat management

### **Subscription rules**

* **Purchase eligibility**: Accounts with completed enterprise verification and administrator permissions can purchase plans.

* **Purchase limits**:

   * Minimum purchase: 2 seats by default. The initial purchase must include at least 2 seats. Additional seats can be purchased as needed, provided that the primary account maintains a total of ≥ 2 seats.

   * Purchase duration: Each seat can be purchased for up to 12 months at a time. The combined duration of existing and newly purchased plans cannot exceed 24 months.

* **Activation rules**:

   * Plans take effect immediately after purchase. Assign seats and start using them as soon as possible.

   * If a subsequent plan exists, it automatically takes effect when the current plan expires.

* **Renewal rules**:

   Users with administrator permissions can renew plans in the console.

   * Automatic renewal: If selected at checkout or enabled in the console, the plan renews monthly, for 1 month each time by default. The next month's plan fee for the corresponding seat is automatically deducted at expiration.

   * Manual renewal: If automatic renewal is not selected, renew through the console.

* **Upgrade and downgrade rules**: Subscribed plans cannot be upgraded or downgraded.

* **Cancellation rules**: Subscription purchases are final and non\-refundable. Fees cannot be refunded even if you have not used the entire plan. Choose a plan and subscription period that suit your usage needs.

### Seat management

* Seat definition: The basic unit of the enterprise edition. Each seat allows one enterprise user to use an Agent Plan Team subscription.

* Seat binding limits:

   * A seat can correspond to only one plan type.

   * A subaccount can be bound to at most one seat at a time within a project of the primary account.

   * A seat can be reassigned up to 3 times per subscription month.

* Seat ownership: Seats belong to specific projects and cannot be shared across projects.

### **Permission management and authentication**

* Permission management

   |User group |Members |Recommended permissions |
   |---|---|---|
   |`AgentPlanTeam_Admin` |Agent Plan Team administrators and members who need to manage enterprise seats, models/Harness, and other resources |`ArkFullAccess` |
   |`AgentPlanTeam_User` |Agent Plan Team seat users who only need to use their own seats and cannot manage other members or enterprise configuration |`ArkStandardGlobalAccess`; adding `ArkGlobalInitAccess` is recommended if permissions need to be restricted by project |

* Dedicated API Key rules

   > **tip**

   > Dedicated API Keys for Agent Plan Team differ from ModelArk platform API Keys. Do not use them interchangeably.

   * Each seat has an API Key that can be generated after the seat is assigned to a user.

   * When a seat's plan expires, its API Key becomes unavailable. The plan must take effect again to reactivate the key.

   * After a seat is unbound and assigned to a user again, its API Key changes.

## AI tool integration

If errors occur while using a Agent Plan Team plan, identify the corresponding error scenario to diagnose the issue. For details, see [Error codes](error-codes.md).

**Claude Code**

AI terminal coding assistant for natural language programming

**TraeCode**

Intelligent IDE for next\-generation coding workflows

**Roo Code**

Lightweight coding assistant

**Codex CLI**

Command\-line coding tool from OpenAI

**Pi**

Open\-source terminal coding agent

**OpenCode**

Open\-source AI coding agent tool

**Cline**

VSCode extension for code completion and debugging

**Kilo Code**

Lightweight, high\-performance coding tool

**WorkBuddy**

Tencent's desktop AI agent client

**ZCode**

AI coding assistant from Zhipu

**OpenClaw**

Open\-source, self\-hosted personal AI assistant

**Cursor**

AI\-native code editor

**Hermes Agent**

Open\-source autonomous AI agent

**DeepSeek harness**

Agent framework where everything is a plugin

## Usage agreements

* [General Terms for AI Services](https://docs.byteplus.com/en/docs/legal/AI-Services-terms)

* [Specific Terms for Coding Plan and Agent Plan](https://console.byteplus.com/ark-stg/region:ap-southeast-1/docs/2278469)

* [Specific Terms for the Searchinfinity Service](https://docs.byteplus.com/en/docs/searchinfinity/Specific_Terms_for_the_Searchinfinity_Service)

* [Byteplus Searchinfinity Service Level Agreement](https://docs.byteplus.com/en/docs/searchinfinity/Byteplus_Searchinfinity_Service_Level_Agreement)
