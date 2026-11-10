# Create credential

`POST https://ark.ap-southeast.bytepluses.com/api/v3/vaults/{vault_id}/credentials`

Creates a credential in the specified vault. After the credential is created, you can query, update, or delete it by using the returned credential ID.

Each vault can contain up to 100 credentials. If this limit is exceeded, HTTP 409 `Conflict` is returned.

Select the authentication method through `auth.type`:


* `mcp_oauth`: Uses an OAuth access token to connect to the MCP service.

* `static_bearer`: Uses a static bearer token to connect to the MCP service.

* `mcp_static`: Injects a static key into MCP requests. Injection locations such as request headers or query parameters are supported.

* `environment_variable`: Injects the credential into the runtime environment through an environment variable.

* `env_oauth`: Manually provides an OAuth access token and injects it into the runtime environment through an environment variable.


The response does not return the following cyphertext parameters: `access_token`, `token`, `secret_value`, `refresh_token`, and `client_secret`.

This API does not support idempotency keys. The server does not automatically identify or merge duplicate create requests.

If the create request times out, first query the credential list to confirm whether the target credential has already been created before deciding whether to retry. Directly retrying a create request for the same target returns HTTP 409 `Conflict`, and the response does not include the existing credential ID.

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


**auth** `object` `Required`  |  Authentication configuration

Used to configure the credential's authentication method. Fill in the corresponding parameters based on the value of `type`.

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">When creating <code>mcp_oauth</code>, <code>static_bearer</code>, or <code>mcp_static</code> credentials, the server synchronously accesses the target MCP service:</div>



* <div data-tips="true" data-tips-type="warning">If the returned content does not comply with the MCP protocol, HTTP 424 <code>MCPInvalidResponse</code> is returned.</div>


* <div data-tips="true" data-tips-type="warning">If the MCP service returns 5xx or a connection exception occurs, HTTP 424 <code>UpstreamUnavailable</code> is returned.</div>


* <div data-tips="true" data-tips-type="warning">If validation fails, the credential is not created.</div>



<div data-tips="true" data-tips-type="warning">MCP credentials in the same vault cannot use the same <code>mcp_server_url</code>. If duplicated, HTTP 409 <code>Conflict</code> is returned.</div>



MCP OAuth credential `object`

The caller provides an OAuth access token and optional refresh configuration for connecting to the specified MCP service.


**access_token** `string` `Required`  |  Access token

`auth.access_token`

The OAuth access token used to access the MCP service.



**mcp_server_url** `string` `Required`  |  MCP service URL

`auth.mcp_server_url`

The access address of the MCP service.


* HTTPS must be used. The URL cannot contain user information or point to a local or private network address.

* The fragment after `#` in the URL is not included in requests. The server removes this fragment when saving the URL.

* Cannot be modified after creation.



**type** `string` `Required`  |  Authentication type

`auth.type`

Selects OAuth access token authentication to connect to the MCP service.

Value is fixed to `mcp_oauth`.



**expires_at** `string`  |  Expiration time

`auth.expires_at`

The expiration time of the access token in RFC 3339 format.



**refresh** `object`  |  Refresh configuration

`auth.refresh`

Used to automatically obtain a new token when the access token is about to expire. If this parameter is omitted, the server does not automatically update the access token by using the refresh token.


**client_id** `string`  |  Client ID

`auth.refresh.client_id`

The client ID used when requesting an access token from the OAuth token endpoint. Required when the request includes `refresh`.



**refresh_token** `string`  |  Refresh token

`auth.refresh.refresh_token`

The refresh token used to obtain a new access token from the OAuth token endpoint. Required when the request includes `refresh`.



**resource** `string`  |  Resource identifier

`auth.refresh.resource`

The resource identifier corresponding to the OAuth authorization request.



**scope** `string`  |  Authorization scope

`auth.refresh.scope`

Authorization scopes supported by the OAuth service.


* Multiple scopes are usually separated by spaces, for example, `read write`.

* Specific values depend on the OAuth service provider.

* If this parameter is omitted, the server does not pass `scope` when refreshing the token.



**token_endpoint** `string`  |  OAuth token endpoint

`auth.refresh.token_endpoint`

The OAuth endpoint address used to refresh the access token. Required when the request includes `refresh`.



**token_endpoint_auth** `object`  |  OAuth client authentication

`auth.refresh.token_endpoint_auth`

The client authentication configuration used when the server submits a refresh request to the OAuth token endpoint.


**type** `string` `Required`  |  Authentication method

`auth.refresh.token_endpoint_auth.type`

The client authentication method used when the server submits a refresh request to the OAuth token endpoint:


* `none`: Submit only `client_id` without using a client secret.

* `client_secret_basic`: submits `client_id` and `client_secret` through HTTP basic authentication.

* `client_secret_post`: submits `client_id` and `client_secret` in the request body.



**client_secret** `string`  |  Client secret

`auth.refresh.token_endpoint_auth.client_secret`

OAuth client secret.


* Required when `type` is `client_secret_basic` or `client_secret_post`.

* Must be omitted when `type` is `none`.






Static earer token credential `object`

The caller provides a static bearer token for connecting to the specified MCP service.


**mcp_server_url** `string` `Required`  |  MCP service URL

`auth.mcp_server_url`

The access address of the MCP service.


* HTTPS must be used. The URL cannot contain user information or point to a local or private network address.

* The fragment after `#` in the URL is not included in requests. The server removes this fragment when saving the URL.

* Cannot be modified after creation.



**token** `string` `Required`  |  Bearer token

`auth.token`

Static bearer token used to access the MCP service.



