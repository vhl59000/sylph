# Agent Workflow - Open Company OS

This file tells Claude how to work within this repo. Read it before starting any task.
Company and product context lives in `.claude/CONTEXT.md` (auto-loaded).

---

## 1. Orient first

Before doing anything, identify:
- **What domain** does this task belong to? (content, sales, product, brand, finance, etc.)
- **Which channel or subfolder** is relevant? (e.g. `content/linkedin/`, `sales/outbound/`)
- **Is there a skill** for this task? Check `.claude/skills/` - skills are namespaced as `domain:task` (e.g. `content:linkedin`, `sales:outbound`).

If a skill exists for the task, load and follow it. Skills encode channel-specific rules, tone, format, and the content flywheel steps.

---

## 2. Read before writing

Before generating any content or making decisions:

1. Read the relevant `_insights.md` for the channel/domain - it contains what works, what doesn't, cadence, and audience notes.
2. Read the `_examples/` files for the channel - these are the best-performing pieces and serve as the quality bar.
3. Read any relevant `_templates/` - use them as structural starting points.

If these files are empty, proceed without them but note that examples and insights are missing.

---

## 3. Content lifecycle - always follow this

Every piece of content follows this path:

```
create -> _drafts/ -> review/edit -> _published/ -> update _insights.md -> promote to _examples/ if high-performing
```

| Stage | Folder | When |
|-------|--------|------|
| Working draft | `_drafts/` | While creating or iterating |
| Finalized/published | `_published/` | After it's approved and sent/posted |
| Insights update | `_insights.md` | After publishing - log what worked, what didn't, performance |
| Example | `_examples/` | Only when a piece performs well - used for future generation |

**Always save new content to `_drafts/` first.** Never write directly to `_published/` or `_examples/`.

---

## 4. File naming

Use this format for all content files:

```
YYYY-MM-DD_short-slug.md
```

Example: `2025-04-05_linkedin-context-engineering.md`

---

## 5. Frontmatter

All content files must include frontmatter:

```yaml
---
date: YYYY-MM-DD
channel: linkedin          # the content channel
topic: context-engineering # main topic
format: post               # post, thread, article, email, deck, etc.
status: draft              # draft | published
performance:               # fill in after publishing (views, clicks, engagement, etc.)
notes:                     # anything useful for future generation
---
```

---

## 6. Updating memory

After completing a task, if you learned something non-obvious:
- Log decisions and learnings in `.claude/MEMORY.md`
- Update the relevant `_insights.md` if content performance or patterns were observed

---

## 7. Repo map (quick reference)

```
.claude/
  CONTEXT.md        - stable company/product facts (read-only, update only when company changes)
  MEMORY.md         - living decision + learning log (update often)
  skills/           - domain-namespaced Claude skills

brand/              - brand assets, guidelines, voice
content/            - all content by channel (blog, linkedin, substack, x, newsletter, etc.)
  <channel>/
    _drafts/        - works in progress
    _published/     - archive of published pieces
    _examples/      - best-performing pieces (few-shot reference)
    _templates/     - reusable formats
    _insights.md    - channel learnings

sales/              - outbound sequences, proposals, copywriting
customer-success/   - meeting follow-ups, user research
product/            - roadmap, issues
finance/            - investor updates
partnerships/       - consulting, freelance, influencer
events/             - hackathons, meetups, remote
admin/              - internal ops (email, Slack, LinkedIn)
compliance/         - legal and compliance notes
```

---

## 8. Skills reference

Skills are invoked by their namespace. Always prefer using a skill over ad-hoc generation.

| Domain | Skills |
|--------|--------|
| `content:` | `blog`, `hn`, `launches`, `lead-magnets`, `linkedin`, `newsletter`, `playbooks`, `presentations`, `reddit`, `substack`, `website`, `x`, `youtube` |
| `sales:` | `outbound`, `proposals` |
| `customer-success:` | `meeting-followup`, `user-research` |
| `brand:` | `brand-guidelines` |
| `finance:` | `investor-update` |
| `hr:` | `screening` |
| `events:` | `brief` |
| `partnerships:` | `brief` |
| `product:` | `roadmap` |

**When creating a new skill**, always also create a matching command file in `.claude/commands/<skill-name>.md`. This is what makes it callable as a `/` slash command. The command file should be a short dispatcher that loads and executes the skill file.
