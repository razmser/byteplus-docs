# Update credential

`POST https://ark.ap-southeast.bytepluses.com/api/v3/vaults/{vault_id}/credentials/{credential_id}`

Update the display information, metadata, or rotatable authentication information of the specified credential. If the request body does not contain any updatable parameters, the API returns the current credential information without modifying the content or update time.

For OAuth credentials, you can rotate access tokens, update expiration times, enable automatic refresh, or update the information required for automatic refresh. Omitted parameters retain their original values.

The response does not return the following cyphertext parameters: `access_token`, `token`, `secret_value`, `refresh_token`, and `client_secret`.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**credential_id** `string` `Required`  |  Credential ID

Credential ID, starting with `vcrd-`.

**vault_id** `string` `Required`  |  Vault ID

Vault ID, starting with `vlt-`.

### Body parameters

**auth** `object`  |  Authentication configuration

Authentication information used to update the credential.

* If omitted, all original values are retained.

* If provided, you only need to specify `type` and the parameters to modify; other unchanged parameters do not need to be provided again.

* `type` identifies the current credential type and must match the current value.

> **Note**

> When `mcp_static.token` is updated, the server synchronously accesses the target MCP service:

* > If the returned content does not comply with the MCP protocol, HTTP 424 `MCPInvalidResponse` is returned.

* > If the MCP service returns 5xx or a connection exception occurs, HTTP 424 `UpstreamUnavailable` is returned.

* > If validation fails, the credential is not updated.

MCP OAuth credential `object`

Used to update an MCP OAuth credential. You can rotate the access token, enable automatic refresh, or update the information required for automatic refresh.

**type** `string` `Required`  |  Authentication type

`auth.type`

Used to identify the current credential as the MCP OAuth type. Required when updating `auth`; it cannot be changed to another type.

Value is fixed to `mcp_oauth`.

**access_token** `string`  |  Access token

`auth.access_token`

The OAuth access token used to access the MCP service. If omitted, the original value is retained; if provided, it is replaced as a whole.

**expires_at** `string`  |  Expiration time

`auth.expires_at`

The expiration time of the access token in RFC 3339 format. The server uses this time to determine whether automatic refresh is needed; if omitted, the original value is retained; if provided, it is replaced in full.

**mcp_server_url** `string`  |  MCP service URL

`auth.mcp_server_url`

The access address of the MCP service. You do not need to provide it when updating; if provided, it must match the current value, otherwise HTTP 409 `Conflict` is returned.

**refresh** `object`  |  Access token refresh

`auth.refresh`

Used to enable or update automatic access token refresh. When enabling automatic refresh, provide the complete object. After automatic refresh is enabled, you only need to provide the parameters to update; parameters that are not provided retain their original values.

**client_id** `string`  |  Client ID

`auth.refresh.client_id`

The client ID used when requesting an access token from the OAuth token endpoint. Required when enabling automatic refresh; you do not need to provide it when updating automatic refresh information. If provided, it must match the current value.

**refresh_token** `string`  |  Refresh token

`auth.refresh.refresh_token`

The refresh token used to obtain a new access token from the OAuth token endpoint. Required when enabling automatic refresh; when updating automatic refresh information, omitting this parameter retains the original value, and providing it replaces it as a whole.

**resource** `string`  |  Resource identifier

`auth.refresh.resource`

The resource identifier corresponding to the OAuth authorization request. If omitted, the original value is retained; if provided, it is replaced as a whole.

**scope** `string`  |  Authorization scope

`auth.refresh.scope`

The permission scope that the OAuth token is allowed to access. If omitted, the original value is retained; if provided, it is replaced as a whole.

**token_endpoint** `string`  |  OAuth token endpoint

`auth.refresh.token_endpoint`

The OAuth endpoint address used to refresh the access token. Required when enabling automatic refresh; you do not need to provide it when updating automatic refresh information. If provided, it must match the current value.

