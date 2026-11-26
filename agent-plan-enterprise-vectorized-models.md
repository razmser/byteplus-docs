# Embedding models

Agent Plan Team provides embedding models that add semantic vector capabilities to your tools. They convert conversations, documents, task information, and other content into high\-dimensional vector representations for efficient retrieval based on semantic similarity.

Compared with conventional keyword matching, embedding retrieval recalls relevant content more accurately in scenarios with large volumes of information, long conversations, or distributed materials, substantially reducing omissions and false positives. Embeddings also provide a unified semantic retrieval layer for memory retrieval, knowledge\-base Q&A, and tool calls, helping tools improve multi\-turn context tracking and overall performance without increasing the inference cost of the primary model.

# Core configuration

To use embedding models with Agent Plan Team, configure the following:

* **Dedicated Base URL**: https://ark.ap\-southeast.bytepluses.com/api/plan/v3. The dedicated Base URL for Agent Plan Team includes `/plan`; do not use it interchangeably with other Base URLs.

* **Dedicated API Key**: [Get a dedicated API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine). Other ModelArk API keys, such as a Coding Plan API Key, cannot be used with Agent Plan Team.

   ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/agent-plan/agent-plan-enterprise-api-key.png)

* **Supported model**: `skylark-embedding-vision` (corresponding model: [skylark-embedding-vision-251215](https://ai.byteplus.com/ark/region:ap-southeast-1/model/detail?Id=skylark-embedding-vision))

> **warning**

* > We recommend consistently using the same embedding model version. Do not mix versions.

* > Embedding models support only Model Names specified in the configuration file. They cannot be switched through Auto mode or the console.

# Integrate embedding models

## OpenClaw

OpenClaw supports embedding model configuration, addressing the limitations of native LLM memory, hallucinations, and inability to adapt to private data to improve accuracy and reliability for complex tasks.

1. Prerequisite: OpenClaw is installed and configured. See [Install OpenClaw](agent-plan-enterprise-openclaw.md#b51c87f8) and [Configure OpenClaw](agent-plan-enterprise-openclaw.md#48c0b1ea).

2. Configure the embedding model by using either of the following methods.

   **Web UI**

1. Run the following command to open the Web UI.

         ```Bash
         openclaw dashboard
         ```

      2. In the left navigation pane, select **Configuration** \> **Settings** \> **Advanced**, then click **Open** to view and modify the configuration. Add the following `memorySearch` configuration under `agents.defaults`. Replace `<ARK_API_KEY>` with your [dedicated API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine).

         ```JSON
         {
           "agents": {
             "defaults": {
               "memorySearch": {
                 "provider": "byteplus",
                 "apiKey": "<ARK_API_KEY>",
                 "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/plan/v3",
                     "model": "skylark-embedding-vision",
                 "dimension": 1024
               }
             }
           }
         }
         ```

      3. After completing the configuration, save the configuration file and click **Update**. Reconnect to the service and enter the Web UI after the update is complete.

   **Terminal**

1. Run the following command in a terminal to open the OpenClaw configuration file.

         ```Bash
         nano ~/.openclaw/openclaw.json
         ```

      2. Add the following `memorySearch` configuration under `agents.defaults`. Replace `<ARK_API_KEY>` with your [dedicated API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan-enterprise/mine).

         ```JSON
         {
           "agents": {
             "defaults": {
               "memorySearch": {
                 "provider": "byteplus",
                 "apiKey": "<ARK_API_KEY>",
                 "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/plan/v3",
                     "model": "skylark-embedding-vision",
                 "dimension": 1024
               }
             }
           }
         }
         ```

      3. Restart the gateway for the configuration to take effect.

         ```Bash
         openclaw gateway restart
         ```
