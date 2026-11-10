# Use Vaults authentication

When you use Managed Agents to build applications that need to access third\-party services, you can use vaults (credential vaults) to store and isolate credentials for each application user. Your server only needs to create a vault for each user, write credentials, and store the mapping between the user and `vault_id`; you do not need to maintain key storage yourself or repeatedly pass tokens in every call.

When creating a session, pass the current user's vault ID through `vault_ids`; when the agent calls MCP services, it automatically uses the corresponding credential to authenticate. This lets the same agent safely serve multiple users and access only the third\-party resources each user is authorized to use.

> **Example**

> You have built a GitHub code assistant using Managed Agents. When Alice starts a task, the server creates a session and binds Alice's vault, and the agent uses Alice's GitHub permissions; when Bob starts a task, the server binds Bob's vault, and the agent uses Bob's GitHub permissions.

## Prerequisites

Before you begin, make sure you have:

* An API key configured as the `ARK_API_KEY` environment variable. For details, see [API key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey).

* A created Agent. For details, see [Define an Agent](define-agent.md).

* A created Environment. For details, see [Configure cloud environments](configure-cloud-environment.md).

For the base URL and authentication used in the examples in this section, see [Base URL and authentication](base-url-and-authentication.md).

## Create a vault

> **Permission boundaries**

> Vaults and credentials are shared within a workspace and are not isolated separately by API key. Any service that holds any API key in the same workspace can reference the credentials in a session by vault ID. Only expose workspace API keys to trusted services.

Vaults are credential collections bound to an end user. Give it a `display_name`. You can optionally use `metadata` tags to map it back to your own user record:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/vaults \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "display_name": "Alice",
    "metadata": {"external_user_id": "usr_abc123"}
  }'
```

The response is the complete vault record:

```json
{
  "type": "vault",
  "id": "vlt-20260701120000-pqrst",
  "display_name": "Alice",
  "metadata": {"external_user_id": "usr_abc123"},
  "created_at": "2026-06-29T10:00:00Z",
  "updated_at": "2026-06-29T10:00:00Z"
}
```

## Add credentials

|Type |Supported use cases |Injection method |
|---|---|---|
|`mcp_oauth` |MCP servers use OAuth 2.0. |When a session connects to the MCP URL, the access token is injected; after automatic refresh is enabled, the platform obtains and injects a new token when the token is about to expire. |
|`static_bearer` |MCP uses a fixed bearer token (API key or personal access token). |No refresh flow. Injected directly. |
|`mcp_static` |MCP uses a fixed key that must be injected as a bearer token, custom header, or URL query parameter. |Select the injection location through `injection_type`. |
|`environment_variable` |CLI, SDK, and direct API calls that authenticate through environment variables. |Opaque placeholder in the sandbox. Replaced with the real value at the **egress point**. The agent never sees the key. |
|`env_oauth` |CLIs or SDKs use short\-lived OAuth access tokens. |Injected by `secret_name`; after automatic refresh is enabled, a new token is obtained when the session uses the credential. |

The actual keys you provide (`token`, `access_token`, `refresh_token`, `client_secret`, `secret_value`) are sensitive **write\-only** parameters, and are never returned in API responses.

### OAuth callback URL

When you use ModelArk to initiate the OAuth authorization flow, configure the callback URL based on the credential type:

|Credential type |Callback URL |
|---|---|
|MCP OAuth (`mcp_oauth`) |`https://console.byteplus.com/api/top/ark/ap-southeast-1/2024-01-01/VaultOAuthCallback` |
|Environment OAuth (`env_oauth`) |`https://ark-agent.ap-southeast.bytepluses.com/api/v3/vault_env_oauth_callback` |

ModelArk uses fixed callback URLs by channel and credential type. The application does not need to provide a custom callback URL.

> **Callback URL boundaries**

* > Register the exact full URL from the table in the third\-party OAuth application. Do not use a wildcard for the entire console or `ark-agent` domain.

* > MCP OAuth and environment OAuth use different callback URLs and are not interchangeable.

### MCP OAuth credentials

Use `mcp_oauth` when the MCP server uses OAuth 2.0. When creating the credential, configure the MCP service address and the current access token.

To enable automatic refresh, also provide `expires_at` and the complete `refresh` object when creating or updating the credential. When a session connects to the MCP service, the platform reads this credential. When the access token enters the 5\-minute refresh window before `expires_at`, the platform automatically obtains and injects a new token. This mechanism is triggered by read operations and does not refresh on a background schedule.

Choose one of the following three types for `refresh.token_endpoint_auth.type`:

* **`none`**: Public client. Do not provide `client_secret`.

* **`client_secret_basic`**: Uses HTTP basic authentication with `client_secret`. Also provide `client_secret`.

