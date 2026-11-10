# Delete agent

`DELETE https://ark.ap-southeast.bytepluses.com/api/v3/agents/{agent_id}`

Deletes the specified agent. After deletion, the agent and its historical versions can no longer be queried or restored through the API.

* When there is an associated session whose `status` is `running`, the delete request returns 400 `InvalidAction`; associated sessions in other states do not prevent deletion.

* Agents with a value for `archived_at` (archived) can still be deleted; after deletion, archive query capability is no longer retained.

* If the agent is deleted again, does not exist, or the caller has no access, 404 `InvalidAgent` is returned.

## Authentication

This API supports the following authentication method(s). For details, see [Base URL and authentication](base-url-and-authentication.md).

* API Key authentication. Go to the [API Key Management](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) page. Obtain a long\-lived API key.

---

## Request parameters

### Path parameters

**agent_id** `string` `Required`  |  Agent ID

The ID of the agent to delete, which starts with `agent-`. You can obtain this ID from the response of the create agent API, or from the agent list page in the console.

## Response parameters

**id** `string`  |  Agent ID

The ID of the deleted agent, which starts with `agent-` and is the same as `agent_id` in the request.
