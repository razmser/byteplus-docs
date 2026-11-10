# Delete vault

`DELETE https://ark.ap-southeast.bytepluses.com/api/v3/vaults/{vault_id}`

Delete the specified vault and all credentials in it.

<div data-tips="true" data-tips-type="danger" data-tips-is-title="true">Warning</div>


<div data-tips="true" data-tips-type="danger">Deleting a vault also deletes all credentials in it.</div>


<div data-tips="true" data-tips-type="danger">After deletion, the vault or credentials cannot be restored, and subsequent runs can no longer use these credentials.</div>


<div data-tips="true" data-tips-type="danger">Before deletion, make sure your business no longer uses this vault.</div>


If the vault is still referenced by a session in the `running` state, HTTP 409 `Conflict` is returned. First interrupt the related session and wait until it is no longer in the `running` state, or wait until the current conversation run ends before retrying.

<span id="authentication"></span>
## Authentication

This API supports the following authentication methods. For details, see [Base URL and authentication](base-url-and-authentication.md).


* API Key authentication. Go to the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.



---



<span id="request-parameters"></span>
## Request parameters

<span id="path-parameters"></span>
### Path parameters


**vault_id** `string` `Required`  |  Vault ID

Vault ID, starting with `vlt-`.


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**id** `string`  |  Vault ID

The deleted vault ID. It starts with `vlt-` and is the same as the value in the path parameter.



**type** `string`  |  Object type

Indicates that the current object is a deleted vault record.

Value is fixed to `vault_deleted`.
