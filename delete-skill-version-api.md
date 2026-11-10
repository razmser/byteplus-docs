# Delete specified version of skill

`DELETE https://ark.ap-southeast.bytepluses.com/api/v3/skills/{skill_id}/versions/{version}`

Deletes the specified version of a custom skill in the current account without deleting the skill itself.

If a skill has multiple versions, you must first delete all non\-latest versions before you can delete the last remaining latest version. Deleting the latest version while other versions still exist returns HTTP 400 `InvalidAction`.

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

Specifies the skill version to delete.



**version** `string` `Required`  |  Version number

Specifies the version to delete.


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**id** `string`  |  Version ID

The ID of the deleted version, used to identify the version.



**type** `string`  |  Deletion result type

Identifies the resource type of the successful deletion response, fixed as `skill_version_deleted`.
