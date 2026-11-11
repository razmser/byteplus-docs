# Configure cloud environments

This topic describes how to create and manage cloud sandbox environments for ModelArk Managed Agents, including environment templates, resource specifications, outbound network policies, prebuild scripts, environment variables, startup scripts, and artifact storage.

**Environment** describes the sandbox template used by the agent at runtime. An environment can be reused by multiple sessions, but each session starts an independent sandbox instance, and their file system states are isolated from each other.

Managed Agents supports both cloud environments and self\-hosted environments. This topic only describes cloud environments where sandboxes are managed by ModelArk. If you need to execute tools or access internal network resources in enterprise infrastructure, choose a self\-hosted environment. For details, see [Self-hosted environment overview](self-hosted-environment-overview.md).

## Create environment

You can create an environment on the [Environments](https://ai.byteplus.com/ark/region:ap-southeast-1/managed-agents/environments?projectName=default) page in the console, or use the API.

In the console, click **Create environment** and configure the following items as needed:

* **Type**: Select the cloud\-hosted environment.

* **Environment template**: Select a base template.`base` is suitable for standard command\-line, code execution, and file processing tasks; `browser` is suitable for web access, page parsing, or automation tasks that require browser runtime dependencies.

* **Resource specification**: Select the CPU and memory specification for the cloud sandbox. If no option is selected, `1x` is used by default.

* **Preinstalled packages**: Configure the system packages and language dependencies installed during the prebuild phase.

* **Prebuild script**: Configure the build script to run after dependencies are installed.

* **Environment variables**: Configure non\-sensitive key\-value pairs to inject into the sandbox process.

* **Initialization script**: Set the initialization commands to run when each session starts, such as creating directories or pulling data required for the current task.

* **Product storage**: Optional. When you need to retain agent artifacts long\-term, select your own TOS directory. If you do not select one, ModelArk public TOS bucket is used.

> **Tip**

> Use a clear and unique name when creating an environment, so you can distinguish different purposes such as development, testing, and production.

Use the following code sample to create an environment by using the API:

```bash
environment=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/environments" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<'EOF'
{
  "name": "<ENVIRONMENT_NAME>",
  "config": {
    "type": "cloud",
    "networking": {
      "type": "unrestricted"
    },
    "template_id": "browser",
    "spec": "4x",
    "packages": {
      "pip": ["pandas"],
      "apt": ["curl"]
    },
    "build_script": "python -c \"import pandas; print('prebuild ready')\"",
    "env": {
      "MY_KEY_0": "value_0",
      "MY_KEY_1": "value_1"
    },
    "setup_script": "mkdir -p /workspace/bootstrap && echo ready > /workspace/bootstrap/status.txt"
  }
}
EOF
)

ENVIRONMENT_ID=$(jq -er '.id' <<<"$environment")

echo "Environment ID: $ENVIRONMENT_ID"
```

Main configuration items:

* **`name`**: The environment name, which must be unique in the current project.

* **`description`**: The description of the environment.

* **`config.type`**: The runtime environment type. For a cloud\-hosted environment, the value is `cloud`.

* **`config.networking.type`**: The outbound network access policy for the sandbox.`unrestricted` does not restrict access targets, but it is still subject to the platform's general security blocking policies. `limited` allows access only to explicitly configured domain names and network rules required for platform operation.

* **`config.template_id`**: The cloud environment template.`base` is the default template and is suitable for standard command\-line and code execution tasks; `browser` includes preinstalled browser runtime dependencies and is suitable for tasks that need to access web pages or perform browser automation. For template differences, see [Environment template](cloud-sandbox-reference.md#environment-templates).

* **`config.spec`**: The resource specification for the cloud sandbox. If this parameter is not configured, `1x` is used by default.`1x`, `2x`, `4x`, and `8x` correspond to 2, 4, 8, and 16 vCPUs, respectively. Each vCPU is paired with 2 GiB of memory. For optional specifications and resource mappings, see [Sandbox specifications](cloud-sandbox-reference.md#sandbox-specs).

* **`config.packages`**: Install system packages and language dependencies during the prebuild phase. After configuration, sessions that use this environment can use these dependencies directly without reinstalling them. Dependency versions can be explicitly specified. If no version is specified, the latest version is installed.

   |Parameter |Package manager |Example |
   |---|---|---|
   |`apt` |System packages (apt\-get) |`"ffmpeg"` |
   |`cargo` |Rust (cargo) |`"ripgrep@14.0.0"` |
   |`gem` |Ruby (gem) |`"rails:7.1.0"` |
   |`go` |Go modules |`"golang.org/x/tools/cmd/goimports@latest"` |
   |`npm` |Node.js (npm) |`"express@4.18.0"` |
   |`pip` |Python (pip) |`"pandas==2.2.0"` |

* **`config.env`**: Environment variables injected into the sandbox process. Configure this when the agent needs to read non\-sensitive business parameters, such as a time zone or service address.

* **`config.build_script`**: Executes after `config.packages` is installed, and is used to download dependencies in advance, compile projects, or generate files that can be reused by subsequent sessions.

* **`config.setup_script`**: The initialization script executed during the sandbox startup phase in each session. Configure this when you need to prepare directories for the current session, pull runtime data, or read environment variables and session files.

* **`config.tos`**: The TOS storage location for the agent's final artifacts. Configure this when you need to retain reports, files, or code generated by the agent long\-term. If this is not configured, artifacts use ModelArk public TOS.

> **Note**

> `config.networking`, `config.template_id`, `config.spec`, `config.packages`, `config.build_script`, `config.env`, `config.setup_script`, and `config.tos` apply only to `cloud` environments. When creating or updating a self\-hosted environment, pass only `config.type: self_hosted`.

> **Tip**

> The server validates the size of the complete `config`. If the configuration after creation or update exceeds the current limit, the API returns HTTP 400 `InvalidParameter`, and the error message includes the maximum allowed number of bytes. The update API validates the complete merged configuration.

### Prepare dependencies and initialization content for sessions

When creating an environment, you can prepare dependencies and files that multiple sessions will use in advance, or configure separate initialization logic for each session.

Choose `config.packages`, `config.build_script`, or `config.setup_script` based on whether the content needs to be reused across multiple sessions.

|Your requirement |Recommended configuration |Result |
|---|---|---|
|Each session needs direct access to the same set of system packages, language dependencies, or fixed\-version tools. |`config.packages` |Sessions created with this environment can use these dependencies directly without running installation commands again. |
|Compile the project, download base model files, or generate caches so that later sessions can reuse the content. |`config.build_script` |The script runs after dependency installation is complete. The generated files, caches, and build artifacts can be used by later sessions. |
|Prepare directories, pull short\-term data, or read environment variables and session files for the current task. |`config.setup_script` |It runs once when each session starts and prepares runtime content only for the current session. |

> **Tip**

> Do not install dependencies that multiple sessions need to use in `config.setup_script`; otherwise, they are installed repeatedly each time a session starts. Put fixed dependencies in `config.packages`; put post\-install compilation, downloads, and cache generation in `config.build_script`.

## Configure outbound network policy

Cloud environments support two outbound network policies. When selecting a policy, first determine whether the agent's tool execution needs to access arbitrary public internet targets or only a small number of fixed domain names.

|Policies |Supported use cases |Key configuration |
|---|---|---|
|`unrestricted` |The sandbox needs to access multiple public internet targets that are not fixed, such as general web crawling, temporary resource downloads, or debugging external APIs. |You only need to set `config.networking.type` to `unrestricted`. |
|`limited` |The sandbox should access only fixed external services, such as enterprise APIs, specified object storage domains, or fixed callback URLs. |Set `config.networking.type` to `limited` and list the allowed domain names in `allowed_hosts`. |

To create a cloud environment with limited outbound access, see the following example:

```bash
environment=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/environments" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<'EOF'
{
  "name": "<ENVIRONMENT_NAME>",
  "config": {
    "type": "cloud",
    "networking": {
      "type": "limited",
      "allowed_hosts": [
        "api.example.com",
        "*.example-cdn.com"
      ],
      "allow_package_managers": true
    },
    "packages": {
      "pip": ["requests"]
    }
  }
}
EOF
)

ENVIRONMENT_ID=$(jq -er '.id' <<<"$environment")

echo "Environment ID: $ENVIRONMENT_ID"
```

The limited outbound access policy includes the following configuration items:

* `allowed_hosts`: A list of external domain names that the sandbox is allowed to access directly. Only bare domain names or leftmost wildcard domain names are supported, such as `api.example.com` and `*.example-cdn.com`. Do not enter URLs, ports, paths, query parameters, IP addresses, or `localhost`.

* `allow_package_managers`: Whether to additionally allow package managers to access the platform\-configured package repository domains. After this is enabled, the sandbox can access the corresponding package repositories for `apt`, `cargo`, `gem`, `go`, `npm`, and `pip` dependencies. When it is disabled or omitted, `packages` will not automatically allow access to external package repositories.

> **Note**

> `limited` is a fail\-closed policy. If `allowed_hosts` is omitted and `allow_package_managers` is not enabled, the sandbox does not allow access to additional external domain names. After you update an environment, only sessions created or upgraded later use the new policy. Existing sessions continue to use the configuration frozen at creation time.

## Configure artifact storage

When you need to store artifacts long\-term in your own TOS bucket, configure `config.tos` for the environment. To continue using ModelArk public TOS, omit this parameter.`config.tos` controls the persistent location used when the platform registers output artifacts with the Files API. It is not the same as the sandbox mount directory with `type: tos` in `resources`.

|Your configuration |Artifact location |What you need to do |
|---|---|---|
|Do not configure `config.tos` |ModelArk managed TOS bucket |You need to download the required files before the platform TTL expires. When a session is deleted, the platform cleans up associated artifacts in the default storage. |
|Configure `config.tos` |Your TOS bucket. When the platform registers artifacts from the outputs directory, the Files API writes them with the prefix `{prefix}outputs/{env-id}/{session-id}/`; the final object path is based on `tos.object_key` in the response. |Manage the object lifecycle in TOS. Deleting a session does not delete objects in the bucket. |

To write artifacts to your own TOS bucket, the following conditions must be met:

* The target bucket has been authorized in the ModelArk project. For details, see [User TOS authorization](project-configuration.md#4eb1b277).

* The bucket and the Managed Agents service must be deployed in the same region. Cross\-region buckets are rejected during the precheck phase.

* `bucket` and `prefix` must be provided together. You cannot configure only one of the two parameters.

* `prefix` uses a relative path, such as `ark-files/`. The prefix does not need to be created in advance.

* When the platform registers artifacts from the outputs directory, it appends `outputs/{env-id}/{session-id}/` after this prefix as the Files API write prefix. The final object path is determined by `tos.object_key` in the response and usually also includes `ark_processed/{file_id}/{file_type}/{filename}`.

* When you call the Files API directly and pass in `tos[prefix]`, no environment ID or session ID is appended, but the Files API still generates a server\-side processing directory under that prefix.

The following example shows how to create an environment with artifact storage and update the storage location for subsequent sessions:

```Bash
# Create an environment that stores outputs in your TOS bucket.
environment=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/environments" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<'EOF'
{
  "name": "<ENVIRONMENT_NAME>",
  "config": {
    "type": "cloud",
    "networking": {
      "type": "unrestricted"
    },
    "tos": {
      "bucket": "<TOS_BUCKET>",
      "prefix": "ark-files/"
    }
  }
}
EOF
)

ENVIRONMENT_ID=$(jq -er '.id' <<<"$environment")
echo "Environment ID: $ENVIRONMENT_ID"
# Example output: Environment ID: env-20260723142038-xxxxx

# Replace the output storage configuration for future sessions.
updated_environment=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/environments/$ENVIRONMENT_ID" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<'EOF'
{
  "config": {
    "tos": {
      "bucket": "<NEW_TOS_BUCKET>",
      "prefix": "new-prefix/"
    }
  }
}
EOF
)

jq '{id, tos: .config.tos}' <<<"$updated_environment"
# Example output:
# {
#   "id": "env-20260723142038-xxxxx",
#   "tos": {"bucket": "<NEW_TOS_BUCKET>", "prefix": "new-prefix/"}
# }
```

> **Note**

> Update the environment first, and then create sessions that need to use the new storage configuration. Existing sessions continue to use the storage configuration from when they were created and do not automatically switch to the new bucket or `prefix`.

## Use an environment in a session

After creating an environment, pass `environment_id` when creating a session to reuse the runtime configuration and artifact storage configuration of that environment.

```bash
session=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/sessions" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<EOF
{
  "agent": "$AGENT_ID",
  "environment_id": "$ENVIRONMENT_ID",
  "title": "Quickstart session"
}
EOF
)

SESSION_ID=$(jq -er '.id' <<<"$session")

echo "Session ID: $SESSION_ID"
```

If only a specific task needs to use a different artifact storage location, you can use an environment override when creating a session instead of modifying the reusable environment. For details, see [Override output storage](start-session.md#override-output-storage).

## Understand the relationship between environments and sessions

* Multiple sessions can reference the same environment, but each session gets an independent sandbox instance.

* File system state is not shared between sessions.

* The environment itself is not versioned. If you frequently update environment configurations, we recommend recording changes on the business side so that you can trace which version of the configuration a session used.

* After an environment is archived, existing sessions continue to use the environment configuration saved at creation time. You can no longer update the environment or use it to create new sessions.

* Before deleting an environment, you must delete all associated sessions. Sessions whose `status` is `terminated` or whose `archived_at` has a value still retain the association and must also be deleted first.

## Manage environments

You can list, view, update, archive, or delete environments.

```bash
# List environments
environments=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/environments" \
    -H "Authorization: Bearer $ARK_API_KEY"
)

# Retrieve a specific environment
environment=$(
  curl -sS --fail-with-body "https://ark.ap-southeast.bytepluses.com/api/v3/environments/$ENVIRONMENT_ID" \
    -H "Authorization: Bearer $ARK_API_KEY"
)

# Update environment name, description, and prebuild config
environment=$(
  curl -sS --fail-with-body -X POST "https://ark.ap-southeast.bytepluses.com/api/v3/environments/$ENVIRONMENT_ID" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d @- <<'EOF'
{
  "name": "<UPDATED_ENVIRONMENT_NAME>",
  "description": "<UPDATED_ENVIRONMENT_DESC>",
  "config": {
    "spec": "8x",
    "build_script": "python -c \"print('updated prebuild')\""
  }
}
EOF
)

# Archive an environment while retaining its configuration
environment=$(
  curl -sS --fail-with-body \
    "https://ark.ap-southeast.bytepluses.com/api/v3/environments/$ENVIRONMENT_ID/archive" \
    -H "Authorization: Bearer $ARK_API_KEY" \
    -H "Content-Type: application/json" \
    -d '{}'
)

# Delete an environment (only if no sessions reference it)
curl -sS --fail-with-body -X DELETE \
  "https://ark.ap-southeast.bytepluses.com/api/v3/environments/$ENVIRONMENT_ID" \
  -H "Authorization: Bearer $ARK_API_KEY"
```

When updating an environment, you can modify its name, description, and runtime configuration. For parameter rules and impact scope, see [Update environment](update-environment-api.md).

When you no longer want new sessions to use an environment but still need to keep its configuration for queries, choose [Archive environment](archive-environment-api.md). When you are sure you no longer need to keep the environment, delete all associated sessions first, and then [Delete environment](delete-environment-api.md).

Archiving only changes the availability of the environment configuration. It does not migrate or back up the cloud sandbox, temporary artifacts in ModelArk public TOS, or objects in your own TOS bucket. Existing sessions continue to use the environment configuration saved at creation time.

## Preinstalled runtimes

Cloud sandboxes include common language runtimes, databases, and tools by default. To confirm the specific built\-in versions, directory purposes, or full list, see [Cloud sandbox reference](cloud-sandbox-reference.md).
