# Ark CLI guide for Coding Plan

<span id="9066e158"></span>
# 1. Install and sign in to Ark CLI

**Environment requirement:**  Node.js 16 or later


<Tabs>
<Tab zoneid="TihNbjuudw" title="Install and sign in manually">
<TabTitle>Install and sign in manually</TabTitle>

<span id="d1e731f9"></span>
## Install Ark CLI

Run the following commands in a terminal:

```Bash
# 1. Install the latest version through npm
npm install -g @byteplus/ark-cli@latest

# 2. Verify the installation
arkcli --version
```


<span id="e0e22070"></span>
## Sign in to Ark CLI

```Bash
# Sign in
arkcli auth login

# Check the sign-in status
arkcli auth status
```



</Tab>
<Tab zoneid="fLlmxtWyCD" title="Install and sign in through an AI agent">
<TabTitle>Install and sign in through an AI agent</TabTitle>

Copy the following prompt to an AI agent, such as Claude Code, Codex, Cursor, or TRAE. The AI agent will install Ark CLI automatically.

```Plain
Install Ark CLI for me by using the following command: https://sf16-sg.tiktokcdn.com/obj/eden-sg/psjryh/ljhwZthlaukjlkulzlp/intro/byteplus.md
```



</Tab>
</Tabs>


<span id="897067d3"></span>
# 2. Configure Coding Plan

> Ark CLI currently supports mainstream tools such as Claude Code, Codex, OpenCode, OpenClaw, and TRAE. Support for more AI agents is being added.


Run `arkcli helper` in a terminal, then follow the TUI prompts to configure the plan and model.

To configure Claude Code for Coding Plan, complete the following steps in the TUI:

<video src="https://arkdocs-en.tos-ap-southeast-1.volces.com/videos/Arkcli/cp-personal.mp4" controls></video>


Select the Coding Plan profile, select a model, and then select Claude Code.

<span id="c799b518"></span>
# 3. Use Ark CLI

<span id="d3f63cc8"></span>
## Capability overview

Ark CLI commands for Coding Plan cover three areas: **plan transactions, configuration, and usage**. For more capabilities, see the [Ark CLI guide](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/2536875).


* Plan transactions: Purchase or renew a plan, and query the plans associated with the current account.

* Configuration: Configure a model, query the models supported by a plan, and check the configuration status.

* Usage: Query the remaining quota and usage.


<span id="e0a8c9ef"></span>
## Plan transactions


<Tabs>
<Tab zoneid="iBNs0XzO5b" title="Use an AI agent">
<TabTitle>Use an AI agent</TabTitle>


<span aceTableMode="list" aceTableWidth="1,2"></span>
|Capability |Example prompt |
|---|---|
|Purchase a plan |"Purchase a Coding Plan Pro subscription for me." |
|Renew a plan |"Renew my Coding Plan subscription for one month." |
|Query plans associated with the current account |"Which plans am I subscribed to, and what are their specifications?" |



</Tab>
<Tab zoneid="Fx42JoHpJM" title="Use a terminal">
<TabTitle>Use a terminal</TabTitle>

* Purchase a plan:

   ```Bash
   # arkcli plans buy --plan <PLAN_NAME> --type <PLAN_TYPE> [--duration <MONTHS>] [--yes]
   
   # Parameters:
   #     PLAN_NAME: coding-plan
   #     PLAN_TYPE: lite or pro
   #     MONTHS: An integer from 1 to 12. The default value is 1.
   #     --yes: Return the BytePlus console URL where you can complete the purchase. The CLI does not place the order or charge the account.
   
   # Preview the purchase price and get the BytePlus subscription URL
   arkcli plans buy --plan coding-plan --type pro --duration 1
   # Get the BytePlus console URL to complete the purchase
   arkcli plans buy --plan coding-plan --type pro --duration 1 --yes
   ```
   

* Renew a plan:

   ```Bash
   # arkcli plans renew --plan <PLAN_NAME> [--duration <MONTHS>] [--yes]
   
   # Parameters:
   # PLAN_NAME: coding-plan
   # MONTHS: An integer from 1 to 12. The default value is 1.
   # --yes: Return the BytePlus console URL where you can complete the renewal. The CLI does not renew the plan or charge the account.
   
   # Preview the renewal price and get the BytePlus subscription URL
   arkcli plans renew --plan coding-plan --duration 1
   # Get the BytePlus console URL to complete the renewal
   arkcli plans renew --plan coding-plan --duration 1 --yes
   ```
   

* Query plans associated with the current account:

   ```Bash
   arkcli plans get
   ```
   


</Tab>
</Tabs>


<span id="b4b4a12b"></span>
## Configuration


<span aceTableMode="list" aceTableWidth="1,2"></span>
|Capability |Example prompt |
|---|---|
|Query models supported by a plan |"Which models does Coding Plan Pro support?" |


<span id="fd51788d"></span>
## Usage


<span aceTableMode="list" aceTableWidth="1,2"></span>
|Capability |Example prompt |
|---|---|
|Query the remaining quota |"How much Coding Plan quota do I have left this month?" |
|Query five\-hour usage |"How much of my five\-hour quota is left?" |
|Query model usage |"How much quota did I use with dola\-seed\-2.0\-lite this week?" |
