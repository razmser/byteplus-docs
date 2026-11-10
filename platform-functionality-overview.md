# Platform functionality overview

This guide helps developers quickly set up access, manage permissions, and start using models on BytePlus ModelArk.

<span id="4ea687c7"></span>
# Prerequisites


1. Registration: To use BytePlus services, you need to register a BytePlus account and complete authentication. For detailed instructions, see [Registration](https://docs.byteplus.com/en/docs/byteplus-platform/docs-signing-up-your-account).

2. (Optional) Sub\-account creation: If your account is used by multiple users, we recommend that you create sub\-accounts for better permission management. Specifically, log in with the main account, go to [IAM > Users](https://docs.byteplus.com/en/docs/byteplus-platform/docs-creating-a-user), create sub\-accounts, and assign them appropriate permissions through the preset policy.


<span id="280b0770"></span>
## IAM permission management

If your account is used by multiple users or is owned by an organization, you can use IAM to manage the permissions of sub\-accounts.

Identity and Access Management (IAM) is a permission management system, one of the **free services** provided by BytePlus. You can create IAM users (sub\-accounts) for a BytePlus account (main account) and grant them access permissions, so as to achieve permission isolation between enterprise employees.

> For example, if you don't want operations personnel to perform high\-risk operations such as deletion, you can grant them the ArkReadOnlyAccess permission, so that they can only view all resources of ModelArk, thereby improving resource security.


<span id="inference-model"></span>
# Model and inference experience

<span id="d6bd279d"></span>
## Foundation models

After completing the account and permission settings, go to the [ModelArk console](https://ai.byteplus.com/ark/region:ap-southeast-1?projectName=default), view released LLMs in the **model square**, and click a model card to view model details.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/3810306ade1943f8abab02257103546a~tplv-goo7wpa0wc-image.image) </span>

<span id="inference"></span>
## Playground

To quickly verify the effect of a fine\-tuned model released on the model square, go to **Playground** to try it out. For more information, see [Language models](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/language-models).

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/be7f975b46374e44bbe2ecfb17278b2f~tplv-goo7wpa0wc-image.image) </span>

<span id="model-invocation"></span>
## API calling model

Models in the model square can be called through APIs. You can go to the model details page and click **API call guide** in the upper right corner to view instructions.

<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/fe1c5282b5ac4fe8a4a26cc23450b1ba~tplv-goo7wpa0wc-image.image) </span>

<span id="75c01778"></span>
## 1. Pre\-requisites for Access

<span id="54fa0fbc"></span>
### Register a BytePlus Account

First\-time users must create a BytePlus account and complete authentication: [Registration & Authentication Guide](https://docs.byteplus.com/en/docs/byteplus-platform/docs-signing-up-your-account).

<span id="12b02862"></span>
### Create Sub\-Accounts (For Teams, Optional but Recommended)

If multiple users share your main account, use **Identity and Access Management (IAM)**  to:


* Create sub\-accounts for team members

* Assign granular permissions to control resource access


**Steps**:


1. Log in with your main account and go to [IAM > Users](https://docs.byteplus.com/en/docs/byteplus-platform/docs-creating-a-user)

2. Create sub\-accounts and assign roles via **preset policies** (e.g., `ArkReadOnlyAccess` for read\-only access)


<span id="cff9826f"></span>
## 2. Secure Permission Management with IAM

IAM is a **free, enterprise\-grade service** that lets you:


* Isolate permissions between team members

* Manage access to ModelArk resources (models, endpoints, etc.)


<span id="a0e82c19"></span>
### Key Concepts:


* **Main Account**: Owns all resources and manages sub\-accounts

* **Sub\-Accounts**: Created under the main account with specific permissions (no standalone billing)

* **Preset Policies**: Ready\-to\-use permission templates, e.g.:

   * `ArkFullAccess`: Full control over ModelArk resources

   * `ArkReadOnlyAccess`: View\-only access (ideal for non\-technical team members)

> Example: Grant `ArkReadOnlyAccess` to analysts to let them view model metrics without modifying configurations, enhancing security.


<span id="b68cbf18"></span>
## 3. Explore and Use Models in ModelArk

After setting up accounts and permissions, access the [ModelArk Console](https://ai.byteplus.com/ark/region:ap-southeast-1?projectName=default) to interact with models.

<span id="5fa263c9"></span>
### Discover Foundation Models in Model Square

Browse released LLMs in the **Model Square**:


1. View key details: model description, parameters, supported tasks, and pricing (including free trial quotas)

2. Click a model card to see technical specs, usage examples, and API documentation


<span id="4b33e23a"></span>
### Test Models Instantly with Playground

Use the **Playground** for quick model validation without coding:


* Input prompts and see real\-time responses

* Adjust parameters (temperature, max tokens, etc.) to tune output

* Ideal for:

   * Evaluating base model capabilities

   * Testing fine\-tuned models

   * Demonstrating proofs\-of\-concept to stakeholders


Learn More About Playground: [Language models](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/language-models).

<span id="f22646f9"></span>
### Call Models via API for Integration

For production use, invoke models programmatically through our API:


1. Go to the model details page

2. Click **API Call Guide** (top\-right corner) for:

   * SDK installation instructions (Python, Go, Java, etc.)

   * Code examples for common scenarios (chat, text generation, etc.)

   * Authentication steps (using API keys or IAM sub\-account credentials)
