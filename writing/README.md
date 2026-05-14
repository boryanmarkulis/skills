# writing skill

A daily Substack writing ritual for Claude Code. One command — `/writing` — runs the full flow: coaching brief from your last post, draft pass, grammar check, publish.

Built on the writing frameworks of Rudolf Flesch, Verlyn Klinkenborg, and Stephen King.

## What it does

**Phase A — Brief**
Pulls your last published post, runs a coaching analysis against named writing principles, checks how your last experiment went, sets a new one, and generates three Sparks (story, reflection, tension angles) to write from.

**Phase B — Grammar pass**
When you paste your draft, it fixes typos, grammar, and punctuation only. Doesn't touch your voice or phrasing. Flags anything that actually breaks the meaning.

**Phase C — Publish**
Confirms title + first line, asks for explicit approval, publishes via the Substack MCP, saves a local copy, and returns the live URL.

Every 14 sessions it runs a bi-weekly trend review across your last 14 posts and coaching entries.

## Requirements

- Claude Code
- [Substack MCP](https://github.com/jakobhoeg/substack-mcp) installed and configured (handles auth to your Substack)

## Setup

**1. Install the skill**

Copy the `writing` folder into your Claude Code project:
```
cp -r writing /your-project/.claude/skills/writing
```

Or globally (available in all projects):
```
cp -r writing ~/.claude/skills/writing
```

**2. First run**

Type `/writing` in Claude Code. The skill will detect that `config.md` is missing and walk you through a short setup — a few questions about what you're writing about and any goals you have. Takes about two minutes.

It creates `.claude/skills/writing/config.md` which is your personal context file. Edit it anytime to update your Sparks context as your focus shifts.

**3. Every day after that**

Just type `/writing`. The full ritual runs automatically.

## Files the skill creates

| File | What it is |
|------|-----------|
| `.claude/skills/writing/config.md` | Your personal context (topics, goals) — edit freely |
| `sources/substack/coaching.md` | Append-only coaching log, one entry per post |
| `sources/substack/state.json` | Session counter and last-run date |
| `sources/substack/<date>-<slug>.md` | Local copy of each published post |

The `sources/substack/` path is the default — you can change it during setup.

## Customizing the principles

`principles.md` is the coaching reference. It ships with principles from Flesch, Klinkenborg, and King. You can add your own:

```markdown
## Your principle set name -- Author

- **Principle name** -- Description of the principle and when it applies.
```

The coaching layer will pick them up automatically as long as you follow the same format.

## How the coaching works

After each post, the skill:
1. Pulls one Strong quote (something that worked, named principle, why it landed)
2. Pulls one Weak quote (something that didn't, named principle, why it failed)
3. Detects patterns across the last 7 entries (3+ hits = real pattern)
4. Checks how the last session's experiment played out in the published post
5. Sets a new experiment for the next post — one concrete instruction tied to a named principle

The experiment system is the core habit loop. It's not general advice — it's a specific thing to try in the next post, with accountability on the session after that.

## What it won't do

- Rewrite your draft. Grammar fixes only.
- Publish without your explicit confirmation.
- Edit past coaching entries (append-only by design).
- Invent principles that aren't in `principles.md`.
