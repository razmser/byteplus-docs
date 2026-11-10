# Agent

ModelArk Managed Agents is a fully managed agent service launched by ModelArk, providing an out\-of\-the\-box agent experience. An agent, the basic component of ModelArk Managed Agents, is a configuration template that includes basic information, system prompt, and extended capabilities such as skills, tools, and MCP. It can be reused by any session and supports version management. This document explains how to define an agent.

## Agent definition parameters

For the complete parameter definitions of an agent, including name, model, system prompt, skills, tools, MCP, multi\-agent collaboration, context compression, metadata, and more, see [Create agent](create-agent-api.md).

## Prerequisites

1. Get the API keyThe API key is authentication information for calling ModelArk models and services.

   Go to the [API keys page](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) and create your API key.

2. Install `curl` and `jq`, and configure environment variables. The API key is sensitive information. If it is accidentally leaked, it may cause financial loss or security risks. Therefore, do not write the API key in plaintext in your code. Configure it in environment variables instead.

   Replace `your_api_key_here` in the following command with your API key, and run the command in the terminal to configure the API key in an environment variable. For details, see [Environment variable configuration guide](environment-variable-configuration-guide.md).

   **macOS**

```Bash
   export ARK_API_KEY="your_api_key_here"
   ```

   **Linux**

```Bash
   export ARK_API_KEY="your_api_key_here"
   ```

   **Windows_CMD**

```CMD
   setx ARK_API_KEY "your_api_key_here"
   ```

   **Windows_PowerShell**

```PowerShell
   $env:ARK_API_KEY = "your_api_key_here"
   ```

3. Activate the Managed Agents service.

   Go to the [Managed Agents activation page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?advancedActiveKey=manageAgent) to activate the service.

4. Activate the model service.

   Go to the [Model activation page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement) to activate the model service.

## Create an agent

After you create an agent, the API returns a permanent agent ID and the initial version number `1`. When you create a session later, you can directly reference this agent ID.

A single account can create up to 1000 agents.

The following example creates a trending news agent with a built\-in toolset and a custom skill. The example explicitly configures the enabled state and permission policy for the built\-in tool, and saves the agent ID and current version from the response to environment variables:

```Bash
AGENT_RESPONSE=$(curl -sS --fail-with-body https://ark.ap-southeast.bytepluses.com/api/v3/agents \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "NewsAgent01",
    "model": {
      "id": "dola-seed-2-1-turbo-260628",
      "speed": "standard"
    },
    "description": "A helper that summarizes trending news into images.",
    "system": "You are a trending news search and summary assistant. You can summarize the top 10 trending news items each day as a summary image.",
    "skills": [
      {
        "type": "custom",
        "skill_id": "skill-20260812080348-****"
      }
    ],
    "tools": [
      {
        "type": "agent_toolset_20260916",
        "default_config": {
          "enabled": true,
          "permission_policy": {
            "type": "always_allow"
          }
        }
      }
    ]
  }') || exit 1

jq . <<<"$AGENT_RESPONSE" || exit 1
AGENT_ID=$(jq -er '.id' <<<"$AGENT_RESPONSE") || exit 1
AGENT_VERSION=$(jq -er '.version' <<<"$AGENT_RESPONSE") || exit 1
export AGENT_ID AGENT_VERSION
printf 'Agent ID: %s\nAgent version: %s\n' "$AGENT_ID" "$AGENT_VERSION"
```

Here, `skill-20260812080348-****` indicates the `skill_id` obtained after you upload a custom skill.

Example response:

```Plain Text
{
  "id": "agent-20260812081435-*****",
  "type": "agent",
  "name": "NewsAgent01",
  "description": "A helper that summarizes trending news into images.",
  "version": 1,
  "model": {
    "id": "dola-seed-2-1-turbo-260628",
    "speed": "standard"
  },
  "system": "You are a trending news search and summary assistant. You can summarize the top 10 trending news items each day as a summary image.",
  "tools": [
    {
      "type": "agent_toolset_20260916",
      "default_config": {
        "enabled": true,
        "permission_policy": {
          "type": "always_allow"
        }
      }
    }
  ],
  "skills": [
    {
      "type": "custom",
      "skill_id": "skill-20260812080348-****"
    }
  ],
  "created_at": "2026-08-12T08:14:35Z",
  "updated_at": "2026-08-12T08:14:35Z"
}
```

## Update an agent and its versions

An agent is a versioned resource. Each time you update the configuration, you must explicitly pass the current version number. If the version number does not match, the update fails. After the update succeeds, the system generates a new version.

The following example first queries the current version of the agent, and then submits an update. After you run the creation example on this page, you can directly reuse `AGENT_ID`. When updating another agent, first write its ID to this environment variable.

