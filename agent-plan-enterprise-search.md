# BytePlus Searchinfinity

BytePlus Searchinfinity is a search service built for large language models. Through flexible APIs, it provides search across multiple verticals and sources to supplement facts published after a model's training\-data cutoff, verify information sources, and retrieve timely content, improving the breadth and reliability of model responses.

# Capabilities

BytePlus Searchinfinity supports the following core capabilities. For details, see the [BytePlus Searchinfinity API documentation](https://docs.byteplus.com/en/docs/searchinfinity/Searchinfinity_available_APIs).

* **Web search**: Returns structured fields such as the title, source site, URL, summary, and body text, with up to 50 results per request.

* **Multimodal search**: Returns image content for a text query, including image titles, URLs, dimensions, and shapes.

* **Authoritative source filtering**: Restricts results to content from trusted official sites.

* **Time range filtering**: Supports preset ranges for the past day, week, month, or year, as well as custom date ranges.

* **Query rewriting**: Automatically rewrites long, conversational questions into search queries to improve recall.

# Using BytePlus Searchinfinity

## Step 1: Subscribe to a plan and enable BytePlus Searchinfinity

1. The administrator purchases a plan in [Agent Plan Team](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise?agentMode=close) and assigns seats to users. For plan details, see [Subscription overview](agent-plan-enterprise-plan-overview.md).

2. The administrator enables AFP deductions for BytePlus Searchinfinity on the **Harness management** tab.

3. Seat users enable AFP deductions in the Harness section.

4. Seat users obtain their dedicated API Key for BytePlus Searchinfinity integration to configure the Skill in their tool.

## Step 2: Use BytePlus Searchinfinity in your tools

You can currently use BytePlus Searchinfinity in tools through a Skill. For integration details, see the [AI Tool Integration Guide](https://docs.byteplus.com/en/docs/searchinfinity/AI_Tool_Integration_Guide).

### Skill integration

Use a Skill to quickly install and integrate BytePlus Searchinfinity into AI tools. The following configuration options cover commonly used AI tools. For other tools, install and use the BytePlus Searchinfinity Skill through commands or a conversation as needed.

**Claude Code**

1. Install and configure Claude Code. For instructions, see [Claude Code](agent-plan-enterprise-claude-code.md).

2. Run the following command in your terminal to install the BytePlus Searchinfinity Skill `byted-bp-searchinfinity-web-search`.

   ```Bash
   install-skill https://github.com/bytedance/agentkit-samples/tree/main/skills/byted-bp-searchinfinity-web-search
   ```

3. After installation, run `claude` to start Claude Code.

4. Provide the [dedicated Agent Plan Team API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine) in a conversation to configure the Skill.

5. After configuration, you can use BytePlus Searchinfinity in Claude Code.

**OpenCode**

1. Install and configure OpenCode. For instructions, see [OpenCode](agent-plan-enterprise-opencode.md).

2. Run the following command in your terminal to install the BytePlus Searchinfinity Skill `byted-bp-searchinfinity-web-search`.

   ```Bash
   install-skill https://github.com/bytedance/agentkit-samples/tree/main/skills/byted-bp-searchinfinity-web-search
   ```

3. After installation, run `opencode` to start OpenCode.

4. Provide the [dedicated Agent Plan Team API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine) in a conversation to configure the Skill.

5. After configuration, you can use BytePlus Searchinfinity in OpenCode.

**OpenClaw**

1. Install and configure OpenClaw. For instructions, see [OpenClaw](agent-plan-enterprise-openclaw.md).

2. Run the following command in your terminal to install the BytePlus Searchinfinity Skill `byted-bp-searchinfinity-web-search`.

   ```Bash
   install-skill https://github.com/bytedance/agentkit-samples/tree/main/skills/byted-bp-searchinfinity-web-search
   ```

3. After installation, run the following command to start OpenClaw.

   ```Bash
   openclaw tui
   ```

4. Enter the [dedicated Agent Plan Team API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine) in the conversation to configure the Skill.

5. After configuring the Skill, you can use BytePlus Searchinfinity in OpenClaw.

**Hermes Agent**

1. Install and configure Hermes Agent. For instructions, see [Hermes Agent](agent-plan-enterprise-hermes-agent.md).

2. Run the following command in your terminal to install the BytePlus Searchinfinity Skill `byted-bp-searchinfinity-web-search`.

   ```Bash
   install-skill https://github.com/bytedance/agentkit-samples/tree/main/skills/byted-bp-searchinfinity-web-search
   ```

3. After installation, start Hermes Agent.

4. Enter the [dedicated Agent Plan Team API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine) in a conversation to configure the Skill.

5. After configuration, you can use BytePlus Searchinfinity in Hermes Agent.

# User benefits

Each account receives 2,000 free calls per month. Once the free quota is exhausted, calls within the plan are charged at 3 AFP per call.

# FAQ

* How do I disable BytePlus Searchinfinity?

   BytePlus Searchinfinity is a postpaid service billed by usage. The official website does not currently support manually disabling the service. To request that the service be disabled, submit [Ticket](https://console.byteplus.com/workorder/create?step=2&SubProductID=P00001514).

* How do I view billable usage statistics for BytePlus Searchinfinity?

   You can view billable usage for BytePlus Searchinfinity in [Data management in the BytePlus Searchinfinity console](https://console.byteplus.com/search-infinity/web-search-interface).

# Service agreements

* [Specific Terms for the BytePlus Searchinfinity Service](https://docs.byteplus.com/en/docs/searchinfinity/Specific_Terms_for_the_Searchinfinity_Service)

* [BytePlus Searchinfinity Service Level Agreement](https://docs.byteplus.com/en/docs/searchinfinity/Byteplus_Searchinfinity_Service_Level_Agreement)
