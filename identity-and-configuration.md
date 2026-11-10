# Identity & Configuration

Identity and configuration: sign\-in, local config, non\-interactive bootstrap, and the Profile identity facet every command runs against.

* `auth` covers SSO sign\-in/out and ARK API keys; `config` covers local profile settings and the `update.mode` policy;

* `profile` is the `type × region × project × owner_trn × api_keys` identity facet and the config source for all commands;

* `resources` is read\-only and resolves an endpoint into its authoritative model and modality, the calibration baseline for Skills and the API Explorer.

## auth

Authentication and credential management: SSO login, status inspection, logout, and ARK API Key generation/selection.

* `auth login` is the interactive entry; use `--no-browser` two\-phase flow in headless environments (fetch URL, paste back the Base64 code);

* `auth login` signs in with BytePlus SSO and configures the profile;

* Credential storage paths are an implementation detail — business code always reads `cfg.APIKey` from the profile, never the storage directly.

### auth apikey

Fetch and configure an ARK API Key

```Bash
arkcli auth apikey
```

|Flag |Type |Description |
|---|---|---|
|`--key-id` |int64 |Specify the API Key ID to select (for non\-interactive mode) |

### auth apikey create

Create and save an ordinary ARK API Key

```Bash
arkcli auth apikey create
```

|Flag |Type |Description |
|---|---|---|
|`--yes` |bool |Skip the confirmation prompt (non\-interactive mode) |

### auth login

Log in to BytePlus ModelArk

```Bash
arkcli auth login
```

|Flag |Type |Description |
|---|---|---|
|`--code` |string |No\-browser Phase 2: enter the Base64 authorization code shown in the browser to complete login (for agents/sandboxes) |
|`--no-browser` |bool |No\-browser mode: complete SSO login on any device, then manually paste the authorization code |

### auth logout

Remove stored credentials

```Bash
arkcli auth logout
```

|Flag |Type |Description |
|---|---|---|
|`--reset-service-account` |bool |Reserved for products that manage an additional SDK login state |

### auth status

Show current auth status

```Bash
arkcli auth status
```

### auth whoami

Show the current authenticated identity

```Bash
arkcli auth whoami
```

## config

Local configuration management entry. Profile creation, inspection, switching, and deletion belong to the `profile` domain.

* Still supported here: `config reset` (wipes all local state, destructive), `config set update.mode automatic|disabled` (update policy), and `config lang`;

* Profile attribution and legacy yaml troubleshooting belong to the `profile` domain.

### config lang get

Print the effective persisted UI language

```Bash
arkcli config lang get
```

### config lang set

Persist the global UI language

```Bash
arkcli config lang set <locale>
```

### config lang unset

Remove the persisted UI language and use the build default

```Bash
arkcli config lang unset
```

### config reset

Remove all local config files

```Bash
arkcli config reset
```

### config set

Set an allowlisted global configuration value

```Bash
arkcli config set <key> <value>
```

## profile

A Profile is arkcli's unified identity facet between control plane and data plane: `type × region × project × owner_trn × api_keys` bound as one group, serving as the central config source for every command.

* Common operations: list/show/create/switch/delete/rename, manage API Keys and per\-type default resources inside a profile;

* `profile project` re\-selects the active project after login: project\-scoped profiles are re\-derived, personal plan profiles are kept as\-is — no identity swap, no re\-login;

* The first\-login project selector offers a synthetic "all account resources" option (the default), meaning no ProjectName is set and everything is account\-wide.

### profile apikey get

Print the plaintext default API key for a profile (machine\-readable)

```Bash
arkcli profile apikey get
```

|Flag |Type |Description |
|---|---|---|
|`--profile` |string |Profile name (default: active profile) |
|`--plain` |bool |Print only the key itself, without JSON envelope |

### profile create

Create a new profile (interactive or inline)

```Bash
arkcli profile create
```

