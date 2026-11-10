# Quick start (code)

Run your first ModelArk\-managed agent in four steps: create an agent, create an environment, start a session, send a message and receive a streaming response.

## Prerequisites

### 1. Obtain and configure an API key

1. **Get an API key**: go to the [API keys page](https://ai.byteplus.com/ark/region:ap-southeast-1/apiKey) and create your API key.

2. **Configure the environment variable**: run the following command in the terminal. Replace `your_api_key_here` with your ModelArk API key to configure the API key as an environment variable.

For how to configure persistent environment variables, see [Environment variable configuration guide](environment-variable-configuration-guide.md).

**macOS**

```Bash
export ARK_API_KEY="your_api_key_here"
```

**Linux**

```Bash
export ARK_API_KEY="your_api_key_here"
```

**Windows_CMD**

```Bash
setx ARK_API_KEY "your_api_key_here"
```

**Windows_PowerShell**

```PowerShell
$env:ARK_API_KEY = "your_api_key_here"
```

### 2. Activate the Managed Agents service

Go to the [Managed Agents activation page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?advancedActiveKey=manageAgent) to activate the service.

### 3. Activate the model service

Go to the [Model activation page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement) to activate the models you need to use.

## 1. Create an agent

Create an agent and define its model, system prompt, and available tools.

```Bash
agent=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/agents" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<'EOF'
{
  "name": "Quick Start Agent",
  "model": {"id": "dola-seed-2-1-turbo-260628"},
  "system": "You are an efficient programming assistant skilled at writing code and troubleshooting issues.",
  "tools": [
    {"type": "agent_toolset_20260916"}
  ]
}
EOF
)

AGENT_ID=$(jq -er '.id' <<<"$agent")

echo "Agent ID: $AGENT_ID"
```

## 2. Create environment

An environment defines the sandbox environment used when an agent runs code and calls tools. You can configure preinstalled dependencies, environment variables, and artifact storage. When creating a session, reference the environment by using `environment_id`.

This example uses a cloud\-managed environment with `config.type=cloud`. ModelArk starts the sandbox on demand and automatically hibernates it, so you do not need to maintain hosts yourself.

> `name` must be unique within the current project; if it duplicates the name of an existing environment, the creation request fails.

```Bash
environment=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/environments" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<'EOF'
{
  "name": "demo-env",
  "config": {
    "type": "cloud",
    "networking": {"type": "unrestricted"}
  }
}
EOF
)

ENVIRONMENT_ID=$(jq -er '.id' <<<"$environment")

echo "Environment ID: $ENVIRONMENT_ID"
```

## 3. Start a session

Create a session that references your agent and environment.

```Bash
session=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/sessions" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<EOF
{
  "agent": "$AGENT_ID",
  "environment_id": "$ENVIRONMENT_ID",
  "title": "Quickstart session"
}
EOF
)

SESSION_ID=$(jq -er '.id' <<<"$session")

echo "Session ID: $SESSION_ID"
```

## 4. Send messages and stream responses

After you obtain `session_id`, first establish an SSE connection through `SessionEvents_streamEvents` and wait for the server to return `: ready`, then send a `user.message` event through `SessionEvents_send`. The client receives the agent status, tool calls, and response content through the established connection.

> **Note**

> SSE only pushes events generated after the connection is established. It does not replay historical events. If you send the message before establishing the connection, the client will miss events generated before the connection is established.

```Bash
# Open the SSE stream and wait for the ready comment before sending the message
STREAM_DIR=$(mktemp -d)
STREAM_PIPE="$STREAM_DIR/events"
mkfifo "$STREAM_PIPE"

cleanup_stream() {
  exec 3<&- || true
  if kill -0 "$STREAM_PID" 2>/dev/null; then
    kill "$STREAM_PID" 2>/dev/null || true
  fi
  wait "$STREAM_PID" 2>/dev/null || true
  rm -f "$STREAM_PIPE"
  rmdir "$STREAM_DIR" 2>/dev/null || true
}

curl -sS -N --fail-with-body \
  "https://ark.ap-southeast.bytepluses.com/api/v3/sessions/$SESSION_ID/events/stream" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Accept: text/event-stream" >"$STREAM_PIPE" &
STREAM_PID=$!
exec 3<"$STREAM_PIPE"
trap cleanup_stream EXIT

stream_ready=false
while IFS= read -r line <&3; do
  if [[ $line == ": ready" ]]; then
    stream_ready=true
    break
  fi
done
if [[ $stream_ready != true ]]; then
  printf 'SSE stream closed before it was ready.\n' >&2
  exit 1
fi

# Send the user message after the stream is ready
curl -sS --fail-with-body \
  "https://ark.ap-southeast.bytepluses.com/api/v3/sessions/$SESSION_ID/events" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d @- >/dev/null <<'EOF'
{
  "events": [
    {
      "type": "user.message",
      "content": [
        {"type": "text", "text": "Write a Python script to generate the first 20 Fibonacci numbers and save them to fibonacci.txt."}
      ]
    }
  ]
}
EOF

# Process events from the established stream
stream_finished=false
while IFS= read -r line <&3; do
  [[ $line == data:* ]] || continue
  json=${line#data: }
  case $(jq -r '.type' <<<"$json") in
    agent.message)
      jq -j '.content[] | select(.type == "text") | .text' <<<"$json"
      ;;
    agent.tool_use)
      printf '\n[Using tool: %s]\n' "$(jq -r '.name' <<<"$json")"
      ;;
    session.status_idle)
      printf '\n\nAgent finished.\n'
      stream_finished=true
      break
      ;;
  esac
done

if [[ $stream_finished != true ]]; then
  printf 'SSE stream closed before the Agent finished.\n' >&2
  exit 1
fi

cleanup_stream
trap - EXIT
```

**Example output:**

```Plain Text
[Using tool: write]

[Using tool: bash]

[Using tool: read]
Completed tasks:
1. Wrote the Python script /workspace/generate_fibonacci.py to generate the Fibonacci sequence.
2. Successfully ran the script. The first 20 generated Fibonacci numbers are: [0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610, 987, 1597, 2584, 4181].
3. The result is saved to the /workspace/fibonacci.txt file. Each Fibonacci number is clearly listed in order, and the content has been verified as correct.

Agent finished.
```

## Full script

Combine the four steps above into a directly runnable script. Add `set -euo pipefail` at the beginning so the script exits immediately if any step fails.

**Instruction:**

1. Save the following code as `quickstart.sh`.

2. Make sure you have configured the `ARK_API_KEY` environment variable as described in the Prerequisites section, and have installed `curl` and `jq`.

3. **Run:**

   ```Bash
   bash quickstart.sh
   ```

```Bash
#!/usr/bin/env bash
set -euo pipefail

export ARK_API_KEY="${ARK_API_KEY:? Please export ARK_API_KEY first}"
ARK_BASE_URL="https://ark.ap-southeast.bytepluses.com"

# Step 1: Create Agent
agent=$(
  curl -sS --fail-with-body "$ARK_BASE_URL/api/v3/agents" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<'EOF'
{
  "name": "Quick Start Agent",
  "model": {"id": "dola-seed-2-1-turbo-260628"},
  "system": "You are an efficient programming assistant skilled at writing code and troubleshooting issues.",
  "tools": [
    {"type": "agent_toolset_20260916"}
  ]
}
EOF
)
AGENT_ID=$(jq -er '.id' <<<"$agent")
echo "Agent ID: $AGENT_ID"

# Step 2: Create Environment
environment=$(
  curl -sS --fail-with-body "$ARK_BASE_URL/api/v3/environments" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<'EOF'
{
  "name": "demo-env",
  "config": {
    "type": "cloud",
    "networking": {"type": "unrestricted"}
  }
}
EOF
)
ENVIRONMENT_ID=$(jq -er '.id' <<<"$environment")
echo "Environment ID: $ENVIRONMENT_ID"

# Step 3: Create Session
session=$(
  curl -sS --fail-with-body "$ARK_BASE_URL/api/v3/sessions" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<EOF
{
  "agent": "$AGENT_ID",
  "environment_id": "$ENVIRONMENT_ID",
  "title": "Quickstart session"
}
EOF
)
SESSION_ID=$(jq -er '.id' <<<"$session")
echo "Session ID: $SESSION_ID"

# Step 4: Open the SSE stream before sending the message
STREAM_DIR=$(mktemp -d)
STREAM_PIPE="$STREAM_DIR/events"
mkfifo "$STREAM_PIPE"

cleanup_stream() {
  exec 3<&- || true
  if kill -0 "$STREAM_PID" 2>/dev/null; then
    kill "$STREAM_PID" 2>/dev/null || true
  fi
  wait "$STREAM_PID" 2>/dev/null || true
  rm -f "$STREAM_PIPE"
  rmdir "$STREAM_DIR" 2>/dev/null || true
}

curl -sS -N --fail-with-body \
  "$ARK_BASE_URL/api/v3/sessions/$SESSION_ID/events/stream" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Accept: text/event-stream" >"$STREAM_PIPE" &
STREAM_PID=$!
exec 3<"$STREAM_PIPE"
trap cleanup_stream EXIT

stream_ready=false
while IFS= read -r line <&3; do
  if [[ $line == ": ready" ]]; then
    stream_ready=true
    break
  fi
done
if [[ $stream_ready != true ]]; then
  printf 'SSE stream closed before it was ready.\n' >&2
  exit 1
fi

# Send the user message after the stream is ready
curl -sS --fail-with-body \
  "$ARK_BASE_URL/api/v3/sessions/$SESSION_ID/events" \
  -H "Authorization: Bearer $ARK_API_KEY" \
  -H "Content-Type: application/json" \
  -d @- >/dev/null <<'EOF'
{
  "events": [
    {
      "type": "user.message",
      "content": [
        {"type": "text", "text": "Write a Python script to generate the first 20 Fibonacci numbers and save them to fibonacci.txt."}
      ]
    }
  ]
}
EOF

stream_finished=false
while IFS= read -r line <&3; do
  [[ $line == data:* ]] || continue
  json=${line:5}
  json=${json# }
  case $(jq -r '.type' <<<"$json") in
    agent.message)
      jq -j '.content[] | select(.type == "text") | .text' <<<"$json"
      ;;
    agent.tool_use)
      printf '\n[Using tool: %s]\n' "$(jq -r '.name' <<<"$json")"
      ;;
    session.status_idle)
      printf '\n\nAgent finished.\n'
      stream_finished=true
      break
      ;;
  esac
done

if [[ $stream_finished != true ]]; then
  printf 'SSE stream closed before the Agent finished.\n' >&2
  exit 1
fi

cleanup_stream
trap - EXIT
```

### Full SDK examples

**Python**

```Python
import json
import os
import time

import httpx
from arkruntime import Ark


client = Ark(
    api_key=os.environ["ARK_API_KEY"],
    base_url="https://ark.ap-southeast.bytepluses.com/api/v3",
)
suffix = str(int(time.time()))

agent = client.agents.create(
    name=f"quick-start-agent-{suffix}",
    model={"id": "dola-seed-2-1-turbo-260628"},
    system="You are an efficient programming assistant who excels at writing code and troubleshooting issues.",
    tools=[{"type": "agent_toolset_20260916"}],
)
environment = client.environments.create(
    name=f"quick-start-env-{suffix}",
    config={
        "type": "cloud",
        "networking": {"type": "unrestricted"},
    },
)
session = client.sessions.create(
    agent=agent.id,
    environment_id=environment.id,
    title="Quickstart session",
)

try:
    stream_url = "https://ark.ap-southeast.bytepluses.com/api/v3/sessions/{}/events/stream".format(session.id)
    stream_headers = {
        "Authorization": "Bearer {}".format(os.environ["ARK_API_KEY"]),
        "Accept": "text/event-stream",
    }
    with httpx.stream(
        "GET", stream_url, headers=stream_headers, timeout=None
    ) as response:
        response.raise_for_status()
        lines = response.iter_lines()
        for line in lines:
            if line == ": ready":
                break
        else:
            raise RuntimeError("SSE stream closed before it was ready.")

        client.sessions.events.send(
            session.id,
            events=[
                {
                    "type": "user.message",
                    "content": [
                        {
                            "type": "text",
                            "text": "Use Python to output the first 20 Fibonacci numbers.",
                        }
                    ],
                }
            ],
        )

        agent_finished = False
        for line in lines:
            if not line.startswith("data:"):
                continue
            data = line.removeprefix("data:").strip()
            if data == "[DONE]":
                break
            event = json.loads(data)
            print(event)
            if event["type"] in {
                "session.status_idle",
                "session.status_terminated",
            }:
                agent_finished = True
                break
        if not agent_finished:
            raise RuntimeError("SSE stream closed before the Agent finished.")
finally:
    client.sessions.delete(session.id)
    client.environments.delete(environment.id)
    client.agents.delete(agent.id)
```

**Go**

```Go
package main

import (
    "context"
    "fmt"
    "os"
    "time"

    "github.com/volcengine/ark-runtime-go/arkruntime"
    agentmodel "github.com/volcengine/ark-runtime-go/arkruntime/model/agent"
    envmodel "github.com/volcengine/ark-runtime-go/arkruntime/model/environment"
    sessionmodel "github.com/volcengine/ark-runtime-go/arkruntime/model/session"
)

func main() {
    ctx := context.Background()
    client := arkruntime.NewClientWithApiKey(
        os.Getenv("ARK_API_KEY"),
        arkruntime.WithBaseUrl("https://ark.ap-southeast.bytepluses.com/api/v3"),
    )
    suffix := fmt.Sprint(time.Now().Unix())

    agent, err := client.CreateAgent(ctx, &agentmodel.CreateAgentRequest{
        Name:   "quick-start-agent-" + suffix,
        Model:  agentmodel.ModelConfig{ID: "dola-seed-2-1-turbo-260628"},
        System: agentmodel.NewOptString("You are an efficient coding assistant."),
        Tools:  []agentmodel.ToolItem{{Type: "agent_toolset_20260916"}},
    })
    if err != nil {
        panic(err)
    }

    environment, err := client.CreateEnvironment(ctx, &envmodel.CreateEnvironmentRequest{
        Name: "quick-start-env-" + suffix,
        Config: envmodel.NewOptEnvConfig(envmodel.EnvConfig{
            Type: envmodel.EnvConfigTypeCloud,
            Networking: envmodel.NewOptNetworkingConfig(envmodel.NetworkingConfig{
                Type: envmodel.NetworkingTypeUnrestricted,
            }),
        }),
    })
    if err != nil {
        panic(err)
    }

    session, err := client.CreateSession(ctx, &sessionmodel.CreateSessionRequest{
        Agent:         sessionmodel.NewStringAgentIdentifier(agent.ID),
        EnvironmentID: sessionmodel.NewOptString(environment.ID),
        Title:         sessionmodel.NewOptString("Quickstart session"),
    })
    if err != nil {
        panic(err)
    }
    defer client.DeleteAgent(ctx, agent.ID)
    defer client.DeleteEnvironment(ctx, environment.ID)
    defer client.DeleteSession(ctx, session.ID)

    content := sessionmodel.ManagedAgentsMessageContentBlock{
        OneOf: sessionmodel.NewManagedAgentsTextBlockManagedAgentsMessageContentBlockSum(
            sessionmodel.ManagedAgentsTextBlock{Text: "Use Python to output the first 20 Fibonacci numbers."},
        ),
    }
    event := sessionmodel.ManagedAgentsEventParams{
        OneOf: sessionmodel.NewManagedAgentsUserMessageEventParamsManagedAgentsEventParamsSum(
            sessionmodel.ManagedAgentsUserMessageEventParams{
                Content: []sessionmodel.ManagedAgentsMessageContentBlock{content},
            },
        ),
    }

    stream, err := client.StreamSessionEvents(ctx, session.ID)
    if err != nil {
        panic(err)
    }
    defer stream.Close()

    _, err = client.SendSessionEvents(ctx, session.ID, &sessionmodel.SendSessionEventsRequest{
        Events: []sessionmodel.ManagedAgentsEventParams{event},
    })
    if err != nil {
        panic(err)
    }

    agentFinished := false
    for stream.Next() {
        frame := stream.Event()
        fmt.Printf("%s: %s\n", frame.Type, frame.RawPayload)
        if frame.Type == "session.status_idle" || frame.Type == "session.status_terminated" {
            agentFinished = true
            break
        }
    }
    if err := stream.Err(); err != nil {
        panic(err)
    }
    if !agentFinished {
        panic("SSE stream closed before the Agent finished.")
    }
}
```

**Java**

```Java
package com.ark.sample;

import com.volcengine.ark.runtime.models.agent.Agent;
import com.volcengine.ark.runtime.models.agent.CreateAgentRequest;
import com.volcengine.ark.runtime.models.agent.ModelConfig;
import com.volcengine.ark.runtime.models.agent.ToolItem;
import com.volcengine.ark.runtime.models.environment.CreateEnvironmentRequest;
import com.volcengine.ark.runtime.models.environment.EnvConfig;
import com.volcengine.ark.runtime.models.environment.EnvConfigType;
import com.volcengine.ark.runtime.models.environment.Environment;
import com.volcengine.ark.runtime.models.environment.NetworkingConfig;
import com.volcengine.ark.runtime.models.environment.NetworkingType;
import com.volcengine.ark.runtime.models.session.AgentIdentifier;
import com.volcengine.ark.runtime.models.session.CreateSessionRequest;
import com.volcengine.ark.runtime.models.session.ManagedAgentsMessageContentBlock;
import com.volcengine.ark.runtime.models.session.ManagedAgentsTextBlock;
import com.volcengine.ark.runtime.models.session.ManagedAgentsUserMessageEventParams;
import com.volcengine.ark.runtime.models.session.SendSessionEventsRequest;
import com.volcengine.ark.runtime.models.session.Session;
import com.volcengine.ark.runtime.service.ArkService;
import okhttp3.ResponseBody;
import okio.BufferedSource;
import retrofit2.Response;

import java.util.Arrays;

public class ManagedAgentsQuickStart {
    public static void main(String[] args) throws Exception {
        ArkService service = ArkService.builder()
                .apiKey(System.getenv("ARK_API_KEY"))
                .baseUrl("https://ark.ap-southeast.bytepluses.com/api/v3")
                .build();
        String suffix = String.valueOf(System.currentTimeMillis());

        Agent agent = service.createAgent(
                CreateAgentRequest.builder()
                        .name("quick-start-agent-" + suffix)
                        .model(ModelConfig.builder().id("dola-seed-2-1-turbo-260628").build())
                        .system("You are an efficient programming assistant.")
                        .tools(Arrays.asList(ToolItem.builder().type("agent_toolset_20260916").build()))
                        .build());
        Environment environment = service.createEnvironment(
                CreateEnvironmentRequest.builder()
                        .name("quick-start-env-" + suffix)
                        .config(EnvConfig.builder()
                                .type(EnvConfigType.CLOUD)
                                .networking(NetworkingConfig.builder()
                                        .type(NetworkingType.UNRESTRICTED)
                                        .build())
                                .build())
                        .build());
        Session session = service.createSession(
                CreateSessionRequest.builder()
                        .agent(AgentIdentifier.ofString(agent.getId()))
                        .environmentId(environment.getId())
                        .title("Quickstart session")
                        .build());

        try {
            ManagedAgentsTextBlock text = ManagedAgentsTextBlock.builder()
                    .text("Use Python to output the first 20 Fibonacci numbers.")
                    .build();
            ManagedAgentsUserMessageEventParams event =
                    ManagedAgentsUserMessageEventParams.builder()
                            .content(Arrays.<ManagedAgentsMessageContentBlock>asList(text))
                            .build();

            Response<ResponseBody> response =
                    service.streamSessionEvents(session.getId()).execute();
            if (!response.isSuccessful() || response.body() == null) {
                throw new IllegalStateException("Stream request failed: " + response.code());
            }
            try (ResponseBody body = response.body()) {
                BufferedSource source = body.source();
                service.sendSessionEvents(
                        session.getId(),
                        SendSessionEventsRequest.builder()
                                .events(Arrays.asList(event))
                                .build());

                String line;
                boolean agentFinished = false;
                while ((line = source.readUtf8Line()) != null) {
                    if (line.startsWith("data:")) {
                        System.out.println(line.substring(5).trim());
                    }
                    if (line.contains("session.status_idle")
                            || line.contains("session.status_terminated")) {
                        agentFinished = true;
                        break;
                    }
                }
                if (!agentFinished) {
                    throw new IllegalStateException(
                            "SSE stream closed before the Agent finished.");
                }
            }
        } finally {
            service.deleteSession(session.getId());
            service.deleteEnvironment(environment.getId());
            service.deleteAgent(agent.getId());
            service.shutdownExecutor();
        }
    }
}
```

## How it works

When you send a user event, the ModelArk\-managed agent will:

1. **Configure the sandbox**: Your environment configuration determines how the sandbox is built.

2. **Run the agent loop**: ModelArk determines which tools to use based on your message.

3. **Execute tools**: Start the sandbox, and run file writes, bash commands, and other tool calls inside the sandbox.

4. **Stream events**: You receive real\-time updates while the agent is working.

5. **Enter the idle state**: When the agent has no more tasks to perform, it emits a `session.status_idle` event.

## Design tips

When configuring an agent and a session, distinguish long\-term capabilities from one\-off tasks based on the configuration lifecycle:

* **Write long\-term capabilities into the agent definition**: Configure the system prompt, skills, tools, and MCP server through `Agents_create` or `Agents_update` so they can be reused across multiple sessions.

* **Pass one\-off tasks through session events**: Send a `user.message` event through `SessionEvents_send`. Do not write the current task into the agent's `system` parameter.

Writing one\-off tasks into `system` reduces the reusability of the agent definition; putting long\-term rules into session events requires repeated transmission and can also cause configuration inconsistencies across sessions.

## Next steps

[Define an agent](define-agent.md)

Define the agent's model, system prompt, tool set, and runtime behavior.

[Configure the environment](configure-cloud-environment.md)

Configure the cloud sandbox environment where the agent runs, including the network, preinstalled dependencies, and environment variables.

[Agent tools](tools.md)

Configure the set of tools that the agent can call in a session.

[Session event stream](session-event-stream.md)

Receive agent messages, tool calls, and status updates in real time through the SSE event stream.