* **`client_secret_post`**: Puts `client_secret` in the POST request body. Also provide `client_secret`.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/vaults/vlt-20260701120000-pqrst/credentials \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "display_name": "Alice Slack",
    "auth": {
      "type": "mcp_oauth",
      "mcp_server_url": "https://mcp.slack.com/mcp",
      "access_token": "xoxp-...",
      "expires_at": "2099-12-31T23:59:59Z",
      "refresh": {
        "token_endpoint": "https://slack.com/api/oauth.v2.access",
        "client_id": "1234567890.0987654321",
        "scope": "channels:read chat:write",
        "refresh_token": "xoxe-1-...",
        "token_endpoint_auth": {
          "type": "client_secret_post",
          "client_secret": "abc123..."
        }
      }
    }
  }'
```

### MCP static bearer credentials

Use `static_bearer` when the MCP server accepts a fixed bearer token (API key or personal access token). No refresh flow is required:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/vaults/vlt-20260701120000-pqrst/credentials \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "display_name": "Linear API key",
    "auth": {
      "type": "static_bearer",
      "mcp_server_url": "https://mcp.linear.app/mcp",
      "token": "lin_api_your_linear_key"
    }
  }'
```

### MCP static credential

Use `mcp_static` when the MCP server uses a fixed key but does not necessarily use bearer authentication. Select the injection method through `injection_type`:

* **`bearer`**: Injected through the `Authorization: Bearer <token>` request header. Do not pass `injection_name`, and do not include the `Bearer` prefix in `token`.

* **`header`**: Injected through a custom request header. You must specify the request header name with `injection_name`.

* **`query`**: Injected through a URL query parameter. You must specify the parameter name with `injection_name`.

The following example injects the key into the `X-API-Key` request header:

```json
{
  "auth": {
    "type": "mcp_static",
    "mcp_server_url": "https://mcp.example.com/mcp",
    "token": "<MCP_API_KEY>",
    "injection_type": "header",
    "injection_name": "X-API-Key"
  }
}
```

> **Tip**

> **Principle of least privilege.** Limit the permission scope of the fixed key to the minimum set required by the agent. The agent can perform any operation allowed by the key. An overly broad permission scope increases the impact if the agent behaves abnormally.

### Environment variable credentials

Use `environment_variable` to authenticate to external services through environment variables. It applies to CLI, SDK, or direct API calls.

`networking.allowed_hosts` controls which outbound hosts the key can be replaced for:

* `"type": "limited"` + an explicit host list (**recommended**).

* `"type": "unrestricted"` (use only when the domain names accessed by the caller cannot be enumerated in advance).

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/vaults/vlt-20260701120000-pqrst/credentials \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "display_name": "Notion API key for sandbox",
    "auth": {
      "type": "environment_variable",
      "secret_name": "NOTION_API_KEY",
      "secret_value": "sk-your-secret-here",
      "networking": {
        "type": "limited",
        "allowed_hosts": ["api.notion.com"]
      }
    }
  }'
```

> **Tip**

> **Replacement is for outbound only.** If the client uses a stored key to exchange for a temporary access token, the returned token enters the runtime environment without redaction. For these flows, token exchange and storage should be managed on the application side.

### Environment OAuth credential

When the CLI or SDK needs to read an OAuth access token from an environment variable, use `env_oauth`. When creating the credential, first configure the current access token (`access_token`), the environment variable name for injecting the token (`secret_name`), and the target hosts allowed to carry the token (`networking`).

For access tokens that expire, you can enable automatic refresh. When creating or updating a credential, provide the following information:

* **Refresh timing**: Provide the expiration time of the current access token through `expires_at`.

* **Token endpoint**: Provide the token endpoint of the third\-party OAuth service through `refresh.token_endpoint`.

* **Refresh token**: Provide the credential used to exchange for a new access token through `refresh.refresh_token`.

* **Client identity**: Provide the OAuth client identity and authentication method through `refresh.client_id` and `refresh.token_endpoint_auth`. If the authentication method requires a client secret, also provide `refresh.token_endpoint_auth.client_secret`.

When a session accesses a target host allowed by `networking`, the platform reads this credential. When the access token enters the refresh window 5 minutes before `expires_at`, the platform calls `refresh.token_endpoint` to obtain a new token, updates the credential, and then injects the new token into the outbound request. This mechanism is triggered by read operations and does not refresh on a background schedule.

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/vaults/vlt-20260701120000-pqrst/credentials \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "display_name": "GitHub CLI OAuth",
    "auth": {
      "type": "env_oauth",
      "access_token": "<OAUTH_ACCESS_TOKEN>",
      "expires_at": "<ACCESS_TOKEN_EXPIRES_AT>",
      "refresh": {
        "token_endpoint": "https://github.com/login/oauth/access_token",
        "client_id": "<OAUTH_CLIENT_ID>",
        "refresh_token": "<OAUTH_REFRESH_TOKEN>",
        "token_endpoint_auth": {
          "type": "client_secret_post",
          "client_secret": "<OAUTH_CLIENT_SECRET>"
        }
      },
      "secret_name": "GITHUB_TOKEN",
      "networking": {
        "type": "limited",
        "allowed_hosts": ["api.github.com"]
      }
    }
  }'
```

