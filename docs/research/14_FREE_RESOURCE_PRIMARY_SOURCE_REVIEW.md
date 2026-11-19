# Free-resource primary-source review

**Documentation/review date:** 2026-11-19  
**Scope:** Only the 12 requested entries. This is conservative catalog input, not a promise of current runtime availability. Official product/documentation pages are primary sources; search snippets are not treated as extracted-page proof. This report contains research only; no implementation code is included.

**Status meanings:**
- **Verified:** the listed facts are supported by official page content extracted on the review date. This does not guarantee future availability.
- **Partial:** an official source was found, but a material field remains unpublished or could not be extracted; the gap is explicit.
- **Unknown:** no safe value should be put in the catalog. Unknown does not mean zero, unlimited, or not required.

## 1. Kilo Auto Free (`kilo-auto/free`)

- **Official URLs:** https://kilo.ai/docs/gateway/models-and-providers and https://kilo.ai/docs/gateway/usage-and-billing
- **Access / account / card:** Select `kilo-auto/free` through Kilo. Free models support authenticated and anonymous access; Auto Free needs no credits. The anonymous path therefore does not require an account; no card claim is needed or made.[24]
- **Published limits / exhaustion:** All free-model requests, anonymous or authenticated, are limited to **200 requests per hour per IP address**. Rate limiting returns HTTP **429**.[45]
- **Catalog behavior:** **Dynamic alias / rotating upstreams.** The resolved model is selected per session from a curated free set and changes server-side as availability shifts. Do not treat the alias as a fixed model identity.[24]
- **Data-use caveat:** Auto Free may route to providers that log prompts/outputs and use them to improve services. **Do not submit personal or confidential data.** NVIDIA free endpoints have additional trial terms.[24]
- **Status:** **Verified, 2026-11-19.** This also matches the independently verified finding; no runtime test was repeated.

## 2. OpenCode free models / Zen

- **Official URLs:** https://opencode.ai/docs/console/models/ and https://opencode.ai/docs/models/
- **Access / account / card:** In OpenCode, select `opencode/<model-id>` from the live catalog. Free-tier chat models require OpenCode; the direct endpoints document paid models or Go for other clients. The general paid Console flow uses sign-in, billing details, credits, and an API key, but the page does **not** clearly state a universal card requirement for every free model, so that field remains **unknown**.[26][27]
- **Published limits / exhaustion:** Free entries are explicitly **limited-time**. There is no published roster-wide quota. A model-specific exception is **LongCat 2.5 Free: 100 requests/day**. Other limits and exhaustion behavior are **unknown** unless exposed at runtime.[26]
- **Catalog behavior:** **Rotating, limited-time roster with deprecations.** `GLM-5.3-Flash Free` is officially marked deprecated **2026-11-18**. The local direct `opencode run -m opencode/deepseek-v4.1-flash-free` smoke test succeeded on 2026-11-19; that is point-in-time evidence, not permanence.[26]
- **Data-use caveat:** Privacy is **per model**, not a safe global “free = no training” label. DeepSeek V4.1 Flash Free is documented as zero-retention/no-training; Inkling 2 Small Free retains data and may train; several stealth/free models allow improvement use; NVIDIA-backed free entries prohibit personal/confidential data. Beam retains data for 30 days for safety, and LongCat is hosted in China with no training/retention stated by OpenCode.[26]
- **Status:** **Verified, 2026-11-19**, including the independently executed smoke-test evidence. Current roster membership must be refreshed rather than hardcoded.

## 3. AWS Kiro Free

