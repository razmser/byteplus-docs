# Ark CLI guide

Ark CLI is the official command\-line tool for BytePlus ModelArk. You can use it in a local terminal to quickly authenticate and manage resources. You can also install its capabilities in commonly used AI agents and use natural language to invoke models, manage inference endpoints, generate content, and query usage and billing information.

## Get started

**Environment requirement:**  Node.js 16 or later

### 1. Install and sign in to Ark CLI

**Install and sign in manually**

#### Install Ark CLI

Run the following commands in a terminal:

```bash
# 1. Install Ark CLI through npm
npm install -g @byteplus/ark-cli

# 2. Verify the installation
arkcli --version
```

#### Sign in to Ark CLI

```bash
# Sign-in method 1: Browser sign-in (recommended)
arkcli auth login

# Sign-in method 2: Browserless sign-in / remote terminal sign-in
arkcli auth login --no-browser

# Check the sign-in status
arkcli auth status

# Install skills in your preferred AI agent, such as Claude Code, Cursor, TRAE, or Gemini CLI
arkcli +connect
```

**Install and sign in through an AI agent**

Copy the following prompt to an AI agent, such as Claude Code, Codex, Cursor, or TRAE. The AI agent will install Ark CLI automatically.

```Plaintext
Install Ark CLI for me by using the following command: https://sf16-sg.tiktokcdn.com/obj/eden-sg/psjryh/ljhwZthlaukjlkulzlp/intro/byteplus.md
```

> **warning**

> To uninstall the skills, run `arkcli +connect uninstall` in a terminal.

### 2. Use Ark CLI

After configuration is complete, restart your AI agent. You can then describe your request in natural language. The following table provides some examples.

|What you want to do |Message to send to the AI agent |
|---|---|
|Query models |Show me the Dola and Dreamina models available on ModelArk. |
|Query usage |Show me how many tokens this account has used today and this week. |
|Query bills |Show me the latest bill and cost details. |
|Query plans |Show me the current Coding Plan subscription and seat information. |
|Change an API Key |Replace the API Key in the test profile with a valid one. |
|Chat with a model |Use a Dola model to answer this question: Should I carry an umbrella in Beijing today? |
|Understand an image |Describe what is in this image. |
|Understand a document |Extract the key information from this PDF. |
|Generate an image |Generate an image of a Shiba Inu running under cherry blossoms. |
|Generate a video |Generate a five\-second video of a cat jumping. |
|Deploy an endpoint |Deploy a test endpoint and tell me how to invoke it. |
|Clean up resources |Delete the test endpoint, generation task, and chat response that were just created. |

## Core capabilities

|Domain |Capabilities |Command |
|---|---|---|
|Authentication and identity |Sign in to ModelArk, check authentication status and the current identity, generate and switch API Keys, and switch between and manage multiple profiles. |`arkcli auth`, `arkcli profile` |
|Chat and inference |Use multimodal and multi\-turn chat, stream output, and configure thinking\-related parameters. |`arkcli +chat` |
|Image and video generation |Generate images or videos from text, images, or reference assets. |`arkcli +gen` |
|Multimodal understanding |Understand inputs such as images, documents, videos, and audio. |`arkcli +understand` |
|Model discovery |List and search foundation models, view model details and supported parameters, and check available resources. |`arkcli models` |
|Inference resources and deployment |Create, query, update, start, and stop inference endpoints, or quickly deploy an inference endpoint for a specified model. |`arkcli infer`, `arkcli +deploy` |
|Model fine\-tuning |Create and manage fine\-tuning jobs for SFT, LoRA, DPO, and RL workflows; validate datasets; preview hyperparameters and costs; and export and deploy model artifacts. |`arkcli train` |
|Managed Agents |Create, inspect, update, debug, and interact with BytePlus ModelArk Managed Agents and their sessions, skills, files, and MCP integrations. |`arkcli agent` |
|Documentation search |Search ModelArk documentation, retrieve a ModelArk document by URL, and list documentation pages with pagination. |`arkcli docs` |
|Usage statistics |Query inference usage, free quota or resource pack balances, subscription usage, per\-model usage details, and team seat usage. |`arkcli usage` |
|Billing and pricing |Query itemized ModelArk bills and break down costs by billing cycle, endpoint, API Key, or product; query model and plan prices. |`arkcli billing`, `arkcli pricing` |
|Plans and seats |Query, purchase, and renew Coding Plan subscriptions; view supported models; and manage personal or team plans. |`arkcli plans` |
|AI agent integration |Automatically detect local AI agents, and install, view, or uninstall Ark CLI skills. |`arkcli +connect` |
|Diagnostics |Provides a unified diagnostics entry point for CLI health, account, error, infer\-endpoint, model, and metrics. |`arkcli doctor` |

## Command syntax

Ark CLI uses the following command structure:

```bash
# Shortcut command
arkcli +<shortcut> [flags]

# Domain command
arkcli <domain> <resource> <verb> [flags]

# Domain shortcut command
arkcli <domain> +<shortcut> [flags]

# Raw API call
arkcli api <registered-action> --params '{...}'
```

Use `--help` to view detailed usage information for any command:

```bash
arkcli --help
arkcli +chat --help
arkcli models search --help
arkcli train finetune create --help
```

### Global flags

|Flag |Description |
|---|---|
|`--api-key <key>` |Override the default API Key. |
|`--base-url <url>` |Specify a custom data\-plane API base URL. |
|`--debug` |Output request and response debugging information. |
|`--dry-run` |Preview request metadata without sending the request. |
|`--page-all` |Automatically retrieve data from all pages. |
|`--page-delay` |Set the delay between page requests. The default value is 200 milliseconds. |
|`--page-limit <n>` |When used with `--page-all`, limit the maximum number of pages to retrieve. The default value is 10. |
|`--profile <name>` |Specify the profile to use. |
|`--project-name <name>` |Override the current project. |
|`--format json` |Set the output format to JSON, which is the default format. |
|`--region <region>` |Override the current region. |
|`--transform <path>` |Process the output using a GJSON expression. |
