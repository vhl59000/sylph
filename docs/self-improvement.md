# How Open Company OS Improves Itself

This is the most important concept in the entire repo. Without it, you have a static prompt library. With it, you have a system that gets better every week.

---

## The core loop

```
                    +------------------+
                    |   You ask Claude  |
                    |   to create       |
                    |   content         |
                    +--------+---------+
                             |
                             v
                    +------------------+
                    | Claude reads:     |
                    |  - _insights.md   |
                    |  - _examples/     |
                    |  - SKILL.md       |
                    |  - CONTEXT.md     |
                    +--------+---------+
                             |
                             v
                    +------------------+
                    | Claude generates  |
                    | a draft in        |
                    | _drafts/          |
                    +--------+---------+
                             |
                             v
                    +------------------+
                    | You review, edit, |
                    | and publish       |
                    +--------+---------+
                             |
                             v
              +--------------+---------------+
              |              |               |
              v              v               v
      +-------+----+ +------+-----+ +-------+------+
      | Move to     | | Update     | | If top       |
      | _published/ | | _insights  | | performer:   |
      |             | | .md        | | copy to      |
      +-------------+ +------------+ | _examples/   |
                                      +--------------+
```

Every cycle through this loop makes the next cycle better. Here's how each piece works.

---

## 1. `_insights.md` - the learning log

Each channel has an `_insights.md` file that captures what works and what doesn't. This is NOT a static document - it's a living log that grows with every piece of content you publish.

### What to log after publishing

```markdown
## What works
- [2025-04-15] Short hook + single emoji opener drove 2x engagement vs long intros
- [2025-04-20] Benchmark posts with specific numbers ("I tested 14 tools") get 3x more shares
- [2025-05-01] Posts published Tue-Thu 9am get 40% more impressions than Mon/Fri

## What doesn't work
- [2025-04-18] "What I learned:" sections feel generic - insights should live inside the narrative
- [2025-04-25] Numbered lists in event recaps feel corporate - prose works better
```

### How Claude uses it

Every content skill starts with: "Read `_insights.md` for this channel." Claude applies these patterns before generating. The more specific your insights, the better the output.

### Tips

- **Be specific.** "Short posts work" is useless. "Posts under 100 words with a question CTA get 2x comments" is useful.
- **Include dates.** Patterns change over time. Dated entries let you spot trends.
- **Include numbers.** "High engagement" means nothing. "47K impressions, 300 likes, 80 comments" lets Claude calibrate.
- **Log failures too.** Knowing what to avoid is as valuable as knowing what works.

---

## 2. `_examples/` - the quality bar

The `_examples/` folder contains your best-performing content pieces. These serve as few-shot examples that Claude reads before generating new content.

### What goes in `_examples/`

Only pieces that performed significantly above average. Quality over quantity - 3-5 examples per channel is plenty.

### How to curate

After a piece performs well:
1. Copy it from `_published/` to `_examples/`
2. Add a note in the frontmatter about WHY it performed well:

```yaml
---
date: 2025-04-15
channel: linkedin
topic: benchmark-analytics-agents
format: post
status: published
performance:
  impressions: 47000
  likes: 300
  comments: 80
notes: "Benchmark format + specific number in hook + contrarian verdict drove shares. Question CTA drove comments."
---
```

### How Claude uses it

Skills read `_examples/` as few-shot references. Claude matches the structure, tone, and patterns from these examples. Better examples = better first drafts.

### Tips

- **Rotate examples.** If your voice evolves, swap out old examples for recent ones.
- **Diverse formats.** Include different post types so Claude has references for any request.
- **Annotate what worked.** The `notes` field is gold - it tells Claude what to replicate.

---

## 3. Skill self-improvement - the edit diff

This is the most powerful and least obvious mechanism. When you edit Claude's draft before publishing, those edits are signal about your preferences.

### How it works

Every content skill includes a "self-improvement step" in its workflow:

> **When moving to `_published/`:**
> Scan the conversation for learnings. Diff: what did Claude draft vs. what the user kept? Which words, emojis, or structures got swapped, added, or cut? Any new pattern worth reusing?
> Add the learning to the right place in the skill file.

### Example

Claude drafts a LinkedIn post with:
```
What I learned from the conference:
1. AI agents are the future
2. Data quality matters more than model size
3. The community is incredible
```

You rewrite it to:
```
Three things hit me at the conference.

AI agents aren't coming - they're here. But the teams winning aren't the ones with the biggest models. They're the ones with the cleanest data.

And honestly? The best part wasn't the talks. It was the hallway conversations.
```

The skill captures:
```markdown
### Voice tells
- **NO "What I learned:" section.** Insights live inside the narrative.
- **NO numbered lists in recaps.** Prose with natural breaks works better.
- **"And honestly?" as a conversational aside** - breaks rhythm, sounds authentic.
```

These rules get added to the SKILL.md file, so next time Claude won't make the same mistakes.

### Tips

- **Don't just edit - explain why.** If you rewrite a section, tell Claude why: "This sounds too corporate" or "Lead with the insight, not the meta-statement."
- **Claude will ask.** Good skills prompt Claude to capture learnings after every publish cycle.
- **Review skill files quarterly.** They accumulate rules fast. Prune outdated ones.

---

## 4. `MEMORY.md` - cross-cutting learnings

While `_insights.md` is channel-specific and skill files capture voice rules, `MEMORY.md` captures cross-cutting decisions and preferences.

### What goes in MEMORY.md

- Tool/API quirks: "Notion MCP can't query database rows - use Chrome injection instead"
- Process decisions: "Email drafts go to Gmail only, never to the repo"
- Voice preferences: "Never use em-dashes in any content"
- Workflow patterns: "Always check sent folder before flagging something as overdue"

### How Claude uses it

`MEMORY.md` is auto-loaded in every session. Claude applies these rules across all tasks, not just content generation.

---

## 5. Putting it all together

### After every piece of content

1. Move from `_drafts/` to `_published/`
2. Update `_insights.md` with performance data and patterns
3. If top performer: copy to `_examples/` with annotated frontmatter
4. If Claude's draft needed significant edits: update the skill file with the pattern

### Weekly maintenance (5 minutes)

- Scan `_published/` from the past week
- Update `_insights.md` with any performance data that's now available
- Promote any standout pieces to `_examples/`

### Monthly maintenance (15 minutes)

- Review skill files - prune outdated rules, consolidate similar ones
- Review `_examples/` - rotate in recent top performers, remove stale ones
- Review `MEMORY.md` - archive resolved items, add new cross-cutting patterns

---

## The compound effect

Week 1: Claude generates generic content. You edit heavily.
Week 4: Claude matches your voice 70% of the time. You tweak details.
Week 12: Claude drafts are nearly publish-ready. You make minor adjustments.
Month 6: Claude knows your voice, your audience, your patterns, and your preferences. First drafts are as good as what you'd write yourself - sometimes better, because they consistently apply every rule you've ever taught it.

This only happens if you close the loop. The system doesn't improve by magic - it improves because you feed your edits, insights, and examples back into it.

**The 30 seconds after publishing are the most valuable 30 seconds in the entire workflow.** Don't skip them.
