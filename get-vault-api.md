# Get vault

`GET https://ark.ap-southeast.bytepluses.com/api/v3/vaults/{vault_id}`

Retrieves detailed information about the specified vault.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**vault_id** `string` `Required`  |  Vault ID

Vault ID, starting with `vlt-`.

## Response parameters

**created_at** `string`  |  Creation time

The creation time of the vault in RFC 3339 format.

**display_name** `string`  |  Vault display name

The display name of the vault, used to identify the vault.

**id** `string`  |  Vault ID

Vault ID, starting with `vlt-`.

**scene** `string`  |  Vault scenario

The vault's usage scenario.

* `default`: General business scenario.

* `dreams`: Memory organization scenario.

**type** `string`  |  Object type

Indicates that the current object is a vault.

Value is fixed to `vault`.

**updated_at** `string`  |  Update time

The latest update time of the vault in RFC 3339 format.

**metadata** `object`  |  Custom metadata

String key\-value pairs set for the vault, which can be used to categorize or associate business information. Returns `null` if not set.
