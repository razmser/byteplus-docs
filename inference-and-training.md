# Inference & Training

Inference deployment and model training: the endpoint lifecycle, fine\-tuning jobs, and training data management.

* Interactive endpoint creation uses `+deploy` (with model clarification and reuse checks); scripted / CI / unguarded runs use the `infer` raw CRUD path;

* `train` covers fine\-tuning jobs (`mcj-*`), training data comes from `dataset` (`ds-*` / `dsv-*`), and the export artifact is a `cm-*` custom model;

* The export links to `models custommodel` and `infer`: register the model, then bring it online through the endpoint workflow.

## +deploy

Guided endpoint deployment: model clarification, reuse check, and profile default sync (guardrailed alternative to `infer endpoint create`)

```Bash
arkcli +deploy
```

|Flag |Type |Description |
|---|---|---|
|`--name` |string (required) |Endpoint name |
|`--description` |string |Endpoint description |
|`--model` |string (required) |Model ID (Foundation model or Custom model) |
|`--model-unit-id` |string |Model unit ID |
|`--rate-limit` |string |Rate limit configuration (JSON string) |
|`--batch-only` |bool |Batch only mode |
|`--moderation` |string |Moderation configuration (JSON string) |
|`--data-delivery` |bool |Data delivery mode |
|`--dedicated-to-ptu` |bool |Dedicated to PTU |
|`--dedicated-to-dynamic-ptu` |bool |Dedicated to dynamic PTU |
|`--content-generation` |string |Content generation configuration (JSON string) |
|`--inference-foundry` |string |Inference foundry configuration (JSON string) |
|`--tags` |string |Tags (JSON string) |
|`--yes` |bool |Confirm creation of the billable Endpoint with the disclosed parameters. If model activation is also required, non\-interactive execution still needs ARKCLI_ALLOW_HEADLESS_ACTIVATION=1; \-\-yes alone cannot authorize activation. |
|`--allow-data-collected` |bool |Allow data collected |
|`--service-info` |string |Service info (JSON string) |
|`--need-watermark` |bool |Need watermark |
|`--watermark-info` |string |Watermark info (JSON string) |
|`--intelligent-router` |string |Intelligent router configuration (JSON string) |
|`--is-intelligent` |bool |Is intelligent |
|`--fallback` |string |Fallback configuration (JSON string) |
|`--limit-coefficient` |string |Limit coefficient configuration (JSON string) |
|`--deployment-type` |string |Deployment type |
|`--is-agentic` |bool |Is agentic |
|`--agentic-strategy` |string |Agentic strategy configuration (JSON string) |
|`--is-aicc` |bool |Is AICC |
|`--specify-region` |string |Specify region |
|`--set-default` |string |Promote the successfully created endpoint as the active profile default for the given modality (text\|image\|video). |

## dataset

Full lifecycle management of Datasets: create, query, update, delete, version (`ds-*` / `dsv-*`), download, and format validation.

* Designed for fine\-tuning: training/validation set reuse, version pinning, and format validation;

* Fine\-tune jobs reference Dataset artifacts via `--dataset`, bridging into the `train` domain workflow.

### dataset create

Create a dataset and submit its first V1 version

```Bash
arkcli dataset create
```

