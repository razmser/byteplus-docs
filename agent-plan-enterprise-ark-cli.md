# Ark CLI guide

Ark CLI is the official command\-line tool for ModelArk. It lets you quickly configure and manage Agent Plan Team plans, seats, models, and harness capabilities in a local terminal.

# 1. Install and sign in to Ark CLI

**Environment requirement:**  Node.js 16 or later

**Install and sign in manually**

## Install Ark CLI

Run the following commands in a terminal:

```Bash
# 1. Install the latest version through npm
npm install -g @byteplus/ark-cli@latest

# 2. Verify the installation
arkcli --version
```

## Sign in to Ark CLI

```Bash
# Sign in
arkcli auth login

# Check the sign-in status
arkcli auth status

```

**Install and sign in through an AI agent**

Copy the following prompt to an AI agent, such as Claude Code, Codex, Cursor, or Trae. The AI agent installs Ark CLI automatically.

```Plain
Install Ark CLI for me by using the following command: https://sf16-sg.tiktokcdn.com/obj/eden-sg/psjryh/ljhwZthlaukjlkulzlp/intro/byteplus.md
```

# 2. Configure Agent Plan Team

> Ark CLI currently supports mainstream tools such as Claude Code, Codex, OpenCode, OpenClaw, and Trae. Support for more AI agents is being added.

Run `arkcli helper` in a terminal, then follow the TUI prompts to configure the plan, model, and harness capabilities.

# 3. Use Ark CLI

## Capability overview

Ark CLI supports Agent Plan Team commands across six areas: **transactions, configuration, usage, management, security, and scenario capabilities**. For more capabilities, see the [Ark CLI guide](ark-cli.md).

* Transactions: Purchase or renew plans, and query the plans associated with the current account.

* Configuration: Configure models and harness capabilities, query models supported by the plan, and check configuration status.

* Usage: Query the remaining quota, usage, usage by model or tool, and usage by seat.

* Management: Assign seats, query seat information, and list seat usage.

* Security: Rotate API keys.

* Scenario capabilities: Generate images or videos, and use harness capabilities.

## Transactions

**Use an AI agent**

|Capability |Example prompt |
|---|---|
|Purchase a plan |"Purchase five Agent Plan Team Max seats for me." |
|Renew a plan |"Renew several Agent Plan Team seats for one month." |
|Query plans associated with the current account |"Which plans am I subscribed to, and what are their specifications?" |

**Use a terminal**

* Purchase a plan:

   ```Bash
   arkcli plans buy --plan <PLAN_NAME> --type <PLAN_TYPE> [--duration <MONTHS>] [--quantity <SEAT_COUNT>] [--yes]
   ```

* Renew a plan:

   ```Bash
   arkcli plans renew --plan <PLAN_NAME> [--duration <MONTHS>] [--seat-ids <s1,s2>] [--yes]
   ```

* Query plans associated with the current account:

   ```Bash
   arkcli plans get
   ```

## Configuration

|Capability |Example prompt |
|---|---|
|Configure all harness capabilities |"Configure all Agent Plan Team harness capabilities in Claude Code on my computer." |
|Configure one harness capability |"Install the BytePlus Searchinfinity MCP in Codex for me." |
|Remove configuration |"Remove the BytePlus Searchinfinity MCP configuration from Claude Code." |
|Query configuration status |"Show which local AI tools use Agent Plan Team and whether all MCPs are installed." |
|Query models supported by a plan |"Which models does Agent Plan Team Max support?" |

## Usage

|Capability |Example prompt |
|---|---|
|Query the remaining quota |"How many AFP credits do I have left for Agent Plan Team this month?" |
|Query usage |"How many AFP credits did I use with dola\-seed\-2.1\-turbo this week?" |
|Query seat usage |"Show the usage for each seat in our team this week." |
|Query model or harness usage |"How many AFP credits did I use with dola\-seed\-2.1\-turbo this week?" |

## Management

Management capabilities are available only to administrators.

**Use an AI agent**

