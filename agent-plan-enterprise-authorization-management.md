# Authorization management

> **tip**

> You must configure both Agent Plan Team seats and IAM permissions. After assigning a seat to an IAM subuser, add the subuser to the corresponding user group and grant the required permissions. Otherwise, the subuser cannot view or use Agent Plan Team features.

# User group planning

To centrally manage enterprise member permissions, we recommend creating the following two user groups based on member responsibilities.

|User group |Users |Recommended permissions |
|---|---|---|
|`AgentPlanTeam_Admin` |Agent Plan Team administrators who need to manage enterprise seats, models, harness, and more |`ArkFullAccess` |
|`AgentPlanTeam_User` |Agent Plan Team seat users who only need to use their own seats and cannot manage other members or enterprise configuration |`ArkStandardGlobalAccess`; add `ArkGlobalInitAccess` if you need to restrict permissions by project |

> **tip**

1. > For first\-time use, create new user groups. For subsequent users, add them to the existing user groups.

2. > To perform management operations for the Agent Plan Team Personal Edition, such as purchasing and renewing plans under an enterprise account, join the Agent Plan Team administrator user group.

3. > For IAM management permissions, see [Access control using IAM](use-iam-to-manage-permissions.md).

# Procedure

## Step 1: Create a user group

Using the primary account or an account with the `IAMFullAccess` permission, go to [Access control IAM - User groups](https://console.byteplus.com/iam/identitymanage/usergroup) and click **Create New User Group**.

![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/en-user-group-1.png)

Use `AgentPlanTeam_Admin` for the administrator user group and `AgentPlanTeam_User` for the user group.

After creating both user groups, click **Authorize Now**.

## Step 2: Add permission policies

Add the following permissions to the `AgentPlanTeam_Admin` administrator group: `ArkFullAccess`. Add the following permissions to the `AgentPlanTeam_User` user group: `ArkStandardGlobalAccess`, `ArkGlobalInitAccess` (optional).

## Step 3: Add members

1. Select the user group to which you want to add members, then click **Add User**.

2. Select the users to add to the user group, then click **OK**.

After these steps are complete, the user has the corresponding user group permissions.
