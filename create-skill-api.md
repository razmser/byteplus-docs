# Create skill

`POST https://ark.ap-southeast.bytepluses.com/api/v3/skills`

Upload skill files and create a custom skill.

<span id="authentication"></span>
## Authentication

This API supports the following authentication methods. For details, see [Base URL and authentication](base-url-and-authentication.md).


* API Key authentication. Go to the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.



---



<span id="request-parameters"></span>
## Request parameters

<span id="body-parameters"></span>
### Body parameters


**display_title** `string`  |  Display name

Used to set the display name of the skill. If omitted, the server uses the `name` in `SKILL.md`.



**files** `file[]`  |  Skill files

Use the `files` parameter name to upload skill files.`files[]` is a compatible parameter name for `files`. Both refer to the same group of skill files.

At least one of `files` and `files[]` must be passed. You can use either parameter name. If both are passed, the server merges and processes the files.

This parameter supports the following upload methods:


* A single ZIP file.

* A single `SKILL.md` file.

* Pass this parameter repeatedly to upload multiple files that preserve relative paths.


The uploaded content must meet the following limits:


* Each request can upload up to 500 files, and the total original size of all uploaded files must not exceed 30 MiB.

* When uploading a ZIP file, each extracted file must not exceed 30 MiB, and the total size of all files must not exceed 120 MiB.

* A ZIP file or multi\-file directory can contain only one top\-level directory, and that directory must directly contain exactly one `SKILL.md`.

* The YAML frontmatter of `SKILL.md` must contain non\-empty `name` and `description` values.

* After leading and trailing whitespace is removed, `name` must be 1–64 characters long and can contain only lowercase English letters, digits, and hyphens (`-`). It cannot start or end with a hyphen, or contain consecutive hyphens.



**files[]**  `file[]`  |  Skill files

Compatible parameter name for `files`. The upload methods and validation rules are the same as `files`.

When passing `files[]` repeatedly, each file's `filename` must preserve the path relative to the skill directory. If both parameters are passed, the server merges and processes the files.



**protection_enabled** `boolean` `Default false`  |  Content protection switch

Used to control whether content protection is enabled for the skill.


* `true`: The ModelArk platform injects protection instructions when running the skill to reduce the risk of skill content being output directly.

* `false`: The ModelArk platform does not enable this protection.


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**created_at** `integer`  |  Creation time

The skill creation time, as a Unix timestamp in seconds.



**display_title** `string`  |  Display name

Display name of the skill. If it is not configured, the server returns the `name` from `SKILL.md`.



**id** `string`  |  Skill ID

The unique identifier of the created skill, starting with `skill-`. This ID is required for subsequent skill queries, updates, deletions, and management of different skill versions.



**latest_version** `string`  |  Latest version

The version number of the created skill, fixed as `1`.



**object** `string`  |  Resource type.

Used to identify the resource type of the response object, fixed as `skill`.



**protection_enabled** `boolean`  |  Content protection switch

Indicates whether content protection is enabled for the skill.`true` indicates that it is enabled, and `false` indicates that it is not enabled.



**source** `string`  |  Source of the skill

The skill source, fixed as `custom`. This value indicates a custom skill uploaded by the current account.



**updated_at** `integer`  |  Skill update time.

The last update time of the skill, as a Unix timestamp in seconds. When creating a skill, this value is the same as `created_at`.



**description** `string`  |  Skill description

The skill description parsed by the ModelArk platform from `SKILL.md`.



**name** `string`  |  Skill name

The skill name defined in `SKILL.md`.
