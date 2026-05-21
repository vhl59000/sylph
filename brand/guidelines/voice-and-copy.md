# Voice & Copy Guidelines

## Brand Positioning

Newsquick is news intelligence for prediction market traders — a Newsquawk equivalent purpose-built for traders on Polymarket and Kalshi.

### Core Messaging Hierarchy

1. **Tagline:** *News intelligence for prediction markets.*
2. **One-liner:** Newsquawk for prediction markets — real-time news mapped to market implications, for traders on Polymarket and Kalshi.
3. **Extended description:** Prediction markets just crossed $22B/month in combined volume. Top traders still rely on Twitter and gut feel to figure out which markets a news event affects. Newsquick ingests news in real time, maps each event to its direct, indirect, and second-order market implications, and delivers structured intelligence to professional traders.

### Brand Beliefs

- Every new asset class earns a dedicated news intelligence tool. Equities got Bloomberg. FX got Newsquawk. Prediction markets are next.
- Intelligence beats signals. We make traders make better calls — we don't make calls for them.
- Latency and coverage are moats. Structured market mapping is the wedge.
- Own-trading exists to prove the product and label the data, not to compete with our customers.

## Voice Characteristics

### Tone Spectrum

| Attribute | We ARE | We are NOT |
|-----------|--------|------------|
| Direct | Lead with the punchline, declarative sentences | Hedging, soft openers, "I think" |
| Technical | Practitioner-level, real numbers, named traders | Jargon-heavy, gatekeeping, fake-quant |
| Founder-led | First-person where it earns trust, opinionated | Marketing-team-by-committee, corporate-speak |
| Concise | Every word earns its place | Terse, cold, incomplete |
| Operator-grade | Speaks the language of traders on the desk | Pop finance, retail-investor framing |

### Writing Rules

**Product name casing**
- Correct: "Newsquick maps every news event to its market implications."
- Correct mid-sentence: "...we built Newsquick to fix that."
- Wrong: "NewsQuick", "newsquick" (mid-sentence proper noun), "NEWSQUICK"

**Key terms - use precisely**
- **Prediction market**: A binary or scalar outcome contract on Polymarket or Kalshi. Not "betting market" or "wagering platform" in B2B copy.
- **News intelligence**: Our product category. Not "alerts", "signals", or "AI trading".
- **Impact mapping / market implications**: What L3 produces. Not "predictions" or "forecasts".
- **Direct / indirect / second-order**: Type A through D from CONTEXT.md. Use the technical labels when accuracy matters; use plain English in marketing copy.

**Sentence structure**
- Short, declarative. Fragments allowed for emphasis.
- Dashes (—) for asides; em dash, not hyphen with spaces.
- Oxford comma.
- Numbers as digits: "$22B", "85,000 markets", "300ms latency".

**Active voice**
- Yes: "Newsquick maps every news event to the markets it moves."
- No: "Every news event is mapped to the markets it moves by Newsquick."

### Word Choices

**Prefer:**
- "trader" over "user"
- "market" over "asset" (when talking about Polymarket/Kalshi contracts)
- "intelligence" over "AI" in product copy
- "ship" over "deliver"
- "fast" over "efficient"
- "low-latency" over "real-time" when latency is the point
- "operator" over "professional"

**Avoid:**
- "leverage", "utilize", "synergize", "empower"
- "best-in-class", "cutting-edge", "next-generation", "revolutionary"
- "seamless" (overused)
- "robust" (vague)
- "solution" as a noun for the product
- "AI trading bot", "algorithmic trading", "quant tool" — wrong framing
- "Robinhood for X", "Coinbase for X" — wrong reference class

## Copy Formats

### Headlines
- Short. 2-5 words ideal. Fragments fine.
- Anchor on the comparable when useful: "Newsquawk for prediction markets."

**Examples:**
- *News intelligence for prediction markets.*
- *Every news event, mapped to every market.*
- *The Bloomberg moment for prediction markets.*
- *Built for traders on Polymarket and Kalshi.*

### Product Descriptions
- Lead with what it does, not what it is.
- One sentence = one idea.
- End with the benefit to a trader, not the feature.

**Pattern:** [Action verb] every [news event / market move] — [trader benefit].

### Feature Copy
- Start with the verb.
- Under 15 words per feature line.
- No periods on feature lines (unless they're full sentences).

**Examples:**
- Ingest news in real time across geopolitical, political, and corporate sources
- Map each event to direct, indirect, and second-order market implications
- Deliver structured alerts via dashboard, SSE, and API

### Social Media

- **LinkedIn:** insights from building, not announcements. Founder voice. Numbers and named examples (Domer, $22B/mo, etc.). Never "excited to share".
- **Twitter/X:** sharper, punchier. Fragments OK. Personality welcome. Use the trader voice — the way Domer, Adhi, and Evan write.
- **Substack / blog:** longer-form analysis. Lead with a market event, then unpack the intelligence layer.
- Exclamation marks: sparingly, max one per post. Usually zero.

### Email

- Subject lines: clear and specific, never clickbait. "Newsquick — May intel update" not "🚀 Something big is coming".
- Body: conversational but purposeful, founder-to-trader.
- One CTA per email.
- Sign-offs: "— Valentin" for founder emails, "— Newsquick" for product/transactional.

## Describing the Product's Capabilities

When writing about what Newsquick does, organize around these pillars:

1. **Ingest** — low-latency capture of geopolitical, political, and corporate news (Newsquawk, RSS, SSE).
2. **Match** — pgvector-driven mapping of each news item to relevant Polymarket and Kalshi markets.
3. **Intelligence** — Claude-Sonnet-powered classification of impact type (direct, constraint, conditional, thematic).
4. **Deliver** — real-time alerts, dashboard, and API for human-in-the-loop trading decisions.

Never position Newsquick as "an AI trading bot" or "an algorithmic trading platform." Newsquick is **news intelligence infrastructure** that traders use to make better, faster decisions. The trader is always in the loop.
