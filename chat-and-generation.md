# Chat & Generation

Data\-plane workflows and the records they produce: the `+chat`, `+gen` and `+understand` workflows, plus the domains that query their sessions and tasks afterwards.


* Open\-ended dialogue uses `+chat`, tasks with a defined output shape use `+understand`, generation uses `+gen`;

* `chat` and `gen` manage data\-plane artifacts after the fact (`get` / `list` / `delete`) and never start a new call themselves;

* All three workflows accept a temporary API key / base URL / endpoint, so scripts and CI runs are reproducible.


<span id="chat-command"></span>
## +chat

Fast chat / reasoning via the Responses API (multimodal with @file)

```Bash
arkcli +chat <prompt>
```



|Flag |Type |Description |
|---|---|---|
|`--model` |string |Model name or endpoint ID (defaults to active profile's Resources.Text.Default if omitted) |
|`--input` |string_slice |Input asset reference, e.g. @photo.jpg; repeatable for multiple files |
|`--stream` |bool |Stream the response incrementally |
|`--previous-response-id` |string |Continue from a prior response id |
|`--instructions` |string |System\-style instructions prepended to the prompt |
|`--temperature` |string |Sampling temperature (float, e.g. 0.7) |
|`--top-p` |string |Nucleus sampling top_p (float, e.g. 0.9) |
|`--max-output-tokens` |int |Cap on tokens emitted in the assistant reply |
|`--store` |bool |Persist this response so previous\-response\-id can resume from it later |
|`--reasoning-effort` |string |Reasoning depth: minimal \| low \| medium \| high |
|`--tools` |string_slice |Quick tool by type, e.g. \-\-tools web_search; repeat for multiple. For function tools use \-\-tools\-file. |
|`--tools-file` |string |Path to a JSON file holding a tools array (full SDK shape). Merged after \-\-tools entries. |
|`--tool-choice` |string |Tool invocation policy: auto \| required \| none |
|`--max-tool-calls` |int |Cap the number of tool calls per turn |
|`--caching` |string |Server\-side prompt cache: enabled \| disabled |
|`--cache-prefix` |bool |Enable prefix\-cache mode (only meaningful with \-\-caching enabled) |
|`--thinking` |string |Internal reasoning: auto \| enabled \| disabled |
|`--expire-at` |int |Stored response GC timestamp (epoch seconds; only meaningful with \-\-store) |
|`--include-events` |bool |Stream mode only: emit each SDK event as one NDJSON line on stdout instead of human\-readable text |
|`--text-format` |string |Output format: text \| json_object \| json_schema |
|`--text-schema` |string |Path to a JSON Schema file (required when \-\-text\-format=json_schema) |
|`--text-schema-name` |string |Name to attach to the JSON Schema (default: arkcli_response) |
|`--text-strict` |bool |Force strict schema compliance (only meaningful with \-\-text\-format=json_schema) |
|`--no-progress` |bool |Suppress the periodic stderr heartbeat shown during non\-stream calls |


<span id="gen-command"></span>
## +gen

Generate an asset from a prompt (image returns inline; video returns a task id immediately — pass \-\-wait to block until the video finishes)

```Bash
arkcli +gen <prompt>
```



|Flag |Type |Description |
|---|---|---|
|`--model` |string |Generation model name or endpoint ID (defaults to active profile's Resources..Default if omitted) |
|`--modality` |string |Explicit generation modality: image \| video (required when \-\-model is an endpoint ID; defaults to image when \-\-model is also omitted) |
|`--input` |string_slice |Reference asset; repeat for multiple. Local path with @ prefix, or http(s)/file/tos URL. Optional role prefix first:/last:/ref:/none: |
|`--name` |string |Optional task name override |
|`--version` |string |Model version override |
|`--size` |string |Image output size |
|`--image-count` |int |Number of images to generate (image task; \>1 enables sequential auto) |
|`--n` |int |Alias of \-\-image\-count (image task); takes precedence when both are set |
|`--resolution` |string |Asset resolution |
|`--duration` |int |Video duration in seconds |
|`--frames` |int |Video frame count (overrides duration on supporting models) |
|`--seed` |int |Seed for deterministic generation |
|`--watermark` |bool |Whether to add watermark |
|`--generate-audio` |bool |Whether to generate synchronized audio |
|`--guidance-scale` |string |Classifier\-free guidance scale (image task; float, e.g. 7.5) |
|`--optimize-prompt` |bool |Whether to apply server\-side prompt optimization (image task) |
|`--output-format` |string |Image output format: jpeg \| png |
|`--response-format` |string |Image response format: url \| b64_json (image task; server default url) |
|`--prompt-thinking` |string |Optimize\-prompt thinking mode: auto \| enabled \| disabled (image task; only relevant when \-\-optimize\-prompt is set) |
|`--prompt-mode` |string |Optimize\-prompt mode: standard \| fast (image task) |
|`--sequential` |string |Sequential image generation: auto \| disabled. Empty leaves arkcli's automatic auto\-mode (when \-\-image\-count \> 1) in place; pass disabled to force single\-image generation even with \-\-image\-count \> 1 |
|`--ratio` |string |Output aspect ratio override, e.g. 16:9 / 9:16 / 1:1 (video task) |
|`--camera-fixed` |bool |Lock the virtual camera (video task) |
|`--return-last-frame` |bool |Return last frame URL alongside final video (video task) |
|`--draft` |bool |Draft mode: faster + cheaper, lower quality (video task) |
|`--service-tier` |string |Service tier override (video task) |
|`--safety-id` |string |Caller\-supplied safety identifier (video task) |
|`--execution-expires-after` |int |Server\-side task TTL in seconds (video task) |
|`--priority` |int |Scheduling priority 0\-9, higher = sooner (video task; gated by per\-model supported_params) |
|`--callback-url` |string |URL to receive task lifecycle event POSTs (video task; created/running/succeeded/failed) |
|`--wait` |bool |Block until the video task finishes before returning (video task; default false — submit and return the task id immediately for async polling via 'arkcli gen get') |
|`--extra-body` |string |Forward\-compat JSON object whose keys merge into the top\-level wire request, e.g. \-\-extra\-body '{"new_field":"value"}' (video task) |
|`--tools` |string_slice |Enable a tool; currently supports web_search. Repeatable. |
|`--stream` |bool |Stream image generation frames as NDJSON (image task only); each line is one image_generation.\* event JSON |
|`--save-to` |string |Directory to save generated asset locally (default: current dir; pass \-\-save\-to="" to disable) (default: .) |
|`--force` |bool |Skip per\-model supported_params validation and submit as\-is (escape hatch when the model catalog is stale) |
|`--open` |bool |Open the generated asset(s) with the OS default app after download. Default: auto — opens only when stdout is an interactive terminal (a human ran it), stays quiet under agents/pipes/CI. Pass \-\-open to force on, \-\-no\-open to force off. |
|`--no-open` |bool |Never auto\-open the generated asset(s), even on an interactive terminal (overrides the auto/\-\-open default). |


<span id="understand-command"></span>
## +understand

Multimodal understanding (image/doc/video) via sub\-skills over the Responses API; omit the sub\-skill to auto\-route by \-\-input modality

```Bash
arkcli +understand <sub-skill|prompt> [prompt]
```



|Flag |Type |Description |
|---|---|---|
|`--input` |string_slice |Input asset, e.g. @photo.jpg; repeatable. Local image/video/doc files upload automatically. |
|`--model` |string |Override the sub\-skill's default model (name or endpoint ID) |
|`--stream` |bool |Stream the response incrementally |
|`--system-prompt-override` |string |Completely replace the sub\-skill's built\-in system prompt |
|`--system-prompt-append` |string |Append extra instructions after the sub\-skill's built\-in system prompt |
|`--temperature` |string |Sampling temperature (float, e.g. 0.7) |
|`--top-p` |string |Nucleus sampling top_p (float, e.g. 0.9) |
|`--max-output-tokens` |int |Cap on tokens emitted in the reply |
|`--reasoning-effort` |string |Reasoning depth: minimal \| low \| medium \| high |
|`--thinking` |string |Internal reasoning: auto \| enabled \| disabled |
|`--store` |bool |Persist this response so it can be retrieved later |
|`--no-progress` |bool |Suppress the periodic stderr heartbeat shown during non\-stream calls |
|`--timeout` |string |Maximum time for local file preparation and inference (for example 2m or 10m) |


<span id="domain-chat"></span>
## chat

Conversation record management: `chat get` inspects history, `chat delete` removes records, `chat list-input-items` lists session inputs.


* Data\-plane artifacts: records are produced by Responses API calls such as `+chat`; this domain handles post\-hoc query and cleanup;

* Read\-mostly; deletion has a confirmation gate.


<span id="chat-delete-command"></span>
### chat delete

Delete a stored response by ID

```Bash
arkcli chat delete <response-id>
```


<span id="chat-get-command"></span>
### chat get

Fetch a stored response by ID

```Bash
arkcli chat get <response-id>
```



|Flag |Type |Description |
|---|---|---|
|`--include` |string_slice |Include hint (image_url\|audio_url\|encrypted_content); repeat for multiple. NOTE: SDK ↔ backend versions can drift — current production accepts none of the documented values on some accounts; if you hit `Invalid value for 'include'` simply drop the flag |


<span id="chat-list-input-items-command"></span>
### chat list\-input\-items

Page through a stored response's input items

```Bash
arkcli chat list-input-items <response-id>
```



|Flag |Type |Description |
|---|---|---|
|`--after` |string |Cursor: list items after this item ID |
|`--before` |string |Cursor: list items before this item ID |
|`--limit` |int |Page size (1\-100, server default 20) |
|`--order` |string |Sort order: asc \| desc (server default desc) |
|`--include` |string_slice |Include hint (image_url\|audio_url\|encrypted_content); repeat for multiple. NOTE: SDK ↔ backend versions can drift — current production accepts none of the documented values on some accounts; if you hit `Invalid value for 'include'` simply drop the flag |


<span id="domain-gen"></span>
## gen

Generation task record management: `gen get` queries task details and results, `gen list` shows history, `gen delete` removes records.


* Tasks are produced by the `+gen` workflow; this domain handles post\-hoc query, retrieval, and cleanup;

* Video tasks execute asynchronously — poll status until rendering completes.


<span id="gen-delete-command"></span>
### gen delete

Delete an async content\-generation task by ID (must confirm with \-\-yes when non\-interactive)

```Bash
arkcli gen delete <task-id>
```



|Flag |Type |Description |
|---|---|---|
|`--yes` |bool |Skip interactive confirmation (inline\-friendly) |


<span id="gen-get-command"></span>
### gen get

Fetch an async content\-generation task by ID (auto\-downloads the asset once the task has succeeded)

```Bash
arkcli gen get <task-id>
```



|Flag |Type |Description |
|---|---|---|
|`--save-to` |string |Directory to save the asset locally once the task succeeded (default: current dir; pass \-\-save\-to="" to disable) (default: .) |
|`--open` |bool |Open the downloaded asset with the OS default app. Default: auto — opens only on an interactive terminal, stays quiet under agents/pipes/CI. Pass \-\-open to force on, \-\-no\-open to force off. |
|`--no-open` |bool |Never auto\-open the downloaded asset, even on an interactive terminal (overrides the auto/\-\-open default). |


<span id="gen-list-command"></span>
### gen list

Page through async content\-generation tasks (video / 3D / future modalities)

```Bash
arkcli gen list
```



|Flag |Type |Description |
|---|---|---|
|`--page-num` |int |1\-indexed page number; server default applies when omitted |
|`--page-size` |int |Page size; server default applies when omitted |
|`--status` |string |Filter by task status: succeeded \| failed \| running \| queued \| cancelled |
|`--model` |string |Filter by model name or endpoint ID |
|`--service-tier` |string |Filter by service tier override |
|`--task-id` |string_slice |Filter by task ID; repeat for multiple |