**type** `string` `Required`  |  Authentication type

`auth.type`

Selects static bearer token authentication to connect to the MCP service.

Value is fixed to `static_bearer`.




MCP static credential `object`

The caller provides the static key and its injection method to authenticate requests sent to the specified MCP service.


**mcp_server_url** `string` `Required`  |  MCP service URL

`auth.mcp_server_url`

The access address of the MCP service.


* HTTPS must be used. The URL cannot contain user information or point to a local or private network address.

* The fragment after `#` in the URL is not included in requests. The server removes this fragment when saving the URL.

* Cannot be modified after creation.



**token** `string` `Required`  |  Key value

`auth.token`

The static key to inject into MCP requests.

Do not include the `Bearer` prefix; when `injection_type` is `bearer`, the request header is `Authorization: Bearer <token>`.



**type** `string` `Required`  |  Authentication type

`auth.type`

Select to inject the static key into MCP requests. Use `injection_type` to specify the injection location, such as a request header or query parameter.

Value is fixed to `mcp_static`.



**injection_name** `string`  |  Injection name

`auth.injection_name`

Custom request header name or query parameter name.


* When `injection_type` is `header`, this parameter is required. The request header name must comply with HTTP token rules and cannot use `Authorization`, `Host`, `Content-Length`, `Cookie`, or `Proxy-Authorization`; passing these names returns HTTP 400 `InvalidParameter`.

* When `injection_type` is `query`, this parameter is required. This parameter must be a single parameter name and cannot contain `?`, `&`, or `=`.

* When `injection_type` is `bearer`, this parameter cannot be passed.



**injection_type** `string` `Default bearer`  |  Injection method

`auth.injection_type`

How the key is injected into outbound MCP requests:


* `bearer`: uses `Authorization: Bearer <token>`, and `injection_name` cannot be passed.

* `header`: uses the custom request header name specified by `injection_name`.

* `query`: uses the URL query parameter name specified by `injection_name`.




Environment variable injection credential `object`

The caller provides the environment variable name, credential value, and network scope to inject the credential into the runtime environment.


**networking** `object` `Required`  |  Network scope

`auth.networking`

Controls the scope of target hosts into which the credential can be injected.


**type** `string` `Required`  |  Network scope type

`auth.networking.type`

Controls the scope of target hosts into which the credential can be injected:


* `unrestricted`: Can be injected into requests sent to any public network host.

* `limited`: Can only be injected into requests sent to hosts listed in `allowed_hosts`.



**allowed_hosts** `string[]`  |  Allowed host list

`auth.networking.allowed_hosts`

Required when `type` is `limited`. Contains 1–16 public hosts into which credentials can be injected.

Each item must be a bare domain name, wildcard domain name, or public IPv4 address, for example, `api.example.com`, `*.example.com`, or `192.0.2.1`.

URLs, ports, paths, IPv6 addresses, and private network addresses are not supported. This parameter must be omitted when `type` is `unrestricted`.




**secret_name** `string` `Required`  |  Environment variable name

`auth.secret_name`

The environment variable name used when injecting the credential into the runtime environment. The name must match `^[A-Za-z_][A-Za-z0-9_]*$`.

The name must be unique within the current vault. If it is duplicated, HTTP 409 `Conflict` is returned. This parameter cannot be modified after creation.



**secret_value** `string` `Required`  |  Environment variable value

`auth.secret_value`

The credential value injected into the runtime environment. It cannot be empty and can be up to 4096 bytes.



**type** `string` `Required`  |  Authentication type

`auth.type`

Select to inject the credential into the runtime environment through an environment variable.

Value is fixed to `environment_variable`.




Environment OAuth credential `object`

The caller provides an OAuth access token, environment variable name, and network scope to inject the token into the runtime environment.


**access_token** `string` `Required`  |  Access token

`auth.access_token`

The OAuth access token injected into the runtime environment and used for target service authentication.



**networking** `object` `Required`  |  Network scope

`auth.networking`

Controls the scope of target hosts into which the access token can be injected.


**type** `string` `Required`  |  Network scope type

`auth.networking.type`

Controls the scope of target hosts into which the credential can be injected:


* `unrestricted`: Can be injected into requests sent to any public network host.

* `limited`: Can only be injected into requests sent to hosts listed in `allowed_hosts`.



**allowed_hosts** `string[]`  |  Allowed host list

`auth.networking.allowed_hosts`

Required when `type` is `limited`. Contains 1–16 public hosts into which credentials can be injected.

Each item must be a bare domain name, wildcard domain name, or public IPv4 address, for example, `api.example.com`, `*.example.com`, or `192.0.2.1`.

URLs, ports, paths, IPv6 addresses, and private network addresses are not supported. This parameter must be omitted when `type` is `unrestricted`.




**secret_name** `string` `Required`  |  Environment variable name

`auth.secret_name`

The environment variable name used when injecting the access token into the runtime environment. The name must match `^[A-Za-z_][A-Za-z0-9_]*$`.

The name must be unique within the current vault. If it is duplicated, HTTP 409 `Conflict` is returned. This parameter cannot be modified after creation.



**type** `string` `Required`  |  Authentication type

`auth.type`

Select to manually provide an OAuth access token and inject it into the runtime environment through an environment variable.

Value is fixed to `env_oauth`.





**display_name** `string` `Required`  |  Credential display name

The display name of the credential, used to identify the credential in the credential list and details.



**metadata** `object`  |  Custom metadata

String key\-value pairs used to categorize or associate business information. At creation, the complete object provided in this request is saved, with up to 16 pairs; keys must be 1 to 64 characters long, and values can be up to 512 characters long.


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