- **Official URLs:** https://kiro.dev/pricing/, https://kiro.dev/docs/getting-started/authentication/, https://kiro.dev/docs/billing/related-questions/, and https://kiro.dev/docs/privacy-and-security/data-protection/
- **Access / account / card:** Kiro supports browser sign-in with GitHub, Google, AWS Builder ID, IAM Identity Center, or an external identity provider. Its documented CLI API-key generation flow starts with a **paid** Pro/Pro+/Pro Max/Power account, so do not assume a free headless API-key path. A valid credit card is documented for a paid upgrade, not established as a Free-tier requirement.[28][1]
- **Published limits / exhaustion:** Kiro Free is perpetual and includes **50 credits/month**; credits are work units, **not 50 requests**. Free has fixed monthly limits and no add-on credits. Requests pause when no credits remain, until the monthly reset; a paid upgrade is the documented immediate-capacity option.[1][29]
- **Catalog behavior:** **Published plan-specific selection, not a rotating alias.** The pricing page describes access to open-weight models and Claude Sonnet 4.5 with limits. This is a point-in-time plan catalog, not a permanence guarantee.[1]
- **Data-use caveat:** Free-tier content may be used for service improvement by default, subject to opt-out. Free-tier inputs may separately be stored up to **60 days** for abuse detection; opting out of service improvement does not remove that abuse-detection storage. Free/individual content such as prompts and responses is stored in **US East (N. Virginia)**.[2]
- **Status:** **Verified, 2026-11-19.** The official paid-plan billing-address list includes India, but that fact is not evidence by itself of Free-tier eligibility and is not promoted as one.[29]

## 4. Google Antigravity Free

- **Official URLs:** https://antigravity.google/pricing and https://antigravity.google/docs/faq?id=GoogleAntigravity
- **Access / account / card:** The individual plan is **$0/month** and covers the Antigravity app, CLI, IDE extensions, and Python SDK. Personal Google Accounts can use the free tier; users must be at least 18 and in a supported country/territory. No free-tier card requirement was found.[3][4]
- **Published limits / exhaustion:** The plan includes a **baseline agent quota refreshed weekly**. No exact request/token number is published on the pricing page; rate limits and AI-credit pools vary by plan and task complexity. Exact exhaustion behavior is **unknown** from the reviewed page.[3]
- **Catalog behavior:** **Published plan list, but date-sensitive.** The page still lists Claude Sonnet 4.6, Claude Opus 4.6, and GPT-OSS-120b while its own footnote says all three **will be removed on 2026-11-18**. As of this review on 2026-11-19, do not catalog those three as available. **Gemini 3.8 Flash** is the only unambiguously remaining free-model entry on that page.[3]
- **Data-use caveat:** Google says data collection can be turned off in **Settings** and refers users to its Terms of Service. Do not infer “no training,” “no retention,” or confidentiality suitability from the presence of an opt-out alone.[4]
- **Integration restriction:** The FAQ says `agy -p` is for direct scripting/CI, while wrapping `agy` inside third-party coding agents or automating account rotation is unsupported on consumer plans. Do not register Antigravity as a generic orchestrated CLI; use only a permitted direct/API/enterprise path.[4]
- **India:** India appears explicitly in Antigravity’s official supported-country list.[4]
- **Status:** **Verified with an official-page staleness conflict, 2026-11-19.** The removal footnote takes precedence over the still-visible model names for conservative routing.

## 5. OpenRouter `:free` and Free Models Router

- **Official URLs:** https://www.openrouter.ai/docs/api_reference/authentication, https://www.openrouter.ai/docs/api_reference/limits, https://openrouter.ai/docs/guides/routing/routers/free-router, and https://www.openrouter.ai/docs/guides/privacy/data-collection
- **Access / account / card:** API requests use a Bearer credential, commonly an OpenRouter API key created on the keys page; OAuth-based credentials are also documented. A card requirement for `:free` use is not stated. Purchasing credits changes the daily free-model ceiling and must not be misdescribed as part of the base no-purchase tier.[31][6]
- **Published limits / exhaustion:** For `:free` variants, the published limits are **20 requests/minute** and **50 requests/day** when lifetime purchased credits are **less than 10**, or **1,000 requests/day** when lifetime purchased credits are **at least 10**. OpenRouter or the upstream provider can return **429**; rate-limit/reset headers and `GET /api/v1/key` can expose quota state.[6]
- **Catalog behavior:** **Both fixed-variant and rotating-router modes.** A specific `vendor/model:free` selects that free variant while available. `openrouter/free` randomly selects from currently available free models that satisfy request features; availability varies and the caller cannot control the selected model in router mode.[7]
- **Data-use caveat:** OpenRouter prompt/response storage is opt-in except stated safety/legal cases; metadata is stored, and Safety Retention endpoints can retain content for abuse prevention. Requests still go to upstream providers, whose logging/training policies and the user’s routing/privacy settings matter. Do not propagate OpenRouter’s own default retention statement as a guarantee about every upstream.[30][5]
- **Status:** **Verified, 2026-11-19.**

