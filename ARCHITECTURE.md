# Newsquick Brain — Architecture

> The vision behind this repo, the role of each component, and how the loop closes.
> Written 2026-05-21 by Valentin + Claude during the initial setup session.

---

## What this repo is

This is the **company brain** for Newsquick — a news intelligence SaaS for prediction market traders.

It is **not** the product code. The product (the L1→L4 pipeline, Polymarket execution, dashboard, API) lives in `vhl59000/newsquick`. This repo is the **knowledge and operations layer** of the company: who we are, what we sell, how we write, how we sell, who our customers are, what our agents are allowed to do, what they've learned.

It is forked from `getnao/sylph` — an open-source company-brain template built by nao Labs (YC X25) to run their own company. We kept Sylph's structure (skills, agents, content lifecycle, self-improving loop) and personalized everything for Newsquick.

---

## Why a company brain at all

We started with a problem: a lot of context about Newsquick lives in Valentin's head and in scattered notes. As the company scales (YC, beta users, first hires), this knowledge needs to be:

1. **Versioned** — every change tracked, every learning preserved
2. **Multi-agent accessible** — Claude Code, OpenClaw, Cursor, Codex should all read the same source of truth
3. **Self-improving** — every interaction should make the system better, not just emit output
4. **Human-readable** — the CAOs (Valentin, Antoine) must be able to read and edit any file directly

Markdown + git satisfies all four. That's what Sylph is, and that's what we're building on.

---

## The four layers of the brain

### 1. Identity (static)

```
CONTEXT.md              — who we are, what we sell, who we sell to
AGENTS.md               — how agents are expected to work in this repo
brand/guidelines/       — voice, copy rules, visual system
.claude/memory/         — voice rules, founding team profile, product framing
```

Everything else in the repo derives from these files. Agents read them on every run.

### 2. Memory (dynamic, agent-written)

```
.claude/MEMORY.md                       — index of all learnings
.claude/memory/<topic>.md               — detailed learning files
content/<channel>/_insights.md          — per-channel learnings (what works, what doesn't)
inbox/_processed/_insights.md           — patterns observed in CAO inbox writing
```

This is the **self-improving** part. Every skill, after producing output and seeing how the CAO edited it, writes back the lessons. Over weeks, voice and judgment converge.

### 3. Skills (verbs)

```
.claude/skills/<skill-name>/SKILL.md    — workflow markdown for one task type
```

35 skills shipped:
- **Content** (10): `linkedin`, `substack-writer`, `blog-writer`, `newsletter`, `x`, `reddit`, `website`, `slack-community`
- **Sales** (3): `create-campaign`, `crm`, `customer-report`
- **Ops** (2): `zero-inbox`, `process-inbox` ← *new, custom to Newsquick*
- **Finance** (1): `investor-update`
- **HR** (1): `hr-screening`
- **Legal** (2): `create-contract`, `review-contract`
- **Product** (1): `create-issue`
- **Brand** (2): `brand-designer`, `brand-guidelines`
- **Events** (1): `event-manager`
- **Writing** (1): `email-writer`
- **Agent routines** (8): `chief-of-staff`, `cmo`, `product-manager`, etc. — these are the per-agent skills
- **Meta** (4): `sylph-setup`, `sylph-setup-agent`, `sylph-setup-skill`, `sylph-create-skill`

Each skill is structured: MCP connectors → context loading → numbered steps → quality checklist → self-improvement loop.

### 4. Agents (loops)

```
agents/<name>/
  ROLE.md       — identity, responsibilities, decision boundaries
  PROMPT.md     — the recurring routine executed by the scheduler
  _logs/        — daily briefings and outputs
  _plans/       — weekly plans
```

8 agents shipped (Chief of Staff, CMO, PM, Head of Sales, Customer Success, Head of Data, Executive Assistant, Brand Designer). Most are templated but inactive — see `agents/README.md` for the current activation status. At pre-launch (solo founders, no Slack/Notion/CRM wired yet), most agents are on-demand only.

---

## The runtime architecture

