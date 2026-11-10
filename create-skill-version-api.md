# Create new skill version

`POST https://ark.ap-southeast.bytepluses.com/api/v3/skills/{skill_id}/versions`

Upload files for an existing custom skill and create a new version. After creation succeeds, the skill's `latest_version`, `name`, and `description` are updated with the new version's content.

<span id="authentication"></span>
## Authentication

This API supports the following authentication methods. For details, see [Base URL and authentication](base-url-and-authentication.md).


* API Key authentication. Go to the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.



---



<span id="request-parameters"></span>
## Request parameters

<span id="path-parameters"></span>
### Path parameters


**skill_id** `string` `Required`  |  Skill ID

Specifies the skill for which to create a new version.


&nbsp;

<span id="body-parameters"></span>
### Body parameters


**display_title** `string`  |  Display name

Sets the display name of the new version. If omitted, the server uses the `name` in `SKILL.md`.



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


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**created_at** `string`  |  Creation time

The creation time of the version in RFC 3339 format.



**display_title** `string`  |  Display name

The display name of this version.



**id** `string`  |  Version ID

The unique identifier of the created new version, starting with `skillver-`.



**skill_id** `string`  |  Skill ID

The ID of the skill to which this version belongs.



**type** `string`  |  Resource type.

Identifies the resource type of the response object, fixed as `skill_version`.



**version** `string`  |  Version number

The version number of the created version. The server increments the current maximum version number by `1` and returns the new version number as a string.



**description** `string`  |  Skill description

The skill description parsed by the ModelArk platform from `SKILL.md` of this version.
