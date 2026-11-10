# Scan GitHub skills

`POST https://ark.ap-southeast.bytepluses.com/api/v3/skills/github/scan`

Scans `SKILL.md` in a GitHub repository and returns skill candidates and validation results. Candidate skills with `valid` set to `true` in the response can be imported through the import GitHub skill API.

<span id="authentication"></span>
## Authentication

This API supports the following authentication methods. For details, see [Base URL and authentication](base-url-and-authentication.md).


* API Key authentication. Go to the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.



---



<span id="request-parameters"></span>
## Request parameters

<span id="body-parameters"></span>
### Body parameters


**input** `string` `Required`  |  Repository URL

Specifies the GitHub repository to scan. It supports passing either a full repository URL or a repository identifier in the `owner/repo` format.


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**candidates** `object[]`  |  Skill candidates

Contains the skill candidates found in this scan.


**name** `string`  |  Skill name

`candidates.name`

Skill names parsed by the ModelArk platform from the candidate `SKILL.md`.



**path** `string`  |  Skill path

`candidates.path`

The path of the directory where `SKILL.md` is located, relative to the repository root directory. If the repository root directory itself is the skill directory, this value is an empty string.



**valid** `boolean`  |  Validation result

`candidates.valid`

Indicates whether the candidate skill passed `SKILL.md` format validation.`true` indicates that validation passed and the skill can be imported.`false` indicates that validation failed, with the specific reason returned by the `error` parameter.



**error** `string`  |  Validation error

`candidates.error`

When the candidate skill fails validation, the specific error reason is returned.



**existing_skill_id** `string`  |  Existing skill ID

`candidates.existing_skill_id`

When the current account has already imported a skill from the same repository path, the ID of the corresponding skill is returned.



**has_update** `boolean`  |  Has update

`candidates.has_update`

Indicates whether an imported skill has an update. When the commit corresponding to the latest version differs from the commit scanned in this request, the server returns `true`. If the two commits are the same, this parameter is not returned.




**repo** `object`  |  Repository information

Contains the repository and commit information for this scan.


**commit_sha** `string`  |  Commit SHA

`repo.commit_sha`

Indicates the Git commit SHA for this scan. When calling the import API, you must pass this value as is.



**html_url** `string`  |  Repository URL

`repo.html_url`

Provides the web URL of the GitHub repository.



**name** `string`  |  Repository name

`repo.name`

The GitHub repository name.



**owner** `string`  |  Repository owner

`repo.owner`

The GitHub repository owner.
