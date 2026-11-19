# Resource Registry

**Status:** proposed registry and account contract

## 1. Purpose

The registry is the source of truth for what the orchestrator may use. It covers hosted APIs, gateways, coding CLIs, local model servers, independent accounts, browser profiles, browser-AI services, and MCP-backed resources.

It does not store raw credentials and does not turn historical free-tier research into permanent configuration.

## 2. Identity model

Separate three concepts:

| Concept | Example | Meaning |
|---|---|---|
| Adapter/driver | `opencode`, `kiro`, `openai_compatible` | Integration implementation |
| Resource | `opencode-console`, `freellmapi`, `local-ollama` | Logical execution target and capabilities |
| Provider instance/account | `opencode:work`, `opencode:personal` | Independent credentials, environment, catalog, health, and native sessions |

The router selects a provider instance. Sharing a driver does not imply sharing account state.

## 3. Declared resource record

Illustrative schema, not an existing config file:

```yaml
id: opencode-console
type: coding_cli
adapter: opencode_native
enabled: true
capabilities:
  domains: [code, debugging, repository_work]
  strength_tier: 6
  features:
    streaming: true
    native_resume: true
    workspace_write: true
    approvals: true
    subagents: provider_dependent
  context_window: null
privacy:
  class: provider_dependent
  data_use_requires_review: true
selection:
  cost_tier: free_or_byok
  locality: local_process_remote_inference
catalog:
  discovery: provider_live_catalog
  static_model_allowlist: []
```

### Required capability distinctions

- `strength_tier`: locally maintained ordinal used only for candidate ordering.
- `context_window`: tokens if known; `null` means unknown.
- `features`: streaming, tools, vision, structured output, workspace write, native resume, native fork, rollback, approvals, model switch, subagents.
- `resource_types`: LLM API, gateway, coding CLI, local server, browser, MCP.
- `privacy`: training, retention, logging, region, confidentiality eligibility, and source timestamp.
- `integration_policy`: permitted client/transport/embedding shape, including third-party-agent and account-rotation restrictions.
- `cost`: declared price/credit model if officially known; otherwise unknown.
- `provenance`: official URL, local probe, admin declaration, or historical inference.

A feature that has not been tested is `unknown`, not `true`.

## 4. Provider-instance/account record

```yaml
id: opencode:work
resource_id: opencode-console
enabled: true
auth:
  method: external_process
  secret_refs: []
environment:
  XDG_CONFIG_HOME: /home/deval/.accounts/opencode-work/.config
  XDG_DATA_HOME: /home/deval/.accounts/opencode-work/.local/share
  remove_ambient: true
process:
  ownership: one_server_per_account
  native_state: provider_managed
privacy_overrides: {}
limits:
  concurrency: 1
  rpm: null
  monthly_credits: null
```

Rules:

1. `secret_refs` contains references, never raw tokens.
2. The environment is constructed explicitly; ambient credentials are removed unless declared.
3. Paths are examples only. Each adapter must document the actual provider config/auth/session locations before claiming isolation.
4. Account-specific quota and privacy values override resource defaults only with evidence.
5. Per-provider concurrency is shared across its accounts when upstream limits apply provider-wide.

## 5. Observed account state

Stored separately from declared configuration:

| Field | Meaning |
|---|---|
| `status` | `registered`, `probing`, `healthy`, `degraded`, `cooldown`, `authentication_required`, `disabled` |
| `available` | Current eligibility after a fresh check; not an eternal static boolean |
| `cooldown_until` | Provider reset/backoff deadline if known |
| `quota_remaining` | Provider-reported value or `null` |
| `last_error_class` | Normalized auth/rate/quota/provider/capability/timeout class |
| `consecutive_failures` | Drives bounded circuit breaking |
| `last_used_at` / `last_verified_at` | Usage versus verification are separate timestamps |
| `active_executions` | Local concurrency accounting |
| `catalog_version` | Snapshot used by routing |

`quota_remaining: null` and `context_window: null` mean unknown, not unlimited.

## 6. Discovery and catalog lifecycle

### 6.1 Sources

Preference order:

1. official live provider catalog or supported API;
2. provider-native local metadata;
3. explicit administrator declaration;
4. curated local overrides with provenance and expiry;
5. historical research only as an unverified lead.

### 6.2 Refresh

- Refresh on service startup, scheduled interval, explicit operator request, and provider-signalled model failure.
- Keep a catalog timestamp/version with every plan.
- Probe free resources before marking them healthy after a stale interval.
- A removed or deprecated upstream model becomes ineligible even if an old static list still contains it.
- Preserve tombstones so an old cached catalog cannot reintroduce a retired model.
- Never hardcode a permanent list of rotating `:free` model IDs into the router.

### 6.3 FreeLLMAPI

FreeLLMAPI may supply model capabilities, model groups, quotas, cooldowns, and gateway-owned fallback when restored and verified. Prefer a supported read API. A local read-only SQLite path is acceptable only as an explicitly configured integration with schema-version checks; never write to its database.

On 2026-11-19 its user service was inactive. Historical provider/model counts and decision-endpoint availability remain research, not current registry contents.

## 7. Privacy labels

Every provider instance carries a reviewable privacy record:

| Field | Examples |
|---|---|
| `confidentiality` | `suitable`, `unsuitable`, `review_required`, `unknown` |
| `training` | `no_training`, `may_train`, `provider_dependent`, `unknown` |
| `retention` | official duration/policy or `unknown` |
| `logging` | provider security/product improvement/human review where documented |
| `region` | hosting and eligibility constraints where documented |
| `source_url` / `verified_at` | evidence and freshness |

Kilo Auto Free's official documentation warns that it may route to providers that log prompts/outputs and use them for improvement. It must therefore default to `review_required` for confidential content, regardless of price or capability.

## 8. Isolation verification matrix

Do not assume blanket `HOME` isolation. Verify per adapter:

| Boundary | Required evidence before multi-account enablement |
|---|---|
| Credentials | Actual files/database/keychain entries and environment variables |
| Config | Provider-read config path and precedence |
| Native sessions | Storage path and account/session association |
| Background servers | Whether state is shared per account, directory, or machine |
| MCP/hooks/global skills | What is intentionally shared versus isolated |
| Cache/catalog | Whether one account can observe another's catalog or usage |
| Process environment | Undeclared ambient credentials removed |
| Logout/revocation | Existing processes stopped before credentials are cleared |

OpenCode and Antigravity examples in T3 demonstrate why instance-specific server and credential handling matter. They do not prove isolation for every CLI.

## 9. Browser resources

A browser profile record includes:

- dedicated profile path and owning process;
- authentication state (`unknown`, `authenticated`, `expired`, `manual_action_required`);
- permitted services and allowed automation scope;
- CDP endpoint or local launch reference;
- privacy/retention classification;
- no exported cookie/token values in the registry.

A browser profile is not automatically healthy because a directory exists. Authentication and service eligibility require a fresh, non-destructive check.

## 10. Validation and acceptance criteria

- Registry rejects raw secret-looking values and invalid secret references.
- Two accounts of one driver can hold different environment and health state.
- Registry distinguishes declared, observed, and inferred values with provenance.
- A stale/deprecated model cannot be selected after refresh.
- Health checks cause no login, model generation, billing, hook, or workspace side effect.
- A resource with unknown privacy cannot receive confidential input.
- Removing an account disables new plans but does not delete native sessions or execution history.
- Every field exposed through `list_resources` is redacted and safe for Hermes tool output.
