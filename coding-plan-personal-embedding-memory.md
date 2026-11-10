# Memory enhancement - embedding model

ModelArk Coding Plan provides embedding models that enable tools to generate semantic vectors—converting conversations, documents, and task\-related information into high\-dimensional representations for efficient similarity\-based retrieval.

Compared with traditional keyword matching, embedding\-based retrieval is more accurate in scenarios with large information volumes, long conversations, and scattered materials, significantly reducing misses and false matches. In addition, embeddings provide a unified semantic retrieval layer for memory retrieval, knowledge\-base Q&A, and tool calling, helping tools improve cross\-turn context tracking and overall quality without increasing the main model's inference cost.

<span id="1eea923e"></span>
# Core configuration

The following core configuration is required when using embedding models in Coding Plan:


* Supported embedding models: `skylark-embedding-vision`

* Dedicated Base URL: `https://ark.ap-southeast.bytepluses.com/api/coding/v3` (compatible with OpenAI API protocol)

* API Key: [Obtain API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)


<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">warning</div>


<div data-tips="true" data-tips-type="warning">We recommend using the same version of the embedding model consistently. Do not mix different versions.</div>


<span id="72de147a"></span>
# Configure

<div data-tips="true" data-tips-type="warning" data-tips-is-title="true">Note</div>


<div data-tips="true" data-tips-type="warning">As with other models, the Embedding model consumes plan quota based on the number of model calls.</div>


<span id="a4c2cd2f"></span>
## OpenClaw

OpenClaw supports configuring embedding models to solve the problems of limited native memory in large models, hallucinations, and inability to adapt to private data, thereby improving the model's accuracy and reliability when handling complex tasks.


1. Prerequisite: OpenClaw installation and configuration have been completed. For steps, see [OpenClaw (Clawdbot)](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/2183190).

2. Select any of the following methods to configure the embedding model.



<Tabs>
<Tab zoneid="I7m72pB5N6" title="Web UI">
<TabTitle>Web UI</TabTitle>

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


</Tab>
<Tab zoneid="uHhCpoxNVN" title="Terminal">
<TabTitle>Terminal</TabTitle>

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
   


</Tab>
</Tabs>


<span id="8691628e"></span>
## OpenViking

OpenViking is an open\-source context database designed for AI agents, supporting integration and access of multimodal embedding models, enabling vectorization of unstructured data, and providing efficient semantic similarity retrieval capabilities.


1. Prerequisite: OpenViking installation and basic configuration have been completed. For steps, see [OpenViking](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/ModelArk/2288685).

2. Add `embedding` related configuration in the configuration file `~/.openviking/ov.conf`. You need to replace `<ARK_API_KEY>` with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).


```JSON
{
  ...
  "embedding": {
    "dense": {
      "api_base" : "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
      "api_key"  : "<ARK_API_KEY>",
      "provider" : "volcengine",
      "dimension": 1024,
      "model"    : "skylark-embedding-vision"
    },
    "max_concurrent": 10
  }
  ...
}
```
