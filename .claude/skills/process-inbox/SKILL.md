---
name: process-inbox
description: Read unprocessed notes from inbox/, classify each one, file it into the right place in the brain (CONTEXT, MEMORY, content drafts, tasks, voice rules), then move the original to inbox/_processed/. Pull-based: run on a schedule or on demand.
---

# Process Inbox

The CAOs (Valentin, Antoine) write free-form notes into `inbox/` via Obsidian and push to GitHub. This skill is the bridge between that raw input and the structured brain. It runs on the OpenClaw runtime (Mac Mini) on a daily schedule, but can also be invoked on-demand from any Claude/Cursor/Codex session.

## When to use

- Scheduled: once a day (8am via OpenClaw cron) to process the previous day's notes
- On-demand: when the CAO wants immediate processing of a fresh note (`/process-inbox`)
- After a long offline session (weekend, travel) when the inbox has accumulated

## MCP connectors

None required. Skill operates only on local repo files via filesystem + git.

Optional, when present in the runtime:
- Task board MCP (Notion / Linear / Twenty) — to push extracted tasks
- LinkedIn / X MCPs — only if the note explicitly says "post this now" (rare; default is draft-only)

## Context loading

Before processing, read:

1. `CONTEXT.md` — to know company facts that already exist (don't duplicate)
2. `.claude/MEMORY.md` — to know existing voice/preference rules
3. `agents/README.md` — to know which agents are active vs deferred
4. `inbox/README.md` — to remind yourself of the expected formats
5. `inbox/_processed/_insights.md` if it exists — to load lessons from prior runs

---

## Workflow

### Step 1 — Enumerate

List every `.md` file directly inside `inbox/`, excluding:
- `README.md`
- Anything in `inbox/_processed/`
- Dotfiles

Sort by file modification time (oldest first). Process in that order so later notes can reference earlier ones.

If the inbox is empty, exit cleanly with a one-line report: *"Inbox empty, nothing to process."* Do NOT commit on empty runs.

### Step 2 — Classify

For each note, read the full content. Decide which of these classifications applies. **A single note can hit multiple classifications** — extract all relevant signals, don't force a single bucket.

| Tag | When to use | Filing destination |
|-----|-------------|--------------------|
| `fact` | A new fact about Newsquick (product, ICP, pricing, comp, team, terminology) | Append (with diff) to relevant section of `CONTEXT.md` |
| `memory` | A voice/preference/process rule the CAO wants agents to follow | Append to `.claude/memory/<topic>.md` and register in `.claude/MEMORY.md` |
| `idea` | A content idea for a specific channel | Create `content/<channel>/_drafts/<date>_<slug>.md` with the idea as scaffold |
| `task` | A todo, follow-up, or external commitment | Append to `inbox/tasks.md` (or push to external board if MCP available) |
| `feedback` | A correction or rule about something an agent produced | Apply correction to target draft + log learning in the matching `_insights.md` |
| `person` | New info about a trader, investor, or partner | Append to `customer-success/_drafts/profile_<slug>.md` or `partnerships/<role>/profile_<slug>.md` |
| `fyi` | Personal thought, doesn't require action | Archive only, no action beyond filing the note |

Be conservative on `fyi` — when in doubt, classify as something actionable. The CAO can always tell you to back off in feedback.

### Step 3 — File each signal

For each classified signal, perform the appropriate write:

#### `fact`
- Locate the matching section of `CONTEXT.md` (Team, ICP, Pricing, Integrations, etc.)
- Append or amend with the new info
- If the fact **contradicts** existing content: do not overwrite. Add a `<!-- CONFLICT: <new fact> vs <existing fact> — flagged for CAO review -->` and surface in the report
- Never reformat unrelated parts of the file

#### `memory`
- If the rule fits an existing memory file (e.g. voice rules → `.claude/memory/voice-rules.md`), append there
- Otherwise create a new `.claude/memory/<topic>.md` and register a 1-line entry in `.claude/MEMORY.md`
- Always include the date and source: `<!-- Added 2026-05-21 from inbox/2026-05-21.md -->`

#### `idea`
- Detect the channel from the note (the CAO usually says "linkedin idea", "substack idea", "x post about…")
- Create `content/<channel>/_drafts/YYYY-MM-DD_<slug>.md` with frontmatter:
  ```yaml
  ---
  date: YYYY-MM-DD
  channel: <channel>
  topic: <slug>
  format: post
  status: idea
  source: inbox/<original-filename>
  ---
  ```
- Body = the idea text, plus a `## Notes from CAO` section preserving the raw note verbatim

#### `task`
- Append to `inbox/tasks.md` in the format:
  ```
  - [ ] <task> (source: inbox/<filename>, added YYYY-MM-DD)
  ```
- If a task MCP is available, ALSO push the item there

#### `feedback`
- Identify which draft / agent output the feedback is about
- Apply the correction to the draft if specific enough (e.g. "remove paragraph 3")
- Log the underlying rule in the matching `_insights.md` (e.g. `content/linkedin/_insights.md`)
- If the feedback is about a recurring agent behavior (e.g. "investor updates over-index on own-trading"), also append to `.claude/memory/voice-rules.md` or the relevant memory file

#### `person`
- Look for an existing profile file in `customer-success/`, `sales/`, or `partnerships/`
- If found, append. If not, create a new profile file with the person's name as slug
- Include source citation

#### `fyi`
- No write. Just preserve the note via the archive step.

### Step 4 — Archive the original

Move the note from `inbox/<filename>` to `inbox/_processed/YYYY-MM/<filename>`. Create the month subfolder if it doesn't exist.

Use `git mv` so the history is preserved.

### Step 5 — Write the run report

Create `inbox/_processed/_runs/YYYY-MM-DD_HHMM_run.md` with:

```markdown
---
date: YYYY-MM-DD HH:MM
notes_processed: N
files_modified: M
---

# Inbox run — YYYY-MM-DD HH:MM

## Summary
- Notes processed: N
- Classifications: <breakdown by tag>
- Files modified: <list>
- Conflicts flagged: <count>

## Per-note breakdown

### inbox/<filename>
- Signals extracted: <tags>
- Filed to: <list of destination files>
- Notes: <anything the CAO should review>

[repeat per note]

## Conflicts to review
- [if any] <description with file references>

## Self-improvement note
- [if any pattern noticed about how CAO writes inbox notes]
```

### Step 6 — Commit

Stage all changes (modifications + new files + moved notes). Commit with:

```
chore(inbox): process N notes from YYYY-MM-DD
```

Body of commit lists filing destinations briefly. Push to origin.

If there are conflicts flagged in Step 3, the commit message body must include `[!] Conflicts flagged for CAO review`.

---

## Quality checklist

Before committing, verify:

- [ ] Every note in `inbox/` (except README) has been processed and moved
- [ ] No note was silently dropped — every one has at least one classification and one filing destination, even if just archived
- [ ] All new content files have correct frontmatter
- [ ] No write was made to `_published/` or `_examples/` — those are only promoted by the CAO
- [ ] No external send happened (no email sent, no LinkedIn post published) — drafts only
- [ ] Conflicts are flagged, not silently resolved
- [ ] Run report is written to `inbox/_processed/_runs/`

---

## Self-improvement

After each run, scan how the CAO edited the agent's output since the last run (git log on the modified files). Look for patterns:

- Did the CAO consistently move a classification from one bucket to another? → Update the classification heuristics in this skill
- Did the CAO consistently rephrase facts before keeping them? → Note the rephrasing pattern in `inbox/_processed/_insights.md`
- Did the CAO delete entire filings? → Note what kind, and tighten the conservative filter

Append learnings to `inbox/_processed/_insights.md`. Don't rewrite the skill silently — log the pattern and let the CAO promote it to the skill.

---

## Failure modes & escalation

- **Note is gibberish or empty:** archive to `_processed/_gibberish/` and note in the run report. Don't try to extract signal.
- **Note references a person/draft we can't find:** create the file/profile anyway, with a `[?]` placeholder, and surface in the run report.
- **Note explicitly asks for an external send ("post this now to LinkedIn"):** ignore the send. Draft only. Flag in the report: *"Inbox note requested external publish. Draft saved; CAO must approve and publish manually."* Sending is never automated by this skill.
- **Conflicting info between two notes in the same run:** apply the later (more recent) note. Flag the conflict in the run report.
- **MCP failure (task board down, etc.):** continue with file-system filing. Add a note in the report: *"External MCP <X> unavailable; tasks filed locally only."*

---

## Run cadence

- **Scheduled (OpenClaw):** every day at 08:00 local time
- **On-demand (any agent):** `/process-inbox` whenever a fresh note needs immediate processing
- **Manual fallback (Claude Code on laptop):** the CAO can run this from a local Claude session; same behavior, no scheduling required

This skill is deliberately idempotent: running it twice on the same inbox state is a no-op after the first run, because the notes have been moved to `_processed/`.
