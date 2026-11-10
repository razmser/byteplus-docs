# Project authorization

In the lower\-left menu of the console, select **System Settings** \> **Project configuration** to manage quotas and project authorization.


* Quota management lets you view quota values and usage for modules such as online inference, batch inference, and Coding Plan. It also supports allocating quotas to specific projects for exclusive use.

* Project authorization lets you authorize the current project to access resources such as TOS buckets, helping project administrators manage resources and permissions.


<span id="c70ab865"></span>
## Quota management

<span id="account-level-quotas"></span>
### Account\-level quotas

**Account\-level quotas** are used to centrally manage resource quotas for all projects under an account. Users with permissions (ArkFullAccess / ArkStandardGlobalAccess) can go to the **All resources** page on the ModelArk console to view the current account quotas and usage, and allocate project quotas. By default, all projects share the account quota. Once a project is assigned an exclusive quota, that exclusive quota becomes the hard limit for the project.

<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/console/f_project_selection.png) </span>

<span id="project-level-quotas"></span>
### Project\-level quotas

When you select a specific project, you enter **Project\-level quotas**, where project members can view the current project quota usage and the usage of other projects in the shared quota.

<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/console/f_project_selection_default.png) </span>

<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/console/f_quote_breakdown_by_project.png) </span>

<span id="request-a-quota"></span>
### Request a quota

You can view the quotas of different feature modules on the **Quota management** tab, such as the default quota for inference endpoints. The quotas of each feature are at the main account level. All sub\-accounts under the main account share the default quota. If the default quota cannot meet your business needs, click **Apply** in the **Actions** column and follow the instructions to apply.

<span id="88e2f072"></span>
## Project authorization

<span id="4eb1b277"></span>
### User TOS authorization

<span id="step-1-go-to-the-project-authorization-page"></span>
#### Step 1: Go to the project authorization page

Sign in to the ModelArk console. In the lower\-left menu, select **System Settings** \> **Project configuration** \> **Project authorization**, and click the **TOS** tab. On the **TOS** tab, the authorized TOS buckets are displayed. Each TOS bucket is unauthorized by default, and the **Add authorization** button is displayed on the right.

<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/resources/tos-authorization-1.png) </span>

<span id="step-2-start-authorization"></span>
#### Step 2: Start authorization

Click **Add authorization** to view the TOS buckets. Search for the target TOS bucket name, select it, and click **Confirm** in the lower\-right corner.

<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/resources/tos-authorization-2.png) </span>

In the authorization confirmation pop\-up window, click **Authorize**.

<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/resources/tos-authorization-3.png) </span>

<span id="step-3-complete-authorization"></span>
#### Step 3: Complete authorization

After authorization is complete, the authorized TOS bucket is added to the page. You can revoke it by clicking **Cancel**. The API key of this project can use the `tos` parameter in Files API requests to access authorized objects.

<span>![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/resources/tos-authorization-4.png) </span>

<span id="a4d04f70"></span>
## Access control


* Quota management: The main account displays the quotas of all resources under the account by default, and sub\-accounts display the quota status of the default project by default.

* Project authorization: The main account displays the authorization status of the default project by default, and sub\-accounts do not have authorization permissions by default.


Access control is implemented through [IAM policies](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1263493). Only users with required permissions can perform operations related to quota management and project authorization. The recommended permission policies are as follows:


|Policies |Suitable for |Note |
|---|---|---|
|**ArkFullAccess / ArkStandardGlobalAccess** |Platform administrator / Project owner |Has full management permissions for the account and all resources, and can perform operations related to quota management and project authorization. |
|**ArkReadOnlyAccess** |Read\-only role |Has only read\-only permissions and is not allowed to perform any quota management or project authorization operations. |
