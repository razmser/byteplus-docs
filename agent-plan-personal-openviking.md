# OpenViking

OpenViking is a context database built for AI agents. It is primarily suited to multi\-agent collaboration, long\-term memory management, and complex task execution. It uses the `viking://` file system paradigm to organize agent memory, resources, and skills. Its hierarchical loading mechanism supplies context on demand, reducing token consumption and improving runtime efficiency, while directory\-based retrieval optimizes information recall. OpenViking supports multi\-tenant, multi\-user, and multi\-agent isolation. It can also automatically retain long\-term memory after a session ends, enabling continuous accumulation and self\-optimization of agent capabilities. This reduces the cost of context management for developers and significantly improves the overall performance of agents such as OpenClaw.

This topic describes how to configure and use ModelArk Agent Plan in OpenViking.

> **warning**

> OpenViking is evolving rapidly. This topic uses version **0.4.15**. If your usage differs from this topic, see [OpenViking: Context Database for AI Agents](https://github.com/volcengine/OpenViking/blob/main/docs/repository/README_CN.md).

## Prerequisites

You have subscribed to Agent Plan as needed. If you have not subscribed, visit the [Agent Plan console](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan) to subscribe as needed.

Your local environment must meet the following requirements:

* [Python 3.10 or later](https://wiki.python.org/moin/BeginnersGuide/Download) is installed.

* [Go 1.22 or later](https://golang.google.cn/dl/) is installed.

* A C++ compiler is installed: GCC 9 or later, or Clang 11 or later.

> **tip**

* > Run `clang++ --version` and <code>clang++ -std=c++17 -E -x c++ - < /dev/null</code> to check C++ compiler support.

* > If `pip install openviking` returns "No matching distribution found", run `python3 --version` to confirm that Python is version 3.10 or later.

## Install OpenViking

1. Run the following command to install OpenViking.

   ```Bash
   pip install openviking --upgrade --force-reinstall
   ```

2. Run the following command to check the installation.

   ```Bash
   openviking --version
   ```

   If the command returns a version number, OpenViking was installed successfully.

## Configure OpenViking

1. Create the `~/.openviking/ov.conf` configuration file, then update it as follows:

   * `storage.workspace`: Replace with the actual workspace path.

   * `embedding`: Configure a vectorization model for vectorization and semantic retrieval.

      * `embedding.dense.model`: Supported embedding model: `skylark-embedding-vision`.

      * `embedding.dense.api_key`: Replace with your own [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan).

   * `vlm`: Configure a language model for image and content understanding.

      * `vlm.model`: Supported language model. For details, see [Supported models and harness](agent-plan-personal-plan-overview.md#supported-models-harness).

      * `vlm.api_key`: Replace with your own [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan).

      ![图片](https://arkdocs-en.tos-ap-southeast-1.volces.com/images/CodingPlan/image-20260908-203322-616.png)

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
         "api_base": "https://ark.ap-southeast.bytepluses.com/api/plan/v3",
         "dimension": 1024,
         "input": "multimodal"
       }
     },
     "vlm": {
       "backend": "volcengine",
       "api_key": "<ARK_API_KEY>",
       "model": "dola-seed-2.1-turbo",
       "api_base": "https://ark.ap-southeast.bytepluses.com/api/plan/v3",
       "temperature": 0.1,
       "max_retries": 3
     }
   }
   ```

2. Set environment variables for your operating system.

   > **tip**

   > If the configuration file is in another location, specify its path in the environment variable.

   **macOS/Linux**

```Bash
   export OPENVIKING_CONFIG_FILE=~/.openviking/ov.conf
   ```

   **Windows**

Run the following command in PowerShell:

   ```Bash
   $env:OPENVIKING_CONFIG_FILE = "$HOME/.openviking/ov.conf"
   ```

   Or run the following command in CMD:

   ```Bash
   set "OPENVIKING_CONFIG_FILE=%USERPROFILE%\.openviking\ov.conf"
   ```

## Use OpenViking

> **tip**

> Start the service by running `openviking-server` in another terminal before running the following Python example.

1. Create an `example.py` file with the following code.

   ```Python
   from openviking_sdk import SyncHTTPClient
   
   # Initialize OpenViking HTTP client
   client = SyncHTTPClient(url="http://localhost:1933", api_key="<ARK_API_KEY>")
   
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
       client.wait_processed(timeout=120)
   
       # Get abstract and overview of the resource
       abstract = client.abstract(root_uri)
       overview = client.overview(root_uri)
       print(f"Abstract:\n{abstract}\n\nOverview:\n{overview}\n")
   
       # Perform semantic search
       results = client.find("what is openviking", target_uri=root_uri)
       print("Search results:")
       for r in results.get("resources", []):
           print(f"  {r['uri']} (score: {r['score']:.4f})")
   
       # Close the client
       client.close()
   
   except Exception as e:
       print(f"Error: {e}")
   ```

2. Run `python example.py`. The following output indicates that OpenViking is running successfully.

   ```Plain Text
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

## Other operations

### Configure and use OpenViking as a CLI server

To run OpenViking as a standalone HTTP service and connect from any client, follow these steps.

#### Prerequisites

OpenViking is installed and configured. For details, see [Install OpenViking](agent-plan-personal-openviking.md#install-openviking) and [Configure OpenViking](agent-plan-personal-openviking.md#configure-openviking).

#### Start the service

Run the following command to start the service.

```Bash
openviking-server
```

After it starts, run the following command in a new terminal to confirm that the service started successfully. A response containing `"status":"ok"` indicates success.

```Bash
curl http://localhost:1933/health
```

#### Connect with the CLI

1. Create the CLI connection configuration file `~/.openviking/ovcli.conf` with the following content. Replace `<ARK_API_KEY>` with your own [exclusive API Key](https://ai.byteplus.com/ark/region:ap-southeast-1/subscription/agent-plan).

   ```JSON
   {
     "url": "http://localhost:1933",
     "api_key": "<ARK_API_KEY>",
     "output": "table"
   }
   ```

2. Run the following CLI commands in a new terminal.

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

#### Connect with curl

After starting the service, you can also connect to OpenViking by running curl commands in a terminal.

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

### Configure OpenViking memory in OpenClaw

Use OpenViking to provide long\-term memory for OpenClaw. After configuration, OpenClaw automatically retains important information from conversations and recalls relevant content before responding.

#### Prerequisites

OpenClaw is installed and configured. For details, see [Install OpenClaw](agent-plan-personal-openclaw.md#b51c87f8) and [Configure OpenClaw](agent-plan-personal-openclaw.md#48c0b1ea).

#### Installation and configuration

For detailed instructions, see [Install OpenViking memory for OpenClaw](https://github.com/volcengine/OpenViking/blob/main/examples/openclaw-plugin/INSTALL-ZH.md).

After installation, edit `~/.openviking/ov.conf` and use a model supported by Agent Plan. For configuration details, see [Configure OpenViking](agent-plan-personal-openviking.md#configure-openviking).
