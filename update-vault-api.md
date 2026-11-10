# Update vault

`POST https://ark.ap-southeast.bytepluses.com/api/v3/vaults/{vault_id}`

Update the display name or custom metadata of the specified vault. If the request body is empty, or if only an empty `metadata` object is passed, the server returns the current vault information without modifying data or the update time.

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

<span id="body-parameters"></span>
### Body parameters


**display_name** `string`  |  Vault display name

The display name of the vault, used to identify the vault in vault lists and details. If omitted, the original value is retained.



**metadata** `object`  |  Custom metadata

String key\-value pairs used to categorize or associate business information:


* If omitted or an empty object is passed, all original values are retained.

* If a non\-empty value is provided, the corresponding key is added or overwritten.

* If the value is an empty string, the corresponding key is deleted.


Up to 16 pairs after update; keys must be 1 to 64 characters long, and values can be up to 512 characters long.


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**created_at** `string`  |  Creation time

The creation time of the vault in RFC 3339 format.



**display_name** `string`  |  Vault display name

The display name of the vault, used to identify the vault.



**id** `string`  |  Vault ID

Vault ID, starting with `vlt-`.



**type** `string`  |  Object type

Indicates that the current object is a vault.

Value is fixed to `vault`.



**updated_at** `string`  |  Update time

The latest update time of the vault in RFC 3339 format.



**metadata** `object`  |  Custom metadata

String key\-value pairs set for the vault, which can be used to categorize or associate business information. Returns `null` if not set.
