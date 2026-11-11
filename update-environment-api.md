# Update environment

`POST https://ark.ap-southeast.bytepluses.com/api/v3/environments/{environment_id}`

Update the name, description, runtime configuration, or custom metadata of the specified environment.

The updated runtime configuration applies only to sessions created later; existing sessions continue to use the configuration from when they were created.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**environment_id** `string` `Required`  |  Environment ID

The ID of the environment to update, starting with `env-`. It can be obtained from the response of the create environment or list environments API.

### Body parameters

**config** `object`  |  Environment configuration

Specifies the environment runtime configuration to update.

> **Tip**

* > If the entire `config` is omitted or set to `null`, the runtime configuration of the environment is not changed.

* > Only cloud\-hosted environments support updating environment templates, resource specifications, dependency packages, prebuild scripts, environment variables, startup scripts, and artifact storage. Self\-hosted environments do not support runtime configuration changes.

* > The server validates the merged complete configuration. If the current limit is exceeded, HTTP 400 `InvalidParameter` is returned, and the error message includes the maximum allowed number of bytes.

**build_script** `string`  |  Prebuild script

`config.build_script`

A prebuild script that runs after dependency package installation is complete. It is used to download dependencies, compile projects, or generate files that can be reused by later sessions. The script content must use valid UTF\-8 encoding and cannot contain NUL characters.

> **Tip**

> If omitted or set to `null`, the original prebuild script is retained; if a string is passed in, the original value is replaced, and if an empty string `""` is passed in, it is cleared. This parameter applies only to cloud\-hosted environments.

**env** `object`  |  Environment variable

`config.env`

Environment variables used when the environment runs, passed in as string key\-value pairs consisting of variable names and variable values.

> **Tip**

* > If omitted or set to `null`, the original environment variables are retained; if an object is passed in, the existing `env` mapping is replaced as a whole; if an empty object `{}` is passed in, the environment variables are cleared.

* > It can contain up to 64 key\-value pairs. All keys and values combined cannot exceed 32 KiB. Keys cannot be empty and cannot contain `=` or NUL characters. Values cannot contain NUL characters.

**networking** `object`  |  Outbound network policy

`config.networking`

The outbound network configuration of the environment.

> **Tip**

> If omitted or set to `null`, the original outbound network policy is retained; if an object is passed in, the existing `networking` configuration is replaced as a whole.

> For cloud\-hosted environments, `networking.type` can be set to `unrestricted` or `limited`.

**type** `string` `Required`  |  Outbound network policy type

`config.networking.type`

The outbound network mode of the environment. Options: `unrestricted`, `limited`.

> **Tip**

* > `unrestricted` means outbound destinations are not restricted, but the platform's general security blocking policies still apply.

* > `limited` means access is allowed only to destinations covered by the DNS/IP rules required by the platform, domain names in `allowed_hosts`, and additional package repository domains added when `allow_package_managers` is enabled.

* > `limited` is a fail\-closed policy: if no allowed domains are configured, the sandbox can still start normally, but all external network access is blocked.

**allow_package_managers** `boolean`  |  Allow package managers

`config.networking.allow_package_managers`

Whether access to package manager repositories is allowed in limited outbound mode.

> **Tip**

> When enabled, the platform adds package manager repository domains to the `limited` outbound rules for installing `apt`, `cargo`, `gem`, `go`, `npm`, and `pip` dependencies. When disabled or omitted, dependencies declared in `packages` do not automatically allow access to external package repositories.

> If package manager repository domains are not configured on the platform side, enabling this parameter causes the request to fail.

**allowed_hosts** `string[]`  |  Host allowlist

`config.networking.allowed_hosts`

The list of external domain names that the sandbox is allowed to directly access in limited outbound mode.

> **Tip**

* > Only bare domain names or leftmost wildcard domain names are supported, such as `example.com` and `*.example.com`.

* > URLs, ports, paths, query parameters, IP addresses, and `localhost` are not supported. Middle wildcards and multi\-level wildcards are also not supported.

* > When omitted or passed as an empty array, no additional external domain names are allowed.

**packages** `object`  |  Dependency package configuration

`config.packages`

Configures dependency packages for subsequently created sessions. After the update, sessions created with this environment can use these dependencies directly without running additional installation commands.

> **Tip**

* > If omitted or set to `null`, the original dependency package configuration is retained; if an object is passed in, the existing `packages` configuration is replaced as a whole; if an empty object `{}` is passed in, all dependency package configurations are cleared.

* > To append dependencies, first read the existing configuration, merge it locally, and then pass in the complete configuration.

* > After leading and trailing whitespace is removed, each dependency package name cannot be empty, cannot contain NUL characters, and cannot start with a hyphen.

**apt** `string[]`  |  apt package

`config.packages.apt`

System packages installed through apt during the environment prebuild phase.

**cargo** `string[]`  |  Cargo packages

`config.packages.cargo`

Rust crates installed through Cargo during the environment prebuild phase.

**gem** `string[]`  |  gem packages

`config.packages.gem`

Ruby packages installed through RubyGems during the environment prebuild phase.

**go** `string[]`  |  Go packages

