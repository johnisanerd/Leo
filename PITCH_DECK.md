# Leo — Pitch Deck Outline

Slide-by-slide content for the University AI Innovation award pitch. Each section below = one slide. Trim to fit your deck's visual style; text here is the full point, headlines are bolded for slide titles.

---

## Slide 1 — Title
**Leo**
*Your AI Marketing Department, in One Chat*

University AI Innovation Award — [Your name/team] — [Date]

---

## Slide 2 — The Problem
**Small brands need a marketing department. They can only afford a marketing intern.**

- Running a brand today requires strategy, content, SEO, paid ads, email/CRM, PR, competitive intelligence, and analytics — normally 6–8 specialist hires or 6–8 separate SaaS subscriptions.
- Existing AI writing tools only solve one narrow slice ("write me a caption") and have three structural flaws:
  1. **No memory of the brand** — every prompt starts from zero, so output drifts from real voice/audience/visuals.
  2. **No shared context** — the content tool doesn't know what the SEO tool or the competitor-intelligence tool just learned.
  3. **Stop at the draft** — no planning, no distribution, no monitoring, no catching drift after publish.

---

## Slide 3 — The Solution
**One brand-aware co-pilot that runs the whole marketing workflow.**

- Connect your website + social profiles once. Leo builds a persistent **Brand Core** — tone, visuals, audience, messaging, competitors.
- That Brand Core is injected into every AI action Leo takes, so output stays on-brand no matter which task you're running.
- From one chat interface, Leo covers the full loop: **strategy → creation → distribution → measurement → optimisation.**

---

## Slide 4 — How It Works (Product Flow)
**Chat in. Brand-aware output out.**

1. **Ingest** — Firecrawl crawls the website, Apify scrapes social profiles.
2. **Extract** — an LLM pass structures it into a Brand Core object (tone, visuals, audience, messaging, competitors).
3. **Ground** — a RAG layer embeds brand content, competitor snapshots, and analytics so chat answers use the brand's real data.
4. **Generate** — content, ad copy, email sequences, SEO briefs, press releases, campaign plans — produced directly in chat.
5. **Score & monitor** — every asset is graded against the Brand Core (voice score, drift detection) and tracked over time.

---

## Slide 5 — What Makes It Different
**Not another AI writing tool — a unified marketing system.**

- **Brand Core as a first-class object**, not a prompt template — every generation call is grounded in the same structured brand model.
- **Ten functional pillars, one shared state** — Strategy, Content, SEO, Paid Ads, Email/CRM, Community, Analytics, PR, Personal Brand, and Growth Experimentation all read/write the same project data, so insights flow between them automatically.
- **Retrieval-grounded chat** — answers pull from the brand's own content library, competitor data, and analytics, not just general model knowledge.
- **Live competitive intelligence** — a 5-dimension classifier profiles competitors (geography, size, directness, market position, segment) from live web data, streamed in real time.
- **Built for AI-era search** — dedicated GEO (Generative Engine Optimization) and AEO (Answer Engine Optimization) scoring prepares content to be *cited by* ChatGPT, Perplexity, and Gemini — not just ranked by Google.
- **Cost-tuned model routing** — Claude Sonnet for creative/chat, Claude Haiku (5× cheaper) for structured classification, Gemini for extraction and imagery — right model for the job, not one model for everything.

---

## Slide 6 — The AI Under the Hood
**Generative AI, NLP, retrieval, and multimodal vision — working together, not just a chatbot wrapper.**

- **Generative AI**: Claude (chat, content, classification), Gemini (extraction, imagery), DALL-E 3 / Imagen 3 (image generation).
- **NLP**: brand voice extraction, semantic search, structured data extraction from unstructured web/social text, content scoring.
- **Computer Vision**: multimodal image understanding for chat attachments.
- **Retrieval-Augmented Generation**: embeddings (Google `text-embedding-004`) + cosine-similarity retrieval ground every answer in the brand's real data.
- **Agentic tool use**: Claude calls live web-search and company-lookup tools mid-conversation instead of answering from static training data.

---

## Slide 7 — Benefits Delivered
**Replaces a stack of tools and specialists with one system that gets smarter as you use it.**

- **Consolidation** — one brand-aware system instead of separate content, SEO, ad-copy, email, competitive-intelligence, and analytics tools, removing the integration overhead of running them apart.
- **Brand consistency at scale** — every asset is produced against the same Brand Core and automatically scored for on-brand voice, so quality doesn't degrade as output volume grows — the most common failure of generic AI writing tools.
- **Time-to-output** — tasks that normally need a specialist and a brief-and-wait cycle (a competitor teardown, a keyword-gapped blog brief, a five-email nurture sequence, a press release) are produced directly in chat, in minutes.
- **Forward-looking search readiness** — GEO/AEO scoring prepares content for discovery via AI answer engines, ahead of most current marketing tooling.
- **First-party, brand-owned data** — a built-in analytics pixel gives brands their own behavioural data instead of full dependence on third-party platforms, feeding straight back into Leo's own reporting loop.

---

## Slide 8 — Traction & Status
**Prototype stage, validated end-to-end.**

- Fully working system: 34 backend route modules, ~90 AI service modules across 10 marketing pillars, full Next.js frontend.
- Live integrations across LLM providers, web search/research, scraping, analytics, billing, and storage — not a mockup.
- Early testers: 1–10 users.
- Not yet commercially launched — billing infrastructure (Stripe, tiered plans, credit metering) is built and ready.

---

## Slide 9 — Tech Stack
**Production-grade architecture from day one.**

| Layer | Technology |
|---|---|
| Frontend | Next.js 15, TypeScript, Tailwind, shadcn/ui, Zustand |
| Backend | Python, FastAPI, Pydantic |
| Core AI | Anthropic Claude, Google Gemini, OpenAI DALL-E 3 |
| Search/Research | Exa.ai, Tavily, SerpAPI |
| Scraping | Firecrawl, Apify |
| Data | Firebase Firestore, Firebase Auth |
| Infra | Cloudflare R2, Stripe, Resend |

---

## Slide 10 — Vision / What's Next
**From co-pilot to autonomous marketing operator.**

- Broaden the early-tester base into a public beta.
- Deepen the cross-pillar feedback loop so insights (competitor moves, drift alerts, performance data) trigger proactive recommendations, not just answers to questions.
- Expand GEO/AEO into a full AI-search-visibility product as answer engines take share from traditional search.

---

## Slide 11 — Ask / Close
*(Fill in based on what you're asking the judges/audience for — award recognition, feedback, pilot partners, funding, etc.)*

**Leo: your brand's entire marketing department, in one chat.**
