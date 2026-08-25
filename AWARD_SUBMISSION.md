# Leo — University AI Innovation Award Submission

This document answers the submission form questions in detail, based on a direct review of the current codebase (`backend/` FastAPI service + `frontend/` Next.js 15 app) as of 2026-08-25.

---

## Project Details

### Project / Solution Name
**Leo**

### Award Category
University AI Innovation

### Brief Description
Leo is an AI marketing co-pilot that acts as an always-on strategist, writer, analyst, and publisher for a brand. A user connects their brand's website and social profiles once; Leo builds a living model of that brand (voice, visuals, audience, positioning) and then uses it to run nearly every function of a modern marketing team — content creation, SEO, paid ads, email/CRM, competitive intelligence, PR, analytics, and experimentation — from a single conversational interface, backed by ~90 specialised AI services organised into 10 functional "pillars."

### Current Stage
**Prototype** — a working, end-to-end system (chat product + 34 backend route modules + ~90 service modules + full Next.js frontend) built and functioning against live third-party APIs, but not yet operating with a broad external user base.

---

## Innovation

### What problem does your solution solve?

Small and mid-sized brands (and the solo marketers/founders who run marketing for them) need the output of an entire marketing department — brand strategy, content, SEO, paid media, lifecycle email, PR, competitive intelligence, and analytics — but can't afford to hire eight specialists or stitch together eight separate SaaS subscriptions (that also don't share context with each other). Existing AI writing tools solve one narrow slice (e.g. "write me a caption") but:

1. **Have no memory of the brand.** Every prompt starts from zero, so output drifts from the brand's actual voice, audience, and visual identity.
2. **Don't talk to each other.** A content tool doesn't know what the SEO tool learned about keyword gaps, and neither knows what the competitive-intelligence tool found about a rival's new pricing page.
3. **Stop at the draft.** They generate text but don't help plan campaigns, track performance, watch competitors, or catch when content has drifted off-brand.

