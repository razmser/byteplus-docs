# Update environment

`POST https://ark.ap-southeast.bytepluses.com/api/v3/environments/{environment_id}`

Update the description, runtime configuration, or custom metadata of the specified environment.

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

* > `self_hosted` environments do not support modifying the runtime configuration.

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

**type** `string` `Required`  |  Outbound network policy type

`config.networking.type`

The outbound network mode of the environment.`unrestricted` means outbound targets are not restricted.

**packages** `object`  |  Dependency package configuration

`config.packages`

Dependency packages that are preinstalled when the environment starts.

> **Tip**

* > If omitted or set to `null`, the original dependency package configuration is retained; if an object is passed in, the existing `packages` configuration is replaced as a whole; if an empty object `{}` is passed in, all dependency package configurations are cleared.

* > To append dependencies, first read the existing configuration, merge it locally, and then pass in the complete configuration.

* > After leading and trailing whitespace is removed, each dependency package name cannot be empty, cannot contain NUL characters, and cannot start with a hyphen.

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

> **Tip**

> If omitted or set to `null`, the original startup script is retained; if a string is passed in, the original value is replaced, and if an empty string `""` is passed in, it is cleared.

**tos** `object`  |  Artifact storage configuration

`config.tos`

Specifies the persistent storage location for agent artifacts.

> **Tip**

* > If omitted or set to `null`, the original artifact storage configuration is retained.

* > If explicitly passed in, the existing `tos` configuration is replaced as a whole, and both `bucket` and `prefix` must be provided; an empty object `{}` or only one parameter will be rejected.

* > `bucket` must be 3 to 63 bytes long. It can contain only lowercase letters, digits, and hyphens, and must start and end with a lowercase letter or digit.

* > `prefix` can be up to 512 bytes long. It must be a relative directory and cannot contain backslashes, control characters, empty path segments, or `.` or `..` path segments.

* > Before saving the configuration, the server checks that the bucket and prefix already exist, that the bucket is in the same region as the current API access region, and that the role used by ModelArk to access the bucket has read and write permissions.

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

Environment name. This parameter is used only for compatibility to pass back the current value; the environment name cannot be changed after creation, and the server returns HTTP 409 `Conflict` if a different name is passed in.

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
