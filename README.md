# Open Company OS

The open-source operating system for AI-native companies. Run your entire company with AI agents, skills, and a self-improving content flywheel.

**Built by [nao](https://getnao.io).** Inspired by the "Company Brain" concept from [Claire Gouze's Substack](https://thenewaiorder.substack.com).

---

## What is this?

Open Company OS is a single Git repo that contains everything an AI agent needs to operate your company: context about your business, skills for every task, agent routines that run on schedules, and a content flywheel that improves itself over time.

It works with [Claude Code](https://claude.ai/code), [Cursor](https://cursor.sh), [Codex](https://openai.com/codex), or any AI coding agent that can read markdown files.

### The core idea

Instead of starting every AI conversation from scratch, you give the agent a persistent, version-controlled brain:

- **Context** (`.claude/CONTEXT.md`) - who you are, what you sell, your ICP, your team
- **Memory** (`.claude/MEMORY.md`) - decisions made, lessons learned, preferences discovered
- **Skills** (`.claude/skills/`) - detailed instructions for every recurring task
- **Agents** (`agents/`) - AI employees with roles, routines, and schedules
- **Content flywheel** - a self-improving loop where published content feeds back into better generation

### The self-improving content flywheel

This is the key insight that makes Open Company OS different from a static prompt library:

```
Create content
     |
     v
Save to _drafts/ -> Review/edit -> Move to _published/
                                        |
                                        v
                                Update _insights.md
                                (what worked, what didn't,
                                 performance data)
                                        |
                                        v
                              Promote top performers
                              to _examples/
                                        |
                                        v
                              Skills read _examples/ +
                              _insights.md before
                              generating new content
                                        |
                                        v
                              Better content next time
```

Every channel (LinkedIn, blog, newsletter, etc.) has its own `_insights.md` and `_examples/` folder. Skills read these before generating, so the system learns from its own output over time.

When Claude drafts something and you edit it, the skill captures what changed and why - adding that learning back into itself. The skills literally rewrite themselves to get better.

---

## Quick start

### 1. Fork this repo

```bash
gh repo fork getnao/open-company-os --clone
cd open-company-os
```

### 2. Fill in your company context

Edit `.claude/CONTEXT.md` with your company's details:
- What you do, who you serve
- Your team
- Your tone and voice
- Key terminology

### 3. Customize your first skill

Pick one skill to start with (e.g. `.claude/skills/content/linkedin/SKILL.md`):
- Replace `[placeholders]` with your specifics
- Add 2-3 examples of your best content to `content/linkedin/_examples/`
- Fill in `content/linkedin/_insights.md` with what you know works

### 4. Try it

Open the repo in Claude Code and run:

```
/linkedin-writer Write a post about [topic]
```

Claude will read your context, check your insights and examples, then draft in your voice.

### 5. Close the loop

After publishing, move the file from `_drafts/` to `_published/`, update `_insights.md` with performance data, and promote your best pieces to `_examples/`.

---

## Directory structure

```
.claude/
  CONTEXT.md        - stable company/product facts (update when company changes)
  MEMORY.md         - living decision + learning log (updated by Claude)
  skills/           - domain-namespaced skills (the instructions)
  commands/         - slash command dispatchers
  hooks/            - session lifecycle hooks

agents/             - AI employees (ROLE.md + PROMPT.md + _logs/)
brand/              - brand assets, guidelines, voice
content/            - all content by channel
  <channel>/
    _drafts/        - works in progress
    _published/     - archive of published pieces
    _examples/      - best-performing pieces (few-shot reference)
    _templates/     - reusable formats
    _insights.md    - channel learnings (the feedback loop)

sales/              - outbound sequences, proposals, copywriting
customer-success/   - meeting follow-ups, user research
product/            - roadmap, issues
finance/            - investor updates
partnerships/       - consulting, freelance, influencer
events/             - hackathons, meetups, remote
admin/              - internal ops (email, Slack, CRM)
hr/                 - hiring, screening
compliance/         - legal and compliance notes
docs/               - guides for using this system
```

## Skills

Skills are detailed instruction files that tell Claude exactly how to perform a task. They live in `.claude/skills/` and are invoked via slash commands.

| Domain | Skills | What they do |
|--------|--------|-------------|
| `content:` | `linkedin`, `blog`, `substack`, `newsletter`, `x`, `reddit`, `website` | Content creation per channel |
| `sales:` | `create-campaign`, `proposals` | Outbound campaigns and proposals |
| `customer-success:` | `customer-report`, `follow-up` | Client briefs and follow-ups |
| `brand:` | `brand-guidelines`, `create-brand-asset`, `create-presentation` | Brand assets and guidelines |
| `finance:` | `investor-update` | Monthly investor updates |
| `hr:` | `screening` | Job application screening |
| `product:` | `create-issue` | GitHub issue creation |
| `events:` | `event-manager` | Event creation and management |
| `ops:` | `zero-inbox`, `email-writer`, `hemingway` | Inbox triage, email drafting, copy editing |

### How skills self-improve

Every content skill follows this pattern:

1. **Before generating**: read `_insights.md` + `_examples/` for the channel
2. **Generate**: apply the skill's rules + voice calibration + few-shot examples
3. **After publishing**: diff what Claude drafted vs what the user kept, capture the learning, update the skill file

This means your skills get better with every piece of content you publish. See `docs/self-improvement.md` for the full guide.

## Agents

Agents are AI employees that run on schedules. Each has a role definition and a recurring routine.

| Agent | What it does | Cadence |
|-------|-------------|---------|
| **Chief of Staff** | Inbox triage, project pulse, weekly planning, daily briefing | Daily |
| **CMO** | Content planning, drafting, docs maintenance, release comms | Daily |
| **Product Manager** | Issue creation from feedback, PR monitoring, daily recap | Daily |
| **Customer Success** | Follow-up drafting from CSM backlog | Weekly |
| **Head of Data** | Analytics reports, metric monitoring | Daily |
| **Head of Sales** | Pipeline management, outbound campaigns | Weekly |
| **Executive Assistant** | Admin, HR, finance, accounting ops | Daily |
| **Brand Designer** | Visual and motion assets | On-demand |

### How agents work

```
agents/<name>/
  ROLE.md      - who they are, what they own, escalation rules
  PROMPT.md    - the recurring routine (step-by-step)
  _logs/       - daily outputs
  _plans/      - weekly/monthly plans
```

Agents run via Claude Code scheduled tasks (cron). The Chief of Staff orchestrates the others - spawning specialist agents in parallel and collecting their summaries into the daily briefing.

### Key principle: drafts only

No agent ever sends emails, publishes content, or takes irreversible actions. Everything goes to `_drafts/` or `_logs/` for human review. The founder approves and publishes.

---

## Design principles

1. **Git is the source of truth.** Everything is version-controlled markdown. No databases, no SaaS lock-in.
2. **Skills over prompts.** A skill is a detailed, evolving instruction file - not a one-shot prompt. It reads context, applies rules, and improves itself.
3. **Agents over assistants.** An agent has a role, boundaries, and a routine. It knows what it decides vs what it escalates.
4. **The flywheel compounds.** Every published piece makes future generation better. Insights and examples accumulate.
5. **Human in the loop.** AI drafts, humans approve. No auto-sending, no auto-publishing.
6. **Any agent, same brain.** Claude Code, Cursor, Codex - they all read the same `.claude/` files.

---

## Customization guide

### Adding a new content channel

1. Create the folder: `content/<channel>/_drafts/`, `_published/`, `_examples/`, `_templates/`
2. Create `content/<channel>/_insights.md` from the template
3. Create a skill: `.claude/skills/content/<channel>/SKILL.md`
4. Create a command: `.claude/commands/<channel>-writer.md`
5. Add 2-3 examples to `_examples/`

### Adding a new agent

1. Create `agents/<name>/ROLE.md` and `PROMPT.md`
2. Create a matching skill: `.claude/skills/<name>/SKILL.md`
3. Create a command: `.claude/commands/<name>.md`
4. Register a scheduled task (see `agents/README.md`)

### Connecting MCP servers

Create `.mcp.json` at the repo root to connect external tools:

```json
{
  "mcpServers": {
    "your-crm": {
      "type": "http",
      "url": "https://your-crm.example.com/mcp"
    }
  }
}
```

Common integrations: CRM, Gmail, Slack, Calendar, Notion, Canva, LinkedIn.

---

## License

MIT - use it however you want.

## Credits

Built by the team at [nao](https://getnao.io) - the open-source analytics agent builder.

Inspired by the "Company Brain" concept from [Claire Gouze's Substack](https://thenewaiorder.substack.com). Read the original article for the full philosophy behind this approach.