Leo solves this by giving every generation task a shared, persistent "Brand Core" (extracted once from the brand's real website and social presence) and by unifying the full marketing workflow — strategy → creation → distribution → measurement → optimisation — behind one chat interface, so a single non-specialist user can operate at the output level of a small marketing team.

### What makes your solution unique?

- **Brand Core as a first-class object, not a prompt template.** A dedicated ingestion pipeline (`backend/services/ingestion/`) scrapes the brand's website (Firecrawl) and social accounts (Apify), then uses an LLM extraction pass (Gemini) to derive structured tone, visual style, audience, messaging pillars, and competitor list. That structured object is injected into *every* downstream AI call — chat, content generation, ad copy, email, PR — so output is consistently on-brand instead of generic. A **Brand Voice Scorer** and **Brand Drift Detector** then continuously grade new content against that same Brand Core (A–F scoring), closing the loop between generation and quality control.
- **Ten-pillar coverage under one chat, not ten disconnected tools.** The backend is organised into ten marketing "pillars" — Strategy & Planning, Content Creation, SEO & Organic, Paid Advertising, Email/CRM & Lifecycle, Community & Advocacy, Analytics & Reporting, PR & Communications, (Personal Brand), and Growth Experimentation/CRO — comprising roughly 90 individual AI service modules (e.g. `icp_service`, `gtm_service`, `keyword_research_service`, `ad_copy_service`, `churn_risk_service`, `press_release_service`, `ab_test_design_service`). Because they all read/write the same Firestore-backed project state, an insight from one pillar (e.g. a competitor's ad claim) can inform another (e.g. positioning copy) without the user manually copy-pasting context between tools.
- **Retrieval-grounded chat, not a stateless prompt box.** A lightweight RAG layer (`rag_service.py`) embeds the brand's own content library, competitor snapshots, analytics summaries, and blog briefs (Google `text-embedding-004`), and retrieves the most relevant chunks into the system prompt per question — so Leo answers using the brand's actual history and data, not just general knowledge.
- **Live competitive and search-visibility intelligence, not one-off snapshots.** A 5-dimension competitor classification engine (`competitor_classifier_service.py`) runs parallel Exa/Tavily web searches and classifies each competitor's geographic scope, size, directness, market position, and customer segment via Claude, streamed live over SSE. On the search side, purpose-built **GEO (Generative Engine Optimization)** and **AEO (Answer Engine Optimization)** scoring services grade content specifically for citability by AI answer engines (ChatGPT, Perplexity, Gemini) and for featured-snippet/voice-search capture — a forward-looking SEO capability most marketing tools don't yet offer.
- **A model-routing strategy tuned for cost/quality, not one model for everything.** Conversational chat and creative generation run on Claude Sonnet; high-volume structured/classification tasks (competitor classification, scoring) run on the far cheaper Claude Haiku; one-shot brand/website extraction and image generation run on Gemini — each task routed to the model best suited to its cost and latency profile.
- **First-party analytics owned by the brand.** Leo issues each project its own lightweight tracking pixel/token (`tracking_service.py`) so brands get first-party pageview/session analytics without depending on a third party — data that then feeds straight back into Leo's own reporting and insight generation.

---

## AI Technologies

The following categories from the submission checklist apply to Leo:

- [x] **Machine Learning** — embedding-based semantic retrieval (cosine similarity), lead scoring, churn-risk scoring, anomaly detection, and forecasting services built on top of foundation-model reasoning and statistical scoring rather than the LLMs' raw text output alone.
- [ ] **Deep Learning** — not custom-trained; Leo consumes deep-learning-based foundation models (transformer LLMs, diffusion image models) as a service rather than training its own neural networks.
- [x] **Computer Vision** — multimodal image understanding via vision-capable models: users attach images directly in chat and Leo's chat pipeline (Claude/Gemini multimodal input) analyses them as part of generating a response.
- [x] **Natural Language Processing** — the majority of the platform: brand voice extraction, content generation, entity/keyword analysis, semantic search, sentiment-style scoring (GEO/AEO, brand drift, voice scoring), and structured-data extraction from unstructured web/social text.
- [x] **Generative AI** — the core of the product: Anthropic Claude (chat, content, classification), Google Gemini (brand extraction, one-shot generation), and image generation via DALL-E 3 and Google Imagen 3.
- [ ] **Robotics** — not applicable.
- [x] **Other** — Retrieval-Augmented Generation (RAG) and multi-agent/tool-orchestrated LLM workflows (an LLM "tool dispatcher" lets Claude call live web-search, company-lookup, and brand-data tools mid-conversation rather than answering from static training data alone).

### Describe how AI is used

AI is not a single feature bolted onto Leo — it is the entire execution layer, called in five distinct ways:

1. **Conversational orchestration.** The core product is a streaming chat (Server-Sent Events) where Claude is given a system prompt built from the project's live Brand Core, RAG-retrieved brand context, and channel-specific constraints (platform character limits, tone rules per Instagram/LinkedIn/X/TikTok/etc.). Claude can call tools mid-conversation — live web search, "find similar companies," topic research, brand-news lookup — via a tool dispatcher (`tool_dispatcher.py`), so answers are grounded in current information rather than the model's static training data.
2. **Structured extraction and classification.** A multi-stage ingestion pipeline (Apify social scrape → Firecrawl website crawl → Gemini extraction) converts an unstructured brand presence into a structured Brand Core JSON object. The same extract-then-classify pattern powers the 5-dimension Competitor Classifier and the GEO/AEO content scorers — each gathers raw signal (via Exa/Tavily search or scraped page content) and asks Claude/Gemini to return a strictly-schemed JSON judgement, which is what makes downstream UI (scorecards, dimension breakdowns, progress bars) possible.
3. **Retrieval-augmented generation.** Brand content, competitor snapshots, analytics summaries, and blog briefs are chunked and embedded (Gemini `text-embedding-004`); at query time the most relevant chunks are retrieved by cosine similarity and folded into the prompt, so chat answers and generated content reference the brand's actual data instead of generic advice.
4. **Generative content production.** Across the ten pillars, LLMs generate finished marketing assets, not just suggestions: campaign briefs and content packs, ad copy and ad briefs, email/newsletter sequences, landing-page copy, press releases and pitch emails, SEO blog posts with schema markup, carousel graphics (rendered via a headless-browser export pipeline), and images (DALL-E 3 / Imagen 3).
5. **Continuous scoring and monitoring.** Rather than a one-shot output, generated and existing content is re-evaluated over time: Brand Voice Scorer (A–F vs. Brand Core), Brand Drift Detector, Performance Prediction (hook/clarity/CTA strength), GEO/AEO scores, and pillar-level analytics (anomaly detection, cohort analysis, forecasting, funnel analysis) that turn raw first-party tracking data and third-party integrations (GA4, Google Search Console) into a running assessment of what's working.

**Model routing:** conversational chat and long-form/creative generation use Claude Sonnet (`claude-sonnet-4-6`); high-volume structured classification and scoring use Claude Haiku (`claude-haiku-4-5`) at roughly 5× lower cost; one-shot brand/website extraction and image generation use Gemini (`gemini-2.5-flash`, `imagen-3.0-generate-001`), with DALL-E 3 as an alternate image backend.

---

## Impact

### Benefits Delivered

- **Consolidation**: replaces the functional surface area of separate content, SEO, ad-copy, email, competitive-intelligence, PR, and analytics tools with one brand-aware system, removing the integration and context-sharing overhead of running them separately.
- **Brand consistency at scale**: every generated asset is produced against the same Brand Core and is automatically scored for on-brand voice, so output quality doesn't degrade as generation volume increases — a common failure mode of generic AI writing tools.
- **Time-to-output**: tasks that normally require briefing and waiting on a specialist (a competitor teardown, a keyword-gapped blog brief, a five-email nurture sequence, a press release) are produced directly in the chat in minutes.
- **Forward-looking search readiness**: dedicated GEO/AEO scoring prepares brand content for discovery via AI answer engines (ChatGPT, Perplexity, Gemini), not just classic Google search — a capability ahead of most current marketing tooling.
- **First-party, brand-owned data**: the built-in analytics pixel means brands accumulate their own first-party behavioural data rather than depending entirely on third-party platforms, feeding directly back into Leo's own reporting loop.

### Number of Users / Customers
1–10 (early internal/design-partner testers; pre-broad-launch).

### Commercially Deployed?
**No** — billing infrastructure (Stripe subscriptions, credit metering, tiered plan gating for Free/Pro/Agency) is implemented in the codebase, but the product has not yet been commercially launched to paying customers.

---

## Supporting Evidence

- **Pitch Deck / CV (PDF):** *Not yet attached — to be provided separately with the submission.*
- **Product Brochure (Optional):** *Not yet available.*
- **Demo Video / Website / LinkedIn Profile (Optional):** *Not yet available.*
- **Other Supporting Documents (Optional):** See [`LEO_TECHNICAL_OVERVIEW.md`](LEO_TECHNICAL_OVERVIEW.md) in this repository for a full technical inventory (architecture, feature list, API integrations, database schema) that can accompany this submission as supplementary technical evidence.

---

## Reference: Technical Snapshot (for evaluators)

| Layer | Technology |
|---|---|
| Frontend | Next.js 15 (App Router), TypeScript, Tailwind CSS, shadcn/ui (Radix), Zustand, Framer Motion, Recharts |
| Backend | Python, FastAPI, Pydantic, SlowAPI rate limiting — 34 route modules, ~90 service modules |
| Core LLMs | Anthropic Claude (`claude-sonnet-4-6` chat/creative, `claude-haiku-4-5` classification/scoring) |
| Secondary AI | Google Gemini (`gemini-2.5-flash` extraction, `imagen-3.0-generate-001` images), OpenAI DALL-E 3 (image fallback), Google `text-embedding-004` (RAG embeddings) |
| Search / Research | Exa.ai, Tavily, SerpAPI |
| Scraping / Ingestion | Firecrawl (website), Apify (social platforms) |
| Database / Auth | Firebase Firestore, Firebase Auth |
| Storage | Cloudflare R2 |
| Billing | Stripe (subscriptions + metered credits) |
| Email | Resend |
| Analytics integrations | Google Analytics 4, Google Search Console, first-party tracking pixel |

*Sources reviewed for this document: [`backend/config.py`](backend/config.py), [`backend/services/`](backend/services/) (including all `pillar1`–`pillar10` modules, `rag_service.py`, `llm_service.py`, `competitor_classifier_service.py`, `geo_aeo_service.py`, `tracking_service.py`, `ingestion/`), [`backend/api/routes/`](backend/api/routes/), [`frontend/package.json`](frontend/package.json), and [`LEO_TECHNICAL_OVERVIEW.md`](LEO_TECHNICAL_OVERVIEW.md). Stage, deployment status, and user-count figures were confirmed directly with the project owner rather than inferred from code.*
