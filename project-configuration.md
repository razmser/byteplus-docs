# Project authorization

Select **System settings** \> **Project configuration** in the lower\-left corner of the console to use quota management and project authorization.

* Quota management lets you view quota values and usage for modules such as online inference, batch inference, and Coding Plan. It also supports allocating quotas to specific projects for exclusive use.

* Project authorization lets you authorize the current project to access resources such as knowledge bases and TOS buckets, helping project administrators manage resources and permissions.

## Quota management

### Account\-level quotas

**Account\-level quotas** are used to centrally manage resource quotas for all projects under an account. Users with permissions (ArkFullAccess / ArkStandardGlobalAccess) can go to the **All resources** page on the ModelArk console to view the current account quotas and usage, and allocate project quotas. By default, all projects share the account quota. Once a project is assigned an exclusive quota, that exclusive quota becomes the hard limit for the project.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/746128385f07452f9da2dd506f8c272b~tplv-goo7wpa0wc-image.image)

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/f4d2ff70019445da8864ee2aa797ec9a~tplv-goo7wpa0wc-image.image)

### Project\-level quotas

When you select a specific project, you enter **Project\-level quotas**, where project members can view the current project quota usage and the usage of other projects in the shared quota.

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/9455cc8b1fb448469e37be404b12bfea~tplv-goo7wpa0wc-image.image)

![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/6000fb19be124f6ebc54bcfc3580bce2~tplv-goo7wpa0wc-image.image)

### Request a quota

You can view the quotas of different feature modules on the **Quota management** tab, such as the default quota for inference endpoints. The quotas of each feature are at the main account level. All sub\-accounts under the main account share the default quota. If the default quota cannot meet your business needs, click **Apply** in the **Actions** column of the [Quota center](https://console.byteplus.com/quota/productList/ParameterList?ProviderCode=MaaS) page and follow the instructions to apply.

## Project authorization

### User TOS authorization

#### Step 1: Go to the project authorization page

Log in to the ModelArk console.In the lower\-left corner of the console, select **System settings** \> **Project configuration** \> **Project authorization**, and then click the **TOS** tab.The authorized TOS buckets are displayed. Each TOS bucket is unauthorized by default, and the **Add authorization** button is displayed on the right.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/resources/tos-authorization-1.png)

#### Step 2: Start authorization

Click **Add authorization** to view the TOS buckets. Search for the target TOS bucket name, select it, and click **Confirm** in the lower\-right corner.

> * The project that the API key belongs to must be the same as the project of the TOS bucket.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/resources/tos-authorization-2.png)

In the authorization confirmation pop\-up window, click **Authorize**.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/resources/tos-authorization-3.png)

#### Step 3: Complete authorization

After authorization is complete, the authorized TOS bucket is added to the page. You can revoke it by clicking **Cancel**. The API key of this project can use the `tos` parameter in Files API requests to access authorized objects.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/flowcharts/resources/tos-authorization-4.png)

### Knowledge base authorization

In the lower\-left corner of the console, select the corresponding project, go to **System settings** \> **Project configuration** \> **Project authorization**, and click **Authorize**. The ModelArk service can then access and read all knowledge bases in the project.

## Access control

* Quota management: The main account displays the quotas of all resources under the account by default, and sub\-accounts display the quota status of the default project by default.

* Project authorization: The main account displays the authorization status of the default project by default, and sub\-accounts do not have authorization permissions by default.

Access control is implemented through [IAM policies](use-iam-to-manage-permissions.md). Only users with required permissions can perform operations related to quota management and project authorization. The recommended permission policies are as follows:

|Policies |Suitable for |Note |
|---|---|---|
|**ArkFullAccess / ArkStandardGlobalAccess** |Platform administrator / Project owner |Has full management permissions for the account and all resources, and can perform operations related to quota management and project authorization. |
|**ArkReadOnlyAccess** |Read\-only role |Has only read\-only permissions and is not allowed to perform any quota management or project authorization operations. |

If knowledge base authorization permission is required, add the actions `vikingdb:GetCollectionBriefInfo` and `vikingdb:SearchKnowledge` to the created policy.
