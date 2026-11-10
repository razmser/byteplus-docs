# Set skill content protection

`POST https://ark.ap-southeast.bytepluses.com/api/v3/skills/{skill_id}`

Set whether to enable content protection for a custom skill. After content protection is enabled, when a skill runs, the ModelArk platform injects protection instructions to reduce the risk of the skill content being output directly.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**skill_id** `string` `Required`  |  Skill ID

Specifies the skill to update.

### Body parameters

**protection_enabled** `boolean`  |  Content protection switch

Updates the content protection setting for the skill.

* **Omitted or set to ** **`null`**: the content protection switch is not changed, and `updated_at` remains unchanged.

* **Set to the current value**: the content protection switch is not changed, and `updated_at` remains unchanged.

* **Pass a different ** **`true`** ** or ** **`false`** ** value**: updates the content protection setting and `updated_at`.

## Response parameters

**created_at** `integer`  |  Creation time

The skill creation time, as a Unix timestamp in seconds.

**display_title** `string`  |  Display name

Display name of the skill. If it is not configured, the server returns the `name` from `SKILL.md`.

**id** `string`  |  Skill ID

The unique identifier of the updated skill, starting with `skill-`.

**latest_version** `string`  |  Latest version

The latest version number of the skill.

**object** `string`  |  Resource type.

Used to identify the resource type of the response object, fixed as `skill`.

**protection_enabled** `boolean`  |  Content protection switch

Indicates whether content protection is enabled for the skill.`true` indicates that it is enabled, and `false` indicates that it is not enabled.

**source** `string`  |  Source of the skill

The skill source, fixed as `custom`. This value indicates a custom skill that has been uploaded or imported.

**updated_at** `integer`  |  Skill update time.

The last update time of the skill, as a Unix timestamp in seconds.

**description** `string`  |  Skill description

The skill description parsed by the ModelArk platform from `SKILL.md`.

**name** `string`  |  Skill name

The skill name defined in `SKILL.md`.
