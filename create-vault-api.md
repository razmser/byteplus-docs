# Create vault

`POST https://ark.ap-southeast.bytepluses.com/api/v3/vaults`

Create a vault to centrally manage the credentials required by the session runtime. The vault itself does not store ciphertext; ciphertext is stored in each credential within it.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Body parameters

**display_name** `string` `Required`  |  Vault display name

The display name of the vault, used to identify the vault in vault lists and details.

**metadata** `object`  |  Custom metadata

String key\-value pairs used to categorize or associate business information. Up to 16 pairs; keys must be 1 to 64 characters long, and values can be up to 512 characters long.

> **Tip**

> For user\-level credential scenarios, we recommend using the `external_user_id` key to store the user's unique identifier from the customer product, to map the vault to the end user. For example, `{"external_user_id": "usr_abc123"}`.

**scene** `string` `Default default`  |  Vault scenario

Specifies the vault's usage scenario.

* `default`: General business scenario.

* `dreams`: Memory organization scenario.

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

**scene** `string`  |  Vault scenario

The vault's usage scenario.

* `default`: General business scenario.

* `dreams`: Memory organization scenario.