**token_endpoint_auth** `object`  |  OAuth client authentication

`auth.refresh.token_endpoint_auth`

The client authentication configuration used when the server submits a refresh request to the OAuth token endpoint. When enabling automatic refresh, provide the complete object according to the authentication method; when updating automatic refresh information, only explicitly passed parameters are updated.

**type** `string` `Required`  |  Authentication method

`auth.refresh.token_endpoint_auth.type`

The client authentication method used when the server submits a refresh request to the OAuth token endpoint:

* `none`: Submit only `client_id` without using a client secret.

* `client_secret_basic`: submits `client_id` and `client_secret` through HTTP basic authentication.

* `client_secret_post`: submits `client_id` and `client_secret` in the request body.

**client_secret** `string`  |  Client secret

`auth.refresh.token_endpoint_auth.client_secret`

OAuth client secret.

* When automatic refresh is enabled, if `type` is `client_secret_basic` or `client_secret_post`, this parameter is required; if `type` is `none`, this parameter must be omitted.

* When updating automatic refresh information, omitting this parameter retains the original value, and providing it replaces it as a whole.

Static bearer token credential `object`

Used to update a static bearer token credential; you can rotate the token but cannot change the MCP service address.

**type** `string` `Required`  |  Authentication type

`auth.type`

Used to identify the current credential as the static bearer token type. Required when updating `auth`; it cannot be changed to another type.

Value is fixed to `static_bearer`.

**mcp_server_url** `string`  |  MCP service URL

`auth.mcp_server_url`

The access address of the MCP service. You do not need to provide it when updating; if provided, it must match the current value, otherwise HTTP 409 `Conflict` is returned.

**token** `string`  |  Bearer token

`auth.token`

Static bearer token used to access the MCP service. If omitted, the original value is retained; if provided, it is replaced as a whole.

MCP static credential `object`

Used to update an MCP static credential; you can rotate the key but cannot change the target address or injection method.

**type** `string` `Required`  |  Authentication type

`auth.type`

Used to identify the current credential as the MCP static key type. Required when updating `auth`; it cannot be changed to another type.

Value is fixed to `mcp_static`.

**injection_name** `string`  |  Injection name

`auth.injection_name`

Custom request header name or query parameter name. You do not need to provide it when updating; if provided, it must match the current value, otherwise HTTP 409 `Conflict` is returned.

**injection_type** `string`  |  Injection method

`auth.injection_type`

The injection method for the static key. You do not need to provide it when updating; if provided, it must match the current value, otherwise HTTP 409 `Conflict` is returned. Options: `bearer` / `header` / `query`.

**mcp_server_url** `string`  |  MCP service URL

`auth.mcp_server_url`

The access address of the MCP service. You do not need to provide it when updating; if provided, it must match the current value, otherwise HTTP 409 `Conflict` is returned.

**token** `string`  |  Key value

`auth.token`

The static key to inject into MCP requests.

* If omitted, the original value is retained.

* If provided, it is replaced as a whole and cannot be empty.

* When `injection_type` is `bearer`, do not include the `Bearer` prefix.

Environment variable injection credential `object`

Used to update an environment variable credential. You can replace the credential value and network scope, but cannot change the environment variable name.

**type** `string` `Required`  |  Authentication type

`auth.type`

Used to identify the current credential as the environment variable type. Required when updating `auth`; it cannot be changed to another type.

Value is fixed to `environment_variable`.

**networking** `object`  |  Network scope

`auth.networking`

Controls the scope of target hosts into which the credential can be injected. If omitted, the original value is retained; if provided, it is replaced in full and revalidated.

**type** `string` `Required`  |  Network scope type

`auth.networking.type`

Controls the scope of target hosts into which the credential can be injected:

* `unrestricted`: Can be injected into requests sent to any public network host.

* `limited`: Can only be injected into requests sent to hosts listed in `allowed_hosts`.

**allowed_hosts** `string[]`  |  Allowed host list

