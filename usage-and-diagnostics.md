# Usage & Diagnostics

Usage, billing and diagnostics: inference volume, settlement amounts, plus health checks and error\-code attribution.

* `usage` reports inference volume (near real time, 5\-30 minute lag); `billing` reports settlement amounts (T+1, finance ledger) — the two must not be mixed;

* On any error, start with `doctor error` for error\-code attribution, then follow its routing advice;

* `doctor` also covers endpoint / model / metrics diagnostics and Ark media origin verification.

## billing

Query ModelArk itemized billing (settled amounts, token\-based charges), filterable by billing month, month range, Endpoint, API Key, and product code.

* Billing is the financial view: T+1 settlement amounts, distinct from the near\-realtime inference volume in the `usage` domain;

* Use it for reconciliation, per\-EP / per\-API\-Key cost breakdowns, and monthly cost review.

### billing list

List ARK split\-bill detail (settlement amount × token usage) for a billing month or month range. Defaults to account\-wide ARK billing; pass \-\-endpoint/\-\-apikey/\-\-apikey\-sid to drill down, \-\-split\-dim apikey for API\-key attribution, or \-\-product to override the product set.

```Bash
arkcli billing list
```

|Flag |Type |Description |
|---|---|---|
|`--start` |string (required) |Single billing month in YYYY\-MM (max 24 months ago). Single month if used alone; pair with \-\-end for a range. Required. |
|`--end` |string |Range end month in YYYY\-MM (inclusive). Omit for single\-month query. Range capped at 24 months. |
|`--day` |string |Filter to a specific YYYY\-MM\-DD within \-\-start (perf hint upstream). Requires \-\-interval=day or \-\-interval=detail; incompatible with \-\-end range |
|`--interval` |string |Aggregation granularity: month (default) \| day \| detail |
|`--endpoint` |string |Filter to a specific endpoint ID (ep\-...). Mutually exclusive with \-\-apikey/\-\-apikey\-sid. |
|`--apikey` |string |Filter by API key value (ark\-...); shortcut auto\-resolves it to apikey SID via arkbff. Mutually exclusive with \-\-endpoint/\-\-apikey\-sid. |
|`--apikey-sid` |string |Filter by API key SID (apikey\-{ts}\-{xxx}; from `arkcli api apikey.list`). Skips arkbff resolution. Mutually exclusive with \-\-endpoint/\-\-apikey. |
|`--split-dim` |string |Slice account\-wide billing by API key: apikey. BytePlus does not support account\-wide endpoint splitting; use \-\-endpointfor one endpoint. Empty = account\-level totals (default). |
|`--product` |string_slice |Filter by billing product code (repeatable). Omit to see all products; the Product field on each returned row tells you the real codes available for this account. |
|`--project` |string_slice |Filter by project ID (repeatable). Defaults to the profile's selected project when unset (skipped only when profile has no project / 'account\-wide'); pass \-\-project= to clear, or \-\-projectto override. |
|`--instance-no` |string |Filter to a specific billing instance ID |
|`--bill-category` |string_slice |Filter by bill category (repeatable): consume\-use, consume\-new, consume\-renew, consume\-formalize, consume\-modify, consume\-trial, refund\-terminate, refund\-modify, transfer\-manual, transfer\-system |
|`--billing-mode` |string_slice |Filter by billing mode (repeatable): 1 (subscription) \| 2 (pay\-as\-you\-go) \| 3 (contract) \| 4 (fulfillment) |
|`--ignore-zero` |bool |Skip line items with discounted price = 0 |
|`--mine` |bool |Limit to bills attributed to resources owned by the current SSO sub\-user. Pair with \-\-mine\-by to choose attribution dimension. Subscription/account\-level bills (Coding Plan / Coding Plan Team) are excluded since they have no IAM owner. Mutually exclusive with \-\-endpoint/\-\-apikey/\-\-apikey\-sid/\-\-split\-dim. |
|`--mine-by` |string |Scope of \-\-mine: endpoint (default, 'infra ownership' — total spend on endpoints I deployed, regardless of caller) \| apikey (cost causation — bills caused by my API keys). Two views never overlap and are not addable: endpoint rolls up by which infra was used; apikey rolls up by who paid. Default aligns with usage stats \-\-mine\-by. |
|`--limit` |int |Pagination mode: return at most N rows per wire call (1\-300). In multi fan\-out (\-\-end / \-\-mine), this applies per fan\-out; total returned ≤ limit × fanout_count. Always check is_truncated + partial_failures summary before sum. |
|`--offset` |int |Pagination mode: skip N rows per wire call. Loop offset += limit until offset \>= total_records (single fan\-out) or use shell loop for multi fan\-out. |
|`--output` |string |Write the full Output (items + summary + partial_failures) to FILE as JSON; stdout shows metadata only (items omitted). Auto\-raises the internal page cap to 1000 (300k rows) so large accounts can dump full detail in one go; user \-\-page\-limit=N still wins. |

