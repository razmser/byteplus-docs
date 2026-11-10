# Create environment

`POST https://ark.ap-southeast.bytepluses.com/api/v3/environments`

Create an environment to configure the runtime environment for managed agents. After it is created, you can reference the environment by the returned environment ID when creating a session.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Body parameters

**config** `object` `Required`  |  Environment configuration

Defines the runtime mode and startup configuration of the environment.

> **Tip**

* > When creating a `cloud` environment, you must provide `networking`, and `networking.type` can currently only be set to `unrestricted`.

* > When creating a `self_hosted` environment, you can provide only `type`.

**type** `string` `Required`  |  Environment type

`config.type`

The runtime mode of the environment.`cloud` means the runtime environment is provided by ModelArk, and `self_hosted` means the runtime environment is provided by the caller.

**env** `object`  |  Environment variable

`config.env`

Environment variables used when the environment runs, passed in as string key\-value pairs consisting of variable names and variable values.

> **Tip**

> It can contain up to 64 key\-value pairs. All keys and values combined cannot exceed 32 KiB. Keys cannot be empty and cannot contain `=` or NUL characters. Values cannot contain NUL characters.

**networking** `object`  |  Outbound network policy

`config.networking`

The outbound network configuration of the environment.

**type** `string` `Required`  |  Outbound network policy type

`config.networking.type`

The outbound network mode of the environment.`unrestricted` means outbound targets are not restricted.

**packages** `object`  |  Dependency package configuration

`config.packages`

Dependency packages that are preinstalled when the environment starts.

> **Tip**

> After leading and trailing whitespace is removed, each dependency package name cannot be empty, cannot contain NUL characters, and cannot start with a hyphen.

**apt** `string[]`  |  apt package

`config.packages.apt`

System packages preinstalled through apt when the environment starts.

**cargo** `string[]`  |  Cargo packages

`config.packages.cargo`

Rust crates preinstalled through Cargo when the environment starts.

**gem** `string[]`  |  gem packages

`config.packages.gem`

Ruby packages preinstalled through RubyGems when the environment starts.

**go** `string[]`  |  Go packages

`config.packages.go`

Module dependencies preinstalled through the Go toolchain when the environment starts.

**npm** `string[]`  |  npm packages

`config.packages.npm`

Node.js packages preinstalled through npm when the environment starts.

**pip** `string[]`  |  pip package

`config.packages.pip`

Python packages preinstalled through pip when the environment starts.

**type** `string`  |  Configuration type

`config.packages.type`

The type identifier of the dependency package configuration.

Value is fixed to `packages`.

**setup_script** `string`  |  Startup script

`config.setup_script`

The initialization script executed when the environment starts.

**tos** `object`  |  Artifact storage configuration

`config.tos`

Specifies the persistent storage location for agent artifacts.

> **Tip**

* > If `tos` is omitted, agent artifacts are stored in ModelArk default storage. Files in the default storage are retained only temporarily; download them before the platform TTL expires. For long\-term retention, configure your own TOS bucket and manage the object lifecycle yourself.

* > When `tos` is configured, both `bucket` and `prefix` must be provided.

* > `bucket` must be 3 to 63 bytes long. It can contain only lowercase letters, digits, and hyphens, and must start and end with a lowercase letter or digit.

* > `prefix` can be up to 512 bytes long. It must be a relative directory and cannot contain backslashes, control characters, empty path segments, or `.` or `..` path segments.

* > Before saving the configuration, the server checks that the bucket and prefix already exist, that the bucket is in the same region as the current API access region, and that the role used by ModelArk to access the bucket has read and write permissions.

* > After you configure `tos`, when the platform registers artifacts from the outputs directory, it uses `{prefix}outputs/{env-id}/{session-id}/` as the write prefix for the Files API. The final object path is determined by `tos.object_key` in the Files API response and usually also includes `ark_processed/{file_id}/{file_type}/{filename}`.

* > When you call the Files API directly and pass in `tos[prefix]`, no environment ID or session ID is appended, but the Files API still generates a server\-side processing directory under that prefix. This rule also does not represent the sandbox mount directory for `type: tos` in `resources`.

**bucket** `string`  |  TOS bucket

`config.tos.bucket`

The name of the TOS bucket that stores agent artifacts.

**prefix** `string`  |  Object prefix

`config.tos.prefix`

The base directory prefix for agent artifacts in the TOS bucket. When the platform registers outputs artifacts, it uses `{prefix}outputs/{env-id}/{session-id}/` as the write prefix for the Files API.

The final object path is determined by `tos.object_key` in the Files API response and usually also includes `ark_processed/{file_id}/{file_type}/{filename}`.

**name** `string` `Required`  |  Environment name

The name of the environment, used to identify and manage the environment in the current project.

> **Tip**

> The name must be 3 to 64 characters long. It can contain only lowercase letters, digits, periods, underscores, and hyphens, and must start with a lowercase letter or digit. Names must be unique within the same project.

**description** `string`  |  Environment description

Used to describe the purpose of the environment, up to 300 characters.

**metadata** `object`  |  Custom metadata

Used to record the business attributes of the environment. Pass it in as string key\-value pairs to make it easier to associate and manage resources.

> **Tip**

> It can contain up to 16 key\-value pairs. Keys can be up to 64 characters long, and values can be up to 512 characters long.

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

**env** `object`  |  Environment variable

`config.env`

Environment variables used when the environment runs, returned as string key\-value pairs.

**networking** `object`  |  Outbound network policy

`config.networking`

The currently effective outbound network configuration of the environment. For a `self_hosted` environment, the server always returns `type` fixed as `unrestricted`.

**type** `string`  |  Outbound network policy type

`config.networking.type`

The outbound network mode of the environment.`unrestricted` means outbound targets are not restricted.

**packages** `object`  |  Dependency package configuration

`config.packages`

Dependency packages that are preinstalled when the environment starts.

**apt** `string[]`  |  apt package

`config.packages.apt`

System packages preinstalled through apt when the environment starts.

**cargo** `string[]`  |  Cargo packages

`config.packages.cargo`

Rust crates preinstalled through Cargo when the environment starts.

**gem** `string[]`  |  gem packages

`config.packages.gem`

Ruby packages preinstalled through RubyGems when the environment starts.

**go** `string[]`  |  Go packages

`config.packages.go`

Module dependencies preinstalled through the Go toolchain when the environment starts.

**npm** `string[]`  |  npm packages

`config.packages.npm`

Node.js packages preinstalled through npm when the environment starts.

**pip** `string[]`  |  pip package

`config.packages.pip`

Python packages preinstalled through pip when the environment starts.

**type** `string`  |  Configuration type

`config.packages.type`

The type identifier of the dependency package configuration.

Value is fixed to `packages`.

**setup_script** `string`  |  Startup script

`config.setup_script`

The initialization script executed when the environment starts.

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
