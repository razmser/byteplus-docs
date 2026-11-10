# Cloud sandbox reference

This topic describes the programming languages, databases, common utilities, and basic resource specifications preinstalled in the Managed Agents cloud sandbox, so you can quickly reference them when configuring environments or evaluating task dependencies.

The cloud sandbox runs in an isolated Linux container provided by ModelArk. Runtimes, database clients, and common command\-line tools are preinstalled and can be used directly by the agent.

> The specifications described in this topic apply to `cloud` environments.

## Programming languages

Common programming languages and their corresponding package management tools are preinstalled and can be used directly for script execution, project builds, and dependency installation.

|Programming languages |Version |Package management tool |
|---|---|---|
|Python |3.12+ |pip, uv |
|Node.js |20+ |npm, yarn, pnpm |
|Go |1.25+ |go modules |
|Rust |1.77+ |cargo |
|Java |21+ |maven, gradle |
|Ruby |3.3+ |bundler, gem |
|PHP |8.3+ |composer |
|C/C++ |GCC 13+ |make, cmake |

## Databases

Lightweight databases and database clients are provided by default, suitable for local data processing or connecting to external databases/instances.

|Databases |Description |
|---|---|
|SQLite |Preinstalled and ready to use |
|PostgreSQL client |`psql` client for connecting to external databases. |
|Redis client |`redis-cli` for connecting to external Redis instances. |

* SQLite can be used locally.

* Database servers, such as PostgreSQL and Redis, do not run in the sandbox environment by default. The sandbox provides client tools for connections.

## Utilities

Multiple categories of commonly used tools are built in, covering scenarios such as system operations, development and builds, file search, process inspection, and text processing.

### System tools

|Tool |Description |
|---|---|
|`git` |Version control. |
|`curl`, `wget` |HTTP clients. |
|`jq` |JSON processing. |
|`tar`, `zip`, `unzip` |Archiving, compression, and decompression tools. |
|`ssh`, `scp` |Remote access and file transfer tools (network access required). |
|`tmux`, `screen` |Terminal multiplexing tools. |

### Development tools

|Tool |Description |
|---|---|
|`make`, `cmake` |Build systems. |
|`ripgrep` (`rg`) |Fast file search. |
|`tree` |Directory structure visualization. |
|`htop` |Process monitoring. |

### Text processing

|Tool |Description |
|---|---|
|`sed`, `awk`, `grep` |Streaming text processing tools. |
|`vim`, `nano` |Text editors. |
|`diff`, `patch` |File comparison and patch tools. |

## Sandbox specifications

The following are the basic resource specifications of the cloud sandbox, which can be used to evaluate runtime resources and network capabilities.

|Attributes |Configuration |
|---|---|
|Operating system |Ubuntu 22.04 LTS |
|Architecture |x86_64 (amd64) |
|Memory |4 GB |
|Disk space |10 GB |
|Network |Enabled by default |

## Key directories and agent permissions

The file system of the cloud sandbox is divided by purpose into three categories: **workspace**, **mount directory**, and **temporary directory**. Under the mount directory `/mnt`, read\-only knowledge and resources (memory, skills, uploaded files) are further separated from read/write outputs and storage (outputs, storage). The agent accesses these directories through standard file tools, and permissions are enforced by the platform.

### Directory layout

```text
/
├── workspace/                # workspace, read/write, the agent's main working directory
│   └── AGENTS.md             # system prompt, read-only
├── mnt/                      # mount directory
│   ├── memory/               # agent memory, read-only, see persistent-memory
│   ├── skills/               # agent skills, read-only, see skills
│   └── session/
│       ├── uploads/          # user-uploaded file mount directory, read-only, see upload-and-mount-files
│       ├── outputs/          # agent output directory, read/write, registered and downloaded through Files API
│       └── storage/          # TOS directory mounted through session resources, read/write
└── tmp/                      # temporary directory, read/write
```

### Directory permissions

|Directory |Purpose |Agent read permission |Agent write permission |
|---|---|---|---|
|`$HOME` |User home |✓ |✓ |
|`/workspace` |Workspace, the agent's main working directory<br><br>**Note**: `/workspace/AGENTS.md` (system prompt) has read\-only permissions. |✓ |✓ |
|`/mnt` |Mount directory |✓ |✓ |
|`/mnt/memory` |Agent memory directory, see [Memory](persistent-memory.md). |✓ |✗ |
|`/mnt/skills` |Agent skills directory, see [Skills](skills.md). |✓ |✗ |
|`/mnt/session/uploads` |Files API uploaded file mount directory, see [Upload and mount files](upload-and-mount-files.md). |✓ |✗ |
|`/mnt/session/outputs` |Agent output directory. Files written here are registered with the Files API; when you need to write artifacts to your own TOS bucket, configure `config.tos`. |✓ |✓ |
|`/mnt/session/storage` |The session's TOS resource mount directory. For details, see [Upload and mount files](upload-and-mount-files.md). |✓ |✓ |
|`/tmp` |Temporary directory |✓ |✓ |
|Any other path |\- |✗ |✗ |

### Distinguish artifact storage from TOS resource mounts

Both `/mnt/session/outputs` and `/mnt/session/storage` may be associated with TOS, but they serve different purposes:

|Capability |Sandbox path |Purpose |
|---|---|---|
|Environment output storage |`/mnt/session/outputs` |The agent writes final deliverables. If `config.tos` is not configured, ModelArk public TOS is used; configure this parameter when you need to write to your own TOS bucket. The sandbox path used by the agent remains unchanged. |
|Session TOS resource |`/mnt/session/storage` |Mount existing TOS objects to the sandbox as session inputs or shared data, configured through `resources`. |

For details about configuring environment output storage, see [Configure cloud environment](configure-cloud-environment.md#configure-output-storage).

### Usage rules

When reading and writing files in the sandbox, the agent should follow these rules to avoid accidentally modifying read\-only resources or leaving intermediate artifacts in user\-visible directories:

* **Main working directory**: Use `/workspace` to store the agent's main work, such as project files, intermediate build artifacts, and script execution results.

* **Read user input**: Read files uploaded by users through the Files API from `/mnt/session/uploads`; this directory is read\-only. To modify files, create derived files under `/workspace` or `/mnt/session/outputs`.

* **Write final artifacts**: Write final deliverables that need to be returned to the user to `/mnt/session/outputs`; files written to this directory are registered with the Files API and stored in the location specified by the environment or session override.

* **Reuse data across sessions**: When multiple sessions need to read and write the same set of business files, mount a TOS directory to `/mnt/session/storage` through session `resources`.

* **Temporary files**: Use `/tmp` to store temporary caches and intermediate command results; its lifecycle follows the session.

* **Read\-only mounts**: Do not modify `/mnt/memory`, `/mnt/skills`, or `/mnt/session/uploads`; these directories hold the agent's memory, capabilities, and user input resources.

* **Report artifact paths**: If final deliverable files are generated, tell the user the corresponding file paths in the final response.