|Capability |Example prompt |
|---|---|
|List seats |"List all current Team Agent Plan Team seats and show who is assigned to each." |
|Assign a seat |"Assign seat `seat-abc123` to my colleague `zhangsan`." |
|Query the assignment of a specific seat |"Who is currently assigned to `seat-abc123`?" |

**Use a terminal**

**List Team seats and their assignments:**

```Bash
arkcli plans team seat-list --plan agent-plan-team
```

**Query the assignment of a specific seat:**

```Bash
arkcli plans team seat-list --plan agent-plan-team --seat-id seat-abc123
```

**Assign seats to employees:**

```Bash
# Assign seat-abc123 to an IAM subuser. Replace <user-id> with the subuser ID.
arkcli plans team seat-assign --plan agent-plan-team --bind seat-abc123=<user-id>

# Assign multiple seats by repeating --bind.
arkcli plans team seat-assign --plan agent-plan-team \
  --bind seat-abc123=<user-id> \
  --bind seat-def456=<user-id>
```

## Security

**Use an AI agent**

|Capability |Example prompt |
|---|---|
|Rotate a subuser's Agent Plan Team seat API key |"Rotate the API key for my Team seat." |
|Rotate the API key of another Agent Plan Team seat as an administrator |"Rotate the API key for zhangsan's seat in Team." |

**Use a terminal**

> **warning**

> The rotation command requires secondary confirmation. The original API key becomes invalid immediately after execution. Update harness configurations promptly.

**A subuser rotates their own Team seat API key:**

```Bash
arkcli plans team rotate-apikey
```

**An administrator rotates other Team seat API keys in batches:**

```Bash
arkcli plans team rotate-apikey --seat-ids s1,s2
```

## Scenario capabilities

> **tip**

* > Ark CLI supports configuring harness capabilities included in Agent Plan Team. For harness capabilities that require additional installation, Ark CLI can install and configure the corresponding skills and MCPs.

* > Related harness capabilities:

   * > [BytePlus Searchinfinity](agent-plan-enterprise-search.md)

* > In addition to the harness capabilities above, Ark CLI includes commands for image and video generation. You can invoke them directly through an AI agent or terminal.

**Use an AI agent**

Use prompts to describe your image or video generation requirements. For capability details, see [visual model capabilities supported by Agent Plan Team](agent-plan-enterprise-visual-models.md#7a1c4e9b).

**Image generation:**

```Bash
"Use Agent Plan Team to generate a 4K image of a cyberpunk cat sitting on a Tokyo street, with neon reflections."
```

**Video generation:**

```Bash
Generate a video: "Generate a 5-second 16:9 video: drone footage of a sunrise over snowy mountains, slowly panning right. Wait for completion."
List tasks: "Which video generation tasks are currently in progress?"
Query progress: "Has the video I just generated finished?"
Cancel a task: "Cancel the video generation task I just started."
```

**Use a terminal**

**1. Image generation**

```Bash
arkcli +gen "A cyberpunk cat sitting on a Tokyo street, with neon reflections"
```

```Bash
arkcli +gen "Cyberpunk cat, neon reflections, photorealistic" \
  --model dola-seedream-5.0-pro \
  --size 1024x1024 \
  --image-count 4 \
  --save-to ./out
```

**Image\-to\-image generation:**

```Bash
arkcli +gen "Change this cat to a watercolor style" \
  --input @./cat.png \
  --model dola-seedream-5.0-pro \
  --save-to ./out
```

**2. Video generation**

```Bash
# Generate a video
# Method 1: Asynchronous
arkcli +gen "Drone footage of a sunrise over snowy mountains, slowly panning" --modality video --duration 5 --ratio 16:9
# Method 2: Synchronous
arkcli +gen "Drone footage of a sunrise over snowy mountains, slowly panning" --modality video --duration 5 --ratio 16:9 --wait --save-to ./out

# Query one video task (automatically download after success)
arkcli gen get cgt-20260708-xxxxxxxx --save-to ./out

# List recent tasks (with pagination)
arkcli gen list --page-size 10

# Delete a task
arkcli gen delete cgt-20260708-xxxxxxxx --yes
```
