# Delete environment

`DELETE https://ark.ap-southeast.bytepluses.com/api/v3/environments/{environment_id}`

Permanently deletes the specified environment in the current project. The environment can be deleted only when it is no longer associated with any undeleted sessions; sessions whose `status` is `terminated` or whose `archived_at` has a value must still be deleted first.

> **Deletion conditions**

> As long as the environment is still associated with undeleted sessions, the server returns HTTP 400 `InvalidAction`. Sessions whose `status` is `terminated` or whose `archived_at` has a value still count as associated; delete all associated sessions before deleting the environment.

> The delete operation cannot be undone. When the environment does not exist, has been deleted, or does not belong to the current project, the API returns HTTP 404 `InvalidEnvironment.NotFound`.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**environment_id** `string` `Required`  |  Environment ID

The ID of the environment to delete, starting with `env-`. It can be obtained from the response of the create environment or list environments API.

## Response parameters

**id** `string`  |  Environment ID

The deleted environment ID starts with `env-` and is the same as the value in the path parameter.

**type** `string`  |  Object type

The object type is fixed as `environment_deleted`.
