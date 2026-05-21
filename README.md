# Skills

A collection of Claude Code skills I've built and use daily. Each skill is a self-contained workflow you can drop into any Claude Code project.

## What's a skill?

Skills are prompt files that tell Claude Code how to run a specific workflow when you type a slash command. They live in your project's `.claude/skills/` directory (or `~/.claude/skills/` for global use).

When you type `/writing`, Claude reads the skill file and runs the workflow end-to-end.

## Skills in this repo

| Skill | What it does |
|-------|-------------|
| [writing](./writing/) | Daily Substack writing practice — coaching brief, draft pass, publish |
| [align](./align/) | Mission and time alignment audit — are you working on the right things? |

## How to install a skill

1. Copy the skill folder into your project's `.claude/skills/` directory:
   ```
   cp -r writing /your-project/.claude/skills/writing
   ```
   Or for global use (available in all projects):
   ```
   cp -r writing ~/.claude/skills/writing
   ```

2. Follow the setup steps in the skill's `README.md`.

3. Type the slash command in Claude Code to run it.

## How these work

Each skill folder contains:
- `SKILL.md` — the main skill file Claude reads when you invoke it
- `principles.md` or other references the skill uses
- `README.md` — setup, customization, and usage notes

Skills are plain markdown. You can read and edit them directly.

## Philosophy

These are opinionated workflows built from daily use, not generic templates. They're designed to get out of the way once configured and just work. The coaching and process logic is the real value — the configuration is minimal by design.
