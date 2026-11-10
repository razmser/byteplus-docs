# OpenViking

**OpenViking** is a context database purpose\-built for AI agents, designed for scenarios such as multi\-agent collaboration, long\-term memory management, and complex task execution. It adopts a `viking://` filesystem paradigm to organize and manage an agent's **memories, resources, and skills** in a unified structure. With hierarchical, on\-demand loading, it supplies context only when needed—reducing token consumption while improving runtime efficiency. Directory\-based retrieval further enhances recall quality.

OpenViking supports **isolation across tenants, users, and agents**. After a session ends, it can automatically persist long\-term memory, enabling continuous capability accumulation and self\-optimization. This reduces the developer burden of context management and significantly improves the overall performance of agent frameworks such as OpenClaw.

ModelArk Coding Plan can be used with OpenViking. Follow this guide to configure and start using it.

> **Note**

> The OpenViking tool is undergoing rapid iteration. The version used in this document is **0.4.15**. If there are differences between actual usage and the steps below, see [OpenViking: Context database for AI agents](https://github.com/volcengine/OpenViking/blob/main/README.md).

> **Tip**

> You must first visit [ModelArk Coding Plan event](https://www.byteplus.com/en/activity/codingplan), subscribe to a plan as needed, and then configure and use the tool.

# Key configuration

## Model configuration

### VLM model

The VLM models and configuration methods supported by Coding Plan are as follows:

**Configure Model Name (specified in configuration file)**

Specify the Model Name in the tool configuration file to switch models in real time. Supported Model Names for configuration:

> **warning**

> When configuring Model Name, you can use all lowercase letters, and you can also directly copy the model name from the [activation management page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=model).

* `dola-seed-2.0-pro`

* `dola-seed-2.0-lite`

* `dola-seed-2.0-code`

* `glm-5.2`

* `glm-5.3-flash`

* `deepseek-v4-flash`

* `deepseek-v4-pro`

* `deepseek-v4.1-flash`

> **tip**

> The DeepSeek models are provided in compliance with applicable local laws and regulations.

**Configure ark-code-latest (console management)**

1. Specify the model as `ark-code-latest` in the configuration file.

2. Use [the activation management page](https://ai.byteplus.com/ark/region:ap-southeast-1/openManagement?LLM=%7B%7D&advancedActiveKey=model) to select or switch the target model. The change will take effect within 3–5 minutes.

   Supports Auto mode, which uses a dual\-dimension intelligent algorithm based on "effect + speed" to automatically select the model.

### Embedding model

The Embedding model supported by Coding Plan: `skylark-embedding-vision`

## Base URL

Use the dedicated Coding Plan Base URL: `https://ark.ap-southeast.bytepluses.com/api/coding/v3` (compatible with OpenAI API protocol)

> **Note**

> Please use the Coding Plan exclusive Base URL; otherwise, additional charges may apply.

## API key

[Get API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey)

# Install and configure OpenViking

## Prerequisites

* [Python 3.10 or higher](https://wiki.python.org/moin/BeginnersGuide/Download) is installed.

* [Go 1.22 or higher](https://golang.google.cn/dl/) is installed.

* A C++ compiler is installed, with GCC 9+ or Clang 11+.

> **Tip**

> Check C++ compiler support using the commands `clang++ --version` and <code>clang++ -std=c++17 -E -x c++ - < /dev/null</code>.

## Install

Run the following command to install OpenViking.

```Bash
pip install openviking --upgrade --force-reinstall
```

After installation, run the following command to view the installation result. If the version number is displayed, the installation is successful.

```Bash
openviking --version
```

## Configure

1. Create the configuration file `~/.openviking/ov.conf` with the following configuration information. The configuration information that needs to be modified is as follows:

   * `storage.workspace`: Replace with the actual workspace path.

   * `embedding`: Configure the Embedding model for vectorization and semantic retrieval.

      * `embedding.dense.model`: Supported Embedding model `skylark-embedding-vision`.

      * `embedding.dense.api_key`: Replace with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

   * `vlm`: Configure the VLM model for image and content understanding.

      * `vlm.model`: Supported VLM models, see [VLM model](coding-plan-personal-ai-openviking.md#vlm-model) for details.

      * `vlm.api_key`: Replace with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

```JSON
{
  "storage": {
    "workspace": "/home/your-name/openviking_workspace"
  },
  "log": {
    "level": "INFO",
    "output": "stdout"
  },
  "embedding": {
    "dense": {
      "backend": "volcengine",
      "api_key": "<ARK_API_KEY>",
      "model": "skylark-embedding-vision",
      "api_base": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
      "dimension": 1024,
      "input": "multimodal"
    }
  },
  "vlm": {
    "backend": "volcengine",
    "api_key": "<ARK_API_KEY>",
    "model": "dola-seed-2.0-pro",
    "api_base": "https://ark.ap-southeast.bytepluses.com/api/coding/v3",
    "temperature": 0.1,
    "max_retries": 3
  }
}
```

2. Set server configuration environment variables.

   Select the appropriate method to set environment variables based on your system environment.

> **Tip**

> If the configuration file is located elsewhere, you need to specify the corresponding file path in the environment variable.

**macOS/Linux**

Run the following command to set the environment variable to point to the corresponding configuration file.

```Bash
export OPENVIKING_CONFIG_FILE=~/.openviking/ov.conf
```

**Windows**

Select any of the following methods to set the environment variable to point to the corresponding configuration file.

* **PowerShell**

   ```PowerShell
   $env:OPENVIKING_CONFIG_FILE = "$HOME/.openviking/ov.conf"
   ```

* **CMD**

   ```cmd
   set "OPENVIKING_CONFIG_FILE=%USERPROFILE%\.openviking\ov.conf"
   ```

# Get started with OpenViking

1. Create the Python file `example.py` with the following code content.

   ```Python
   import openviking as ov
   
   # Initialize OpenViking client with data directory
   client = ov.OpenViking(path="./data")
   
   try:
       # Initialize the client
       client.initialize()
   
       # Add resource (supports URL, file, or directory)
       add_result = client.add_resource(
           path="https://raw.githubusercontent.com/volcengine/OpenViking/refs/heads/main/README.md"
       )
       root_uri = add_result['root_uri']
   
       # Explore the resource tree structure
       ls_result = client.ls(root_uri)
       print(f"Directory structure:\n{ls_result}\n")
   
       # Use glob to find markdown files
       glob_result = client.glob(pattern="**/*.md", uri=root_uri)
       if glob_result['matches']:
           content = client.read(glob_result['matches'][0])
           print(f"Content preview: {content[:200]}...\n")
   
       # Wait for semantic processing to complete
       print("Wait for semantic processing...")
       client.wait_processed()
   
       # Get abstract and overview of the resource
       abstract = client.abstract(root_uri)
       overview = client.overview(root_uri)
       print(f"Abstract:\n{abstract}\n\nOverview:\n{overview}\n")
   
       # Perform semantic search
       results = client.find("what is openviking", target_uri=root_uri)
       print("Search results:")
       for r in results.resources:
           print(f"  {r.uri} (score: {r.score:.4f})")
   
       # Close the client
       client.close()
   
   except Exception as e:
       print(f"Error: {e}")
   ```

2. Run `python example.py`. If the following output is displayed, OpenViking is running successfully.

   ```plaintext
   Directory structure:
   ...
   Content preview: ...
   
   Wait for semantic processing...
   Abstract:
   ...
   Overview:
   ...
   Search results:
     viking://resources/... (score: 0.8523)
     ...
   ```

# CLI server configuration and usage of OpenViking

To run OpenViking as a standalone HTTP service and connect from any client, refer to the following steps for configuration.

## Prerequisites

OpenViking has been installed and configured. For details, see [Install and configure OpenViking](coding-plan-personal-ai-openviking.md#install-openviking).

## Start the service

Execute the following command to start the service.

```Bash
openviking-server
```

After the service has started, execute the following command in a new command\-line interface to verify whether the service started successfully. If `"status":"ok"` is returned, the service has started successfully.

```Bash
curl http://localhost:1933/health
```

## Connect using CLI

1. Create the CLI connection configuration file `~/.openviking/ovcli.conf` with the following configuration information. Replace `<ARK_API_KEY>` with your own [API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/apikey).

   ```JSON
   {
     "url": "http://localhost:1933",
     "api_key": "<ARK_API_KEY>",
     "output": "table"
   }
   ```

2. Execute the following CLI command in a new command\-line interface.

   ```Bash
   # Check system health
   openviking observer system
   
   # Add a resource to memory
   openviking add-resource https://raw.githubusercontent.com/volcengine/OpenViking/refs/heads/main/README.md
   
   # List all synchronized resources
   openviking ls viking://resources
   
   # Query
   openviking find "what is openviking"
   ```

## Connect using cURL

After starting the service, you can also use the curl command in the command\-line interface to connect to OpenViking.

```Bash
# Add a resource
curl -X POST http://localhost:1933/api/v1/resources \
  -H "Content-Type: application/json" \
  -d '{"path": "https://raw.githubusercontent.com/volcengine/OpenViking/refs/heads/main/README.md"}'

# List resources
curl "http://localhost:1933/api/v1/fs/ls?uri=viking://resources/"

# Semantic search
curl -X POST http://localhost:1933/api/v1/search/find \
  -H "Content-Type: application/json" \
  -d '{"query": "what is openviking"}'
```

# Configure OpenViking memory functionality in OpenClaw

OpenViking provides persistent memory capability for OpenClaw. Once configuration is complete, OpenClaw will automatically remember important information from conversations and recall relevant content before responding.

## Prerequisites

OpenClaw has been installed and configured. For details, see [OpenClaw (Clawdbot)](https://ai.byteplus.com/ark/region:ap-southeast-1/docs/coding-plan-enterprise-ai-openclaw).

## Installation and configuration

For specific steps, see [Install OpenViking memory for OpenClaw](https://github.com/volcengine/OpenViking/blob/main/examples/openclaw-plugin/INSTALL.md).

After installation is complete, edit the configuration file `~/.openviking/ov.conf` and use a model supported by Coding Plan. For configuration information, see [Configure](coding-plan-personal-ai-openviking.md#ceac2da4).