> **Note**

* > **When enabling automatic refresh, provide both****`expires_at`****and the complete****`refresh`****object.** Without `expires_at`, the platform cannot determine when to refresh; without `refresh`, the platform will not call the OAuth token endpoint.

* > **The OAuth token endpoint must return JSON.** `access_token` must be a string; if `expires_in` is returned, this parameter must be an integer number of seconds. Refresh fails if `expires_in` returns a string value such as `"3600"`. The platform retains the current access token, and this outbound request may return 401 because the token has expired. Check the types of the parameters returned by `refresh.token_endpoint` and the `refresh.token_endpoint_auth` configuration; if the refresh token is invalid, reauthorize and update the credential.

* > **Replacement happens at the sandbox egress point, not inside the sandbox.** Processes in the sandbox see an opaque placeholder, not the real value. The specific impacts are as follows:

   * > Clients that validate credential formats at startup may reject the placeholder.

   * > Clients that use the key to sign requests (for example, AWS SigV4) generate invalid signatures.

   * > Environment variable injection is suitable only for clients that write the key value unchanged into outbound request headers.

* > **Restrict injection targets through credentials.** Set `type: limited` in the credential's `networking`, and explicitly list the outbound hosts where injection is allowed through `allowed_hosts`.

### Credential constraints

* **Matching keys must be unique within each vault.**  `mcp_server_url` (`mcp_oauth`, `static_bearer`, `mcp_static`) and `secret_name` (`environment_variable`, `env_oauth`) cannot be duplicated among active credentials in the same vault. Duplicate creation returns 409.

* **Matching keys are immutable.**  To change `mcp_server_url` or `secret_name`, delete the old credential and create a new one.

* **Each vault can contain up to 100 credentials.**  Returns 409 when the limit is exceeded.

MCP\-type credentials (`mcp_oauth`, `static_bearer`, `mcp_static`) immediately connect to the target MCP server to probe the handshake when they are created. If the handshake cannot be completed, the create request returns a 4xx error.`environment_variable` and `env_oauth` do not validate whether the key or access token is valid when they are created; invalid values appear as authentication errors or downstream service errors when a session accesses the corresponding host, but they do not prevent the session from continuing to run.

## Reference vaults when creating a session

When creating a session, pass the `vault_ids` array to attach one or more vaults to the session:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/sessions \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "agent": "agent-20260701120000-abcde",
    "environment_id": "env-20260701120000-fghij",
    "vault_ids": ["vlt-20260701120000-pqrst"],
    "title": "Alice Slack digest"
  }'
```

**Runtime behavior**:

* When an agent connects to an MCP URL and **no credential matches** `mcp_server_url`, the agent attempts an anonymous connection; if the server requires authentication, an error is returned.

* **If multiple vaults contain matching credentials, the first matching vault is used.**

* **Multi\-agent credential scope**: In [multi-agent](multi-agent.md), vault credentials take effect **per thread**; if an agent's own definition declares a matching MCP server, the agent uses these credentials for authentication.

## Rotate credentials

Key values and `display_name` can be updated. Structural parameters (`mcp_server_url`, `secret_name`, `token_endpoint`, `client_id`) are locked after creation. To modify structural parameters, delete the old credential and create a new one:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/vaults/vlt-20260701120000-pqrst/credentials/vcrd-20260701120500-uvwxy \
  -X POST \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "auth": {
      "type": "mcp_oauth",
      "access_token": "xoxp-new-...",
      "expires_at": "2099-12-31T23:59:59Z",
      "refresh": {"refresh_token": "xoxe-1-new-..."}
    }
  }'
```

Automatic refresh information for OAuth credentials can be updated as follows:

* **Enable automatic refresh**: Provide both `expires_at` and the complete `refresh` object in `auth`. When `token_endpoint_auth.type` is `client_secret_basic` or `client_secret_post`, also provide `client_secret`; when it is `none`, omit `client_secret`.

* **Update automatic refresh information**: Provide only the parameters that need to change; parameters that are not provided retain their original values. You can update `refresh_token`, `token_endpoint_auth`, `resource`, and `scope`, but cannot modify `token_endpoint` or `client_id`.

## Credential lifecycle

While a session is running, the platform re\-resolves the current value when it uses a credential. After a credential is updated or deleted, subsequent requests read the new credential state, with no need to restart the session.

For `mcp_oauth` and `env_oauth` credentials with automatic refresh enabled, read operations trigger automatic refresh when the access token enters the 5\-minute refresh window. After a refresh succeeds, the platform persists the new access token and expiration time; if the OAuth service also rotates `refresh_token`, the platform saves the new `refresh_token` as well.

## Other operations

* **List vaults and credentials**: Call `GET /vaults` or `GET /vaults/{vault_id}/credentials`. Results are paginated and sorted from newest to oldest.

* To disable a single credential, delete that credential; to disable all credentials for an end user, delete the corresponding vault. Deletion operations cannot be undone.