## doctor

Run CLI health check (installation / connectivity / configuration)

```Bash
arkcli doctor
```

### doctor +verify\-origin

Verify ModelArk generation\-origin features for 1\-20 images or videos

```Bash
arkcli doctor +verify-origin <media-url> [media-url...]
```

|Flag |Type |Description |
|---|---|---|
|`--query-id` |string_slice |Resume an existing verification QueryID without creating a task; repeat up to 20 times |
|`--timeout` |string |Maximum wait time for the entire batch, for example 2m or 10m (default: 2m0s) |
|`--yes` |bool |After reviewing the price, confirm once for every media item in this batch and create all verification tasks |

### doctor account

Diagnose the current BytePlus account (identity / readiness / balance / IAM / VMP / TOS)

```Bash
arkcli doctor account
```

### doctor error

Diagnose an Ark error code and route to its fix reference

```Bash
arkcli doctor error <code>
```

### doctor infer\-endpoint

Diagnose an inference endpoint (status, model, usage, quota pressure)

```Bash
arkcli doctor infer-endpoint <endpoint-id>
```

|Flag |Type |Description |
|---|---|---|
|`--auto-bind` |bool |When BytePlus VMP and the ModelArk service\-linked role are ready but no workspace is bound, reuse the first workspace or create ark_default and bind ModelArk telemetry |
|`--window` |string |Lookback window in Go duration syntax (e.g. 1h, 24h; '7d' is not supported by time.ParseDuration) |

### doctor metrics

Query named PromQL metrics from VMP (catalog\-driven)

```Bash
arkcli doctor metrics <id>
```

|Flag |Type |Description |
|---|---|---|
|`--auto-bind` |bool |When BytePlus VMP and the ModelArk service\-linked role are ready but no workspace is bound, reuse the first workspace or create ark_default and bind ModelArk telemetry |
|`--filter` |stringArray |Inject an extra label\-matcher (repeatable, label=value); template must declare a ${@filters} directive (default: []) |
|`--format` |string |Output shape (bare\-form 'metrics' only — list/describe always print JSON): json \| scalar \| table |
|`--param` |stringArray |Bind a catalog parameter (repeatable, key=value) (default: []) |
|`--step` |string |Sample step (Go duration; default = window/60 clamped to [10s,60s]) |
|`--window` |string |Lookback window (Go duration; '7d' not supported) (default: 1h) |
|`--workspace-id` |string |VMP workspace UUID (default: auto\-resolve via ark telemetry config) |

### doctor metrics describe

Show one catalog entry's full metadata (JSON output only)

```Bash
arkcli doctor metrics describe <id>
```

### doctor metrics list

List all named PromQL queries in the catalog (JSON output only)

```Bash
arkcli doctor metrics list
```

### doctor metrics raw

Run an arbitrary PromQL query (must reference an ark_\* metric)

```Bash
arkcli doctor metrics raw
```

|Flag |Type |Description |
|---|---|---|
|`--auto-bind` |bool |When BytePlus VMP and the ModelArk service\-linked role are ready but no workspace is bound, reuse the first workspace or create ark_default and bind ModelArk telemetry |
|`--format` |string |Output shape: json \| scalar \| table |
|`--promql` |string |PromQL expression (required) |
|`--step` |string |Sample step (Go duration; default auto) |
|`--window` |string |Lookback window (Go duration; '7d' not supported) (default: 1h) |
|`--workspace-id` |string |VMP workspace UUID (default: auto\-resolve) |

### doctor model

Diagnose a foundation model (existence, endpoints, usage, error rate, quota)

```Bash
arkcli doctor model <model-name>
```

|Flag |Type |Description |
|---|---|---|
|`--auto-bind` |bool |When BytePlus VMP and the ModelArk service\-linked role are ready but no workspace is bound, reuse the first workspace or create ark_default and bind ModelArk telemetry |
|`--window` |string |Lookback window (Go duration syntax, e.g. 1h, 24h) |

## usage

ModelArk usage queries: `usage stats` (tokens/requests, 5\-30 min delay), `usage plan` (plan quota snapshot), `usage balance` (free quota / media asset library / plan), `usage seats --with-usage` (team seat usage).

