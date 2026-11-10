# Manage API keys

When calling large models or relevant applications via code, you need an API Key as an authentication credential. The **API Key management** page in the ModelArk console helps you create and manage API Keys. If this is your first time, complete an end\-to\-end call by following the [Quick start](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1399008) guide first.

<span id="feature-entry"></span>
## Feature entry

In the lower\-left menu of the console, select [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey?apikey=%7B%7D).

On this page, you can create, query, and manage API Keys.

<span id="permission-model"></span>
## Permission model

API Keys provide the following capabilities:


|Capability |Description |
|---|---|
|Isolate call permissions by resource project |You can switch resource projects in the lower\-left corner of the console. API Keys created in a project can only be used as authentication credentials for resources within that project. |
|All permissions |Allows access to all resources under the resource project. |
|Custom permissions |Customizes access permissions for specific resources, enabling flexible control over the API Key's permissions for particular resources within the project. Currently, fine\-grained permission settings are supported by **Model ID** and **custom inference endpoint**. |
|IP call allowlist |Restricts the IP addresses that can call the model. After enabling, only IP addresses added to the allowlist can call the model. |


<span id="create-an-api-key"></span>
## Create an API Key

Create an API Key as the authentication credential for calling large models or applications. When creating, choose a capability combination from the **Permission model** section above based on your business needs.

<span id="query-an-api-key"></span>
## Query an API Key

You can query existing API Keys on the API Key management page and view information such as the current API Key's status, permissions, last used time, and creator.

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">Do not disclose the API Key in any way to avoid security risks or financial loss caused by unauthorized use.</div>


<span id="edit-permissions"></span>
## Edit permissions

You can adjust the permissions accessible by the API Key at any time, including the IP call allowlist and specific accessible resources, to flexibly configure API Key usage permissions for different scenarios and ensure a balance between system security and business requirements.

<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">API Key permission control applies to the data\-plane API call permissions related to Model ID and custom inference endpoints. To set control\-plane permissions, including permission to create API Keys, use Identity and Access Management (IAM). For details, see <a href="https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1263493">Access control using IAM</a>.</div>


<div style="text-align: center">
<img src="https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/278e965843ba4e14a93718a05092846c~tplv-goo7wpa0wc-image.image =695x" /></div>


<span id="disable-enable-an-api-key"></span>
## Disable / enable an API Key


* After disabling, the API Key will be in the **Disabled** status. Requests using this API Key will fail authentication, so please proceed with caution.

* After enabling, the API Key will be in the **Active** status and can be used normally.

> **Disable vs delete**: Disabling is **reversible**—you can re\-enable the same API Key at any time, and the key value stays the same. Deletion is **irreversible**—a deleted API Key cannot be recovered, and all requests using it will immediately fail authentication. In production, prefer disabling first; only delete after confirming the key is no longer in use.


<span id="security-settings"></span>
## Security settings

> This section is for **project administrators (main account)** . Regular project members (sub\-accounts) will not see the **Restrict member permissions** toggle described here.


Control project members so that they can only manage API Keys they created, thereby enhancing the security and compliance of API Key management.

By default, members of a resource project can view and manage all API Keys under the current project. For stricter permission control, the project administrator can turn on **Restrict member permissions**. After enabling, project members can only view, edit, disable/enable, and delete API Keys they created, and cannot manage API Keys created by others.

> You need to select a resource project in the lower\-left corner of the console to view **Security settings**.


<span>![图片](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/3c7609571b47462481291f0f91d18331~tplv-goo7wpa0wc-image.image) </span>

<span id="delete-an-api-key"></span>
## Delete an API Key

If you no longer need the API Key for authentication, you can delete it to prevent accidental misuse later.

<span id="quota-limits"></span>
## Quota limits

A primary account supports creating up to 50 API Keys.

To increase the quota, select **System Settings** \> **Project Configuration** \> **Quota Management** \> **Other** in the lower\-left menu of the console.

**Note**: This action requires IAM permissions for the quota center.

<span id="next-step"></span>
## Next step

After obtaining your API Key, you can:


* [Quick start](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1399008): Complete your first end\-to\-end call to verify that the API Key works.

* [Text generation](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/1399009): Learn the full capabilities of the Chat Completions API.
