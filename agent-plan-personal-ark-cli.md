# Ark CLI guide

Ark CLI is the official command\-line tool for ModelArk. It lets you quickly configure and manage Agent Plan plans, models, and harness capabilities in a local terminal.

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

# 2. Configure Agent Plan

Run `arkcli helper` in a terminal, then follow the TUI prompts to select the plan, model, and harness capabilities to configure.

# 3. Use Ark CLI

## Capability overview

Ark CLI supports Agent Plan commands across five areas: **transactions, configuration, usage, security, and scenario capabilities**. For more capabilities, see the [Ark CLI guide](ark-cli.md).

* Transactions: Purchase or renew plans, and query the plans associated with the current account.

* Configuration: Configure models and harness capabilities, query models supported by the plan, and check configuration status.

* Usage: Query the remaining quota, usage, and usage by model or tool.

* Security: Rotate API keys.

* Scenario capabilities: Generate images or videos, and use harness capabilities.

## Transactions

**Use an AI agent**

|Capability |Example prompt |
|---|---|
|Purchase a plan |"Purchase an Agent Plan Max plan for me." |
|Renew a plan |"Renew my Agent Plan plan for one month." |
|Query plans associated with the current account |"Which plans am I subscribed to, and what are their specifications?" |

**Use a terminal**

* Purchase a plan:

   ```Bash
   # arkcli plans buy --plan <PLAN_NAME> --type <PLAN_TYPE> [--duration <MONTHS>] [--yes]
   
   # Parameters:
   #     PLAN_NAME: agent-plan
   #     PLAN_TYPE: small, medium, large, or max
   #     MONTHS: An integer from 1 to 12. The default value is 1.
   #     --yes: Confirm the purchase. Without --yes, the command returns an order preview and agreement. We recommend previewing the order first.
   
   # Examples:
   # Preview: Return only the agreement URL
   arkcli plans buy --plan agent-plan --type max --duration 1
   # Confirm the purchase
   arkcli plans buy --plan agent-plan --type max --duration 1 --yes
   ```

* Renew a plan:

   ```Bash
   # arkcli plans renew --plan <PLAN_NAME> [--duration <MONTHS>] [--yes]
   
   # Parameters:
   # PLAN_NAME: agent-plan
   # MONTHS: An integer from 1 to 12. The default value is 1.
   # --yes: Confirm the renewal. Without --yes, the command returns an order preview and agreement. We recommend previewing the order first.
   
   # Examples:
   # Preview: Return only the agreement URL
   arkcli plans renew --plan agent-plan --duration 1
   # Confirm the renewal
   arkcli plans renew --plan agent-plan --duration 1 --yes
   ```

* Query plans associated with the current account:

   ```Bash
   arkcli plans get
   ```

## Configuration

|Capability |Example prompt |
|---|---|
|Configure all harness capabilities |"Configure all Agent Plan harness capabilities in Claude Code on my computer." |
|Configure one harness capability |"Install the BytePlus Searchinfinity MCP in Codex for me." |
|Remove configuration |"Remove all BytePlus Searchinfinity MCP configuration from Claude Code." |
|Query configuration status |"Show which local AI tools use Agent Plan and whether all MCPs are installed." |
|Query models supported by a plan |"Which models does Agent Plan Max support?" |

## Usage

|Capability |Example prompt |
|---|---|
|Query the remaining quota |"How much Agent Plan quota do I have left this month?" |
|Query usage |"How much have I used this week?" |
|Query model or harness usage |"How much of dola\-seed\-2.1\-turbo did I use this week?" |

## Security

**Use an AI agent**

|Capability |Example prompt |
|---|---|
|Rotate an API key |"Rotate the API key for my Agent Plan." |

**Use a terminal**

> **warning**

> This command requires secondary confirmation. The old API key becomes invalid after execution.

```Bash
arkcli plans personal rotate-apikey
```

## Scenario capabilities

> **tip**

* > Ark CLI supports configuring harness capabilities included in Agent Plan. For harness capabilities that require additional installation, Ark CLI can install and configure the corresponding skills and MCPs.

* > Related harness capabilities:

   * > [BytePlus Searchinfinity](agent-plan-personal-search.md)

* > In addition to the harness capabilities above, Ark CLI includes commands for image and video generation. You can invoke them directly through an AI agent or terminal.

**Use an AI agent**

Use prompts to describe your image or video generation requirements. For capability details, see [visual model capabilities supported by Agent Plan](agent-plan-personal-visual-models.md#7a1c4e9b).

**Image generation:**

```Bash
"Use Agent Plan to generate a 4K image of a cyberpunk cat sitting on a Tokyo street, with neon reflections."
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
