# Download skill of specified version

`GET https://ark.ap-southeast.bytepluses.com/api/v3/skills/{skill_id}/versions/{version}/content`

Get the download URL for the skill ZIP package of the specified version. The server returns an HTTP 302 response and provides a download URL valid for 1 hour in the `Location` response header.

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

Specifies the skill for which to download version content.



**version** `string` `Required`  |  Version number

Specifies the version number to download.


&nbsp;

<span id="response-parameters"></span>
## Response parameters


**Location** `string`  |  Download URL

The download URL for the skill ZIP package of the specified version, valid for 1 hour.
