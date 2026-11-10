# Access control using IAM

To ensure the security of your cloud resource usage, avoid accessing ModelArk directly with your BytePlus account (the root account). Instead, we recommend using [Identity and Access Management](https://console.byteplus.com/iam/identitymanage/user) (IAM) identities to access ModelArk.

This document explains how to use IAM to manage ModelArk resources and console permissions. For more information about IAM, see [IAM Overview](https://docs.byteplus.com/en/docs/byteplus-platform/docs-overview-3).

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">IAM is used to manage user permissions for resources and the console, i.e., control\-plane permissions.</div>


<div data-tips="true" data-tips-type="warning">To manage model invocation permissions, configure API key permissions. For details, see <a href="https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1361424">Manage API Keys</a>.</div>


<span id="concepts"></span>
# IAM basics


|Concept |Description |
|---|---|
|Main account |Main account.<br><br><br>* Root user, the owner of cloud service resources and the subject of resource measurement and resource billing. By default, the main account has all permissions. |
|IAM user/IAM account |An IAM identity.<br><br><br>* An IAM user is created by a main account or an IAM user/role with admin permissions. An IAM user belongs to the main account. It is not an independent BytePlus account.<br><br>* IAM users do not own resources and cannot conduct billing independently. Their bills are paid by the main account uniformly.<br><br>* IAM users cannot log in to the console or call API operations to access resources under the main account without authorization.<br><br>> For more information, refer to [IAM Users](https://docs.byteplus.com/en/docs/byteplus-platform/docs-creating-a-user). |
|User group |A user group is an IAM identity that classifies and authorizes IAM users with the same responsibilities to efficiently manage IAM users and their permissions.<br><br><br>* When the responsibilities of an IAM user change, you can move it to the user group corresponding to its new responsibilities without affecting other IAM users.<br><br>* When the permissions of a user group change, you can modify the permission policy for the user group and apply the policy to all IAM users.<br><br>> For more information, refer to [User Group Management](https://docs.byteplus.com/en/docs/byteplus-platform/docs-managing-user-groups). |
|Role |An IAM role is a virtual user that can be granted a set of permission policies. Unlike IAM users, IAM roles do not have permanent credentials such as login passwords and access keys and need to be used by a trusted entity. In this case, the trusted entity receives a security token as a temporary identity certificate of the IAM role, which allows the entity to play the IAM role to access authorized resources.<br><br>> For more information, refer to [Managing Roles](https://docs.byteplus.com/en/docs/byteplus-platform/docs-managing-roles). |


<span id="system-policies-role"></span>
# Permission policy

A policy describes permissions by explicitly defining the authorized resources, permitted actions, and applicable conditions. IAM users, user groups, and roles obtain permissions by associating policies. IAM provides two types of policies:


* **System default policies** are centrally created by BytePlus product teams and maintained by BytePlus. Users are not allowed to modify these policies.

* **User custom policies** are fully managed by users, who are responsible for policy versioning and lifecycle management, including creation, updates, and deletion.


<span id="0c818edb"></span>
## ModelArk system default policies

<span id="arkfullaccess"></span>
### ArkFullAccess

The permission for ModelArk administrators to access ModelArk services. They can view and configure all resources. This permission is suitable for roles such as algorithm or R&D personnel. The main account or account administrator can view policy details by going to [Policy management > ArkFullAccess](https://console.byteplus.com/iam/policymanage/System/ArkFullAccess?tab=content).

<span id="arkstandardglobalaccess"></span>
### ArkStandardGlobalAccess

The permission to use the basic features of ModelArk. Users can view and configure all resources except model activation. The main account or account administrator can view policy details by going to [Policy management > ArkStandardGlobalAccess](https://console.byteplus.com/iam/policymanage/System/ArkStandardGlobalAccess?tab=content).

<span id="2d08de72"></span>
### ArkReadOnlyAccess

The permission for ModelArk read\-only users to read ModelArk services. They can view all resources, including fine\-tuning tasks, models in the model repository, and inference endpoints. This permission is suitable for roles such as product or operations personnel. The main account or account administrator can view policy details by going to [Policy management > ArkReadOnlyAccess](https://console.byteplus.com/iam/policymanage/System/ArkReadOnlyAccess?tab=content).

<span id="722cb2eb"></span>
### ArkExperienceAccess

The permission to access the ModelArk experience center. Users can view and experience models in the model square. This permission is suitable for users who experience ModelArk and model capabilities in the early stage. The main account or account administrator can view policy details by going to [Policy management > ArkExperienceAccess](https://console.byteplus.com/iam/policymanage/System/ArkExperienceAccess?tab=content).

<span id="a2216333"></span>
### ArkGlobalInitAccess

Provides the basic permissions required for project\-level isolation and is designed to be used in combination with standard policies to implement project\-based access control. This is suitable for users who require permission isolation by project.

The root account or account administrators can view the policy details via the link below: [Policy management > ArkGlobalInitAccess](https://console.byteplus.com/iam/policymanage/System/ArkGlobalInitAccess?tab=content).

<span id="serviceroleforark"></span>
### ServiceRoleForArk (service\-associated role policy)

The permission for service\-associated roles to access service resources under your account by using ModelArk. The main account or account administrator can view the trust policy details by going to [Role > ServiceRoleForArk](https://console.byteplus.com/iam/identitymanage/role/ServiceRoleForArk/).

<span id="custom-policies"></span>
## Custom permission policy

If system policies do not meet your requirements, you can create custom policies to grant permissions as needed. Custom policies enable fine\-grained access control and are an effective way to enhance the security of resource access.

**Note**: You are responsible for maintaining and updating versions of custom policies.

<span id="user"></span>
### **Scenario 1: Model Invocation and Management Permissions**

This example demonstrates how to create a policy that grants permissions for model access, suspension, deletion, editing, and API invocation.

```json
{
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ark:CreateEndpoint",
                "ark:ListFoundationModels",
                "ark:ListCustomModels",
                "ark:ListFoundationModelVersions",
                "ark:ListEndpoints",
                "ark:StopEndpoint",
                "ark:StartEndpoint",
                "ark:GetEndpoint",
                "ark:DeleteEndpoint",
                "ark:UpdateEndpoint",
                "ark:GetModelChargeItem",
                "ark:InnerGetModelUnitPrice",
                "ark:GetEndpointExampleCode",
                "ark:GetApiKey",
                "tag:GetTagValues",
                "tag:GetTagKeys"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```


If you need to limit the scope of use, you can modify the value of the Resource parameter. That is, you can implement least privilege by specifying the user scope, user group, project, or resource ID.

```json
"Resource": [
                "trn:iam::2100000xxx:user/username",
                "trn:iam::2100000xxx:group/usergroupname",
                "trn:iam::2100000xxx:project/projectname",
                "trn:ark:ap-southeast:2100000xxx:endpoint/ep-20240407133918-*****"
                ]
```


<span id="68d11b0c"></span>
### **Scenario 2: Managing Account Permissions by Resource Project**

BytePlus provides Resource Projects and tagging capabilities to help you manage account permissions on a per\-resource basis.

The `ArkGlobalInitAccess` policy includes the basic permissions required to enable project\-level isolation. By combining it with other policies, you can implement project\-based permission management.


|Global Policy |Policy for<br><br>Target Project |Account Permission Description |
|---|---|---|
|ArkGlobalInitAccess |ArkExperienceAccess |Permission to access ModelArk experience center. Users can view and experience models in the model square **within the project**. |
|ArkGlobalInitAccess |ArkReadOnlyAccess |Permission to read ModelArk service resources. Users can access the experience center **within the project** and view all resources, including fine\-tuning tasks, models in the model repository, and inference endpoints. |
|ArkGlobalInitAccess |ArkStandardGlobalAccess |Permission to use basic features of ModelArk. Users can view and configure all resources except model activation **within the project**. |
|ArkGlobalInitAccess |ArkFullAccess |The administrator permission of ModelArk. Administrators can view and manage all resources **within the project**. |


<span id="1c1f4cab"></span>
# Resource management

You can use Resource Projects and tags to group and label resources, enabling fine\-grained permission management.

<span id="c73ff7ba"></span>
## Resource project management

Resource Projects allow you to group cloud resources based on dimensions such as purpose, permissions, and ownership, supporting hierarchical resource management within an organization.

Each cloud resource can belong to only one project. Adding a resource to a project does not change its relationships with other cloud resources.

By performing IAM authorization at the project level, you can maintain resource isolation and data security. You can also view resource consumption and billing at the project level to calculate cloud resource usage costs.

For more information on cost allocation, see the [Byteplus Resource Management](https://docs.byteplus.com/en/docs/resourcemanagement/Tag_Overview) documentation.

<span id="e84a123d"></span>
## Tag management

Resource tags consist of key–value (KV) pairs. You can use tags to classify and manage cloud resources from multiple dimensions, enabling scenarios such as tag\-based authorization and cost allocation.

You can go to [Billing center > Bill management > Cost tag](https://console.byteplus.com/finance/bill/tag/) and enable the cost tag `sys:ark:createdBy`. The cost tag is displayed in the Tag column of the bill details. For more information about tag management, refer to [Tag Management](https://docs.byteplus.com/en/docs/resourcemanagement/Manage_Tags_and_Resources).

<span id="c3e7649f"></span>
# Example procedure

The following example shows how to configure the `ArkFullAccess` permission of `default (default project)` for a user.


1. Go to [Access control > Users](https://console.byteplus.com/iam/identitymanage/user), find the target user, and click **Add permission** in the **Actions** column.

2. Search for and check the **ArkGlobalInitAccess** policy in the Policy section and select **No** for Project\-specific.

3. Search for and check the **ArkFullAccess** policy in the Policy section, select **Yes** for Project\-specific, and select the project to which the permission applies. In this example, select `default (default project)`.

4. Click **Submit**.


You can also configure a policy to grant project\-specific permission to a user group.
