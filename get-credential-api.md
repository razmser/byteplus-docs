# Get credential

`GET https://ark.ap-southeast.bytepluses.com/api/v3/vaults/{vault_id}/credentials/{credential_id}`

Retrieves details about the specified credential.

The response does not return the following cyphertext parameters: `access_token`, `token`, `secret_value`, `refresh_token`, and `client_secret`.

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


**auth** `object`  |  Authentication configuration

The credential's authentication method and publicly returnable configuration. The parameter structure is determined by `type`. The response returns only one structure that matches `type`.


MCP OAuth credential `object`

The server returns the public configuration of the MCP OAuth credential, but does not return access tokens, refresh tokens, or client secrets.


**mcp_server_url** `string`  |  MCP service URL

`auth.mcp_server_url`

The access address of the MCP service.



**type** `string`  |  Authentication type

`auth.type`

Indicates that this credential uses an OAuth access token to connect to the MCP service.

Value is fixed to `mcp_oauth`.



**expires_at** `string`  |  Expiration time

`auth.expires_at`

The expiration time of the access token in RFC 3339 format.



**refresh** `object`  |  Refresh configuration

`auth.refresh`

The refresh configuration for the OAuth access token, excluding the refresh token.


**client_id** `string`  |  Client ID

`auth.refresh.client_id`

The client ID used when requesting an access token from the OAuth token endpoint.



**resource** `string`  |  Resource identifier

`auth.refresh.resource`

The resource identifier corresponding to the OAuth authorization request.



**scope** `string`  |  Authorization scope

`auth.refresh.scope`

The permission scope that the OAuth token is allowed to access.



**token_endpoint** `string`  |  OAuth token endpoint

`auth.refresh.token_endpoint`

The OAuth endpoint address used to refresh the access token.



**token_endpoint_auth** `object`  |  OAuth client authentication

`auth.refresh.token_endpoint_auth`

The client authentication configuration used when the server submits a refresh request to the OAuth token endpoint.


**type** `string`  |  Authentication method

`auth.refresh.token_endpoint_auth.type`

The client authentication method used when the server submits a refresh request to the OAuth token endpoint:


* `none`: Submit only `client_id` without using a client secret.

* `client_secret_basic`: Submit client information through HTTP basic authentication.

* `client_secret_post`: Submit client information in the request body.






Static earer token credential `object`

The server returns the public configuration of the static bearer token credential, but does not return the token.


**mcp_server_url** `string`  |  MCP service URL

`auth.mcp_server_url`

The access address of the MCP service.



**type** `string`  |  Authentication type

`auth.type`

Indicates that this credential uses a static bearer token to connect to the MCP service.

Value is fixed to `static_bearer`.




MCP static credential `object`

The server returns the public injection configuration of the MCP static credential, but does not return secret values.


**injection_type** `string`  |  Injection method

`auth.injection_type`

The secret injection method returned by the server:


* `bearer`: Injected through the `Authorization: Bearer <token>` request header.

* `header`: Injected through the custom request header specified by `injection_name`.

* `query`: Injected through the URL query parameter specified by `injection_name`.



**mcp_server_url** `string`  |  MCP service URL

`auth.mcp_server_url`

The access address of the MCP service.



**type** `string`  |  Authentication type

`auth.type`

Indicates that this credential injects static secrets into MCP requests. The injection location is specified by `injection_type`.

Value is fixed to `mcp_static`.



**injection_name** `string`  |  Injection name

`auth.injection_name`

Custom request header name or query parameter name. This parameter is returned when `injection_type` is `header` or `query`; it is not returned when `injection_type` is `bearer`.




Environment variable injection credential `object`

The server returns the public injection configuration of the environment variable credential, but does not return environment variable values.


**networking** `object`  |  Network scope

`auth.networking`

The range of target hosts into which the credential can be injected.


**type** `string`  |  Network scope type

`auth.networking.type`

The target host range of the credential:


* `unrestricted`: Can be injected into requests sent to any public network host.

* `limited`: Can only be injected into requests sent to hosts listed in `allowed_hosts`.



**allowed_hosts** `string[]`  |  Allowed host list

`auth.networking.allowed_hosts`

When `type` is `limited`, returns the list of public network hosts into which credentials are allowed to be injected. Each item is a plain domain name, wildcard domain name, or public IPv4 address. Not returned when `type` is `unrestricted`.




**secret_name** `string`  |  Environment variable name

`auth.secret_name`

The environment variable name used when injecting the credential into the runtime environment.



**type** `string`  |  Authentication type

`auth.type`

Indicates that this credential injects credentials into the runtime environment through environment variables.

Value is fixed to `environment_variable`.




Environment OAuth credential `object`

The server returns the public injection configuration of the environment OAuth credential, but does not return access tokens, refresh tokens, or client secrets.


**networking** `object`  |  Network scope

`auth.networking`

The range of target hosts into which the access token can be injected.


**type** `string`  |  Network scope type

`auth.networking.type`

The target host range of the credential:


* `unrestricted`: Can be injected into requests sent to any public network host.

* `limited`: Can only be injected into requests sent to hosts listed in `allowed_hosts`.



**allowed_hosts** `string[]`  |  Allowed host list

`auth.networking.allowed_hosts`

When `type` is `limited`, returns the list of public network hosts into which credentials are allowed to be injected. Each item is a plain domain name, wildcard domain name, or public IPv4 address. Not returned when `type` is `unrestricted`.




**secret_name** `string`  |  Environment variable name

`auth.secret_name`

The environment variable name used when injecting the access token into the runtime environment.



**type** `string`  |  Authentication type

`auth.type`

Indicates that this credential uses an OAuth access token and injects the token into the runtime environment through an environment variable.

Value is fixed to `env_oauth`.





**created_at** `string`  |  Creation time

The creation time of the credential in RFC 3339 format.



**id** `string`  |  Credential ID

Credential ID, starting with `vcrd-`.



**type** `string`  |  Object type

Indicates that the current object is a vault credential.

Value is fixed to `vault_credential`.



**updated_at** `string`  |  Update time

The latest update time of the credential in RFC 3339 format.



**vault_id** `string`  |  Vault ID

The ID of the vault to which the credential belongs, starting with `vlt-`.



**display_name** `string`  |  Credential display name

The display name of the credential, used to identify the credential.



**metadata** `object`  |  Custom metadata

String key\-value pairs set for the credential, which can be used to categorize or associate business information.
