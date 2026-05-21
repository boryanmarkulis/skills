# /align — Mission & Time Alignment Audit

A read-only diagnostic skill for Claude Code that tells you whether your work this week actually matches what you said matters. One command, one honest report.

---

## What it does

`/align` reads your intent layer (priorities, goals, mission) and your activity layer (daily logs, git history, project files), then produces a structured report answering one question: are you spending your time on the right things?

Output includes:

- **Verdict** — Aligned / Drifting / Off-track with a one-sentence reason
- **Mission check** — activity clusters classified as on-mission, infrastructure, or off-thesis
- **Priority alignment table** — each priority marked Active / Warm / Cold based on 14-day touches
- **Drift signals** — cold priorities, unlogged work that grew, active projects off the list
- **Writing consistency check** — last publish date and post topic classification
- **Profit vs build balance** — paid work vs. unpaid exploration
- **Top 3 realignment moves** — specific actions, not vague suggestions

---

## How to use

```
/align
```

That's it. No arguments. The skill reads the files and returns the report. By default it does not save the output. To save: ask it to save and it will write to `audits/align-YYYY-MM-DD.md`.

---

## Installation

1. Copy `SKILL.md` into your skills directory:
   - Claude Code: `.claude/skills/align/SKILL.md`
   - Agents: `.agents/skills/align/SKILL.md`

2. Adapt the skill for your environment (see below).

3. Run `/align`.

---

## Adapting to your workflow

The skill is written for a specific brain structure. You will need to update five things:

### 1. Mission statement

In the Analysis Steps section, the mission check hardcodes:

```
Mission: accelerate clean energy adoption on planet Earth.
```

Replace with your own mission. This is the lens through which all activity gets classified.

### 2. File paths

The skill reads from these files by default:

| File | What it is |
|------|------------|
| `me/priorities.md` | Your current priority list |
| `me/goals.md` | Milestones and paused items |
| `me/me.md` | Identity and context |
| `me/work.md` | Revenue, strategy, tools |
| `areas/favorite-problems.md` | Mission filter questions |
| `decisions/log.md` | Direction change log |
| `daily/` | Daily entries (last 14 days) |
| `log.md` | Session log |
| `network/log.md` | Network/outreach log |
| `projects/*/README.md` | Active project status |

Rename these to match your own folder structure. If you don't have some of these, remove or replace those sections in the skill.

### 3. Writing platform

The skill checks `sources/substack/state.json` for last publish date and scans `sources/substack/` for recent post topics. If you use a different platform (Ghost, WordPress, a local draft folder), update those paths and remove the Substack-specific logic.

If you don't write publicly, remove the Substack section entirely.

### 4. Paid work reference

The Profit vs Build Balance section hardcodes:

```
SET (paid $3k/mo): {X activity items}
```

Replace `SET` and `$3k/mo` with your own engagement name and rate. If you have multiple revenue streams, list them. If you're not freelancing, reframe this section as whatever balance matters to you (e.g., client work vs. product, revenue vs. growth).

### 5. Activity window

The skill uses a 14-day window for activity and 60-day window for decisions. Adjust these if your work moves faster or slower.

---

## Design principles

- **Read-only by default.** The only write operation is the optional saved report. The skill never edits your files.
- **Honest, not reassuring.** If something is cold, it says it's cold. The point is to catch drift early.
- **Dense output.** One table, one list, one verdict. No padding.
- **No external dependencies.** Just file reads and a git log command.

---

## Example output

```
## Alignment Audit — 2026-05-21

**Verdict: Drifting**
Top priority is cold; two unpaid builds running hot while paid work is warm.

### Mission Check
Mission: accelerate clean energy adoption on planet Earth.
4 major activity clusters in last 14 days:
- On-mission: 2 (Sunny WhatsApp bot, SET CRM rollout)
- Infrastructure: 1 (Brain compile system)
- Off-thesis: 1 (Time Back form — consulting, not energy)

### Priority Alignment
| # | Priority | Touches (14d) | Status |
|---|----------|---------------|--------|
| 1 | SET Month 2 delivery | 1 day, 0 commits | Warm |
| 2 | Sunny V10 | 5 days, 3 commits | Active |
| 3 | Sol V1 | 0 days, 0 commits | Cold |

### Drift Signals
- Priorities with zero touches: Sol V1
- Unlogged work that grew: Brain improvements (4 sessions, no priority entry)
- Active projects off the priority list: none
- Paused items still showing up: none

### Substack
Last published: 2026-05-14 (7 days ago) — Warm
Posts in last 14d: 1 — On-mission: 1, Adjacent: 0, Off-thesis: 0

### Profit vs Build Balance
SET (paid $3k/mo): 6 activity items
Unpaid builds: 11 activity items
Ratio: Inverted
Unpaid work is nearly 2x paid work in activity volume — rebalance toward SET delivery.

### Top 3 Realignment Moves
1. Open Sol V1 plan and do one concrete step today — it's been cold for 14 days
2. Shift 2 Brain sessions this week to SET CRM adoption work
3. Write the Time Back handoff and step back — it's off-thesis and leaking attention
```

---

## Source

Part of [boryanmarkulis/brain](https://github.com/boryanmarkulis/brain) — a Claude Code-based second brain for founders building in the clean energy space.