`config.packages.go`

Module dependencies installed through the Go toolchain during the environment prebuild phase.

**npm** `string[]`  |  npm packages

`config.packages.npm`

Node.js packages installed through npm during the environment prebuild phase.

**pip** `string[]`  |  pip package

`config.packages.pip`

Python packages installed through pip during the environment prebuild phase.

**type** `string`  |  Configuration type

`config.packages.type`

The type identifier of the dependency package configuration.

Value is fixed to `packages`.

**setup_script** `string`  |  Startup script

`config.setup_script`

An initialization script that runs during the startup phase of each session sandbox. It is used to prepare directories, short\-lived data, or runtime configuration required for this run. Initialization logic that needs to read environment variables, session files, or other runtime resources should be placed in this script.

> **Tip**

> If omitted or set to `null`, the original startup script is retained; if a string is passed in, the original value is replaced, and if an empty string `""` is passed in, it is cleared.

**spec** `string`  |  Resource specification

`config.spec`

The CPU and memory specification used by the cloud environment.`1x`, `2x`, `4x`, and `8x` correspond to 2, 4, 8, and 16 vCPUs, respectively. Each vCPU is paired with 2 GiB of memory.

> **Tip**

> If omitted or set to `null`, the original resource specification is retained; if `1x`, `2x`, `4x`, or `8x` is passed in, the original value is replaced. This parameter applies only to cloud\-hosted environments.

**template_id** `string`  |  Environment template

`config.template_id`

The base template used by the cloud environment.`base` is suitable for general tasks, and `browser` is suitable for web access and browser automation tasks.

> **Tip**

> If omitted or set to `null`, the original environment template is retained; if `base` or `browser` is passed in, the original value is replaced. This parameter applies only to cloud\-hosted environments.

**tos** `object`  |  Artifact storage configuration

`config.tos`

Specifies the persistent storage location for agent artifacts.

> **Tip**

* > If omitted or set to `null`, the original artifact storage configuration is retained.

* > If explicitly passed in, the existing `tos` configuration is replaced as a whole, and both `bucket` and `prefix` must be provided; an empty object `{}` or only one parameter will be rejected.

* > The current API does not support clearing a configured `tos`. To switch to the default ModelArk storage, create a new environment without configuring `tos`.

* > `bucket` must be 3 to 63 bytes long. It can contain only lowercase letters, digits, and hyphens, and must start and end with a lowercase letter or digit.

* > `prefix` can be up to 512 bytes long. It must be a relative directory and cannot contain backslashes, control characters, empty path segments, or `.` or `..` path segments.

* > The bucket must be in the same region as the current API access region, and the current project must have read and write permissions.

* > Updating an environment affects only sessions created afterward. Existing sessions continue to use the environment configuration saved when they were created.

**bucket** `string`  |  TOS bucket

`config.tos.bucket`

The name of the TOS bucket that stores agent artifacts.

**prefix** `string`  |  Object prefix

`config.tos.prefix`

The base directory prefix for agent artifacts in the TOS bucket. When the platform registers outputs artifacts, it uses `{prefix}outputs/{env-id}/{session-id}/` as the write prefix for the Files API.

The final object path is determined by `tos.object_key` in the Files API response and usually also includes `ark_processed/{file_id}/{file_type}/{filename}`.

**type** `string`  |  Environment type

`config.type`

The runtime mode of the environment.`cloud` means the runtime environment is provided by ModelArk, and `self_hosted` means the runtime environment is provided by the caller.

> **Tip**

> If omitted or set to `null`, the current type is retained. If provided, it must be set to `cloud` or `self_hosted`, and the server validates the complete configuration based on the merged type.

**description** `string`  |  Environment description

Used to describe the purpose of the environment, up to 300 characters. If omitted or set to `null`, the original value is retained. If set to the empty string `""`, the value is cleared.

**metadata** `object`  |  Custom metadata

Used to record the business attributes of the environment. Pass it in as string key\-value pairs to make it easier to associate and manage resources.

> **Tip**

* > If omitted, set to `null`, or set to an empty object `{}`, custom metadata is not changed.

* > **Per\-key update**: non\-empty values are used to add or overwrite entries, and empty strings are used to delete the corresponding keys. After the update, it can contain up to 16 key\-value pairs, with keys up to 64 characters long and values up to 512 characters long.

**name** `string`  |  Environment name

Used to change the environment name so that you can identify and manage the environment based on its current purpose.

> **Tip**

* > If omitted or set to `null`, the current name is retained.

* > The name must be 3 to 64 characters long. It can contain only lowercase letters, digits, periods, underscores, and hyphens, and must start with a lowercase letter or digit. The server does not automatically trim leading or trailing spaces or convert case.

* > The new name must be unique within the current project. If it is already used by another environment that has not been deleted, the server returns HTTP 409 `ResourceConflict`.

* > Changing the name does not change the environment ID or the environment associations of existing agents or sessions.

## Response parameters

**created_at** `string`  |  Creation time

The time when the environment was created, in UTC using RFC 3339 format.

**id** `string`  |  Environment ID

