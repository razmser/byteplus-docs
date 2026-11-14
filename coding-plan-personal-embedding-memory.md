# Memory enhancement - embedding model

ModelArk Coding Plan provides embedding models that enable tools to generate semantic vectors—converting conversations, documents, and task\-related information into high\-dimensional representations for efficient similarity\-based retrieval.

Compared with traditional keyword matching, embedding\-based retrieval is more accurate in scenarios with large information volumes, long conversations, and scattered materials, significantly reducing misses and false matches. In addition, embeddings provide a unified semantic retrieval layer for memory retrieval, knowledge\-base Q&A, and tool calling, helping tools improve cross\-turn context tracking and overall quality without increasing the main model's inference cost.

# Core configuration

The following core configuration is required when using embedding models in Coding Plan:

* Supported embedding models: `skylark-embedding-vision`

* Dedicated Base URL: `https://ark.ap-southeast.bytepluses.com/api/coding/v3` (compatible with OpenAI API protocol)

* API Key: [Obtain API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

> **warning**

> We recommend using the same version of the embedding model consistently. Do not mix different versions.

# Configure

> **Note**

> As with other models, the Embedding model consumes plan quota based on the number of model calls.

## OpenClaw

OpenClaw supports configuring embedding models to solve the problems of limited native memory in large models, hallucinations, and inability to adapt to private data, thereby improving the model's accuracy and reliability when handling complex tasks.

1. Prerequisite: OpenClaw installation and configuration have been completed. For steps, see [OpenClaw (Clawdbot)](coding-plan-personal-ai-openclaw.md).

2. Select any of the following methods to configure the embedding model.

**Web UI**

1. Execute the following command to open the Web UI.

```Bash
openclaw dashboard
```

2. In the left menu bar, select **configuration** \- **All Settings** \- **Raw**, and add `memorySearch` related configuration information under the `agents.defaults` node. The added configuration information is as follows. You need to replace `<ARK_API_KEY>` with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

   ```JSON
   {
     ...
     "agents": {
       "defaults": {
         ...
         "memorySearch": {
           "provider": "openai",
               "model": "skylark-embedding-vision",
           "remote": {
             "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
             "apiKey": "<ARK_API_KEY>"
           }
         }
         ...
       }
     }
     ...
    }
   ```

3. After configuration is complete, first click **Save** in the upper right corner to save, then click **Update** to update the configuration.

**Terminal**

1. Execute the following command in the terminal to open the OpenClaw configuration file.

```Bash
nano ~/.openclaw/openclaw.json
```

2. Add `memorySearch`\-related configuration information under the `agents.defaults` node. The configuration information to be added is as follows. You need to replace `<ARK_API_KEY>` with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

   ```JSON
   {
     ...
     "agents": {
       "defaults": {
         ...
         "memorySearch": {
           "provider": "openai",
               "model": "skylark-embedding-vision",
           "remote": {
             "baseUrl": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
             "apiKey": "<ARK_API_KEY>"
           }
         }
         ...
       }
     }
     ...
   }
   ```

3. After completing the configuration, restart the gateway to apply the configuration.

   ```Bash
   openclaw gateway restart
   ```
