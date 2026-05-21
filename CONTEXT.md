# Newsquick - Company Context

> Loaded automatically in every Claude session when this repo is mounted.
> Keep accurate. Update when the company materially changes.

## What Newsquick is

Newsquick is a news intelligence SaaS for prediction market traders — a Newsquawk equivalent purpose-built for the emerging prediction markets ecosystem. We ingest geopolitical, political, and corporate news in real time, then run an intelligence layer that maps each news item to its direct, indirect, and second/third-order implications across prediction markets (Polymarket, Kalshi). Traders subscribe to get faster, more structured market context than Twitter + gut feel.

**One-liner:** Newsquawk for prediction markets — real-time news mapped to market implications, for traders on Polymarket and Kalshi.

## The problem we solve

Prediction markets just crossed $22B/month in combined volume across Polymarket and Kalshi, with 85K+ active markets. The professional infrastructure that exists for FX (Newsquawk, ~$2-5K/month, loyal customer base) and equities (Bloomberg Terminal, ~$24K/year) does not exist for prediction markets. Top traders — Domer ($2.5M+ on Polymarket), the French Whale Théo ($30M on Trump 2024), Evan Semet (six figures/month) — still rely on manual Twitter scanning and gut feel to figure out which markets a news event affects. Newsquick is the systematic intelligence layer they're missing.

## How it works

Four-layer pipeline:

1. **L1 — Ingest:** Low-latency news from Newsquawk feeds, RSS, SSE. Geopolitical, political, corporate events, faster than general-purpose feeds.
2. **L2 — Matching:** pgvector embeddings map each news item to relevant prediction markets. Gemma4 local model (Ollama) for pre-filtering.
3. **L3 — Intelligence:** Claude Sonnet classifies impact type and identifies effects:
   - **Type A — Direct causation:** News directly moves a market
   - **Type B — Logical constraint:** Markets are logically inconsistent given the news
   - **Type C — Implicit conditional:** Domain knowledge links A and B (e.g. Trump pardon news → Stewart Rhodes market via J6 context)
   - **Type D — Thematic correlation:** Correlated narrative indicators (Fed rates + inflation + unemployment)
4. **L4 — Execution:** Polymarket CLOB. We trade with our own product as proof of product and to generate proprietary P&L labels.

Market graph: 325 market events clustered, 2,945+ semantic/causal edges, d3-force interactive visualization. Structural rules + LLM validation for edge detection.

Delivery: real-time alerts, dashboard, API.

## ICP (Ideal Customer Profile)

- **Primary:** Pro traders on Polymarket and Kalshi running $10K–$10M positions. Comp: Domer (@ImJustKen), Evan Semet, Théo. Currently use manual scanning + gut feel.
- **Secondary:** Small trading boutiques structuring themselves on prediction markets — need data infrastructure, will pay API/enterprise tier.
- **Tertiary:** Retail traders. Self-serve, price-sensitive, lower tier.
- **Buyer:** The trader themselves (small shops) or the head trader (boutiques).
- **User:** Same as buyer — traders are hands-on operators.

## Pricing (planned)

- **Retail:** $99/mo
- **Pro:** $499/mo
- **API / Boutique:** custom, expected $2-5K/mo per seat at scale

## Integrations

- **Trading platforms:** Polymarket CLOB (live), Kalshi (planned)
- **News sources:** Newsquawk, RSS, SSE feeds
- **Data:** PostgreSQL + pgvector
- **AI:** Claude Sonnet (L3 intelligence), Gemma4 via Ollama (L2 pre-filter)
- **Delivery:** SSE / WebSocket alerts, REST API, web dashboard
- **Infra:** Local Docker for dev, Mac Mini for production, separate dev/prod DBs

## Team

- **Valentin Henry-Leo** — Co-founder / CEO / CAO (valentin.henryleo@gmail.com). French. Drives product, strategy, GTM, and the YC application. Owns the SaaS narrative and trader-side relationships.
- **Antoine [last name TBC]** — Co-founder / CTO. ENS maths + MVA (Mathématiques, Vision, Apprentissage). Research internship at École des Ponts et Chaussées on AI for mathematics. Ex-quantitative researcher at **Point72 / Cubist Systematic Strategies**. Owns the intelligence layer, modeling, and trading infrastructure.

## Company