The environment ID is generated by the server and starts with `env-`.

**name** `string`  |  Environment name

Environment name.

**type** `string`  |  Object type

Object type. Fixed to `environment`.

**updated_at** `string`  |  Update time

The time when the environment was last updated, in UTC using RFC 3339 format.

**archived_at** `string`  |  Archive time

The time when the environment was first archived, in UTC using RFC 3339 format. If the environment has not been archived, this parameter is not returned.

**config** `object`  |  Environment configuration

The currently effective runtime configuration of the environment.

**type** `string`  |  Environment type

`config.type`

The runtime mode of the environment.`cloud` means the runtime environment is provided by ModelArk, and `self_hosted` means the runtime environment is provided by the caller.

**build_script** `string`  |  Prebuild script

`config.build_script`

The prebuild script configured for the environment. It runs after dependency packages are installed.

**env** `object`  |  Environment variable

`config.env`

Environment variables used when the environment runs, returned as string key\-value pairs.

**networking** `object`  |  Outbound network policy

`config.networking`

The currently effective outbound network configuration of the environment. For a `self_hosted` environment, the server always returns `type` fixed as `unrestricted`.

**type** `string`  |  Outbound network policy type

`config.networking.type`

The outbound network mode of the environment. Options: `unrestricted`, `limited`.

> **Tip**

* > `unrestricted` means outbound destinations are not restricted, but the platform's general security blocking policies still apply.

* > `limited` means access is allowed only to destinations covered by the DNS/IP rules required by the platform, domain names in `allowed_hosts`, and additional package repository domains added when `allow_package_managers` is enabled.

* > `limited` is a fail\-closed policy: if no allowed domains are configured, the sandbox can still start normally, but all external network access is blocked.

**allow_package_managers** `boolean`  |  Allow package managers

`config.networking.allow_package_managers`

Whether access to package manager repositories is allowed in limited outbound mode.

> **Tip**

> When enabled, the platform adds package manager repository domains to the `limited` outbound rules for installing `apt`, `cargo`, `gem`, `go`, `npm`, and `pip` dependencies. When disabled or omitted, dependencies declared in `packages` do not automatically allow access to external package repositories.

> If package manager repository domains are not configured on the platform side, enabling this parameter causes the request to fail.

**allowed_hosts** `string[]`  |  Host allowlist

`config.networking.allowed_hosts`

The list of external domain names that the sandbox is allowed to directly access in limited outbound mode.

> **Tip**

* > Only bare domain names or leftmost wildcard domain names are supported, such as `example.com` and `*.example.com`.

* > URLs, ports, paths, query parameters, IP addresses, and `localhost` are not supported. Middle wildcards and multi\-level wildcards are also not supported.

* > When omitted or passed as an empty array, no additional external domain names are allowed.

**packages** `object`  |  Dependency package configuration

`config.packages`

The dependency packages configured for the environment.

**apt** `string[]`  |  apt package

`config.packages.apt`

System packages installed through apt during the environment prebuild phase.

**cargo** `string[]`  |  Cargo packages

`config.packages.cargo`

Rust crates installed through Cargo during the environment prebuild phase.

**gem** `string[]`  |  gem packages

`config.packages.gem`

Ruby packages installed through RubyGems during the environment prebuild phase.

**go** `string[]`  |  Go packages

`config.packages.go`

Module dependencies installed through the Go toolchain during the environment prebuild phase.

**npm** `string[]`  |  npm packages

`config.packages.npm`

Node.js packages installed through npm during the environment prebuild phase.

**pip** `string[]`  |  pip package

`config.packages.pip`

Python packages installed through pip during the environment prebuild phase.

**type** `string`  |  Configuration type

`config.packages.type`

The type identifier of the dependency package configuration.

Value is fixed to `packages`.

**setup_script** `string`  |  Startup script

`config.setup_script`

The startup script configured for the environment. It runs when each session sandbox starts.

**spec** `string`  |  Resource specification

`config.spec`

The cloud sandbox resource specification currently in effect for the environment.`1x`, `2x`, `4x`, and `8x` correspond to 2, 4, 8, and 16 vCPUs, respectively. Each vCPU is paired with 2 GiB of memory.

**template_id** `string`  |  Environment template

`config.template_id`

The cloud environment template currently used by the environment.`base` means the base template, and `browser` means the browser task template.

**tos** `object`  |  Artifact storage configuration

`config.tos`

Persistent storage location for agent artifacts.

**bucket** `string`  |  TOS bucket

`config.tos.bucket`

The name of the TOS bucket that stores agent artifacts.

**prefix** `string`  |  Object prefix

`config.tos.prefix`

The base directory prefix for agent artifacts in the TOS bucket. When the platform registers outputs artifacts, it uses `{prefix}outputs/{env-id}/{session-id}/` as the write prefix for the Files API.

The final object path is determined by `tos.object_key` in the Files API response and usually also includes `ark_processed/{file_id}/{file_type}/{filename}`.

**description** `string`  |  Environment description

A description of the environment's purpose.

**metadata** `object`  |  Custom metadata

The custom business attributes of the environment, returned as string key\-value pairs.