## 6. Groq free tier

- **Official URLs:** https://console.groq.com/docs/quickstart, https://console.groq.com/docs/rate-limits, https://console.groq.com/docs/billing-faqs, and https://console.groq.com/docs/legal/services-agreement
- **Access / account / card:** Groq requires an account and an API key. A valid payment method (credit card, US bank account, or SEPA debit account) is documented for **upgrading to Developer tier**; the reviewed source does not explicitly say that Free tier requires one.[32][47][10]
- **Published limits / exhaustion:** Limits are organization-level and model-specific across dimensions such as RPM, RPD, TPM, and TPD. The documentation says the account’s limits page shows current exact values and warns that its summary can have exceptions. Exceeding a limit returns **429 Too Many Requests**. No static per-model numbers are copied into this catalog.[9]
- **Catalog behavior:** **Published per-model catalog, not a rotating free alias.** Models and limits can change; Groq’s agreement also allows deprecations. Use the live model/rate-limit view instead of freezing a historical table.[9][47]
- **Data-use caveat:** Groq may process inputs/outputs as needed to provide and reliably operate the service, comply with law, or check AUP compliance. It is not permitted to use inputs/outputs for training or fine-tuning unless the customer explicitly grants permission or instructs it. Third-party model terms still apply.[47]
- **Status:** **Partial, 2026-11-19.** Access, privacy, and exhaustion are verified; exact current per-model free quotas remain account/model-specific and are intentionally not asserted.

## 7. Cloudflare Workers AI free allocation

- **Official URLs:** https://developers.cloudflare.com/workers-ai/get-started/rest-api/, https://developers.cloudflare.com/workers-ai/platform/pricing/, and https://developers.cloudflare.com/workers-ai/platform/data-usage/
- **Access / account / card:** REST access requires a Cloudflare account, Account ID, and API token. Workers AI is included in Workers Free and Paid plans. No card requirement for the free allocation was found; using more than the free allocation requires Workers Paid or another documented paid path.[44][12]
- **Published limits / exhaustion:** **10,000 Neurons/day at no charge**, resetting at **00:00 UTC**. Neurons are Cloudflare’s compute-billing unit, not tokens or requests. Beyond a limit, further operations fail; Workers Free documents upgrade rather than automatic paid overage.[12]
- **Catalog behavior:** **Published per-model catalog, not one rotating free model.** The free allocation is account-wide and measured in Neurons rather than a per-model request count. Confirm the selected model’s plan eligibility at runtime instead of assuming every catalog entry is available to Workers Free.[12]
- **Data-use caveat:** For standard non-stateful inference, Cloudflare documents zero retention after processing/delivery and no training on Customer Content. Stateful features, customer-directed storage, AI Gateway logging/caching, legal/safety exceptions, and operational metadata are explicit exceptions; third-party destinations have their own policies.[13]
- **Status:** **Verified, 2026-11-19.**

## 8. Google AI Studio / Gemini API free tier

- **Official URLs:** https://ai.google.dev/gemini-api/docs/billing, https://ai.google.dev/gemini-api/docs/rate-limits, https://ai.google.dev/gemini-api/docs/pricing, and https://ai.google.dev/gemini-api/docs/available-regions
- **Access / account / card:** New accounts begin on Free tier. The qualification is an active project or free trial, with some projects needing verification; AI Studio provides the API-key/project flow. Paid tier requires a separate billing-account setup, so do not describe paid billing as a Free-tier prerequisite.[16][14]
- **Published limits / exhaustion:** Free access applies only to certain models up to their free-tier limits. Limits are **per project, not per API key**, and RPD resets at **midnight Pacific time**. Current exact limits are shown in AI Studio; Google explicitly says specified limits are not guaranteed and capacity can vary. Exceeding an applicable limit causes a rate-limit error. There is no safe universal static RPM/RPD number to put in this catalog.[14][16]
- **Catalog behavior:** **Per-model published availability, not a rotating alias.** The pricing page marks free availability model by model. Use the live pricing/rate-limit pages rather than carrying forward a historical “all Gemini models are free” claim.[15]
- **Data-use caveat:** The Free tier explicitly says **content is used to improve Google products**; the paid tier says content is not used for that purpose. Do not route confidential material to Free merely because input/output token price is zero.[15]
- **India:** India appears explicitly in the official Gemini API and Google AI Studio supported-region list.[34]
- **Status:** **Verified, 2026-11-19; exact current numeric quotas intentionally left to AI Studio.**

