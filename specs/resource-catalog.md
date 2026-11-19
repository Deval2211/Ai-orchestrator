# Candidate Resource Catalog

**Status:** dated research input for the registry, not a static allowlist  
**Primary-source verification date:** 2026-11-19  
**Rule:** confirm live availability, account requirements, limits, and privacy during onboarding and refresh. A table entry is not a promise of future service.  
**Detailed evidence:** [`../docs/research/14_FREE_RESOURCE_PRIMARY_SOURCE_REVIEW.md`](../docs/research/14_FREE_RESOURCE_PRIMARY_SOURCE_REVIEW.md)

## 1. Registry implications

1. Keep provider/resource IDs stable; discover model IDs dynamically.
2. Store official source URL, verification timestamp, privacy terms, and observed health with each provider instance.
3. Treat “free” as a billing label only. It says nothing about capability, confidentiality, retention, or reliability.
4. Unknown limits and policies remain `null`/unknown, never unlimited or assumed safe.
5. A fallback chain labeled free must contain only free-eligible routes unless paid fallback is explicitly approved.
6. Free-tier exhaustion triggers cooldown or an eligible alternative, never account farming.

## 2. Coding CLIs and integrated agent resources

| Resource | Officially supported free access as checked | Important constraints | Official sources |
|---|---|---|---|
| **Kilo Auto Free** | `kilo-auto/free` selects dynamically per session from a curated free pool; no credits required. Official billing docs limit **all free-model requests, anonymous or authenticated, to 200 requests/hour/IP** and return HTTP 429 at exhaustion. | Mapping changes server-side. Kilo warns that Auto Free may route to providers that log prompts/outputs and use them for improvement; do not send personal/confidential data. | [Using Kilo for Free](https://kilo.ai/docs/getting-started/using-kilo-for-free) · [Models & Providers](https://kilo.ai/docs/gateway/models-and-providers) · [Usage & Billing](https://kilo.ai/docs/gateway/usage-and-billing) |
| **OpenCode free models** | OpenCode Console lists rotating limited-time free models available inside OpenCode. A direct `opencode/deepseek-v4.1-flash-free` smoke test succeeded on 2026-11-19. | Free chat models require OpenCode; other clients receive 403. `GLM-5.3-Flash Free` is marked deprecated 2026-11-18. Privacy is model-specific: DeepSeek free is documented zero-retention/no-training; several other free models collect feedback or retain data. | [Console models](https://opencode.ai/docs/console/models) · [Inference API](https://dev.opencode.ai/docs/console/api/inference) |
| **AWS Kiro Free** | Perpetual $0 tier includes 50 credits/month. Official pricing says it includes open-weight models and limited Claude Sonnet 4.5 access; CLI shares the same plans as IDE. | Credits are work units, not requests; Free has no add-on credits. Free-tier content may be used for service improvement by default subject to opt-out; inputs may separately be stored up to 60 days for abuse detection. Free/individual content is stored in US East (N. Virginia). | [Pricing](https://kiro.dev/pricing/) · [CLI billing](https://kiro.dev/docs/cli/billing/) · [Data protection](https://kiro.dev/docs/privacy-and-security/data-protection/) |
| **Google Antigravity Individual** | $0/month individual plan includes app/CLI/IDE/Python SDK access and a baseline agent quota refreshed weekly. | Numeric baseline quota is unpublished. Claude Sonnet 4.6, Claude Opus 4.6 and GPT-OSS-120b have an official 2026-11-18 removal footnote despite still-visible names. Consumer terms do not support wrapping `agy` in third-party agents or account rotation; use only a permitted direct/API/enterprise path. Data collection opt-out exists but is not proof of zero retention. | [Pricing](https://antigravity.google/pricing/) · [FAQ and integration restrictions](https://antigravity.google/docs/faq?id=GoogleAntigravity) |
| **GitHub Copilot Free / CLI** | Copilot Free includes CLI access, auto model selection, an AI Credits allowance, and 2,000 code completions/month. Copilot Student includes unlimited completions subject to eligibility. | Free/Student models use automatic selection. Exact free AI-credit volume is not published on the checked plan page. From 2026-04-24, Free interactions may be used for model training; individual users can opt out in Copilot settings. | [Plans](https://docs.github.com/en/copilot/get-started/plans) · [Individual billing](https://docs.github.com/en/copilot/concepts/billing-and-usage/individuals/billing) · [Training policy](https://docs.github.com/en/copilot/how-tos/manage-your-account/manage-policies) |
| **Mistral Vibe Free mode** | Free mode includes a monthly usage budget for trying Vibe and Studio. Vibe CLI supports account sign-in and programmatic mode. | **Current official docs say Free mode does not include API access.** Creating API keys/using the API requires billing activation. Vibe Free inputs/outputs are used for model training by default unless the user opts out. `--max-price` is only indicative when model prices are missing/outdated. | [Usage and limits](https://docs.mistral.ai/admin/billing-usage/usage-limits) · [CLI setup](https://docs.mistral.ai/vibe/code/cli/install-setup) · [CLI operation](https://docs.mistral.ai/vibe/code/cli/work-with-cli) · [Training policy](https://help.mistral.ai/en/articles/347617-do-you-use-my-user-data-to-train-your-artificial-intelligence-models) |
| **Cline free promotions** | Rotating limited-time free model promotions for users with a Cline account, available in the IDE extension and CLI. | Not supported through the Cline API. Official docs say free usage may be used to improve model performance/quality. Quota is limited and model availability changes. | [Free Models](https://docs.cline.bot/getting-started/free-models) |

### OpenCode verification note

Two different outcomes were observed locally and must not be conflated:

- direct OpenCode CLI with isolated scratch state: `opencode/deepseek-v4.1-flash-free` returned the requested smoke marker;
- Hermes OpenCode Free ACP bridge: exited before replying and was not used for further delegation.

This demonstrates a working CLI/model route, not a working ACP adapter. Every transport needs its own contract test.

## 3. Hosted inference APIs and gateways

| Resource | Officially supported free access as checked | Important constraints | Official sources |
|---|---|---|---|
| **OpenRouter `:free` / Free Models Router** | `:free` variants and `openrouter/free` provide zero-cost inference from a changing free pool. Official limits list 20 RPM plus 50 requests/day below 10 lifetime purchased credits, rising to 1,000/day at 10+. The router filters requested features and falls back only among free models. | A manually configured fallback from free to paid **is billed**. Use only free fallbacks for zero spend. Upstream privacy varies; OpenRouter metadata/safety retention and upstream terms are separate. Read current quota through `GET /api/v1/key`. | [Free Variant](https://www.openrouter.ai/docs/guides/routing/model-variants/free) · [Free Models Router](https://openrouter.ai/docs/guides/routing/routers/free-router) · [Limits](https://www.openrouter.ai/docs/api_reference/limits) · [Data collection](https://www.openrouter.ai/docs/guides/privacy/data-collection) |
| **Groq Cloud Free Plan** | Official rate-limit page lists Free Plan model limits. Examples checked include `openai/gpt-oss-120b`, `openai/gpt-oss-20b`, and `qwen/qwen3.8-27b` at 30 RPM, 1K RPD, 8K TPM, 200K TPD. | Limits apply at organization level and vary by model; use exact account limits/headers. Groq's agreement prohibits training/fine-tuning on inputs/outputs unless the customer grants permission or instructs it; third-party model terms still apply. | [Rate Limits](https://console.groq.com/docs/rate-limits) · [Services Agreement](https://console.groq.com/docs/legal/services-agreement) |
| **Cloudflare Workers AI** | 10,000 Neurons/day free allocation, reset at 00:00 UTC, available on Workers Free and Paid plans. | Further operations fail at exhaustion. Standard non-stateful inference has zero content retention after processing/delivery and no training; stateful features, directed storage, gateway logging/caching, legal/safety exceptions and operational metadata are separate. | [Pricing](https://developers.cloudflare.com/workers-ai/platform/pricing/) · [Data usage](https://developers.cloudflare.com/workers-ai/platform/data-usage/) · [Errors](https://developers.cloudflare.com/workers-ai/platform/errors/) |
| **Gemini API / Google AI Studio Free** | New accounts can begin on Free tier for certain models; official pricing lists free-of-charge entries for eligible models. | Rate limits are per project, tier/account-specific, shown in AI Studio, and not guaranteed. Free-tier content may be used to improve Google products; do not route confidential code there without an explicit applicable exception. India is listed in the official supported-region list. | [Billing](https://ai.google.dev/gemini-api/docs/billing) · [Rate limits](https://ai.google.dev/gemini-api/docs/rate-limits) · [Pricing](https://ai.google.dev/gemini-api/docs/pricing) · [Regions](https://ai.google.dev/gemini-api/docs/available-regions) |
| **NVIDIA NIM / API Catalog** | NVIDIA Developer Program provides free hosted endpoints for prototyping/research/development/testing. Build pages describe most models as rate-limited up to 40 RPM, with the personal limit shown in the dashboard. | Not a production license. Trial notices record inputs/outputs for the trial/product improvement and warn against confidential/personal data. Free endpoints can be deprecated; the checked `openai/gpt-oss-20b` page explicitly says **Free Endpoint Deprecated**, so live per-model status is mandatory. | [NIM FAQ](https://docs.api.nvidia.com/nim/docs/product) · [Run NIM Anywhere](https://docs.api.nvidia.com/nim/docs/run-anywhere) · [API Catalog](https://build.nvidia.com/explore/discover) · [Example model status](https://build.nvidia.com/openai/gpt-oss-20b/deploy) |
| **Vercel AI Gateway Free tier** | $5/month included credit for a subset of models, with lower per-model limits. Official FAQ says it is a monthly included credit, not an expiring trial. | Purchasing credits moves the team to paid tier and ends the monthly free credit. Some teams may be required to add a valid payment method before using free credits; check the actual account error/status. | [Pricing](https://vercel.com/docs/ai-gateway/pricing) · [FAQ](https://vercel.com/docs/ai-gateway/faq) |
| **Cohere Trial/evaluation keys** | Free limited trial keys for prototyping; official docs list 1,000 API calls/month and per-model trial limits such as 20 requests/minute for listed chat models. | Evaluation rather than unconstrained production use. Model-specific and newer-model restrictions apply. | [Rate Limits](https://docs.cohere.com/docs/rate-limits) · [FAQ](https://docs.cohere.com/docs/cohere-faqs) |

## 4. Local and self-hosted resources

| Resource | Role | Current status |
|---|---|---|
| **FreeLLMAPI** | Optional `llm_gateway` for catalog, model unification, health/cooldown and gateway-owned model fallback | Source exists locally, but the user service was inactive on 2026-11-19. Restore and verify supported contracts before registration as healthy. Historical counts are not current facts. |
| **Ollama/local OpenAI-compatible server** | `local_model_server` for local-only/privacy-sensitive or low-latency tasks when hardware/model capability fits | Included in the resource model by requirement. No installed local server or capable local model is asserted by this documentation. Measure before enabling. |
| **Hermes credential pools/fallback providers** | Native model/key selection where configured | Reuse rather than adding a third router. See [Hermes providers](https://hermes-agent.nousresearch.com/docs/integrations/providers) and [fallback providers](https://hermes-agent.nousresearch.com/docs/user-guide/features/fallback-providers). |

## 5. Entries deliberately not promoted to verified facts

The supplied secondary research included Hugging Face Inference Providers, SambaNova, Cerebras, Z.ai, ModelScope, SiliconFlow, Modal, OmniRoute, 9Router, WaLiAPI, and numerous shutdown dates/model-version claims. They remain **candidate leads**, not verified catalog entries, until their current official pages and license/security posture are checked.

Likewise, claims about Indian university eligibility, regional student grants, rupee prices, promotional end dates, and regional privacy exemptions are not copied into the canonical design without current primary evidence.

## 6. India/local operation notes

- India appears explicitly in Antigravity's official supported-country list and in the Gemini API/Google AI Studio supported-region list. These are the only two India eligibility claims promoted here.
- The local machine successfully reached OpenCode's DeepSeek free route and NVIDIA NIM during this documentation session. That is a point-in-time connectivity observation, not a regional availability guarantee.
- Kiro's official paid-plan billing-address inclusion of India is not treated as proof of Free-tier eligibility.
- No India-specific card-free, student, tax, or regional privacy claim is made from secondary blogs. Account onboarding records actual verification/payment steps and official restrictions without turning one account's result into a universal rule.

## 7. Recommended initial candidate policy

| Task/content | Candidate behavior |
|---|---|
| Public simple tasks | Choose the lowest capable healthy free route; Kilo/OpenCode/OpenRouter/Groq/Cloudflare are candidates after live checks |
| Confidential repository work | Prefer an explicitly suitable paid/local/zero-retention route; block Kilo Auto Free, NVIDIA trial, Google free tier, Cline promotions and any unknown policy. Kiro Free, Copilot Free and Mistral Vibe Free also require privacy review because of their training/service-improvement terms |
| Coding CLI burst work | Evaluate OpenCode free, Kiro Free, Antigravity Individual, Copilot Free and Cline promotions against actual account/quota and privacy needs |
| Zero-spend fallback | Use free-only fallback lists; OpenRouter paid fallback and Vercel purchased-credit transition require explicit approval |
| Production/client workload | Do not assume evaluation/free tiers license production use; review provider terms and choose an appropriate plan |

## 8. Refresh acceptance criteria

- [ ] Live model catalog and account access are checked before registration.
- [ ] Privacy/training/retention terms have official source URLs and a verification timestamp.
- [ ] Exact quotas come from official docs or the actual account dashboard, not secondary tables.
- [ ] Free-only fallback cannot silently become paid.
- [ ] Deprecated/removed models are tombstoned and excluded from new plans.
- [ ] Every selected transport (direct API, CLI, ACP, HTTP server) has its own smoke test.
