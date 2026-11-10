# Delete skill

`DELETE https://ark.ap-southeast.bytepluses.com/api/v3/skills/{skill_id}`

Delete the specified custom skill. Skills and skill versions are independent resources; deleting the last version does not delete the skill.

Before deleting a skill, handle the following in this order:


1. Update the agents that reference the skill and remove the corresponding skill configuration. Deleting a skill does not automatically remove references; if they are not removed, sessions subsequently created by the related agents may fail to run.

2. Delete all versions of the skill. If there are multiple versions, delete the non\-latest versions first, and then delete the last remaining latest version.

3. Call this API to delete the skill.


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

Specifies the skill to delete.


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**id** `string`  |  Skill ID

Identifies the deleted skill.



**type** `string`  |  Deletion result type

Identifies the resource type of the successful deletion response, fixed as `skill_deleted`.