|Flag |Type |Description |
|---|---|---|
|`--name` |string (required) |Dataset name |
|`--description` |string |Description |
|`--dataset-format` |string (required) |Backend SchemaType: LLMSft, LLMPretrain, LLMDPO, LLMRL, EmbeddingSft, ImageGenSft, or VideoGenSft |
|`--local` |string_slice |Local .jsonl file path; repeatable |
|`--tos-uri` |string_slice |Listable TOS URI containing .jsonl objects (tos://bucket/path); repeatable, one bucket only, and ds\-ds/\* upload staging paths are unsupported |

### dataset delete

Delete a dataset

```Bash
arkcli dataset delete <dataset-id>
```

|Flag |Type |Description |
|---|---|---|
|`--yes` |bool |Skip interactive confirmation |

### dataset download

Download TOS objects from a dataset version

```Bash
arkcli dataset download <dataset-id>
```

|Flag |Type |Description |
|---|---|---|
|`--version` |string (required) |Version SID (dsv\-...) or friendly version number (V1, V2, and so on) |
|`--output-dir` |string |Download directory; defaults to ./\-/ |

### dataset get

Get dataset details

```Bash
arkcli dataset get <dataset-id>
```

### dataset list

List datasets

```Bash
arkcli dataset list
```

|Flag |Type |Description |
|---|---|---|
|`--page-number` |int |Page number (default: 1) |
|`--page-size` |int |Items per page (1\-100; default: 100) |
|`--sort-by` |string |Sort field |
|`--sort-order` |string |Sort order: Asc or Desc |
|`--id` |string_slice |Filter by dataset ID; repeatable |
|`--name` |string |Filter by dataset name |
|`--schema-type` |string_slice |Filter by SchemaType; repeatable |
|`--resource-origin` |string_slice |Filter by ResourceOrigin; repeatable |

### dataset update

Update a dataset name or description

```Bash
arkcli dataset update <dataset-id>
```

|Flag |Type |Description |
|---|---|---|
|`--name` |string |Dataset name |
|`--description` |string |Description |

### dataset validate

Validate local files or TOS data against a model fine\-tuning configuration

```Bash
arkcli dataset validate
```

|Flag |Type |Description |
|---|---|---|
|`--local` |string_slice |Local .jsonl file path; repeatable |
|`--tos-uri` |string_slice |Listable TOS URI containing .jsonl objects (tos://bucket/path); repeatable, one bucket only, and ds\-ds/\* upload staging paths are unsupported |
|`--model` |string (required) |Foundation model name |
|`--model-version` |string (required) |Foundation model version |
|`--type` |string (required) |Fine\-tuning type, for example sft, lora, dpo, grpo, or pt |

### dataset version create

Create the next Vn version for an existing dataset

```Bash
arkcli dataset version create <dataset-id>
```

|Flag |Type |Description |
|---|---|---|
|`--description` |string |Description |
|`--local` |string_slice |Local .jsonl file path; repeatable |
|`--tos-uri` |string_slice |Listable TOS URI containing .jsonl objects (tos://bucket/path); repeatable, one bucket only, and ds\-ds/\* upload staging paths are unsupported |

### dataset version download

Download TOS objects from a specific dataset version

```Bash
arkcli dataset version download <dataset-id> <version-id-or-vn>
```

|Flag |Type |Description |
|---|---|---|
|`--output-dir` |string |Download directory; defaults to ./\-/ |

### dataset version get

Get dataset version details

```Bash
arkcli dataset version get <version-sid> | <dataset-id> <version-id-or-vn>
```

|Flag |Type |Description |
|---|---|---|
|`--preview` |bool |Read and display the first 100 KiB (102400 bytes) of every object in the version |

### dataset version list

List dataset versions

```Bash
arkcli dataset version list <dataset-id>
```

|Flag |Type |Description |
|---|---|---|
|`--page-number` |int |Page number (default: 1) |
|`--page-size` |int |Items per page (1\-100; default: 100) |
|`--sort-by` |string |Sort field |
|`--sort-order` |string |Sort order: Asc or Desc |
|`--id` |string_slice |Filter by version SID; repeatable |
|`--version` |string_slice |Filter by friendly Vn version; repeatable |
|`--status` |string_slice |Filter by version status; repeatable |

## infer

Inference endpoint (ep\-\*) lifecycle management: get, list, create, start, stop, delete, update, plus deployment\-capability queries (`capability`).

* `infer endpoint create` is the explicit raw CRUD path for scripted / CI / guardrail\-free scenarios; for guided interactive creation prefer the `+deploy` workflow (model\-reuse check and profile sync);

* Use `capability` to confirm supported deployment forms before creating; start/stop/delete all have confirmation gates.

### infer endpoint capability get

Show which inference deployment forms a model supports (online endpoint / share\-service / batch / PTU / scale\-tier / fallback). Backs maas\-fe's `EndpointSupportedMethods` API. Use this BEFORE `infer endpoint create` to know if a model is even deployable, and which `--type` style flags are valid for it. Pass \-\-custom\-model\-id for a fine\-tuned custom model, or \-\-model + \-\-version for a foundation model.

```Bash
arkcli infer endpoint capability get
```

|Flag |Type |Description |
|---|---|---|
|`--model` |string |Foundation model name (e.g. `seed-2-0-pro`); mutually exclusive with \-\-custom\-model\-id |
|`--version` |string |Model version (used with \-\-model), e.g. `260328` for `seed-2-0-pro-260328` in the [Model list](model-list.md) |
|`--custom-model-id` |string |Custom model id (cm\-xxx); mutually exclusive with \-\-model |

### infer endpoint create

Create an infer endpoint (raw CRUD, no workflow guardrails — for guided deployment with model reuse check and profile sync, use +deploy)

```Bash
arkcli infer endpoint create
```

|Flag |Type |Description |
|---|---|---|
|`--model` |string (required) |Model ID (Foundation model or Custom model) |
|`--name` |string (required) |Endpoint name |
|`--billing-method` |string |Billing method: token |
|`--rpm` |int |Rate limit RPM |
|`--tpm` |int |Rate limit TPM |
|`--yes` |bool |In an interactive terminal, skip the [y/N] model activation prompt. NOTE: in non\-interactive environments (agent/CI/pipe), this flag does NOT authorize activation; activation is always refused. Set ARKCLI_ALLOW_HEADLESS_ACTIVATION=1 only for genuine unattended automation. |

### infer endpoint delete

Delete an infer endpoint (irreversible). Requires confirmation: interactive TTY prompts Y/N; non\-interactive (agent/CI/pipe) needs ARKCLI_ALLOW_HEADLESS_DELETE=1 — \-\-yes does NOT authorize in headless mode.

```Bash
arkcli infer endpoint delete <endpoint-id>
```

|Flag |Type |Description |
|---|---|---|
|`--yes` |bool |Skip the interactive Y/N prompt (TTY only). NOTE: in non\-interactive mode this flag does NOT authorize deletion — set ARKCLI_ALLOW_HEADLESS_DELETE=1 instead. |

### infer endpoint get

Get inference endpoint details (supports both regular endpoints and preset inference endpoints)

```Bash
arkcli infer endpoint get <endpoint-id>
```

### infer endpoint list

List inference endpoints with filters and pagination

```Bash
arkcli infer endpoint list
```

|Flag |Type |Description |
|---|---|---|
|`--format` |string |Output format: table \| json |
|`--model` |string |Filter by model ID (custom model) |
|`--status` |string |Filter by endpoint status |
|`--mine` |bool |Only list endpoints created by the current SSO sub\-user |
|`--page-number` |int |Page number (\>=1) |
|`--page-size` |int |Page size |

### infer endpoint start

Start an infer endpoint. Returns the endpoint's CURRENT status after the start request is accepted (typically 'Scheduling') — the transition to 'Running' is asynchronous; poll `infer endpoint get <id>` until Status='Running' to confirm.

```Bash
arkcli infer endpoint start <endpoint-id>
```

### infer endpoint stop

Stop an infer endpoint. Returns the endpoint's CURRENT status after the stop request is accepted — the transition to 'Stopped' is asynchronous; poll `infer endpoint get <id>` until Status='Stopped' to confirm.

```Bash
arkcli infer endpoint stop <endpoint-id>
```

### infer endpoint update

Update an infer endpoint (name / description / rate limit)

```Bash
arkcli infer endpoint update <endpoint-id>
```

|Flag |Type |Description |
|---|---|---|
|`--name` |string |New endpoint name |
|`--description` |string |New endpoint description (use empty string to clear) |
|`--rpm` |int |Rate limit: requests per minute (must be paired with \-\-tpm) |
|`--tpm` |int |Rate limit: tokens per minute (must be paired with \-\-rpm) |
|`--cg-concurrent-requests` |int |ContentGeneration: max concurrent requests (image gen endpoints) |
|`--cg-create-task-rpm` |int |ContentGeneration: CreateTask RPM (image gen endpoints) |

## train

Fine\-tune training job lifecycle: create, query, logs/trajectory, metric analysis, best\-step selection, and export to a custom model (mcj\-\*).

* Training data comes from the `dataset` domain; this domain owns hyperparameters, resource\-group matching, pricing, and method selection;

* Export bridges into `models custommodel` and `infer`: the exported artifact is a cm\-\* custom model, deployed via the inference\-endpoint workflow.

### train finetune artifacts export

Export one or more mcj outputs (e.g. global_step_2) as deployable cm\-\* entities. Produces `{job_id, items[]}` with per\-artifact custom_model_name / custom_model_id / export_status. Naming: `<prefix>_<artifact-name>` when \-\-custom\-model\-name\-prefix is set; otherwise the backend\-default name is kept. Use `arkcli train finetune artifacts list <mcj-id>` first to discover artifact_name values.

```Bash
arkcli train finetune artifacts export <mcj-id>
```

|Flag |Type |Description |
|---|---|---|
|`--artifact-name` |string_slice |Artifact (output) name to export, e.g. global_step_2. Repeatable. |
|`--custom-model-name-prefix` |string |Prefix for the resulting cm\-\* name. Final name = `<prefix>_<artifact-name>`. Empty = leave the backend default. |

### train finetune artifacts list

List training artifacts of a fine\-tuning job (Outputs[] projection: artifact_name / export_status / custom_model_name / custom_model_id / create_time). Use `--format table` for human\-readable selection. By default fills custom_model_name via concurrent GetCustomModel; pass \-\-lite to skip that lookup.

```Bash
arkcli train finetune artifacts list <mcj-id>
```

|Flag |Type |Description |
|---|---|---|
|`--format` |string |Output format: json (default) \| table |
|`--lite` |bool |Skip per\-cm GetCustomModel name lookup. Faster but custom_model_name will be empty. |

### train finetune capability get

Show which finetune training methods a foundation model (or the source of a custom model) supports. Pass \-\-model + optional \-\-version for a foundation model, OR \-\-model\-id for a custom model (cm\-xxx) — the latter resolves to its source FoundationModel under the hood, mirroring maas\-fe's CustomBoard which derives continued\-finetune support from the base's FinetuneTypes (custom models don't carry their own capability list). Returns supported_types (arkcli flag form: sft / lora / dpo / dpo\-lora / grpo / grpo\-lora / ppo / opd / opd\-lora / pt — matches `train finetune create --type`) and supported_types_wire (backend CustomizationType literal). \-\-version semantics: with \-\-model, empty \-\-version returns the primary version only; \-\-version is mutually exclusive with \-\-model\-id (cm's bound source version is resolved automatically — pass either, never both). Pair with `models finetune-config` to fetch the hyperparameter schema for a chosen \-\-type.

```Bash
arkcli train finetune capability get
```

|Flag |Type |Description |
|---|---|---|
|`--model` |string |Foundation model name. Mutually exclusive with \-\-model\-id. Select a name returned by `arkcli models list` |
|`--version` |string |Model version; empty = primary version only. Mutually exclusive with \-\-model\-id (cm has its own bound source version). Use `arkcli models versions <MODEL_NAME>` to list available versions |
|`--model-id` |string |Custom model ID (cm\-xxx). Mutually exclusive with \-\-model/\-\-version. Resolves to its source FoundationModel and reports that base's capability |

### train finetune create

Create a fine\-tuning job. In an interactive terminal, omitting \-\-name/\-\-type launches a guided creation wizard (gh pr create\-style dual mode); non\-interactive environments (agent/CI/pipe) must pass \-\-name and \-\-type explicitly. Supports legacy flags (\-\-dataset, \-\-epochs, \-\-lr, \-\-lora\-rank, \-\-beta) plus PRD\-aligned additions: local files, RL pipeline, data tolerance, hyperparameters JSON, save\-model limit, trajectory logging.

```Bash
arkcli train finetune create
```

|Flag |Type |Description |
|---|---|---|
|`--name` |string |Job name (required in non\-interactive environments; omit in an interactive terminal to launch the guided wizard) |
|`--type` |string |Customization type: sft \| lora \| dpo \| dpo\-lora \| grpo \| grpo\-lora \| ppo \| opd \| opd\-lora \| pt (required in non\-interactive environments; omit in an interactive terminal to launch the guided wizard) |
|`--description` |string |Job description |
|`--model` |string |Base model name, mutually exclusive with \-\-model\-id. Verify the model and training type with `arkcli train finetune capability get` before creating a job |
|`--model-version` |string |Base model version, used with \-\-model. Choose a version returned by `arkcli models versions <MODEL_NAME>` |
|`--model-id` |string |Custom model ID, mutually exclusive with \-\-model/\-\-model\-version |
|`--dataset` |string |Training dataset: tos://bucket/path or dataset ID (ds\-xxx). Legacy combined flag — prefer \-\-train\-tos\-uri / \-\-train\-dataset |
|`--dataset-version` |string |Dataset version ID (dsv\-xxx), required when \-\-dataset is a dataset ID. Legacy — prefer \-\-train\-dataset\-version |
|`--train-tos-uri` |string |Training data TOS URI (tos://bucket/prefix). PRD\-aligned alias for \-\-dataset when input is a TOS path |
|`--train-dataset` |string |Training dataset and version as:; the legacy separate \-\-train\-dataset\-version form remains supported |
|`--train-dataset-version` |string |Training dataset version id (dsv\-yyy). PRD\-aligned alias for \-\-dataset\-version |
|`--train-file` |string_slice |Local training file path (repeatable; maps to data.training_set.local_files[]) |
|`--train-path` |string_slice |Managed Dataset path JSON (repeatable): {"dataset_id":"ds\-...","dataset_version_id":"dsv\-...","multiplier":1} or use sample_count instead of multiplier |
|`--preset-dataset` |string_slice |Preset dataset JSON (repeatable): {"dataset_version_id":"dsv\-...","inject_multiplier":1}; use exactly one of inject_multiplier or inject_sample_count |
|`--validation-dataset` |string |Validation dataset and version as:; legacy tos:// and separate\-version forms remain supported |
|`--validation-dataset-version` |string |Validation dataset version id (dsv\-xxx) |
|`--validation-tos-uri` |string |Validation data TOS URI (tos://bucket/prefix). PRD\-aligned alias |
|`--validation-dataset-id` |string |Validation dataset id (ds\-xxx). PRD\-aligned alias |
|`--validation-file` |string_slice |Local validation file path (repeatable; maps to data.validation_set.local_files[]) |
|`--validation-percentage` |int |Percentage of training set to use as validation (e.g. 10); mutually exclusive with \-\-validation\-dataset/\-\-validation\-file |
|`--max-invalid-records-ratio` |string |Allowed bad\-sample ratio in [0,1) (e.g. 0.01) |
|`--max-invalid-records-number` |int |Allowed bad\-sample absolute count (positive int) |
|`--shuffle-random-seed` |string |\-1 = random per run, 0 = no shuffle, positive = fixed seed for reproducibility (string so 0 stays distinct from unset) |
|`--hyperparameters` |string |JSON dict or @file (e.g. '{"epoch":"3","batch_size":"8"}' or @hyperparameters.json). Merged with scalar \-\-epochs/\-\-lr/\-\-lora\-rank/\-\-beta (scalars win on conflict) |
|`--epochs` |int |Number of training epochs (legacy shortcut for \-\-hyperparameters epoch) |
|`--lr` |string |Learning rate (legacy shortcut for \-\-hyperparameters learning_rate) |
|`--lora-rank` |int |LoRA rank (legacy shortcut for \-\-hyperparameters lora_rank) |
|`--beta` |string |Beta parameter for DPO (legacy shortcut for \-\-hyperparameters beta) |
|`--save-model-limit` |int |Cap training artifacts kept (1\-10; backend default 1) |
|`--enable-trajectory` |bool |Enable RL rollout\-trajectory logging (recommended for GRPO/PPO debugging) |
|`--pipeline` |string |@file path to JSON RL pipeline config (custom_rl_pipeline; required for GRPO/PPO with custom rollout/grader) |
|`--resource-group` |string |Resource group ID; pass it exactly as returned by discovery, then verify account/project access, job resource templates, and group availability before submission |
|`--yes` |bool |Skip the interactive confirmation prompts: the upload\-to\-TOS gate (\-\-train\-file) and the final submit prompt. NOTE: model activation is always refused in non\-interactive environments (agent/CI/pipe) regardless of \-\-yes; set ARKCLI_ALLOW_HEADLESS_ACTIVATION=1 for unattended activation, otherwise the base model must already be activated. |
|`--estimate` |bool |Run the online token and price estimate without creating a fine\-tuning job |

### train finetune delete

Delete a fine\-tuning job record (irreversible). Use \-\-yes to skip the confirm prompt; recommended for scripts. Only deleteable from a terminal phase (Completed / Failed / Terminated); non\-terminal jobs are refused at the shortcut layer with a hint to run `train finetune stop` first (saves the user a confirm\-prompt round\-trip on a doomed call — the backend would otherwise reject with `OperationDenied.UnsupportedPhase`). For Agents: inspect the target with `train finetune get`, restate the job id and impact, then obtain explicit confirmation before deleting.

```Bash
arkcli train finetune delete <job-id>
```

|Flag |Type |Description |
|---|---|---|
|`--yes` |bool |Skip the interactive confirm prompt |

### train finetune download

Download the training / validation data referenced by a fine\-tuning job to a local directory. Sources are the TOS object paths recorded on the job or managed Dataset references; platform\-managed preset datasets (PresetDatasets) and LocalFiles (which only record the creator's local path) cannot be downloaded and are listed as skipped. The command fails if a same\-named file already exists, to avoid silently overwriting local files.

```Bash
arkcli train finetune download <job-id>
```

|Flag |Type |Description |
|---|---|---|
|`--output-dir` |string |Output directory (default: ./<job\-id\>\-data) |
|`--set` |string |Which set to download: all \| train \| validation (default: all) |

### train finetune get

Get full fine\-tuning job details (status / config / artifacts). Same data as `train finetune status` today; this is the PRD\-canonical read entry. For training\-step progress use `train finetune metrics`; for live polling use `train finetune watch`. Cost is NOT included — the wire schema has no per\-job billing field; use `train finetune create --estimate` for an online pre\-submit estimate.

```Bash
arkcli train finetune get <job-id>
```

### train finetune list

List model fine\-tuning jobs

```Bash
arkcli train finetune list
```

|Flag |Type |Description |
|---|---|---|
|`--page-number` |int |Page number (must be \>= 1; default: 1) |
|`--page-size` |int |Items per page (1\-100; default: 100) |
|`--sort-by` |string |Sort field |
|`--sort-order` |string |Sort order: Asc \| Desc |
|`--phase` |string_slice |Filter by phase (can repeat): Preprocessing \| Queued \| Deploying \| Running \| Pausing \| Paused \| Terminating \| Terminated \| Completing \| Completed \| Failed |
|`--name` |string |Filter by job name (fuzzy match) |
|`--customization-type` |string_slice |Filter by customization type (can repeat): FinetuneSft \| FinetuneLoRA \| DPO \| DPOLoRA \| GRPO \| GRPOLoRA \| PPO \| OPD \| OPDLoRA \| Pretrain |
|`--create-time-after` |string |Filter jobs created after this time (RFC3339, e.g. 2026\-01\-01T00:00:00Z) |
|`--create-time-before` |string |Filter jobs created before this time (RFC3339) |
|`--mine` |bool |Only include jobs created by the current BytePlus SSO sub\-user, using the `sys:ark:createdBy` tag filter. Requires a sub\-user login; root accounts are not supported. |

### train finetune logs

Tail training\-task logs for a fine\-tuning job. Default source is the public ARK Action `GetTaskLogs` (same as the console 'training logs' tab) — system / training\-framework stdout. Reachable from any auth profile. \-\-follow / \-\-tail / \-\-search / \-\-since supported; \-\-follow exits automatically once the job is terminal and no new lines remain; \-\-tail \>1000 auto\-batches. With \-\-custom switches the source to the per\-job CustomTLSConfig topic — RL plugin (rollout / grader) function stdout. \-\-custom requires SSO credentials for direct training\-log access and only works on RL jobs that opted into \-\-enable\-trajectory + project\-level TLS enabled (see `arkcli train finetune tls config get`); \-\-follow is not supported with \-\-custom. \-\-all dumps everything past the \-\-tail cap (pages until backend ListOver), and REQUIRES \-\-output FILE to avoid blowing up Agent context with 10MB+ of logs.

```Bash
arkcli train finetune logs <job-id>
```

|Flag |Type |Description |
|---|---|---|
|`--tail` |int |Tail size: number of lines to fetch per request. Default 200; maas\-fe console default is 2000. Ignored when \-\-all. |
|`--follow` |bool |Poll for new lines while the job is active; stop automatically after a terminal phase has no new lines. Use \-\-interval to tune polling cadence. Not supported with \-\-custom or \-\-all. |
|`--interval` |string |Polling interval for \-\-follow as duration (5s, 30s) or bare integer seconds. Default 5s. |
|`--search` |string |Server\-side filter: GetTaskLogs sources use FilterExpression; TLS custom logs use a content filter. |
|`--since` |string |Start time relative to now (e.g. 30m, 2h). TLS custom logs default to 30 days; GetTaskLogs sources leave start time unset by default. |
|`--json` |bool |Render structured JSON output instead of plain text lines. |
|`--custom` |bool |Read RL plugin function stdout (CustomTLSConfig TLS topic) instead of system / training\-framework logs. Requires SSO credentials + RL job + \-\-enable\-trajectory. |
|`--function-name` |string |TLS \-\-custom only: filter by plugin function name (e.g. SingleRollout\-xxx) using a function_name query. |
|`--all` |bool |Page through all logs, ignoring \-\-tail. Requires \-\-output FILE. GetTaskLogs uses the Ctx cursor; TLS uses Context. |
|`--output` |string |Write logs to a file: plain text for GetTaskLogs, JSON for TLS custom logs. Stdout contains metadata only. Required with \-\-all. |

### train finetune metrics

Fetch training metrics for a fine\-tuning job. Two\-step under the hood: GetModelCustomizationJobMetrics returns the available metric names (e.g. train/loss, eval/loss, train/lr, train/grad_norm; DPO/GRPO jobs use different prefixes), then GetModelCustomizationJobMetricData fetches the per\-step time series. Use \-\-metric (repeatable) to filter; default returns every available metric. Use \-\-output to dump JSON to a file (Agent friendly for chart rendering).

```Bash
arkcli train finetune metrics <job-id>
```

|Flag |Type |Description |
|---|---|---|
|`--metric` |string_slice |Filter to one or more metric names (repeatable). Empty = all available |
|`--min-sample-step` |int |Minimum sampling step (server\-side downsampling) |
|`--max-metric-point` |int |Maximum samples per metric (server downsamples uniformly) |
|`--from-step` |int |Range filter: only return samples with step \>= this value |
|`--to-step` |int |Range filter: only return samples with step <= this value |
|`--output` |string |Write the response JSON to this file path; useful for Agent chart rendering |

### train finetune pause

Pause a running fine\-tuning job (resumable via `train finetune resume`). Distinct from `stop` which terminates irreversibly.

```Bash
arkcli train finetune pause <job-id>
```

### train finetune pricing

Get fine\-tuning pricing for a foundation model. \-\-billing\-method=token filters foundation\-model charge items to entries matching \-\-type: lora / lora\-sft / dpo\-lora / grpo\-lora / opd\-lora → LoRA finetune; sft / dpo / grpo / ppo / opd / pt → full\-model finetune. Empty \-\-type returns all finetune\-related charge items. \-\-billing\-method=instance resolves the exact model\-version/type hyperparameter schema, lists eligible training resource templates, and joins their FlavorSum hourly prices. When \-\-type is given, arkcli cross\-checks against `train finetune capability get` and refuses if the model doesn't support that training method (returning generic charge\-item pricing for an unsupported method would mislead callers).

```Bash
arkcli train finetune pricing
```

|Flag |Type |Description |
|---|---|---|
|`--model` |string (required) |Foundation model name. Select a model that supports the requested training type using `arkcli train finetune capability get` |
|`--type` |string |Customization type: sft \| lora \| lora\-sft \| dpo \| dpo\-lora \| grpo \| grpo\-lora \| ppo \| opd \| opd\-lora \| pt; empty = all |
|`--billing-method` |string |Billing method: token \| instance |
|`--model-version` |string |Foundation model version; required by \-\-billing\-method=instance |
|`--hyperparameters` |string |Instance pricing only: JSON dict or @file; merged with model defaults and scalar \-\-epochs/\-\-lr/\-\-lora\-rank/\-\-beta overrides |
|`--epochs` |int |Instance pricing only: epoch override |
|`--lr` |string |Instance pricing only: learning_rate override |
|`--lora-rank` |int |Instance pricing only: lora_rank override |
|`--beta` |string |Instance pricing only: beta override |
|`--region` |string |Token pricing only: price region, defaults to active profile region |

### train finetune resource\-group list

Check resource\-group access and list job resource requirements and available groups for the selected fine\-tuning model, type, and hyperparameters

```Bash
arkcli train finetune resource-group list
```

|Flag |Type |Description |
|---|---|---|
|`--model` |string |Foundation model name; mutually exclusive with \-\-model\-id |
|`--model-version` |string |Foundation model version; required with \-\-model |
|`--model-id` |string |Existing custom model ID; resolves its foundation model and is mutually exclusive with \-\-model/\-\-model\-version |
|`--type` |string (required) |Customization type: sft \| lora \| dpo \| dpo\-lora \| grpo \| grpo\-lora \| ppo \| opd \| opd\-lora \| pt |
|`--hyperparameters` |string |Hyperparameter JSON or @file; keep it identical to job creation |
|`--epochs` |int |Convenience override for epoch |
|`--lr` |string |Convenience override for learning_rate |
|`--lora-rank` |int |Convenience override for lora_rank |
|`--beta` |string |Convenience override for beta |
|`--only-matched` |bool |Return only resource groups that can run the current job |

### train finetune resume

Resume a Paused fine\-tuning job; when allowed by the backend, also retry a Failed or Terminated job

```Bash
arkcli train finetune resume <job-id>
```

### train finetune sdk login

Write ark_sdk's `~/.ark/authorization.json` from arkcli's STS — replaces `ark login`. Required only when the user runs their own `python3 submit_job.py`: ark_sdk 0.6.0 Python lib's submit hot path reads only this file (not env vars), upstream SDK constraint. For YAML\-spec submission use `train finetune sdk submit <yaml>` instead — it does NOT write authorization.json (uses a 0600 temp credential file scoped to the ark process). Refreshes STS if expired. Refuses to overwrite an existing file from a different account unless \-\-force.

```Bash
arkcli train finetune sdk login
```

|Flag |Type |Description |
|---|---|---|
|`--refresh` |bool |Force a product\-native SSO STS refresh before writing. |
|`--force` |bool |Overwrite an existing authorization.json even if its account_id differs from arkcli's active profile. |
|`--path` |string |Override the output path. Default: ~/.ark/authorization.json (matches ARK_SDK_AUTHORIZATION_FILE env). |

### train finetune status

Get fine\-tuning job details (alias for `get`; reserved for a future slim phase/progress projection if the backend splits the API)

```Bash
arkcli train finetune status <job-id>
```

### train finetune stop

Stop a running fine\-tuning job (irreversible — terminates the job; use `train finetune pause` instead if you want to resume later). Default: [y/N] confirmation; `--yes` skips the prompt.

```Bash
arkcli train finetune stop <job-id>
```

|Flag |Type |Description |
|---|---|---|
|`--yes` |bool |Skip the [y/N] confirmation prompt. Required when running non\-interactively (CI / pipes / agents). |

### train finetune tls config delete

Permanently delete the project's finetune TLS resources — destroys stored trajectory / custom log / trace / explainability data. Irreversible. Pass \-\-yes to skip the [y/N] confirm. After deletion you can re\-enable via `arkcli train finetune tls config enable` (creates fresh empty topics).

```Bash
arkcli train finetune tls config delete
```

|Flag |Type |Description |
|---|---|---|
|`--project-name` |string |Project name override (default: active profile project) |
|`--yes` |bool |Skip the [y/N] confirm prompt |

### train finetune tls config disable

Disable project\-level finetune TLS (stops new ingestion; existing data is retained per TLS retention). Pass \-\-yes to skip the [y/N] confirm. To permanently delete TLS resources (data destroyed), use `arkcli train finetune tls config delete` instead.

```Bash
arkcli train finetune tls config disable
```

|Flag |Type |Description |
|---|---|---|
|`--project-name` |string |Project name override (default: active profile project) |
|`--yes` |bool |Skip the [y/N] confirm prompt |

### train finetune tls config enable

Enable project\-level fine\-tuning TLS resources (creates four post\-paid training TLS topics — billable). Required before any job in this project can record trajectory / custom plugin stdout / trace / token\-level data. Default: [y/N] confirmation since this provisions billable resources; pass \-\-yes to skip (required for non\-interactive callers / Agents). This online provisioning workflow does not support \-\-dry\-run; inspect the active project and confirm explicitly. Note: sub\-account IAM users may not have permission; ask the main\-account admin to enable it in the console.

```Bash
arkcli train finetune tls config enable
```

|Flag |Type |Description |
|---|---|---|
|`--project-name` |string |Project name override (default: active profile project) |
|`--yes` |bool |Skip the [y/N] confirm prompt (required when running non\-interactively) |

### train finetune tls config get

Show project\-level finetune TLS state: whether TLS is enabled and the four topic IDs (trajectory / custom / trace / explainability) used to back `--enable-trajectory` / RL plugin logs / trace / explainability data. Project defaults to active profile project; override with \-\-project\-name.

```Bash
arkcli train finetune tls config get
```

|Flag |Type |Description |
|---|---|---|
|`--project-name` |string |Project name override (default: active profile project) |

### train finetune trajectory get

Fetch a single rollout trajectory record (always full content; one record is ~3KB). Provide either \-\-sample\-id (exact match) or \-\-step + \-\-index (i\-th rollout at that step). Same SSO requirement as `trajectory list`.

```Bash
arkcli train finetune trajectory get <job-id>
```

|Flag |Type |Description |
|---|---|---|
|`--sample-id` |string |Sample id (smpl\-...). Mutually exclusive with \-\-step/\-\-index. |
|`--step` |int |Step number (combine with \-\-index to pick a specific rollout) |
|`--index` |int |0\-based rollout index within the step (default 0) |
|`--since` |string |Time window relative to now. Default 30d. |
|`--output` |string |Write JSON to this file path |

### train finetune trajectory list

List RL rollout trajectories for a fine\-tuning job. Reads directly from the per\-job training TLS topic (TrajectoryTLSConfig.TopicID) — bypasses ARK control\-plane. Default output is a SUMMARY (step / sample_id / phase / reward / advantage / tokens / duration) — Agent\-safe ~150 bytes/entry. Pass \-\-full for the heavy form (sample / trajectory / metrics / reward_details / plugin_details unfolded, ~3KB/entry). Pass \-\-output FILE to dump full JSON to disk and keep stdout to a small metadata blob. Requires SSO\-authenticated profile (same constraint as logs).

```Bash
arkcli train finetune trajectory list <job-id>
```

|Flag |Type |Description |
|---|---|---|
|`--step` |int |Filter by step number (e.g. \-\-step 20) |
|`--phase` |string |Filter by phase: train \| eval |
|`--sample-id` |string |Filter by sample id (smpl\-...) |
|`--query` |string |Raw TLS query expression; overrides \-\-step/\-\-phase/\-\-sample\-id |
|`--limit` |int |Max entries to return (default 200; pages above 100 since TLS API caps each call at 100). Ignored when \-\-all. |
|`--since` |string |Time window relative to now (30m / 2h / 24h). Default 30d (covers full TLS retention). |
|`--full` |bool |Include full sample / trajectory / metrics / reward_details / plugin_details (~3KB/entry, heavy) |
|`--all` |bool |Page until backend exhausts the topic. REQUIRES \-\-output FILE — refuses to dump unbounded data to stdout. |
|`--output` |string |Write full JSON to this file path; stdout shows metadata only (recommended for Agent context). Required when \-\-all is set. |

### train finetune update

Update mutable fields on a fine\-tuning job (name / description). At least one of \-\-name / \-\-description must be provided; empty fields are not transmitted.

```Bash
arkcli train finetune update <job-id>
```

|Flag |Type |Description |
|---|---|---|
|`--name` |string |New job name |
|`--description` |string |New job description |

### train finetune watch

Poll a fine\-tuning job until it reaches a terminal phase (Completed / Failed / Terminated). Default mode: line\-per\-phase progress to stderr; \-\-rich mode adds step progress bar / ETA / per\-metric sparklines (last 30 steps). Saves Agents/CI from writing their own polling loop.

```Bash
arkcli train finetune watch <job-id>
```

|Flag |Type |Description |
|---|---|---|
|`--interval` |string |Poll interval as duration (e.g. 15s, 1m, 1h30m) or bare integer seconds. Default 30s; \-\-rich mode default 5s to match maas\-fe progress chart |
|`--timeout` |string |Total wait limit as duration (e.g. 30m, 2h) or bare integer seconds; 0 = no timeout (default 0) |
|`--quiet` |bool |Suppress per\-tick stderr progress |
|`--rich` |bool |Render PRD\-style TUI: step / progress bar / ETA / sparkline metrics. Auto\-disabled when stderr isn't a TTY. |
|`--metric` |string_slice |(\-\-rich) Metrics to render as sparklines (slash\-separated keys, e.g. train/loss, train/lr, train/grad_norm). Default: train/loss, train/lr, train/grad_norm, train/global_step. Repeatable. |