- Founded: 2026
- Stage: Pre-seed, YC application in progress
- Locations: France
- Website: (TBD)
- GitHub: https://github.com/vhl59000 (private repos for product, this repo public for the brain)
- Entity: Delaware LLC planned for the operating company. No fund structure yet.

## Key terminology

| Term | Definition |
|------|-----------|
| Newsquick | The product. Always one word, capitalized N at start of sentences only. |
| Prediction market | Binary or scalar outcome contract on Polymarket / Kalshi. |
| L1 / L2 / L3 / L4 | The four pipeline layers: ingest, matching, intelligence, execution. |
| Type A / B / C / D | The four impact classifications (direct, constraint, conditional, thematic). |
| Market graph | 325-event + 2945-edge semantic/causal graph between markets. |
| CAO | Chief Agent Officer — Valentin and Antoine, the humans who review and approve everything agents produce. |
| Comp | Comparable company. Our two main comps are Newsquawk (FX) and Bloomberg Terminal (equities). |

## Key competitors and ecosystem

### Comps (news intelligence)
- **Newsquawk** — $2-5K/mo, FX/macro traders, our primary model. Customers cancel Bloomberg before they cancel Newsquawk.
- **Firstquack** — Twitter-native breaking news for traders.
- **Bloomberg Squawk** — $24K/year, equities, not prediction-market-specific.
- **Nothing for prediction markets** — open space.

### Target customers (top traders)
- **Domer (@ImJustKen)** — top historical Polymarket trader, $2.5M+ profit, ex-poker pro.
- **Théo / French Whale** — $30M on Trump 2024, will pay for better intel.
- **Evan Semet** — ex-quant, "six figures per month", runs statistical models on AWS.

### Adjacent firms (not direct competitors)
- **Susquehanna (SIG)** — largest market maker on Kalshi.
- **Jump Trading**, **DRW** — make markets, do statistical pairs trading. Not our customers.

### YC-relevant
- **Sharpe (W24)** — SaaS for quant firms, most similar model.
- **Valence (W26)** — prediction markets infrastructure aggregation.
- **Sequence Markets (W26)** — cross-platform execution infrastructure.

### Target investor
- **5(c) Capital** — first VC dedicated to prediction markets, $35M fund. Founders Adhi Rajaprabhakaran (ex-Kalshi trader #2) and Noah Zingler-Sternig (ex-head of ops Kalshi). Target post-YC.

## Strategic positioning

### Why news intelligence, not just arbitrage
The arb angle (semantic mispricings between correlated markets) is real but it's one use case. The bigger product is systematic intelligence: every news event mapped to its market implications. Applicable to every trader, defensible via data quality + latency + coverage. Arb dries up when everyone has the same tool.

### Why SaaS beats fund-only
Scalable ($2K/mo × 500 traders = $1M ARR, no capital risk), venture-backable (recurring software revenue, not AUM-dependent), data moat (subscribers → feedback → better model), orthogonal to market makers (SIG/Jump don't compete with selling news intel).

### Why now
Kalshi at $12B/mo (March 2026 record), Polymarket at $10B/mo. Kalshi valued $2.2B (Coatue), Polymarket ~$2B. ICE invested $2B in Polymarket. Citadel CEO investing in Kalshi Series C. 3rd Circuit ruled in favor of Kalshi against NJ. US regulatory environment clarifying. Institutional money arriving, no professional news tool exists.

## Standing instructions for Claude

- Default language: **English** for external content; French is fine for internal CAO conversations and casual ops.
- Tone: **direct, technical, founder-led — never fluffy or generic.** We talk like operators to operators. No "leverage", "synergize", "best-in-class", "next-generation", "seamless".
- Always apply `brand/guidelines/voice-and-copy.md` to external-facing content.
- Read `_insights.md` in the relevant folder before generating content.
- "Newsquick" is one word with capital N only at start of a sentence or when it's a proper noun in running text.
- When framing the product: lead with **"news intelligence for prediction markets"**, not "AI trading bot". We sell intelligence, not signals.
- When citing comps: Newsquawk first, Bloomberg second. Never compare to retail trading apps (Robinhood, etc.).
- Don't oversell own-trading: it's proof of product + data moat, not the primary business.
- Numbers we cite: $22B/mo combined volume, 85K+ markets, $10T TAM (5(c) Capital projection).
