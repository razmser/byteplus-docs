# Configure context compaction

The conversation history of a single session grows as interactions continue. After the configuration takes effect, the runtime determines whether to compact the context based on the saved policy, controlling the context length used by subsequent model calls.

Compaction only adjusts the context visible to subsequent model calls. It does not delete event records in the session. To query the complete interaction history, call [List session events](list-events-api.md). To view the meanings of event parameters, see [Session event structure reference](sessions-events-reference-v2.md).

## How it works

`context_management` is an array that contains at most one policy. Currently, the policy type is fixed as `compaction`.

You can choose where to configure it based on the task scope:

1. Set `context_management` in the `agent` object when creating a session, and set `agent.type` to `agent_with_overrides`, only when the current session needs a different policy.

2. When later sessions all need the same policy, save `context_management` to the agent version.

3. If you do not have a custom context compaction policy, do not pass `context_management`. The runtime uses the platform default behavior.

After a session is created, the system freezes only explicitly saved policies into the agent configuration snapshot. Subsequent updates to the base agent do not change existing sessions. When you query an agent or session, the response may display the current platform default policy or default threshold. The current default threshold is `192000` tokens.

> **Tip**

> Context compaction, context caching, and long\-term memory solve different problems:

* > **Context compaction**: Shortens the historical content submitted to the model in a single session.

* > **Context caching**: Reuses repeated input to reduce inference latency and input token cost.

* > **Long\-term memory (memory store)**: Saves and reuses long\-term information across sessions.

## Configure context compaction for an agent

To make later sessions use the same policy by default, configure `context_management` when creating or updating an agent:

```json
{
  "context_management": [
    {
      "type": "compaction",
      "compact_threshold": 100000,
      "instructions": "Keep the task goal, confirmed facts, key identifiers, execution results, unfinished items, and next steps."
    }
  ]
}
```

`compact_threshold` controls when automatic compaction is triggered. Pass this parameter only when you need a fixed trigger threshold. If you omit this parameter or pass `null`, the runtime uses the platform default threshold, and the query response displays the current default value. The current platform default threshold is `192000` tokens.

`instructions` guides the system in generating the compacted summary. Fill it in only when you need to change the focus of the summary. If you have no custom requirements, you can omit it, pass `null`, or pass an empty string.

For the complete request parameters, value rules, and response structure, see [Create agent](create-agent-api.md) and [Update agent](update-agent-api.md).

### Update or clear a policy

When you update an agent, `context_management` replaces the current configuration as a whole:

|Request value |Server\-side handling |Behavior of later sessions |
|---|---|---|
|Omitted or `null` |Keeps the configuration in the current agent version. |Continues to inherit the original policy. |
|`[]` |Clears the custom policy of the agent. |Uses the platform default compaction policy. The query response can display the current platform default policy and default threshold. |
|A single `compaction` policy |Replaces the current configuration as a whole. |Inherits the new policy. |

> **Note**

> Passing `[]` means returning to the platform default compaction policy. It does not disable compaction. When the session history continues to grow and reaches the platform default threshold, which is currently `192000` tokens, the runtime still determines whether to compact earlier context based on the default policy, preventing subsequent model calls from failing because the context is too long. The current API does not provide a configuration option for disabling automatic compaction.

Do not use a threshold higher than the model context window as a switch to disable compaction. The server saves the value, but model calls may fail before the threshold is reached because the input exceeds the model window.

## Configure context compaction for a session

If only the current task needs a different policy, set `agent.context_management` when creating the session, and set `agent.type` to `agent_with_overrides`. The override is written only to the agent configuration snapshot of the current session. It does not create a new agent version or affect other sessions.

```json
{
  "agent": {
    "type": "agent_with_overrides",
    "id": "<AGENT_ID>",
    "context_management": [
      {
        "type": "compaction",
        "compact_threshold": 80000,
        "instructions": "Keep code snippets, variable names, technical decisions, test results, and next steps."
      }
    ]
  },
  "environment_id": "<ENVIRONMENT_ID>"
}
```

Session overrides support the following three ways to pass values:

* **Omit or pass ** **`null`**: Inherits the configuration of the selected agent version.

* **Pass **  **`[]`** : Clears the inherited custom policy and uses the platform default compaction policy instead. The query response can display the current platform default policy and default threshold.

* **Pass one item**: Replaces the policy of the selected agent version as a whole.

For the complete request structure, see [Create session](create-session-api.md).

## Query the configuration

When you query an agent or session, the response displays the context management policy. The current platform default threshold is `192000` tokens:

* If `compact_threshold` has been explicitly saved, the response returns the saved threshold.

* If a `compaction` policy has been saved but `compact_threshold` has not been saved, the response retains the saved `instructions` and displays the current platform default threshold.

* If no custom context compaction policy has been saved, or the custom policy has been cleared by using `[]`, the response can display one `compaction` policy and the current platform default threshold.

* In a multi\-agent session, each member's `context_management` comes from the configuration snapshot frozen when that member was created. When the response displays default values, it also does not modify the member snapshot.

Default values displayed in query responses are for display only and are not written back to the agent version or session snapshot. If the client uses the query result as\-is in a create or update request, the default threshold `192000` is saved as an explicit configuration and no longer follows changes to the platform default value. To continue using the platform default threshold, omit `compact_threshold` or pass `null` when creating or updating.

When compaction actually occurs, the session event stream returns an `agent.thread_context_compacted` event. This event is used to confirm that the thread context has been summarized and compacted. For more information, see [Session event structure reference](sessions-events-reference-v2.md).

## Configuration limits

* `context_management` can contain at most one item, and `type` is fixed as `compaction`.

* An explicitly configured `compact_threshold` cannot be less than 50000. The values of other parameters, length limits, and error codes are subject to the corresponding API reference.

* Agent updates affect only sessions created later and do not modify the configuration snapshots of existing sessions.

* Session upgrade does not support modifying `context_management`. To use a new policy, update the agent and then create a new session.

## Related documents

* **Define an agent**: [Agent](define-agent.md)

* **Start a session**: [Start session](start-session.md)

* **Persistent memory**: [Persistent memory](persistent-memory.md)

* **Context caching**: [Context cache](context-cache.md)
