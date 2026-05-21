# Inbox — the CAO's write surface

This folder is where the **CAOs (Valentin, Antoine)** drop free-form notes for the brain.

Open the repo as an Obsidian vault, write here, push to GitHub. The next time an agent runs `process-inbox` (manual or scheduled), it reads everything in this folder, classifies each note, files it into the right place in the brain (CONTEXT, MEMORY, content drafts, tasks, voice rules…), then moves the original to `_processed/`.

You write free-form, the agent rationalizes.

---

## File naming

Use **daily notes** by default:

```
inbox/2026-05-21.md
inbox/2026-05-22.md
```

You can also drop topic-specific notes when something deserves its own file:

```
inbox/2026-05-21_call-with-domer.md
inbox/2026-05-22_investor-feedback.md
```

Either works. The agent processes both.

---

## What to write here

Anything that's a signal for the brain. The agent will classify on its own. Examples:

- **New facts about the company** — *"Our pricing tiers are now $99 / $499 / custom"*
- **Voice rules / preferences** — *"Stop using 'robust' — it's vague"*
- **Content ideas** — *"LinkedIn post idea: what separates pros from amateurs on Polymarket"*
- **Drafts / fragments** — *"Quick draft of an investor update intro: 'May was the month we...'"*
- **Feedback on what agents produced** — *"Yesterday's investor update over-indexed on own-trading. Refocus on SaaS metrics."*
- **Tasks / todos** — *"Schedule call with Adhi at 5(c) next week"*
- **People context** — *"Met Théo today. He commissioned proprietary neighbor polls on Trump 2024 — will pay for granular intel."*
- **Random reflections** — *"We keep losing time on L4 entry logic — should reframe as 'execution is a stream, not a transaction'"*

If you're unsure whether to write it down, write it down. Cheaper than losing the thought.

---

## Format that works well

Markdown, free-form. Headings if you want, bullets if you want, prose if you want.

The only soft structure that helps the agent:

```markdown
---
date: 2026-05-21
---

# Daily — 2026-05-21

## thoughts
- ...

## ideas
- linkedin: ...
- substack: ...

## feedback
- on yesterday's <draft>: ...

## tasks
- [ ] ...
```

But honestly, just type. The agent figures it out.

---

## What happens after processing

1. Agent reads your notes
2. For each one, classifies it (fact / memory / idea / task / feedback / FYI)
3. Files it into the right place:
   - facts about the company → `CONTEXT.md` (with diff for your review)
   - voice/preference rules → `.claude/MEMORY.md` or `.claude/memory/<topic>.md`
   - content ideas → `content/<channel>/_drafts/YYYY-MM-DD_<slug>.md`
   - tasks → task board (or `inbox/tasks.md` until external board is wired)
   - feedback on a draft → applies edits + logs the rule in `_insights.md`
   - FYI / personal → archive only
4. Moves your note to `inbox/_processed/YYYY-MM/<original-filename>`
5. Commits with a message listing what was filed where
6. You pull on your side and see the structured output

The original is never deleted — `_processed/` is the audit trail.

---

## Workflow

| Step | Where | What |
|------|-------|------|
| 1. Write | Obsidian (mobile or desktop) | Free-form notes into `inbox/` |
| 2. Push | Obsidian Git plugin or manual `git push` | Notes land on GitHub |
| 3. Process | OpenClaw on Mac Mini (cron + on-demand) | Pulls, runs `/process-inbox`, commits |
| 4. Review | Obsidian (pull) | See what was filed where |

---

## Edge cases

- **Note is half-thought, half-garbage:** the agent keeps what's signal and notes the rest in `_processed/`. No silent drops.
- **Conflicting info with CONTEXT.md:** the agent flags the conflict, doesn't overwrite. You decide.
- **Note references a draft that doesn't exist yet:** the agent creates the draft skeleton in the right `_drafts/` folder and notes "drafted scaffold from inbox note".
- **You write the same fact twice:** the agent dedupes.

---

## Don't write here

- Anything secret (API keys, credentials) — this repo is public-ish
- Final drafts you've already polished — put them directly in `content/<channel>/_drafts/`
- Long-form essays — those go in `content/substack/_drafts/` or `content/blog/_drafts/`

The inbox is for **raw signal**, not finished work.