|Flag |Type |Description |
|---|---|---|
|`--name` |string |Profile name (default: {type} *{region}* ) |
|`--type` |string |Profile type: platform \| coding\-plan \| coding\-plan\-team |
|`--region` |string |Region (fixed: ap\-southeast\-1) |
|`--project` |string |Project name (default: default) |
|`--default-api-key` |string |Default API key to bind (optional) |
|`--owner-trn` |string |Owner TRN (optional; usually left empty for self\-heal) |
|`--plan-tier` |string |[coding\-plan only] Force PlanTier (lite\|pro); bypasses ListSubscribeTrade detection |
|`--set-default` |bool |Set this profile as default after creation |
|`--no-interactive` |bool |Skip prompts; fail fast on missing required fields |

### profile delete

Delete a profile (must confirm with \-\-yes for non\-interactive)

```Bash
arkcli profile delete <profile>
```

|Flag |Type |Description |
|---|---|---|
|`--yes` |bool |Skip interactive confirmation (inline\-friendly) |

### profile apikey list

List API keys in current/specified profile (masked)

```Bash
arkcli profile apikey list
```

|Flag |Type |Description |
|---|---|---|
|`--profile` |string |Profile name (default: active) |

### profile apikey refresh

Re\-fetch API key list from control plane and update profile

```Bash
arkcli profile apikey refresh
```

|Flag |Type |Description |
|---|---|---|
|`--profile` |string |Profile name (default: active) |

### profile apikey use

Set default API key by 1\-based index (from `apikey list`) or full key

```Bash
arkcli profile apikey use <index|api-key>
```

|Flag |Type |Description |
|---|---|---|
|`--profile` |string |Profile name (default: active) |

### profile list

List all profiles (active profile marked)

```Bash
arkcli profile list
```

### profile models list

Show PlanTier + Resources default per modality

```Bash
arkcli profile models list
```

|Flag |Type |Description |
|---|---|---|
|`--profile` |string |Profile name (default: active) |

### profile models refresh

Re\-fetch text\-model list, update Resources.Text.Default (take first)

```Bash
arkcli profile models refresh
```

|Flag |Type |Description |
|---|---|---|
|`--profile` |string |Profile name (default: active) |

### profile project

Re\-select active project; deletes old platform profile and creates a fresh one for the new project

```Bash
arkcli profile project [<project-name>]
```

|Flag |Type |Description |
|---|---|---|
|`--yes` |bool |Skip interactive confirmation (inline\-friendly) |

### profile rename

Set display name for a profile (can contain Chinese, spaces)

```Bash
arkcli profile rename <profile> --to <display-name>
```

|Flag |Type |Description |
|---|---|---|
|`--to` |string (required) |Display name (can contain Chinese/spaces; empty string clears label) |

### profile set\-default

Set the default resource id for a modality on the active profile

```Bash
arkcli profile set-default <id>
```

|Flag |Type |Description |
|---|---|---|
|`--profile` |string |Profile name (default: active) |
|`--modality` |string |Modality: text \| image \| video |
|`--skip-verify` |bool |Skip inline verify (bypass control\-plane availability check) |

### profile show

Show profile detail (masked secrets, derived base_url)

```Bash
arkcli profile show [profile]
```

|Flag |Type |Description |
|---|---|---|
|`--profile` |string |Profile name (alternative to positional arg) |

### profile use

Switch the active profile used by all subsequent commands

```Bash
arkcli profile use [profile]
```

## resources

Realtime control\-plane resource queries: list resources visible to the current/specified profile with call compatibility, and resolve an Endpoint to its authoritative model, modalities, and candidate workflows.

* Read\-only; never writes profile.yaml. With an ad\-hoc `ep-...`, resolve here first to decide between chat/understand/gen;

* This is the calibration baseline for Skills and the API Explorer: returned model bindings and modalities are the real callable contract.

### resources list

List available resources for the active profile (real\-time, no cache)

```Bash
arkcli resources list
```

|Flag |Type |Description |
|---|---|---|
|`--profile` |string |Profile name (default: active) |
|`--modality` |string |Modality: text \| image \| video |

### resources resolve

Resolve an endpoint to its bound model, modalities, APIs, and candidate workflows

```Bash
arkcli resources resolve <endpoint-id>
```
