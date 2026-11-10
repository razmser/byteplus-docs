# Tools & Platform

Integration and tooling: connecting ModelArk to third\-party agents, installing Skills, using the raw API fallback, and updating the CLI.

* `helper` configures model/provider and Harness tools; `+connect` installs ArkCLI Skills;

* `api` is the fallback path for registered actions — use a product command first, and reach for this only when nothing covers the case;

* `update` only runs when invoked explicitly (no silent upgrades).

## +connect

Install arkcli AI skills to all detected local agents

```Bash
arkcli +connect
```

|Flag |Type |Description |
|---|---|---|
|`--path` |string |Install arkcli skills to a specific local skills directory instead of detected global agent dirs; relative paths resolve from the current working directory |
|`--refresh` |bool |External build: redownload the latest skill from the CDN (preferred) or GitHub (fallback) before installation (the internal build embeds the skill, so this flag has no effect) |
|`--keep-conflicting` |bool |Keep third\-party generation skills (byted\-ark\-seedance/seedream) that otherwise hijack +gen routing; by default +connect removes them from every agent dir |

### +connect list

List supported AI agents and detection status

```Bash
arkcli +connect list
```

### +connect uninstall

Uninstall arkcli AI skills from detected local agents

```Bash
arkcli +connect uninstall
```

|Flag |Type |Description |
|---|---|---|
|`--path` |string |Uninstall arkcli skills only from a specific local skills directory instead of detected global agent dirs; relative paths resolve from the current working directory |
|`--purge-prefix` |bool |Destructively remove every ark\-/arkcli\- prefixed directory or symlink, including entries not managed by ArkCLI |

## api

Raw API explorer for registered actions

```Bash
arkcli api [registered-action]
```

|Flag |Type |Description |
|---|---|---|
|`--dry-run` |bool |Preview the client execution plan without network requests or side effects |
|`--list` |bool |List all registered API actions |
|`--params` |string |JSON request payload |

## helper

Interactive wizard: configure local AI agents for a Platform Endpoint or supported Plan

```Bash
arkcli helper
```

### helper configure

Configure one harness non\-interactively for a Platform Endpoint or supported Plan

```Bash
arkcli helper configure <harness>
```

|Flag |Type |Description |
|---|---|---|
|`--profile` |string |Target Platform / Plan profile (default: currently active profile) |
|`--model` |string |Plan model ID (default: selected/first model in the plan; unavailable for Platform) |
|`--endpoint` |string |Verified Platform text Endpoint ID (required only for a Platform profile) |
|`--codex-config-scope` |string |(Codex only) Config scope: profile (default, writes ~/.codex/arkcli.config.toml) \| global (writes ~/.codex/config.toml) |
|`--codex-profile` |string |(Codex profile mode only) Codex profile name; default: arkcli |
|`--dsh-config-scope` |string |(DSH only) config scope: home (default, writes $DSH_HOME/cordis.patch.yml) \| profile (writes $DSH_HOME/profiles//cordis.patch.yml; requires \-\-dsh\-profile) |
|`--dsh-profile` |string |(DSH only) DSH profile name for profile scope |

### helper list

List supported AI agents with their installation status and config paths

```Bash
arkcli helper list
```

### helper reset

Remove model/provider configuration injected by arkcli for a harness while preserving other user settings

```Bash
arkcli helper reset <claude-code|codex|deepseek-harness|pi|opencode|openclaw|hermes|zcode|kimi-code|grok|workbuddy>
```

|Flag |Type |Description |
|---|---|---|
|`--codex-config-scope` |string |(Codex only) Removal scope: profile (default, ~/.codex/arkcli.config.toml) \| global (~/.codex/config.toml) |
|`--codex-profile` |string |(Codex profile mode only) Codex profile name; default: arkcli |
|`--dsh-config-scope` |string |(DSH only) config scope: home (default, writes $DSH_HOME/cordis.patch.yml) \| profile (writes $DSH_HOME/profiles//cordis.patch.yml; requires \-\-dsh\-profile) |
|`--dsh-profile` |string |(DSH only) DSH profile name for profile scope |

## update

Upgrade arkcli to the latest stable release

```Bash
arkcli update
```

|Flag |Type |Description |
|---|---|---|
|`--check` |bool |Only check for a new version; do not upgrade |
|`--refresh` |bool |With \-\-check: force a live registry query (default reads the local cache) |
|`--yes` |bool |Skip the interactive confirmation and upgrade directly |