## 9. Mistral free API / Vibe

- **Official URLs:** https://docs.mistral.ai/admin/billing-usage/usage-limits, https://docs.mistral.ai/admin/billing-usage/subscriptions, https://docs.mistral.ai/resources/glossary.md, https://mistral.ai/pricing/, and https://help.mistral.ai/en/articles/347617-do-you-use-my-user-data-to-train-your-artificial-intelligence-models
- **Access / account / card:** Free mode is the default for new accounts and the glossary explicitly says **no credit card required**. It includes Vibe and Studio access. **Current official usage documentation says Free mode does not include API access**: creating API keys and using the API require billing activation and pay-as-you-go.[35][48][17]
- **Published limits / exhaustion:** Free includes a monthly usage budget; the pricing page describes limited messages, web searches, and coding sessions without a safe general numeric total. Without pay-as-you-go, usage can stop at included-usage exhaustion until the next billing period or a settings change. API access itself must not be represented as a free quota.[17][35][37]
- **Catalog behavior:** **Consumer/developer product plan, not a free API model catalog.** Vibe Free includes current product features and coding surfaces, but there is no fixed free-API roster to register.[37][17]
- **Data-use caveat:** Vibe Free inputs/outputs are used for model training **by default unless the user opts out**. Labs and Public Preview models are an explicit exception where opt-out does not apply. Studio Free mode may also use input/output for training subject to opt-out. Temporary Chat is separately documented as not saved to history or used for training, subject to legal retention.[38][18]
- **Status:** **Verified, 2026-11-19. A current “Mistral free API tier” claim is rejected, not merely left unverified.**

## 10. NVIDIA NIM evaluation access

- **Official URLs:** https://build.nvidia.com/search, https://build.nvidia.com/openai/gpt-oss-20b/deploy, and https://docs.nvidia.com/nim/large-language-models/latest/get-started/prerequisites.html
- **Access / account / card:** Hosted model pages expose an OpenAI-compatible trial endpoint and **Generate API Key** flow. The official search-result text describes creating and verifying an account, but the extracted generic build page did not expose that body text, so account-verification details are only **partially verified**. For self-hosted containers, official prerequisites require a free NVIDIA Developer Program membership or an NVIDIA AI Enterprise license; a free **90-day AI Enterprise evaluation license** is a separate option. No card requirement was established.[41][20]
- **Published limits / exhaustion:** The directly extracted official model page states **up to 40 requests per minute for most models**, with the personal limit shown in the dashboard. Use the authenticated dashboard as the routing budget because model/account limits can differ. Free endpoints can also be deprecated, so quota and availability are separate checks.[41]
- **Catalog behavior:** **Volatile hosted evaluation catalog, not a fixed-count resource.** The reviewed `openai/gpt-oss-20b` page itself says that API was deprecated on **2026-11-18**; it must not be used as a current example. Self-hosted support depends on the selected model, container, and hardware.[41][20]
- **Data-use caveat:** NVIDIA’s hosted trial notice says inputs and outputs are recorded to provide the trial and improve NVIDIA products/services, **including AI models**. It says not to upload confidential information or personal data unless expressly permitted, and also documents security/fraud/abuse logging and sharing with third-party service providers.[41]
- **Status:** **Verified with a volatile catalog, 2026-11-19.** Hosted trial privacy, account/key flow, the 40 RPM generic guidance, and model-specific deprecation were checked against official pages. Any fixed model count remains unconfirmed.

## 11. GitHub Copilot Free / CLI

