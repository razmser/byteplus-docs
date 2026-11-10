# Retrieve skill of specified version

`GET https://ark.ap-southeast.bytepluses.com/api/v3/skills/{skill_id}/versions/{version}`

Queries detailed information about a specified skill at a specific version, without returning the skill file content.

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

Specifies the skill to query its versions.



**version** `string` `Required`  |  Version number

Specifies the version number to query.


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**created_at** `string`  |  Creation time

The creation time of the version in RFC 3339 format.



**display_title** `string`  |  Display name

The display name of this version.



**id** `string`  |  Version ID

The unique identifier of this version, starting with `skillver-`.



**skill_id** `string`  |  Skill ID

The ID of the skill to which this version belongs.



**type** `string`  |  Resource type.

Identifies the resource type of the response object, fixed as `skill_version`.



**version** `string`  |  Version number

The version number of the skill.



**description** `string`  |  Skill description

The skill description parsed by the ModelArk platform from `SKILL.md` of this version.
