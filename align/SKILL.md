---
name: align
description: Strategic alignment audit. Checks whether your actual work in the last 14 days matches your stated mission, priorities, and direction. Flags drift, cold priorities, and profit/build imbalance.
triggers:
  - /align
  - "am I on track"
  - "am I aligned"
  - "check alignment"
  - "mission check"
  - "am I drifting"
allowed-tools: Read Bash Glob
---

# /align — Mission & Time Alignment Audit

Read-only diagnostic. Never edit files. The only writable side-effect is the optional saved report.

## What to Read

Gather these before producing output. Never write to them.

**Intent layer** (what should be happening):
- `CLAUDE.md` — mission, identity, active work, current thread list
- `me/priorities.md` — current prioritized list
- `me/goals.md` — milestones, paused items
- `me/me.md` — identity and context
- `me/work.md` — revenue, strategy, tools
- `areas/favorite-problems.md` — mission filter questions
- `decisions/log.md` — last 60 days of direction changes

**Activity layer** (what actually happened):
- `daily/` — last 14 days of daily entries (read each file)
- `log.md` — last 50 lines
- `network/log.md` — last 30 lines
- `projects/*/README.md` — all active project READMEs
- Run: `git log --since="14 days ago" --oneline` — commit signal
- `sources/substack/state.json` — last session date and session count (writing consistency)
- `sources/substack/` — posts dated within the last 14 days (read each file for topic/theme)

---

## Analysis Steps

### 1. Build the priority list

From `me/priorities.md`, extract each numbered priority. This is the ground truth for what should be getting attention.

### 2. Build the activity signal

Scan all daily files, `log.md` tail, and git commits. For each priority, count:
- Daily file mentions (how many days had an entry touching it)
- Commit count touching it
- Whether any session log entries reference it

Classify each priority:
- **Active** — touched on 3+ of last 14 days
- **Warm** — touched 1–2 days
- **Cold** — zero touches in 14 days

### 3. Mission check

For each major activity cluster identified from the activity layer, ask: does it advance the mission stated in `CLAUDE.md`? Group into:
- **On-mission** — directly advances the stated mission or the tools/systems that do
- **Infrastructure** — builds capability that enables the mission (acceptable)
- **Off-thesis** — neither

### 4. Drift signals

Check each of these:
- Priorities with zero touches in 14 days (cold priorities)
- Work that appears frequently in activity but is absent from `me/priorities.md` — was there a decision logged for it?
- Projects in `projects/*/README.md` that have recent activity but are not in the priority list
- Items in `me/goals.md` marked paused that still appear in activity

### 5. Writing check

From `sources/substack/state.json`, check `last_session_date`. If more than 7 days ago, flag as cold.

From the dated post files in `sources/substack/` for the last 14 days, read each and identify the theme/topic. Classify each post:
- **On-mission** — directly related to the stated mission
- **Adjacent** — founder life, productivity, mindset (builds the brand that serves the mission)
- **Off-thesis** — neither

Note: writing is a distribution channel and brand asset for the mission, so Adjacent counts as acceptable. Off-thesis is the flag.

### 6. Profit vs build balance

From `me/work.md`, identify the primary paid engagement. Count activity items tied to paid work vs. unpaid tool-building or exploration.

Classify:
- **Healthy** — paid work has equal or more activity than unpaid builds
- **Inverted** — unpaid work dominates; revenue-generating work is cold

### 7. Verdict

Based on the above, assign one overall verdict:
- **Aligned** — top priorities are active, no major drift, paid work is healthy
- **Drifting** — 1–2 priorities cold, or mild off-thesis activity building up
- **Off-track** — multiple cold priorities, profit/build inverted, or significant off-thesis work

---

## Output Format

Produce this exactly. Fill in real values from your analysis.

```
## Alignment Audit — {YYYY-MM-DD}

**Verdict: {Aligned | Drifting | Off-track}**
{One sentence explaining why.}

### Mission Check
Mission: {mission from CLAUDE.md}
{N} major activity clusters in last 14 days:
- On-mission: {M} ({list them})
- Infrastructure: {K} ({list them})
- Off-thesis: {O} ({list them or "none"})

### Priority Alignment
| # | Priority | Touches (14d) | Status |
|---|----------|---------------|--------|
| 1 | {priority name} | {X days, Y commits} | Active/Warm/Cold |
| 2 | ...
| ...

### Drift Signals
- Priorities with zero touches: {list or "none"}
- Unlogged work that grew: {list or "none"}
- Active projects off the priority list: {list or "none"}
- Paused items still showing up: {list or "none"}

### Writing
Last published: {YYYY-MM-DD} ({X} days ago) — {Active (<7d) | Warm (7-14d) | Cold (14d+)}
Posts in last 14d: {N} — On-mission: {M}, Adjacent: {K}, Off-thesis: {O}
{One line only if cold or off-thesis.}

### Profit vs Build Balance
{Paid engagement name}: {X activity items}
Unpaid builds: {Y activity items}
Ratio: {Healthy | Inverted}
{One line if inverted.}

### Top 3 Realignment Moves
1. {Specific action — what to start, stop, or re-prioritize}
2. {Specific action}
3. {Specific action}
```

---

## Tone

Honest, not reassuring. If something is cold, say it's cold. If profit/build is inverted, say so. The point is to catch drift early, not to feel good.

No emojis. No em dashes. Dense output.

---

## Save Report (optional)

If asked to save, write to `audits/align-{YYYY-MM-DD}.md`. Create the `audits/` folder if needed. Never save unless asked.