* stats reports inference volume (near realtime); billing reports settled amounts (T+1 financial view) — never mix the two;

* Coding Plan only has a quota snapshot, no per\-model plan details — do not substitute stats.

### usage balance

Query the account balance from one of three perspectives: free\-quota (per\-model free inference quota / resource package balance), media\-asset (asset library / media library capacity), or plan (subscription plan balance)

```Bash
arkcli usage balance
```

|Flag |Type |Description |
|---|---|---|
|`--type` |string (required) |Balance type (required): free\-quota \| media\-asset \| plan |
|`--model` |string |[free\-quota] Filter to a single foundation model name (e.g. `seed-2-0-pro`) |
|`--modality` |string |[free\-quota] FoundationModelDomain enum: LLM \| ComputerVision \| Audio \| Embedding \| Router (no aliases) |
|`--page-number` |int |[free\-quota] Page number (1\-based) |
|`--page-size` |int |[free\-quota] Page size (default 20; reduced from 50 to keep ListModelChargeItems within the 10s timeout budget) |
|`--project` |string |[media\-asset] Project scope; defaults to active profile project |
|`--product` |string |[plan] Plan id: coding\-plan \| coding\-plan\-team |
|`--all` |bool |[plan] Query Coding Plan subscriptions even if not subscribed |
|`--seat` |string |[plan] Filter to a specific SeatID (team\-edition only) |

### usage plan

Query Coding Plan and Coding Plan Team quota and period usage

```Bash
arkcli usage plan
```

|Flag |Type |Description |
|---|---|---|
|`--product` |string |Plan product id: coding\-plan \| coding\-plan\-team. Default = auto\-discover Coding Plan subscriptions. |
|`--all` |bool |Query coding\-plan and coding\-plan\-team even if not subscribed. Mutually exclusive with \-\-product. |
|`--seat` |string |[team] Specific SeatID; defaults to caller's bound seat via GetSeatInfo |

### usage seats

List Coding Plan Team seats (admin view). Requires admin permission; sub\-user calls return AccessDenied.

```Bash
arkcli usage seats
```

|Flag |Type |Description |
|---|---|---|
|`--product` |string (required) |Team plan id (required): coding\-plan\-team |
|`--biz-info` |string |Filter by Coding Plan tier: lite \| pro |
|`--billing-status` |string_slice |Filter by billing status (repeatable): Pending / Running / Expired / Reclaimed |
|`--seat-status` |string |Filter by seat status: Idle / Active |
|`--user-id` |string_slice |Filter by sub\-user ID (repeatable; \>1 triggers client\-side fan\-out per user, results merged & deduped by SeatID) |
|`--user-name` |string_slice |Filter by sub\-user name (repeatable; \>1 triggers client\-side fan\-out) |
|`--seat-id` |string_slice |Filter to specific SeatID(s) (repeatable; bypasses project filter) |
|`--project` |string |Project scope. Resolves: this flag \> literal `default`. `plans buy --plan *-team` always creates seats in `default`, so we deliberately ignore the active profile project here — falling back to profile project would silently return an empty list when profile project != `default`. |
|`--page-size` |int |Page size (default 100) |
|`--page-number` |int |1\-based page number; pass to disable auto\-paginate and fetch a single page |
|`--sort-by` |string |Sort field |
|`--sort-order` |string |Sort order: ASC \| DESC |
|`--with-usage` |bool |Join Coding Plan Team per\-seat usage. Adds coding_usage to each row. |

### usage stats

Query inference usage (tokens / requests) by date range, filterable by model, endpoint, or API key

```Bash
arkcli usage stats
```

|Flag |Type |Description |
|---|---|---|
|`--start` |string (required) |Start date in YYYY\-MM\-DD format (required) |
|`--end` |string |End date in YYYY\-MM\-DD format, defaults to today |
|`--interval` |string |Query granularity: Day \| Hour |
|`--endpoint` |string |Filter by endpoint ID |
|`--model` |string |Filter by model name |
|`--apikey` |string |Filter by API key (Values exact match, legacy) |
|`--apikey-suffix` |string |Filter by API key suffix (ValueLike match, e.g. last 12 chars of the key) |
|`--mine` |bool |Limit to resources owned by the current SSO sub\-user; pair with \-\-mine\-by |
|`--mine-by` |string |Scope of \-\-mine: endpoint (default) \| apikey |
|`--by` |string |Group dimensions: model, endpoint, apikey |
|`--show-window-detail` |bool |Break down per\-call billing windows in the response |