The brain is **stateless markdown**. It does nothing on its own. Something must read it and execute. We made an explicit choice about *what* that something is.

```
┌────────────────────────────────────────────────────────────────┐
│  newsquick-brain (GitHub: vhl59000/sylph)                       │
│  source of truth — versioned markdown, no code, no telemetry    │
└─────┬───────────────────┬──────────────────────┬───────────────┘
      │                   │                      │
      │ pull/push         │ pull/push (auto)     │ pull only
      │                   │                      │
┌─────▼─────────┐  ┌──────▼──────┐  ┌────────────▼────────────┐
│ OpenClaw      │  │ Obsidian    │  │ Claude Code             │
│ (Mac Mini)    │  │ (CAO's      │  │ (on Valentin's laptop)  │
│               │  │  laptop +   │  │                          │
│ Scheduler +   │  │  phone)     │  │ Ad-hoc sessions for      │
│ MCP +         │  │             │  │ coding, debugging the    │
│ 24/7 agents   │  │ Write +     │  │ brain, on-demand skills  │
│               │  │  read       │  │                          │
└───────────────┘  └─────────────┘  └─────────────────────────┘
```

### Why OpenClaw, not Claude scheduled tasks

OpenClaw runs on the Mac Mini production box — the same machine that hosts the Newsquick Postgres + L4 execution loop. The Mini is on 24/7 for the product anyway, so colocating the agent runtime there is free. We get:

- A proper scheduler (cron + on-demand)
- MCP connectors (Gmail, Slack later, GitHub now)
- Long-running agents with isolation and logs
- No need to keep Valentin's laptop open

Trade-off: single physical machine = single point of failure. If the Mini reboots or loses power, every agent stops. Acceptable at pre-launch; will move to cloud (Hetzner or Fly.io) when we have paying customers depending on reliability.

### Why Obsidian for the human side

Obsidian opens the brain directory as a vault. Markdown renders cleanly, links resolve, file tree is navigable on desktop and mobile. The **Obsidian Git plugin** auto-pulls/pushes — so the CAOs can write notes from anywhere and they end up in the repo.

Obsidian is **not** a viewer. It is the **primary write surface** for the CAOs. Free-form notes go into `inbox/`. Polished drafts go directly into `content/<channel>/_drafts/`. Anything else (CONTEXT edits, voice rule updates) can be written directly into the relevant file.

### Why Claude Code is still in the picture

For coding the product, debugging skills, running interactive sessions (`/linkedin write a post about X` with iteration), Claude Code on Valentin's laptop is the right interface. OpenClaw is for scheduled and background work; Claude Code is for synchronous co-creation.

---

## The bidirectional flow (the key insight)

The brain is not a one-way prompt library. It's a loop.

```
              CAO writes free-form
              into Obsidian (inbox/)
                       │
                       ▼
              git push (manual or
              auto-push via Obsidian
              Git plugin)
                       │
                       ▼
              GitHub: brain repo
              has new commits
                       │
                       ▼
              OpenClaw agent pulls
              (on schedule or
              on-demand)
                       │
                       ▼
              /process-inbox skill
              runs:
              - classifies each note
              - files into the right
                place (CONTEXT, MEMORY,
                drafts, tasks, _insights)
              - moves original to
                inbox/_processed/
                       │
                       ▼
              Agent commits the
              structured filings
                       │
                       ▼
              CAO pulls on Obsidian
              and sees the brain
              re-organized
                       │
              (later, when reviewing
              drafts the agent produced,
              the CAO edits them. The
              skill diffs the edits and
              writes learnings back into
              _insights.md — closing the
              self-improving loop)
```

The CAO never has to file things manually. They write where the thought happens (inbox), the agent files it. Over time:
- `CONTEXT.md` grows with new facts
- `.claude/memory/` captures voice and process rules
- `content/<channel>/_drafts/` accumulates ideas as drafts
- `_insights.md` files accumulate channel-specific judgment
- Drafts the agent generates become more and more aligned with the CAO's taste

---

## Polling vs webhooks

We chose **pull-based polling** for v1.

