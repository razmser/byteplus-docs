# Tool permission policy

Tool permission policies are used to control whether tool calls initiated by an agent are executed automatically or paused for confirmation from business applications.

The current permission policies apply only to tools executed on the server side:

* Built\-in toolset `agent_toolset_20260916`

* MCP toolset `mcp_toolset`

## Permission policy types

|Policies |Note |
|---|---|
|`always_allow` |Tool calls are executed automatically and do not require human confirmation. |
|`always_ask` |Pause before a tool call and continue after confirmation by business applications. |

The default value of the permission policy depends on whether the entire `default_config` is provided:

|Configuration |Built\-in toolset |MCP toolset |
|---|---|---|
|Omit the entire `default_config` |The server saves `enabled=true` and `always_allow`. |The server saves `enabled=true` and `always_allow`. |
|Provide `default_config` but omit `permission_policy` |Use `always_allow` at runtime. |Use `always_ask` at runtime. |
|Explicitly configure `permission_policy` |Use the configured policy. |Use the configured policy. |

To make the agent’s echoed configuration and runtime behavior directly verifiable, we recommend always explicitly configuring `default_config.enabled` and `default_config.permission_policy`.

## Set a permission policy for an entire toolset

You can configure a unified policy for an entire toolset in `default_config.permission_policy`.

The following example changes the default policy of the built\-in toolset to `always_ask`:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/agents \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "CautiousAgent",
    "model": {
      "id": "dola-seed-2-1-turbo-260628"
    },
    "tools": [
      {
        "type": "agent_toolset_20260916",
        "default_config": {
          "enabled": true,
          "permission_policy": {
            "type": "always_ask"
          }
        }
      }
    ]
  }'
```

Do not pass only `default_config.enabled` while omitting `permission_policy`. Built\-in tools and MCP tools use different runtime defaults for empty policies.

## Override the permission policy for a single tool

In addition to setting a default policy for an entire toolset, you can also apply more fine\-grained overrides to individual tools in `configs`.

The following example keeps the built\-in toolset's default automatic execution, but requires confirmation before each `bash` execution:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/agents \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "CommandsCautiousAgent",
    "model": {
      "id": "dola-seed-2-1-turbo-260628"
    },
    "tools": [
      {
        "type": "agent_toolset_20260916",
        "default_config": {
          "enabled": true,
          "permission_policy": {
            "type": "always_allow"
          }
        },
        "configs": [
          {
            "name": "bash",
            "enabled": true,
            "permission_policy": {
              "type": "always_ask"
            }
          }
        ]
      }
    ]
  }'
```

This override relationship is suitable for keeping most low\-risk tools running automatically, while adding an extra confirmation step only for high\-risk tools.

## Set a permission policy for the MCP toolset

The following example explicitly sets the toolset of a trusted MCP server to `always_allow`, making it easier to directly verify the scope of automatic execution:

**Curl**

```Bash
curl https://ark.ap-southeast.bytepluses.com/api/v3/agents \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "GitHubCollaborationAssistant",
    "model": {
      "id": "dola-seed-2-1-turbo-260628"
    },
    "mcp_servers": [
      {
        "type": "url",
        "name": "github",
        "url": "https://mcp.example.com/github"
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
      },
      {
        "type": "mcp_toolset",
        "mcp_server_name": "github",
        "default_config": {
          "enabled": true,
          "permission_policy": {
            "type": "always_allow"
          }
        }
      }
    ]
  }'
```

> **Note**

> We recommend using the `always_allow` policy explicitly only after you have confirmed that the MCP server and the tools it exposes are trustworthy. If the MCP server has new tools later, the toolset\-level automatic allow policy also applies to the new tools.

## Usage tips

* For read\-only tools, consider `always_allow` first.

* For tools that modify files, execute commands, or access external systems, we recommend considering `always_ask` first.

* For MCP tools, we recommend first narrowing `configs` based on the principle of least privilege, and then deciding whether to allow automatic execution.

For toolset configuration and the runtime event stream corresponding to `user.tool_confirmation`, see [Tools](tools.md). If you want to manage an MCP server and its tools, see [MCP](mcp.md) for details.

## Related documents

[Agent](define-agent.md)

An agent is a configuration template that contains basic information, system prompt, and extended capabilities.

[Skills](skills.md)

Skills are used to add domain knowledge, operation processes, and best practices to an agent.

[MCP](mcp.md)

MCP (Model Context Protocol) is used to connect tools and data sources from third\-party systems to an agent.

[Tools](tools.md)

Tools determine which execution capabilities an agent can call in a session.