`auth.networking.allowed_hosts`

The list of hosts into which injection is allowed when `type` is `limited`.

* Provide 1–16 unique hosts.

* Each item can be a domain name, an IPv4 address, or a wildcard domain name in the form `*.example.com`.

* URLs, ports, paths, or IPv6 addresses are not accepted.

**secret_name** `string`  |  Environment variable name

`auth.secret_name`

The environment variable name used when injecting the credential into the runtime environment. You do not need to provide it when updating; if provided, it must match the current value, otherwise HTTP 409 `Conflict` is returned.

**secret_value** `string`  |  Environment variable value

`auth.secret_value`

The credential value injected into the runtime environment. If omitted, the original value is retained; if provided, it is replaced in full and cannot be empty. The maximum size is 4096 bytes.

Environment OAuth credential `object`

Used to update an environment OAuth credential. You can rotate the access token, enable automatic refresh, update the information required for automatic refresh, or update the network scope.

**type** `string` `Required`  |  Authentication type

`auth.type`

Used to identify the current credential as the environment OAuth type. Required when updating `auth`; it cannot be changed to another type.

Value is fixed to `env_oauth`.

**access_token** `string`  |  Access token

`auth.access_token`

The OAuth access token injected into the runtime environment and used for target service authentication. If omitted, the original value is retained; if provided, it is replaced as a whole.

**expires_at** `string`  |  Expiration time

`auth.expires_at`

The expiration time of the access token in RFC 3339 format. The server uses this time to determine whether automatic refresh is needed; if omitted, the original value is retained; if provided, it is replaced in full.

**networking** `object`  |  Network scope

`auth.networking`

Controls the scope of target hosts into which the access token can be injected. If omitted, the original value is retained; if provided, it is replaced in full and revalidated.

**type** `string` `Required`  |  Network scope type

`auth.networking.type`

Controls the scope of target hosts into which the credential can be injected:

* `unrestricted`: Can be injected into requests sent to any public network host.

* `limited`: Can only be injected into requests sent to hosts listed in `allowed_hosts`.

**allowed_hosts** `string[]`  |  Allowed host list

`auth.networking.allowed_hosts`

The list of hosts into which injection is allowed when `type` is `limited`.

* Provide 1–16 unique hosts.

* Each item can be a domain name, an IPv4 address, or a wildcard domain name in the form `*.example.com`.

* URLs, ports, paths, or IPv6 addresses are not accepted.

**provider_name** `string`  |  OAuth service identifier

`auth.provider_name`

The OAuth service identifier associated with the credential. Supported only when `auth.type=env_oauth`; you do not need to provide it when updating. If provided, the value must match the current value.

**refresh** `object`  |  Access token refresh

`auth.refresh`

Used to enable or update automatic access token refresh. When enabling automatic refresh, provide the complete object. After automatic refresh is enabled, you only need to provide the parameters to update; parameters that are not provided retain their original values.

**client_id** `string`  |  Client ID

`auth.refresh.client_id`

The client ID used when requesting an access token from the OAuth token endpoint. Required when enabling automatic refresh; you do not need to provide it when updating automatic refresh information. If provided, it must match the current value.

**refresh_token** `string`  |  Refresh token

`auth.refresh.refresh_token`

The refresh token used to obtain a new access token from the OAuth token endpoint. Required when enabling automatic refresh; when updating automatic refresh information, omitting this parameter retains the original value, and providing it replaces it as a whole.

**resource** `string`  |  Resource identifier

`auth.refresh.resource`

The resource identifier corresponding to the OAuth authorization request. If omitted, the original value is retained; if provided, it is replaced as a whole.

**scope** `string`  |  Authorization scope

`auth.refresh.scope`

The permission scope that the OAuth token is allowed to access. If omitted, the original value is retained; if provided, it is replaced as a whole.

**token_endpoint** `string`  |  OAuth token endpoint

