# Models & Plans

Models and plans: querying and activating public foundation models, subscription and seat management, unit pricing, plus IAM helpers and platform doc search.

* `models` covers public foundation models only; account custom models (`cm-*`) live under `models custommodel`;

* `plans` covers Coding Plan and Coding Plan Team (personal and enterprise, including seat assignment and API key rotation); `pricing` returns discounted unit prices and subscription prices;

* `iam` queries identity and project lists, mainly for diagnostics and first\-login project selection;

* `docs` searches Ark documentation, API contracts and platform announcements from the terminal.

## docs

Search and read ModelArk documentation: search, list, fetch by URL, plus API doc enumeration and OpenAPI spec retrieval (`apis list/spec`).

* Query model integration guides, API contracts, and platform announcements from the terminal without switching to a browser.

### docs apis list

List published Ark API endpoints

```Bash
arkcli docs apis list
```

|Flag |Type |Description |
|---|---|---|
|`--api-mcp-url` |string |MCP URL for APIs list/spec only; also ARK_DOCS_API_MCP_URL. Does not change search/list/get backends |

### docs apis spec

Fetch one published Ark OpenAPI contract by exactly one selector

```Bash
arkcli docs apis spec
```

|Flag |Type |Description |
|---|---|---|
|`--api-mcp-url` |string |MCP URL for APIs list/spec only; also ARK_DOCS_API_MCP_URL. Does not change search/list/get backends |
|`--id` |string |Filter by the unique catalog id, for example chat\-ChatCompletions_create; mutually exclusive with \-\-service / \-\-api\-path; preferred for the public catalog |
|`--service` |string |Filter by the catalog filename group, for example chat or endpoints; not a coarse service label. Most groups are not unique—use \-\-id or \-\-api\-path when the command refuses. Mutually exclusive with \-\-id / \-\-api\-path |
|`--api-path` |string |Filter by the exact path stored in the catalog, for example /api/v3/chat/completions or /?Action=ListEndpoints&Version=2024\-01\-01. Mutually exclusive with \-\-id / \-\-service |

### docs get

Read an Ark documentation page

```Bash
arkcli docs get <url>
```

|Flag |Type |Description |
|---|---|---|
|`--outline` |bool |Return only the published heading outline (id/level/title) without the body; feed a returned id back through \-\-section to read one part |
|`--section` |string |Read a section including subsections by exact title or #anchor ID; public backend only |
|`--compact` |bool |Emit the body once with citation and continuation metadata; omit chunks and raw_text |
|`--snapshot` |string |Reuse the snapshot returned by get/list to pin pagination and continuation |
|`--chunk-start` |int |Starting chunk index (default 0) |
|`--chunk-count` |int |Number of chunks (default 5); accepts 1\-50 |

### docs list

Browse the Ark documentation catalog

```Bash
arkcli docs list
```

|Flag |Type |Description |
|---|---|---|
|`--query` |string |Filter the published catalog locally by title, description, breadcrumbs or route; use this when search is unavailable instead of retrying the query |
|`--snapshot` |string |Reuse the snapshot returned by get/list to pin pagination and continuation |
|`--limit` |int |Documents per page (default 10); accepts 1\-100 |
|`--offset` |int |Catalog starting index (default 0); public pagination requires a snapshot |

### docs search

Search Ark documentation

```Bash
arkcli docs search <query>
```

|Flag |Type |Description |
|---|---|---|
|`--top-k` |int |Maximum results (default 5); accepts 1\-50 |
|`--snapshot` |string |Reuse the snapshot returned by search to pin the candidate set and ranking while paging (it lives about 5 minutes) |
|`--offset` |int |Starting index in the search results (default 0); requires \-\-snapshot when above 0 |

## iam

Identity and access management helpers: query the current identity (`userid`) and IAM metadata such as project lists.

* Primarily for diagnostics and first\-login project selection; everyday commands resolve project ownership automatically through the profile facet.

### iam userid