- **Official URLs:** https://docs.github.com/en/copilot/get-started/plans, https://docs.github.com/en/copilot/how-tos/manage-your-account/get-started-with-a-copilot-plan, https://docs.github.com/en/copilot/concepts/billing-and-usage/individuals/billing, and https://docs.github.com/en/copilot/how-tos/manage-your-account/manage-policies
- **Access / account / card:** Copilot Free is for eligible individual developers and uses a GitHub account. All plans include Copilot CLI, while Free has limited agents and **auto model selection only**. The current setup page documents sign-in/account creation and does not state a Free-tier card requirement.[21][22]
- **Published limits / exhaustion:** **2,000 code completions/month** are explicitly published. Copilot Free also has a monthly AI-credits allowance for metered features including Chat and CLI, but the current pages do **not** publish its exact numerical amount. Included credits reset at **00:00:00 UTC on the first day of each calendar month**. At exhaustion, the user can upgrade, configure paid additional usage, or wait for reset.[21][42]
- **Catalog behavior:** **Provider-managed automatic model selection**, not a user-selectable fixed free-model catalog.[21]
- **Data-use caveat:** Since **2026-04-24**, GitHub says Copilot Free interactions—including inputs, outputs, code snippets, and associated context—may be used to train and improve AI models. Individual users can disable “Allow GitHub to use my data for AI model training” in Copilot settings.[43]
- **Status:** **Verified, 2026-11-19.** The older “50 premium requests/month” formulation is not confirmed by the current plan/AI-credit pages and is excluded; see flags below.

## 12. Cline free promotions

- **Official URL:** https://docs.cline.bot/getting-started/free-models
- **Access / account / card:** Requires a **Cline account**. Free-tagged models are used through the Cline IDE extension or CLI under the Cline provider, and under ClinePass during promotional periods. Free promotional usage is **not supported through the Cline API**. No free-promotion card requirement is stated.[23]
- **Published limits / exhaustion:** Cline says only “up to a limited usage quota”; no exact quota is published on this page. After exhaustion, documented choices are ClinePass, usage-billed Cline credits, or bringing another provider’s API key.[23]
- **Catalog behavior:** **Explicitly rotating and limited-time.** Current models must be read from the `FREE` tag in the model selector rather than hardcoded.[23]
- **Data-use caveat:** Cline states that free-model usage **may be used to help improve model performance and quality**. The page does not provide a more specific retention period, so that remains **unknown**.[23]
- **Status:** **Verified for the promotion mechanism, 2026-11-19; active models and exact quotas remain unknown until viewed in the product.**

## Claims from secondary or historical research that must not be promoted

1. **OpenCode historical promo shutdowns:** A visible FreeLLMAPI legacy note says `qwen3.6-plus` and `minimax-m3` free promos “already ended.” The current OpenCode free list does not include them as free, but the official deprecation table does not establish those exact historical promo claims or shutdown dates. Mark that historical explanation **unverified**; do not offer them as current free models.[26]
2. **Confirmed OpenCode shutdown, by contrast:** `GLM-5.3-Flash Free` has an official **2026-11-18** deprecation date and must be removed. This claim is confirmed, not unverified.[26]
3. **Confirmed Antigravity removals, despite stale names:** Claude Sonnet 4.6, Claude Opus 4.6, and GPT-OSS-120b carry an official **2026-11-18** removal footnote. Do not preserve them merely because the pricing page still displays their names.[3]
4. **Copilot “50 premium requests/month”:** An older official URL surfaced that wording, but current plan and billing pages instead specify 2,000 completions plus an **unspecified** AI-credits allowance. The number **50 is not confirmed for the current catalog** and must not be converted into a current CLI/chat quota.[21][42]
5. **Mistral “free API” claims:** Current official documentation affirmatively says Free mode **does not include API access**. Treat a current free-API claim as contradicted, not as an unknown quota.[17]
6. **NVIDIA “84 models” or other fixed catalog counts:** Historical local runtime observations are not an official stable catalog size. No fixed count is carried forward; the officially observed model-specific deprecation demonstrates why live discovery is required.[41]
7. **NVIDIA “40 RPM for most models”:** Direct extraction of the official `openai/gpt-oss-20b` page confirmed this generic guidance and the personal-dashboard caveat. Use the authenticated dashboard for exact limits; do not treat 40 RPM as a universal per-model guarantee.[41]
8. **Other secondary numerical or shutdown claims:** The delegated prompt did not include a complete secondary bibliography or every underlying claim. Claims not independently confirmed above are not silently carried into this catalog; they need their own official-source check before documentation.

## Routing and design implications

