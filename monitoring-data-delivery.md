# Monitoring data delivery

ModelArk online inference uses [Vital Managed Service for Prometheus](https://docs.byteplus.com/en/docs/vmp/what-is-vital-managed-service-for-prometheus) (VMP) for monitoring data. By default, monitoring data from all projects under an account is delivered to the account's default workspace. For a multi\-project account that requires data isolation, you can assign a separate delivery workspace to each project.

## Default delivery behavior

* **Initial enablement**: The system automatically creates the `ark_default` workspace in the default project, or you can select an existing workspace. This workspace becomes the account's default workspace.

* **Default delivery**: Monitoring data from projects without a separately configured workspace is delivered to the account's default workspace.

* **New projects**: New projects automatically inherit the account's default workspace, so monitoring does not need to be enabled again.

* **Existing configurations**: Accounts that already have monitoring enabled retain their current workspace configuration and continue using it as before.

## Use cases

* **Most projects**: Projects without a separately configured workspace deliver data to the account's default workspace. Administrators can query monitoring metrics for these projects in one place from the default workspace.

* **Projects requiring data isolation**: Assign a separate workspace to a business project that requires access isolation. Members with query permissions for the project's workspace can view only that project's monitoring data, without seeing data from other projects.

* **Unified queries across projects**: If multiple projects have separate workspaces, use an aggregation query workspace in VMP to combine data from the account's default workspace and the separate project workspaces.

## Console entry point

1. Log in to the [ModelArk console](https://ai.byteplus.com/ark/region:ap-southeast-1/endpoint).

2. In the **Online inference \> Inference endpoints** list, click the target endpoint's name or ID to open its details page.

3. Switch to the **Monitoring** tab.

4. In the **Workspace** section, click **Change workspace** to open the **Change project workspace** dialog.

> **tip**

> This setting applies to the project to which the endpoint belongs. Changing a project's workspace or the account's default workspace requires ArkFullAccess or ArkStandardGlobalAccess permissions.

## Workspace types

|Workspace type |Description |
|---|---|
|Account default workspace |When monitoring is enabled for the first time, the system automatically creates the `ark_default` workspace under the default project, or you can select an existing workspace. Projects without a separately configured workspace deliver data to this workspace by default. |
|Project\-specific workspace |A workspace assigned separately to the current project. Once configured, monitoring data for this project no longer follows changes to the account's default workspace. |

## Workspace switching

1. In the **Change project workspace** dialog, confirm the current project and **Delivery Workspace**.

2. Select the target workspace from the **Delivery Workspace** drop\-down list. The list displays all workspaces under the account and supports searching by workspace name or ID. The account's default workspace is marked **Default**.

3. To also make the selected workspace the account's default, check the corresponding option. All projects using the default workspace will then deliver data to the selected workspace. Projects with separately configured workspaces are not affected.

4. Click **Save changes**.

> **tip**

> If there are no available workspaces under your account, click **Change the current project data delivered to VMP Workspace** in the dialog. After creating a workspace, return to the dialog to select it.

> **warning**

* > After a workspace change, only new monitoring data is delivered to the new workspace. Historical data is not migrated.

* > After a workspace change, the original workspace no longer receives new data. To continue viewing the full monitoring data, use aggregation query workspaces in VMP. For details, see [Creating aggregation query workspaces](https://docs.byteplus.com/en/docs/vmp/Creating_an_aggregate_query_workspace).

* > After a workspace change, existing alert rules must be reconfigured.

## Metric delivery scope

* **Metrics with a valid ** **`project`** ** label**: The label value determines the project. Metrics are delivered only to that project's delivery workspace. If no workspace is specified for the project, metrics are delivered to the account's default workspace.

* **Metrics without a ** **`project`** ** label**: These are treated as metrics that are not specific to a project and are delivered to the union of all configured project workspaces under the account.

## Related documentation

* **Monitoring and alerts**: For details on enabling monitoring, monitoring metrics, and alert configuration, see [Manage inference endpoints](manage-inference-endpoints.md).
