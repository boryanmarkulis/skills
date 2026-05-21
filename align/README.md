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

No arguments. The skill reads the files and returns the report. By default it does not save the output. To save: ask it to save and it will write to `audits/align-YYYY-MM-DD.md`.

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

The mission check reads your mission from `CLAUDE.md`. Make sure your `CLAUDE.md` has a clear mission statement — the skill uses it as the lens to classify all activity.

If you don't use `CLAUDE.md`, update the skill to read your mission from wherever you keep it.

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

The skill checks `sources/substack/state.json` for last publish date and scans `sources/substack/` for recent post topics. If you use a different platform (Ghost, WordPress, a local draft folder), update those paths.

If you don't write publicly, remove the writing section entirely.

### 4. Paid work reference

The Profit vs Build Balance section reads your primary paid engagement from `me/work.md`. Make sure that file names your current revenue engagement clearly so the skill can identify it.

If you have multiple revenue streams or work differently (product vs. freelance, for example), adjust the section to reflect the balance that matters to you.

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
Mission: help independent businesses compete with larger chains.
4 major activity clusters in last 14 days:
- On-mission: 2 (client dashboard, onboarding flow)
- Infrastructure: 1 (internal tooling)
- Off-thesis: 1 (side project — not related to core mission)

### Priority Alignment
| # | Priority | Touches (14d) | Status |
|---|----------|---------------|--------|
| 1 | Client delivery — Phase 2 | 1 day, 0 commits | Warm |
| 2 | Product v2 feature | 5 days, 3 commits | Active |
| 3 | Sales outreach | 0 days, 0 commits | Cold |

### Drift Signals
- Priorities with zero touches: Sales outreach
- Unlogged work that grew: Internal tooling (4 sessions, no priority entry)
- Active projects off the priority list: none
- Paused items still showing up: none

### Writing
Last published: 2026-05-14 (7 days ago) — Warm
Posts in last 14d: 1 — On-mission: 1, Adjacent: 0, Off-thesis: 0

### Profit vs Build Balance
Client retainer: 6 activity items
Unpaid builds: 11 activity items
Ratio: Inverted
Unpaid work is nearly 2x paid work in activity volume — rebalance toward client delivery.

### Top 3 Realignment Moves
1. Open sales outreach list and send one message today — it's been cold for 14 days
2. Cap internal tooling to one session this week, redirect the rest to client work
3. Log or drop the side project — it's off-thesis and taking up untracked attention
```

---

## Source

Part of [boryanmarkulis/skills](https://github.com/boryanmarkulis/skills) — Claude Code skills for daily workflows.
