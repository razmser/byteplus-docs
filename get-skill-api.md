# Get skill

`GET https://ark.ap-southeast.bytepluses.com/api/v3/skills/{skill_id}`

Retrieves detailed information about the specified skill.

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

Specifies the skill to query.


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**created_at** `integer`  |  Creation time

The skill creation time, as a Unix timestamp in seconds.



**display_title** `string`  |  Display name

Display name of the skill. If it is not configured, the server returns the `name` from `SKILL.md`.



**id** `string`  |  Skill ID

The unique identifier of the skill. This ID is required when updating or deleting a skill, or managing skill versions.


* IDs of custom skills and ModelArk preset skills start with `skill-`.



**latest_version** `string`  |  Latest version

The latest version number of the skill.



**object** `string`  |  Resource type.

Used to identify the resource type of the response object, fixed as `skill`.



**protection_enabled** `boolean`  |  Content protection switch

Indicates whether content protection is enabled for the skill.`true` indicates that it is enabled, and `false` indicates that it is not enabled.



**source** `string`  |  Source of the skill

This parameter indicates the skill source:


* `custom` indicates a custom skill which is uploaded or imported.

* `ark` indicates a preset skill on ModelArk.



**updated_at** `integer`  |  Skill update time.

The last update time of the skill, as a Unix timestamp in seconds.



**description** `string`  |  Skill description

The skill description parsed by the ModelArk platform from `SKILL.md`.



**name** `string`  |  Skill name

The skill name defined in `SKILL.md`.