`auth.refresh.token_endpoint`

The OAuth endpoint address used to refresh the access token. Required when enabling automatic refresh; you do not need to provide it when updating automatic refresh information. If provided, it must match the current value.

**token_endpoint_auth** `object`  |  OAuth client authentication

`auth.refresh.token_endpoint_auth`

The client authentication configuration used when the server submits a refresh request to the OAuth token endpoint. When enabling automatic refresh, provide the complete object according to the authentication method; when updating automatic refresh information, only explicitly passed parameters are updated.

**type** `string` `Required`  |  Authentication method

`auth.refresh.token_endpoint_auth.type`

The client authentication method used when the server submits a refresh request to the OAuth token endpoint:

* `none`: Submit only `client_id` without using a client secret.

* `client_secret_basic`: submits `client_id` and `client_secret` through HTTP basic authentication.

* `client_secret_post`: submits `client_id` and `client_secret` in the request body.

**client_secret** `string`  |  Client secret

`auth.refresh.token_endpoint_auth.client_secret`

OAuth client secret.

* When automatic refresh is enabled, if `type` is `client_secret_basic` or `client_secret_post`, this parameter is required; if `type` is `none`, this parameter must be omitted.

* When updating automatic refresh information, omitting this parameter retains the original value, and providing it replaces it as a whole.

**secret_name** `string`  |  Environment variable name

`auth.secret_name`

The environment variable name used when injecting the access token into the runtime environment. You do not need to provide it when updating; if provided, it must match the current value, otherwise HTTP 409 `Conflict` is returned.

**display_name** `string`  |  Credential display name

The display name of the credential, used to identify the credential in the credential list and details. It cannot contain only whitespace characters; if omitted, the original value is retained.

**metadata** `object`  |  Custom metadata

String key\-value pairs used to categorize or associate business information. The pairs are added or overwritten by key; keys that do not appear are retained, and empty strings are saved as regular values. After the update, there can be at most 16 pairs; keys must be 1 to 64 characters long, and values can be up to 512 characters long.

## Response parameters

**auth** `object`  |  Authentication configuration

The credential's authentication method and publicly returnable configuration. The parameter structure is determined by `type`. The response returns only one structure that matches `type`.

MCP OAuth credential `object`

The server returns the authentication type and MCP service URL of the MCP OAuth credential. When automatic refresh is configured, the server also returns the access token expiration time and the parameters used to refresh the access token.

The response does not return the access token, refresh token, or client secret.

**mcp_server_url** `string`  |  MCP service URL

`auth.mcp_server_url`

The access address of the MCP service.

**type** `string`  |  Authentication type

`auth.type`

Indicates that this credential uses an OAuth access token to connect to the MCP service.

Value is fixed to `mcp_oauth`.

**expires_at** `string`  |  Expiration time

`auth.expires_at`

The expiration time of the access token in RFC 3339 format. When a session uses this credential and the access token enters the 5\-minute refresh window, the server attempts to refresh it automatically.

**refresh** `object`  |  Access token refresh

`auth.refresh`

The server returns the parameters used to refresh the access token, but does not return the refresh token or client secret.

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

Static bearer token credential `object`

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

The server returns the authentication type, environment variable name, and network scope of the environment OAuth credential. When automatic refresh is configured, the server also returns the access token expiration time and the parameters used to refresh the access token.

The response does not return the access token, refresh token, or client secret.

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

**expires_at** `string`  |  Expiration time

`auth.expires_at`

The expiration time of the access token in RFC 3339 format. When a session uses this credential and the access token enters the 5\-minute refresh window, the server attempts to refresh it automatically.

**provider_name** `string`  |  OAuth service identifier

`auth.provider_name`

The OAuth service identifier associated with the credential. Supported only when `auth.type=env_oauth`.

**refresh** `object`  |  Access token refresh

`auth.refresh`

The server returns the parameters used to refresh the access token, but does not return the refresh token or client secret.

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
