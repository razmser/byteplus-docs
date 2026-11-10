# Persistent memory

This topic describes how to use memory stores in ModelArk Managed Agents to provide agents with long\-term memory that can be retained across sessions.

By default, each session starts with a new context. After a session ends, preferences, conventions, troubleshooting experience, or business context accumulated by the agent during that run is not automatically carried over to the next task. A memory store is used to save this reusable information and remount it for the agent in subsequent sessions.

## Basic concepts:

A memory store can store multiple memories. Each memory is identified by a separate `path`, making it easy to organize by purpose and reuse across sessions.

Creating, reading, updating, and deleting memories do not depend on sessions. To let the agent use this content during runtime, mount the memory store as a resource when you create a session.

After it is mounted, the content in the memory store appears as a read\-only directory at `/mnt/memory/{memory_store_id}/`. The agent can read the memories in it, but cannot create, update, delete, or move memories while the session is running.

To modify memory content, call [Create memory](create-memory-api.md), [Update memory](update-memory-api.md), or [Delete memory](delete-memory-api.md).

## Create a memory store

You must provide `name` when creating a memory store; `description` is optional. When `description` is provided, this parameter is shown to the agent to describe the content saved in this store and its use cases.

```bash
store=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d '{
      "name": "<MEMORY_STORE_NAME>",
      "description": "<MEMORY_STORE_DESCRIPTION>"
    }'
)

STORE_ID=$(jq -er '.id' <<<"$store")

echo "Memory Store ID: $STORE_ID"
```

The returned memory store ID looks like `memstore-...`. You need to pass this ID when creating a session and mounting memory.

## Preset memory content

Before the agent starts running, you can write reference materials to the store, such as project specifications, user preferences, output formats, and glossaries.

Choose an interface based on the number of items to write:

* **Write a single memory**: Call [Create memory](create-memory-api.md). This is suitable for adding scattered content.

* **Write memories in batches**: Call [Batch create memories](batch-create-memories-api.md). This is suitable for initializing glossaries, user preferences, or output templates.

```Bash
curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/$STORE_ID/memories" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "path": "/<MEMORY_1>.md",
    "content": "<CONTENT_OF_THE_MEMORY>"
  }'
```

The content limit for a single memory is 100 KiB (102400 bytes), approximately 25k tokens. A single store can save up to 2,000 memories. We recommend splitting memory into multiple small, focused files and avoiding long documents.

## Mount a memory store to a session

A memory store must be mounted through the `resources` array when creating a session. Unlike file and code repository resources, memory stores can only be mounted when a session is created, and cannot be added to or removed from a running session.

In the `resources` array, set the resource `type` to `memory_store` and specify the memory store to mount with `memory_store_id`.

After mounting, the agent can only read this memory store.

```bash
session=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/sessions" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<EOF
{
  "agent": "$AGENT_ID",
  "environment_id": "$ENVIRONMENT_ID",
  "resources": [
    {
      "type": "memory_store",
      "memory_store_id": "$STORE_ID"
    }
  ]
}
EOF
)

printf '%s\n' "$session"

SESSION_ID=$(jq -er '.id' <<<"$session")
```

A single session can mount up to 10 memory stores. You can split stores by use case, for example, to manage user preferences, project context, and team\-shared standards separately.

## How agents access memory

* The agent can browse and read memories in the mounted directory, but cannot modify content through the current session.

## View and edit memory

You can use [List memories](list-memories-api.md), [Get memory](get-memory-api.md), [Update memory](update-memory-api.md), and [Delete memory](delete-memory-api.md) to query and maintain memories for reviewing content, correcting errors, or cleaning up outdated information.

### Query the memory list

You can browse memory by path prefix through `path_prefix`, similar to viewing a directory.

```Bash
curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/$STORE_ID/memories? path_prefix=/&order_by=path&depth=2" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  | jq -r '.data[] | "\(.type)  \(.path)"'
```

### Read memory

Reading a single memory returns the full content.

```Bash
curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/$STORE_ID/memories/$MEMORY_ID" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  | jq -r '.content'
```

### Create memory

`create` creates a new memory under the specified `path`; if the path already exists, the original content is not overwritten. To modify an existing memory, use the update interface.

```bash
memory=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/$STORE_ID/memories" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d '{
      "path": "/path/of/the/memory",
      "content": "<CONTENT_OF_THE_MEMORY>"
    }'
)

MEMORY_ID=$(jq -r '.id' <<<"$memory")
MEMORY_SHA=$(jq -r '.content_sha256' <<<"$memory")
```

### Update memory

The update interface can modify the content, the path, or both. Changing the path can be used for renaming or archiving.

```Bash
curl -sS --fail-with-body -X POST "https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/$STORE_ID/memories/$MEMORY_ID" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "path": "/new/path"
  }'
```

### Delete memory

```Bash
curl -sS --fail-with-body -X DELETE "https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/$STORE_ID/memories/$MEMORY_ID" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

## Manage memory stores

In addition to creation, memory stores also support operations such as querying and deletion.

### Query the store list

```Bash
curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

### Delete store

```Bash
curl -sS --fail-with-body -X DELETE "https://ark.ap-southeast.bytepluses.com/api/v3/memory_stores/$STORE_ID" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

## Best practices

When a store reaches the limit of 2,000 memories, new memory writes will fail. Existing memories can still be read and updated. We recommend managing long\-term memory as follows:

* **Split stores by purpose**: Do not put all content into one general\-purpose store. You can create separate stores for users, team\-shared knowledge, and project context.

* **Organize content before approaching the limit**: Regularly delete outdated or duplicate memories, or organize fragmented content into a more stable summary store.