```Bash
: "${AGENT_ID:?Run the create example first or set AGENT_ID}"

CURRENT_AGENT=$(curl -sS --fail-with-body \
  "https://ark.ap-southeast.bytepluses.com/api/v3/agents/$AGENT_ID" \
  -H "Authorization: Bearer $ARK_API_KEY") || exit 1
AGENT_VERSION=$(jq -er '.version' <<<"$CURRENT_AGENT") || exit 1

UPDATE_RESPONSE=$(curl -sS --fail-with-body -X PUT \
  "https://ark.ap-southeast.bytepluses.com/api/v3/agents/$AGENT_ID" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d @- <<EOF
{
    "version": $AGENT_VERSION,
    "system": "You are a trending news search and summary assistant. You can summarize the top 10 trending news items each day as a summary image and audio.",
    "skills": [
      {
        "type": "custom",
        "skill_id": "skill-20260812075208-****"
      },
      {
        "type": "custom",
        "skill_id": "skill-20260729084811-****"
      }
    ]
}
EOF
) || exit 1

jq . <<<"$UPDATE_RESPONSE" || exit 1
AGENT_VERSION=$(jq -er '.version' <<<"$UPDATE_RESPONSE") || exit 1
export AGENT_VERSION
printf 'Updated Agent version: %s\n' "$AGENT_VERSION"
```

Example response:

```Plain Text
{
  "id": "agent-20260812081435-*****",
  "type": "agent",
  "name": "NewsAgent01",
  "description": "A helper that summarizes trending news into images.",
  "version": 2,
  "model": {
    "id": "dola-seed-2-1-turbo-260628",
    "speed": "standard"
  },
  "system": "You are a trending news search and summary assistant. You can summarize the top 10 trending news items each day as a summary image and audio.",
  "tools": [
    {
      "type": "agent_toolset_20260916",
      "default_config": {
        "enabled": true,
        "permission_policy": {
          "type": "always_allow"
        }
      }
    }
  ],
  "skills": [
    {
      "type": "custom",
      "skill_id": "skill-20260812075208-****"
    },
    {
      "type": "custom",
      "skill_id": "skill-20260729084811-****"
    }
  ],
  "created_at": "2026-08-12T08:14:35Z",
  "updated_at": "2026-08-12T08:25:21Z"
}
```

When updating an agent, we recommend that you follow these rules:

* The update request must include the current `version`.

* A new agent version is generated after the configuration is modified.

* When the update API returns HTTP 400 `InvalidParameter` and the error message contains `version: conflict`, it means another request has already updated the agent. Query the agent again, merge the latest configuration, and then submit the request. Do not only increment the local version number.

* If some parameters remain unchanged, you can pass only the parameters that need to be modified. For example, when you update only `system`, you do not need to pass `tools` again.

* `mcp_servers`, `tools`, and `skills` are all treated as a whole when replaced. Once one of these arrays is passed in the request body, the server overwrites the corresponding current configuration with the entire array and does not append it to the existing configuration.

* If you only want to add or adjust one item, first query the latest configuration of the agent, locally merge the entries that need to be retained, and then write back the complete array together with the current `version`.

|Value |How `mcp_servers`, `tools`, and `skills` are handled |Supported use cases |
|---|---|---|
|Omitted or `null` |Retains the current configuration of the corresponding parameter. |Only updates other parameters. |
|Empty array `[]` |Clears the saved configuration of the corresponding parameter. |Explicitly removes all MCP servers, tools, or skills. |
|Non\-empty array |Replaces the current configuration with the entire array in the request. |Read and merge all entries that need to be retained first. |

> **Note**

> To limit the built\-in tool scope of the main agent, explicitly configure the built\-in toolset and disable the default scope. Do not do this by clearing `tools`. For details, see [Check default tool scope](tools.md#tool-defaults-and-boundaries).

## Archive or delete an agent

When you no longer want new sessions to use an agent but still need to retain its configuration and historical versions, call [Archive agent](archive-agent-api.md):

```Bash
curl -sS --fail-with-body \
  "https://ark.ap-southeast.bytepluses.com/api/v3/agents/<AGENT_ID>/archive" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{}'
```

After the agent is archived successfully, it can no longer be updated or used to create new sessions, but its configuration and historical versions can still be queried. Existing sessions are not affected and continue to use the agent configuration snapshot saved when they were created.

If you are sure you no longer need to retain the agent and its historical versions, call [Delete agent](delete-agent-api.md). Agents whose `archived_at` already has a value can still be deleted, but deletion cannot be undone; if an associated session whose `status` is `running` exists, wait for the task to finish or handle the interruption first.

## Design tips

* Put stable capabilities in the agent, and put one\-time tasks in session events.

* `system` should define only roles, constraints, and long\-term rules. Do not write the current task directly into `system`.

* When you need external system capabilities, use MCP first.

* When you need to reuse domain knowledge or execution specifications, mount skills first instead of putting long operation manuals directly into `system`.

## Related documents

[Skills](skills.md)

Skills are used to add domain knowledge, operation processes, and best practices to an agent.

[MCP](mcp.md)

MCP (Model Context Protocol) is used to connect tools and data sources from third\-party systems to an agent.

[Tools](tools.md)

Tools determine which execution capabilities an agent can call in a session.

[Tool permission policies](tool-permission-policy.md)

Tool permission policies are used to control whether tool calls initiated by an agent are executed automatically or paused for confirmation from business applications.
