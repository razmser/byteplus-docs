# Delete credential

`DELETE https://ark.ap-southeast.bytepluses.com/api/v3/vaults/{vault_id}/credentials/{credential_id}`

Delete the specified credential.

<div data-tips="true" data-tips-type="danger" data-tips-is-title="true">Warning</div>


<div data-tips="true" data-tips-type="danger">After deletion, this credential cannot be restored or read. Subsequent runs also cannot use this credential to access MCP services or inject environment variables. Before deleting it, confirm that your business no longer uses this credential.</div>


If the first delete request times out, you can retry with the same credential ID.

If the authentication information and project have not changed, a retry returning 404 `InvalidCredential.NotFound` means that the credential no longer exists. In this case, stop retrying.

<span id="authentication"></span>
## Authentication

This API supports the following authentication methods. For details, see [Base URL and authentication](base-url-and-authentication.md).


* API Key authentication. Go to the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.



---



<span id="request-parameters"></span>
## Request parameters

<span id="path-parameters"></span>
### Path parameters


**credential_id** `string` `Required`  |  Credential ID

Credential ID, starting with `vcrd-`.



**vault_id** `string` `Required`  |  Vault ID

Vault ID, starting with `vlt-`.


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**id** `string`  |  Credential ID

The deleted credential ID. It starts with `vcrd-` and is the same as the value in the path parameter.



**type** `string`  |  Object type

Indicates that the current object is a deleted credential record.

Value is fixed to `vault_credential_deleted`.