Resolve IAM UserID by username prefix (or return current identity's UserID when \-\-username is omitted)

```Bash
arkcli iam userid
```

|Flag |Type |Description |
|---|---|---|
|`--username` |string |Comma\-separated username prefixes to look up (e.g. ivan,bob); omit to return current identity's UserID |

## models

Query and activate public foundation models on ModelArk: list, search, details, and explicit activation of billing items (`activate`).

Choose the exact model ID from the [Model list](model-list.md), such as `seed-2-0-pro-260328`. Parameters that request a foundation model name use the corresponding name, such as `seed-2-0-pro`; pass `260328` separately when a version parameter is available. Check the model's supported capabilities before importing artifacts or creating a training job.

* Covers public foundation models only; account custom models (cm\-\*) live under `models custommodel`;

* Activation is not deployment: `activate` only enables billing; to create a callable endpoint use `+deploy` or the `infer` domain;

* For latency\-sensitive selection, refer to TTFT/TPOT rankings and input\-length comparisons.

### models activate

Activate (open) a foundation model's charge item — wraps OpenModelChargeItem

```Bash
arkcli models activate <foundation-model-name>
```

|Flag |Type |Description |
|---|---|---|
|`--sub-services` |string_slice |SubServices to open: base \| context\-cache \| fast\-infer (comma\-separated; default opens base only) |
|`--yes` |bool |Skip the [Y/N] confirmation prompt (non\-interactive) |

### models custommodel available\-quantizations

List quantization modes available for a custom model

```Bash
arkcli models custommodel available-quantizations <id>
```

### models custommodel delete

Delete a custom model from the repository (irreversible; requires confirmation, or pass \-\-yes)

```Bash
arkcli models custommodel delete <id>
```

|Flag |Type |Description |
|---|---|---|
|`--yes` |bool |Confirm deletion |

### models custommodel get

Show custom model details (base model, quantization, status, active endpoints)

```Bash
arkcli models custommodel get <id>
```

|Flag |Type |Description |
|---|---|---|
|`--transform` |string_slice |Fields to include: id,name,active_endpoints,artifact_types |

### models custommodel list

List custom models in the account repository, with filters for base model, training type, status, and source

```Bash
arkcli models custommodel list
```

|Flag |Type |Description |
|---|---|---|
|`--search` |string |Fuzzy search custom models by name, id, or base model display name |
|`--page` |string |Pagination as <number,size\>, e.g. 1,10 |
|`--base-models` |string_slice |Filter by foundation model names, comma\-separated or repeated |
|`--base-model-ids` |string_slice |Filter by foundation model IDs, comma\-separated or repeated |
|`--customization-types` |string_slice |Filter by training type: lora,sft,dpolora,dpo,grpolora,grpo,ppo,opdlora,opd,pretrain |
|`--supported-customization-types` |string_slice |Filter by base models supporting these training types |
|`--source-jobs` |string_slice |Filter by model customization job IDs |
|`--statuses` |string_slice |Filter by status: preparation,processing,ready,failed,exporting,exportfailed |
|`--sources` |string_slice |Filter by source: import,customization |
|`--mine` |bool |Only include custom models created by current SSO sub\-user |
|`--sort-by` |string |Sort field, default CreateTime |
|`--sort-order` |string |Sort order: asc \| desc (default: desc) |

### models custommodel quantize

Create a quantized variant of a custom model with the given quantization mode

```Bash
arkcli models custommodel quantize <id>
```

|Flag |Type |Description |
|---|---|---|
|`--quantization` |string (required) |Quantization mode |
|`--description` |string |Description for the quantized custom model, max 300 chars |

### models custommodel update

Rename a custom model or update its description

```Bash
arkcli models custommodel update <id>
```

|Flag |Type |Description |
|---|---|---|
|`--name` |string |New custom model name |
|`--description` |string |New description, max 300 chars |

### models custommodel upload

Import a custom model from a TOS artifact into the repository (requires base model, quantization mode, and name)

```Bash
arkcli models custommodel upload
```

|Flag |Type |Description |
|---|---|---|
|`--tos` |string (required) |TOS URI: `tos://<BUCKET>/<PREFIX>` |
|`--base-model` |string (required) |Full foundation model ID matching the imported artifact. Select the exact version from the [Model list](model-list.md). |
|`--name` |string (required) |Custom model name |
|`--quantization` |string (required) |Quantization mode |
|`--description` |string |Description, max 300 chars |

### models finetune\-config

Get authoritative finetune hyperparameter schema for a model version. Returns Parameters[] with Name/DefaultValue/Min/Max/Description per training type. Use this BEFORE `train finetune create` to discover correct field names (GRPO uses lr+num_steps, SFT uses learning_rate+epoch, etc.)

```Bash
arkcli models finetune-config <model-name> [version]
```

|Flag |Type |Description |
|---|---|---|
|`--model` |string |Foundation model name (or pass positionally as first arg). Select a model that supports the requested training type using `arkcli train finetune capability get`. |
|`--version` |string |Model version (or pass positionally as second arg). Empty = first version returned by API. Use `arkcli models versions <MODEL_NAME>` to list available versions |
|`--type` |string |Filter to one customization type: sft \| lora \| dpo \| dpo\-lora \| grpo \| grpo\-lora \| ppo \| opd \| opd\-lora \| pt. Empty = all populated types |

### models get

Get visible model detail, pricing, and rate limits

```Bash
arkcli models get <id> [version]
```

|Flag |Type |Description |
|---|---|---|
|`--id` |string |Model identifier, e.g. `seed-2-0-pro-260328` (or pass positionally) |
|`--version` |string |Optional model version override, e.g. `260328` (or pass positionally) |
|`--no-cache` |bool |Bypass local CardView and ArkModels metadata caches |
|`--web` |bool |Open the model square console page in the browser instead of printing detail |

### models list

List foundation models (supports filters and pagination)

```Bash
arkcli models list
```

|Flag |Type |Description |
|---|---|---|
|`--modality` |string |Filter by modality: text \| image \| video \| audio \| embed |
|`--name` |string |Filter by model name (case\-insensitive substring; use `models search` for richer filters) |
|`--page-number` |int |Page number (\>=1) |
|`--page-size` |int |Page size |
|`--sort-by` |string |Sort field: CreateTime \| UpdateTime \| Name (also accepts create_time / update_time / name) |
|`--sort-order` |string |Sort order: Asc \| Desc (also accepts asc / desc) |
|`--no-cache` |bool |Bypass the local CardView cache and fetch visible models live |

### models search

Search models with keyword + structured filters (modality, context window, capabilities)

```Bash
arkcli models search [keyword]
```

|Flag |Type |Description |
|---|---|---|
|`--keyword` |string |Search keyword (also accepted as positional) |
|`--size` |int |Maximum results to return; 0 = no truncation (default) |
|`--modality` |string |Coarse modality filter: text\|image\|video\|audio (alias for output\-modality) |
|`--input-modality` |string |Filter by input modalities (comma\-separated, e.g. text,image,video) |
|`--output-modality` |string |Filter by output modalities (comma\-separated) |
|`--multimodal` |bool |Only models that accept more than one input modality |
|`--min-context-window` |int |Minimum context_window in tokens (e.g. 200000) |
|`--max-context-window` |int |Maximum context_window in tokens |
|`--min-input-tokens` |int |Minimum max_input_tokens |
|`--min-output-tokens` |int |Minimum max_completion_tokens |
|`--capability` |string_slice |Required capability flag (repeat for AND): thinking\|mcp\|functioncall\|web_browsing\|knowledge_base\|response_format\|reasoning_effort; caching is deprecated, use \-\-cache\-type explicit_cache |
|`--cache-type` |string_slice |Required cache category (repeat for AND): explicit_cache\|implicit_cache\|session_cache\|prefix_cache |
|`--strict-filter` |bool |Reject models with missing enrichment data instead of keeping them (default: keep) |
|`--include-deprecated` |bool |Include shutdown/unavailable models (lifecycle_status=Shutdown or display_name contains 'deprecated/unavailable'); excluded by default |
|`--refresh-cache` |bool |Force a synchronous ArkModels metadata cache refresh before searching |

### models versions

List user\-visible versions of a foundation model with each ActiveConfigurationId (input for `models finetune-config`)

```Bash
arkcli models versions <model-name>
```

|Flag |Type |Description |
|---|---|---|
|`--model` |string |Foundation model name, e.g. `seed-2-0-pro` (or pass positionally) |

## plans

ModelArk subscription plan management (Coding Plan and Coding Plan Team): list holdings, buy, renew, model catalogs, API Key rotation; team edition includes full seat management (seat\-list / seat\-assign / rotate\-apikey).

* Purchase parameters are clarified step by step; on `payment_failed` with an Order ID you resume or cancel the existing order instead of re\-buying;

* Distinct from platform profiles: token/resource packages still use the platform profile + `/api/v3` — do not misroute by the word "plan".

### plans buy

Prepare a Coding Plan purchase (personal or team); the CLI does not place the order — with \-\-yes it returns the BytePlus console subscription URL for the user to complete in the browser

```Bash
arkcli plans buy
```

|Flag |Type |Description |
|---|---|---|
|`--plan` |string (required) |Plan name: coding\-plan \| coding\-plan\-team |
|`--type` |string (required) |Tier: lite \| pro |
|`--duration` |int |Subscription length in months (1\-12, default 1) |
|`--quantity` |int |Seat count (required for coding\-plan\-team) |
|`--estimate` |bool |Request an online price estimate without placing an order |
|`--yes` |bool |Return the BytePlus console subscription URL (payment_via_web) so the user can complete the purchase in the browser; the CLI never places an order or charges the account |

### plans get

Show current Coding Plan subscriptions (personal and team) with tier status

```Bash
arkcli plans get
```

### plans model\-apply

Set the ark\-code\-latest route target of a Coding Plan (auto or a concrete shadow model)

```Bash
arkcli plans model-apply
```

|Flag |Type |Description |
|---|---|---|
|`--plan` |string (required) |Plan name: coding\-plan \| coding\-plan\-team |
|`--model` |string |Route target: model id, output name, or "auto"; required in non\-interactive mode |

### plans model\-list

List models supported by a Coding Plan and the current ark\-latest\-model

```Bash
arkcli plans model-list
```

|Flag |Type |Description |
|---|---|---|
|`--plan` |string (required) |Plan name: coding\-plan \| coding\-plan\-team |

### plans renew

Prepare a Coding Plan renewal (personal or team); the CLI does not renew — with \-\-yes it returns the BytePlus console subscription URL for the user to complete in the browser

```Bash
arkcli plans renew
```

|Flag |Type |Description |
|---|---|---|
|`--plan` |string (required) |Plan name: coding\-plan \| coding\-plan\-team |
|`--duration` |int |Renewal length in months (1\-12, default 1) |
|`--seat-ids` |string |Comma\-separated seat IDs to renew (required for coding\-plan\-team, e.g. seat\-001,seat\-002) |
|`--estimate` |bool |Request an online renewal price estimate without placing an order |
|`--yes` |bool |Return the BytePlus console subscription URL (payment_via_web) so the user can complete the renewal in the browser; the CLI never renews or charges the account |

### plans team rotate\-apikey

Rotate Coding Plan Team seat API Keys; old keys are invalidated immediately

```Bash
arkcli plans team rotate-apikey
```

|Flag |Type |Description |
|---|---|---|
|`--seat-ids` |string |Comma\-separated Coding Plan Team seat IDs for admin batch rotation (required) |
|`--yes` |bool |Skip the [Y/N] confirmation prompt (non\-interactive) |

### plans team seat\-assign

Assign Coding Plan Team seats to sub\-users via GrantSeats; repeat \-\-bind for batch assignment

```Bash
arkcli plans team seat-assign
```

|Flag |Type |Description |
|---|---|---|
|`--plan` |string (required) |Plan name: coding\-plan\-team |
|`--bind` |string_slice (required) |Seat\-to\-user binding (repeatable). Format: \-\-bind seat\-001=user\-123 OR \-\-bind seat\-001=user\-123:user\-name (skips IAM lookup) |
|`--project-name` |string |Project name (default: default) |

### plans team seat\-list

List Coding Plan Team seats with filters

```Bash
arkcli plans team seat-list
```

|Flag |Type |Description |
|---|---|---|
|`--plan` |string (required) |Plan name: coding\-plan\-team |
|`--type` |string |Tier filter: lite \| pro |
|`--seat-ids` |string |Comma\-separated seat IDs (max 1000), e.g. seat\-001,seat\-002 |
|`--user-name` |string |Filter by bound sub\-user name |
|`--seat-status` |int |Seat binding status: 1=Idle, 2=Active |
|`--billing-status` |int |Billing status: 1=Pending, 2=Running, 3=Expired, 4=Reclaimed |
|`--project-name` |string |Project name (default: default) |
|`--page-number` |int |Page number (\>=1) |
|`--page-size` |int |Page size |

## pricing

Query foundation model settlement prices (including per\-account discounts) and Coding Plan and Coding Plan Team subscription prices.

* `Price` is the final post\-contract/promotion/subscription unit price; `OriginalPrice` is the list price;

* Use for cost estimation before model selection, plan value evaluation, and cross\-modal price comparison.

### pricing models

List foundation\-model billing schedule (Price reflects post\-discount unit price for the active account)

```Bash
arkcli pricing models
```

|Flag |Type |Description |
|---|---|---|
|`--model` |string |Filter to a single foundation model name (e.g. `seed-2-0-pro`) or a custom model id (cm\- *); cm\-*  is auto\-resolved to its base foundation model and the response carries a resolved_custom_model field |
|`--modality` |string |Filter by FoundationModelDomain (backend enum): LLM \| ComputerVision \| Audio \| Embedding \| Router |
|`--region` |string |Price region (e.g. ap\-southeast\-1); defaults to active profile region |
|`--page-number` |int |Page number (1\-based, default 1) |
|`--page-size` |int |Page size (default 50) |

### pricing plans

List BytePlus Coding Plan subscription pricing (personal & enterprise)

```Bash
arkcli pricing plans
```

|Flag |Type |Description |
|---|---|---|
|`--plan` |string |Filter to a single Coding Plan id, e.g. coding\-plan\-personal\-lite |
|`--product` |string |Filter by product: coding\-plan |
|`--edition` |string |Filter by edition: personal \| enterprise |