1. **Treat the catalog as volatile.** Discover models at registration/plan time, record the resolved upstream model separately from aliases such as `kilo-auto/free` and `openrouter/free`, attach a verification timestamp, and tombstone deprecated/removed IDs immediately. A successful smoke test proves only that model/account/time combination.
2. **Use per-resource and per-resolved-model privacy labels.** A gateway-level label cannot override upstream training, retention, region, or trial terms. Unknown or unsuitable privacy must block confidential routing. In particular, Kilo Auto Free, NVIDIA hosted trials, Cline promotions, Gemini Free, Mistral Vibe Free, and Copilot Free require affirmative policy review rather than a “free/public” default.
3. **Represent units exactly.** Requests, credits, tokens, Neurons, and AI credits are not interchangeable. Keep unknown numeric limits as `null`/unknown, never as unlimited. Do not derive rupee costs or convert unlike quota units.
4. **Handle exhaustion as a distinct health state.** Respect HTTP 429/402, `Retry-After`, reset windows, and provider dashboards. Distinguish transient capacity, rate limit, exhausted paid balance, deprecated model, and ineligible plan. Fall back only to a policy-compatible resource; never silently escalate to a paid model, enable recharge, or start a subscription.
5. **No credential farming or quota evasion.** Do not rotate accounts, API keys, IPs, browser profiles, or organizations to defeat provider-wide caps. Use only user-authorized accounts and documented authentication flows. Client-only restrictions matter: OpenCode free chat models and Cline promotions must not be repackaged as unrestricted generic API access.
6. **Keep India as an eligibility field, not an assumption.** This review found explicit official support for India in Antigravity and Gemini/AI Studio only. Kiro’s India entry concerns paid-plan billing addresses, not automatic Free-tier eligibility. No other India availability claim is inferred, and no rupee conversion is included.

## Sources

[1] https://kiro.dev/pricing
[2] https://kiro.dev/docs/privacy-and-security/data-protection
[3] https://antigravity.google/pricing
[4] https://antigravity.google/docs/faq?id=GoogleAntigravity
[5] https://www.openrouter.ai/docs/faq
[6] https://www.openrouter.ai/docs/api_reference/limits
[7] https://openrouter.ai/docs/guides/routing/routers/free-router
[9] https://console.groq.com/docs/rate-limits
[10] https://console.groq.com/docs/billing-faqs
[12] https://developers.cloudflare.com/workers-ai/platform/pricing
[13] https://developers.cloudflare.com/workers-ai/platform/data-usage
[14] https://ai.google.dev/gemini-api/docs/rate-limits
[15] https://ai.google.dev/gemini-api/docs/pricing
[16] https://ai.google.dev/gemini-api/docs/billing
[17] https://docs.mistral.ai/admin/billing-usage/usage-limits
[18] https://docs.mistral.ai/vibe
[20] https://docs.nvidia.com/nim/large-language-models/latest/get-started/prerequisites.html
[21] https://docs.github.com/en/copilot/get-started/plans
[22] https://docs.github.com/en/copilot/how-tos/manage-your-account/get-started-with-a-copilot-plan
[23] https://docs.cline.bot/getting-started/free-models
[24] https://kilo.ai/docs/gateway/models-and-providers
[26] https://opencode.ai/docs/console/models
[27] https://opencode.ai/docs/models
[28] https://kiro.dev/docs/getting-started/authentication
[29] https://kiro.dev/docs/billing/related-questions
[30] https://www.openrouter.ai/docs/guides/privacy/data-collection
[31] https://www.openrouter.ai/docs/api_reference/authentication
[32] https://console.groq.com/docs/quickstart
[34] https://ai.google.dev/gemini-api/docs/available-regions
[35] https://docs.mistral.ai/admin/billing-usage/subscriptions
[37] https://mistral.ai/pricing
[38] https://help.mistral.ai/en/articles/347617-do-you-use-my-user-data-to-train-your-artificial-intelligence-models
[41] https://build.nvidia.com/openai/gpt-oss-20b/deploy
[42] https://docs.github.com/en/copilot/concepts/billing-and-usage/individuals/billing
[43] https://docs.github.com/en/copilot/how-tos/manage-your-account/manage-policies
[44] https://developers.cloudflare.com/workers-ai/get-started/rest-api
[45] https://kilo.ai/docs/gateway/usage-and-billing
[47] https://console.groq.com/docs/legal/services-agreement
[48] https://docs.mistral.ai/resources/glossary.md
