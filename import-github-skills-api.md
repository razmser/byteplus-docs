# Import GitHub skills

`POST https://ark.ap-southeast.bytepluses.com/api/v3/skills/github/import`

Before importing GitHub skills, first call the scan GitHub skills API to obtain the skills available for import and the repository information for this scan.

After comparing the selected skills with the existing skills, the server processes them as follows:


* If a selected skill has not been imported, the server creates the skill.

* If a selected skill has been imported, but the commit from this scan is different from the commit corresponding to the latest version, the server creates a new version.

* If a selected skill has been imported and the two commits are the same, the server skips the item.


A successful request does not mean that every item was imported successfully. After the call, check `items[].status` to get the import result for each item.

<span id="authentication"></span>
## Authentication

This API supports the following authentication methods. For details, see [Base URL and authentication](base-url-and-authentication.md).


* API Key authentication. Go to the [API Key management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.



---



<span id="request-parameters"></span>
## Request parameters

<span id="body-parameters"></span>
### Body parameters


**repo** `object` `Required`  |  Repository information

Pass in the repository information returned by the scan API.`commit_sha` must be the original value returned by the scan API.


**commit_sha** `string` `Required`  |  Commit SHA

`repo.commit_sha`

The Git commit SHA returned by the scan API. This value must be passed in the import request as is.



**html_url** `string` `Required`  |  Repository URL

`repo.html_url`

Provides the web URL of the GitHub repository.



**name** `string` `Required`  |  Repository name

`repo.name`

The GitHub repository name.



**owner** `string` `Required`  |  Repository owner

`repo.owner`

The GitHub repository owner.




**selected** `object[]` `Required`  |  Skills to import

Specifies the skill paths to import. Pass in at least one item.


**path** `string` `Required`  |  Skill path

`selected.path`

Specifies the skill to import. Pass in `candidates[].path` returned by the scan API, which is the path of the directory containing `SKILL.md` relative to the repository root directory. When importing a skill from the repository root directory, pass in an empty string.



**protection_enabled** `boolean` `Default false`  |  Content protection switch

`selected.protection_enabled`

Controls whether the server enables content protection when creating a new skill.

\*`true`: When a skill runs, the ModelArk platform injects protection instructions to reduce the risk of the skill content being output directly.


* `false`: The ModelArk platform does not enable content protection.


This parameter takes effect only when the selected path has not been imported and the server creates a new skill. If the selected path has already been imported, the content protection settings of the existing skill are not modified.



&nbsp;

<span id="response-parameters"></span>
## Response parameters


**items** `object[]`  |  Import results

Returns the import result for each selected skill. A successful request does not mean that every item was imported successfully. After the call, check each `items[].status`.


**path** `string`  |  Skill path

`items.path`

The `selected[].path` corresponding to this import result.



**status** `string`  |  Import status

`items.status`

This parameter indicates the import status:


* `ok` indicates that the server has created a new skill.

* `updated` indicates that the server has created a new version for an existing skill.

* `skipped_duplicate` indicates that the current version of the existing skill is the same as the scanned commit, so the server did not import it again.

* `failed` indicates that the skill import failed.



**error_code** `string`  |  Error code

`items.error_code`

The error code returned when the import fails.



**error_message** `string`  |  Error information

`items.error_message`

The error message returned when the import fails.



**skill_id** `string`  |  Skill ID

`items.skill_id`

When the server creates a skill, updates a skill, or skips a duplicate item, it returns the ID of the corresponding skill.
