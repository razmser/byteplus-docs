# ArkClaw

ArkClaw is a cloud AI agent service provided by BytePlus, enabling you to deploy OpenClaw to the cloud with one click. It eliminates complex configuration, offers 24/7 online access, and allows you to use OpenClaw with zero barriers. You can seamlessly use your subscribed ModelArk Coding Plan and say goodbye to pay\-as\-you\-go billing anxiety.

# Key benefits

ModelArk **Coding Plan Pro package**: Enjoy the services provided by ArkClaw, unlock a dedicated virtual machine on the cloud, and stay online 24/7. When your **Coding Plan Pro** expires, the ArkClaw you created will be deleted, and its data will be **retained for 24 hours** for you.

> **Tip**

* > Starting from March 24, 2026, new purchasers of the Coding Plan Lite package will no longer receive the 7\-day free ArkClaw trial.

* > In the Model Playground, each primary account can activate only one ArkClaw instance. Sub\-accounts under the same primary account share data records.

# Step 1: Subscribe to the ModelArk Coding Plan

Visit the [ModelArk Coding Plan page](https://www.byteplus.com/en/activity/codingplan) and click the subscription button to complete the subscription. For details about Coding Plan, see [Coding Plan](coding-plan-personal-plan-overview.md).

# Step 2: Create and use ArkClaw

1. Log in to ModelArk [Model Playground](https://ai.byteplus.com/ark/region:ap-southeast-1/experience).

2. Select **Agent** \> **ArkClaw** from the navigation on the left\-hand side.

3. Click the **Create Now** button to submit the creation application.

> **Note**

> Before creating ArkClaw with a sub\-account, contact your account administrator to grant the sub\-account the following IAM permissions: `iam:CreateRole`, `iam:GetRole`, `iam:AttachRolePolicy`, and `iam:ListAttachedRolePolicies`. After the permissions are configured, you can start creating ArkClaw.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/79cbdc0f7edb4ce6b211d553febb9f63~tplv-goo7wpa0wc-image.image)

4. Once creation is successful, you can converse with ArkClaw in ModelArk.

> **Note**

> To perform personalized configuration and management for ArkClaw, such as configuring message channels, switching the model in use, defining tool personas, configuring Skills, setting scheduled tasks, and managing the ArkClaw service, refer to the tips listed below.

# Tips

See [this](https://docs.byteplus.com/en/docs/ecs/One-click_integration_with_ArkClaw) tutorial for how to complete the following tasks:

* [View and configure Skills](https://docs.byteplus.com/en/docs/ecs/One-click_integration_with_ArkClaw?lang=en#NxLmzbFS): View and install Skills.

* [View and configure scheduled tasks](https://docs.byteplus.com/en/docs/ecs/One-click_integration_with_ArkClaw?lang=en#eCRjvyLk): In addition to real\-time Q&A, ArkClaw can run configured scheduled tasks and proactively send reminders.

* [Configure messaging channels](https://docs.byteplus.com/en/docs/ecs/One-click_integration_with_ArkClaw?lang=en#2QJXIw3g): Quickly integrate with Telegram.

* [Configure persona](https://docs.byteplus.com/en/docs/ecs/One-click_integration_with_ArkClaw?lang=en#8VFxARVI): Define a persona for ArkClaw to make it a more distinctive, "human\-like" AI assistant.

* [Upload/download files](https://docs.byteplus.com/en/docs/ecs/One-click_integration_with_ArkClaw?lang=en#svhvfhkX): Use a TOS bucket in BytePlus Object Storage (TOS) as a cloud drive to quickly transfer files between local and cloud storage.

* [Manage ArkClaw](https://docs.byteplus.com/en/docs/ecs/One-click_integration_with_ArkClaw?lang=en#sMdwUjY6): Adjust the inference model, restart, or reinitialize the ArkClaw service, and more.

# Service rules

[ArkClaw Service Rules](2229122.md)