OpenClaw runs the `process-inbox` skill on a schedule (8am daily). When it fires, it `git pull`s the brain, processes whatever is in `inbox/`, commits, pushes. Latency = the cron interval.

We considered push-based webhooks (GitHub → OpenClaw via Cloudflare tunnel or Tailscale Funnel) but the latency benefit doesn't matter at our stage. Plus, polling is zero plumbing — works the moment OpenClaw has the repo + a GitHub token.

We'll upgrade to webhooks when the gap between writing a note and wanting it processed becomes painful. Not before.

---

## What's set up vs what's pending

### Set up (2026-05-21)
- Brain fork at `https://github.com/vhl59000/sylph`
- Local clone at `/Users/vhl/Projects/YC/newsquick-brain`
- Remotes: `origin` = fork, `upstream` = getnao/sylph
- `CONTEXT.md` filled with Newsquick context (product, ICP, comps, team — both founders)
- `AGENTS.md` updated with CAO definition (Valentin + Antoine)
- `.claude/MEMORY.md` + `.claude/memory/` seeded with founding team, voice rules, product framing
- `brand/guidelines/voice-and-copy.md` rewritten in Newsquick voice
- `agents/README.md` marks which agents are active vs deferred
- `inbox/` folder created with onboarding README
- `process-inbox` skill written, registered in `AGENTS.md` skill index
- This `ARCHITECTURE.md` written

### Pending (in priority order)
1. **Push the brain to GitHub** (`git push origin main`) — so OpenClaw can pull it
2. **Configure OpenClaw on the Mac Mini**:
   - Clone the brain repo into OpenClaw's working directory
   - Create a GitHub PAT with read/write on `vhl59000/sylph`
   - Configure a cron entry (or OpenClaw native scheduler) to run `process-inbox` daily at 8am
   - Test end-to-end: write a note in `inbox/`, push, wait for the agent, verify the note got filed
3. **Set up Obsidian on Valentin's laptop**:
   - Open the brain dir as a vault
   - Install Obsidian Git community plugin
   - Configure auto-pull and auto-push intervals
4. **Confirm Antoine's full name, email, pronouns** — remove the `[last name TBC]` placeholders
5. **First real inbox test** — drop 3-5 notes, run `process-inbox` manually from a Claude Code session on the laptop, validate the classification and filing
6. **Wire MCPs** as integrations come online (Slack, Notion, Linear, Gmail, etc.) — activate the deferred agents one by one as their dependencies land

---

## Known trade-offs

We're aware of these and have decided to accept them for now.

- **Single point of failure** — everything runs on one Mac Mini. Move to cloud when paid customers care.
- **No concurrency safety** — if multiple agents push at once, merge conflicts. Mitigated for now by running agents sequentially in OpenClaw's scheduler.
- **No webhook** — pull latency = cron interval. Upgrade when latency hurts.
- **Sylph templates assume Notion/Slack/Granola** — we haven't replaced all of those references in agent PROMPTs. Will adapt them progressively as we wire integrations.
- **Public-ish repo** — the brain repo is on GitHub. Don't write secrets, API keys, or NDA-sensitive customer data. The `inbox/README.md` reminds the CAOs of this.

---

## How to extend

When something new comes up, the rule is:

- **A new fact about the company** → write a one-liner in `inbox/`, let `process-inbox` file it
- **A new voice or process rule** → same
- **A new content channel** (e.g. start posting on Hacker News) → create `content/hn/_drafts/` + `_published/` + `_examples/` + `_insights.md`, then `/sylph-create-skill hn` to add a writer skill
- **A new domain entirely** (e.g. recruiting needs more structure) → add a folder at the root, write a domain README, optionally add skills
- **A new agent** → `/sylph-create-skill <agent-name>` then create `agents/<agent-name>/ROLE.md` and `PROMPT.md`, register in `agents/README.md`

The system is meant to grow with the company. Anything markdown-shaped fits.

---

## The principle that holds it all together

> Write where the thought happens. Let the agents file. Review the structured output. Edit to teach. The brain gets better every week.

— Valentin & Claude, 2026-05-21
