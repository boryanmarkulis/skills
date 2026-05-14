---
name: writing
description: Daily Substack writing practice. /writing runs end-to-end — brief with quote-based coaching, write, grammar pass, publish.
allowed-tools: Read Write Edit Glob mcp__substack__get_posts mcp__substack__get_post mcp__substack__get_post_stats mcp__substack__get_subscriber_counts mcp__substack__publish_post
---

Daily writing practice for your Substack. One command, one chat flow — brief → write → grammar → publish. Persistent coaching log with quote-based feedback, pattern detection across sessions, and a forward-applied experiment for each new post.

## Setup check

Before running the full flow, check if `.claude/skills/writing/config.md` exists. If it doesn't, run the onboarding flow (see [Onboarding](#onboarding) below) before anything else.

If config exists, read it at the start of every session. It is the source of truth for the Sparks context and any personal writing goals.

---

## Onboarding

Run this once when `config.md` is missing. Walk the user through it conversationally — ask one or two questions at a time, not a form dump.

Ask:
1. What's your Substack URL? (Used to confirm publishing target — the MCP handles the connection, but good to note for the log.)
2. What are you writing about right now? What topics, ideas, or obsessions are you in? (This becomes the Sparks context — a few sentences is enough.)
3. Any personal writing goals or things you want to get better at? (Optional — feeds the coaching layer.)
4. Where do you want posts saved locally? Default is `sources/substack/` — confirm or change.

Once you have answers, write `.claude/skills/writing/config.md` in this format:

```markdown
# Writing Config

Substack: [URL]

## Sparks context
[What the user is thinking about, working on, obsessed with — raw material for post angles]

## Writing goals
[Specific things they want to improve — optional]

## Local posts directory
sources/substack/
```

Tell them: "Config saved. You're set up. Run `/writing` tomorrow and the full ritual will kick in. Today you can run it too — the first session won't have a coaching entry to work from yet, so I'll skip the pattern detection."

---

## When to use

Run `/writing` once a day. The skill handles the full ritual end-to-end. No subcommands.

---

## Process — Phase A: Brief

### Step 1: Read state and inputs

Read `sources/substack/state.json` (or the configured local posts directory + `/state.json`). If missing, create with `{"session_count": 0, "last_session_date": null}`.

Read `sources/substack/coaching.md`. If missing, this is the first run — note the empty state, behavior is described in Step 5.

Read `.claude/skills/writing/config.md` for Sparks context and writing goals.

Read `.claude/skills/writing/principles.md` — this is the coaching reference for all Strong/Weak analysis, pattern detection, and experiments. Every coaching judgment must be anchored in a named principle from this file.

### Step 2: Identify last published post

`get_posts(limit=1)` to get the most recent post ID, title, URL, date. Match the date + slug to a local file in the configured posts directory (`sources/substack/<YYYY-MM-DD>-<slug>.md`). If no local copy exists yet, fall back to `get_post(post_id)` and save the result locally so future briefs stay token-light.

### Step 3: Idempotent coaching write

Check `coaching.md` for an existing entry matching the last published post slug (look for `## YYYY-MM-DD — "post-title-slug"` heading). If an entry already exists, skip generation — re-running `/writing` the same day is a no-op for coaching.

If no entry exists, generate one:

1. Read the last 7 entries from `coaching.md` (or fewer if log is shorter).
2. Read the body of the last published post locally.
3. Pull `get_post_stats(post_id)` for the last post — informs the analysis silently, only mention numbers if genuinely noteworthy.
4. Identify:
   - **Strong**: one exact quote from the post that worked, with a named principle from `principles.md` explaining why. Format: `"[quote]" [Principle name — Author]`. If no principle fits exactly, name the observation directly and skip the bracket.
   - **Weak**: one exact quote that didn't work, with a named principle from `principles.md` explaining why. Same format. Do not invent a principle — if nothing fits, describe the failure plainly.
   - **Pattern**: if strong/weak echoes recurring patterns in the last 7 entries (3+ hits in 7 = real pattern), name the pattern and the principle(s) driving it. Otherwise leave a one-line observation, principle-cited if possible.
   - **Experiment from last post**: look up the previous entry's `Experiment for next post` line. Evaluate the now-published post against it. Mark `hit / missed / partial` and quote evidence. Skip this line on first run only.
   - **Experiment for next post**: one specific instruction tied to a named principle from `principles.md`. Not general advice — a concrete action with the principle named. Format: `Apply [Principle name — Author]: [specific instruction].`
5. Append to `coaching.md` in this exact format:

```
## YYYY-MM-DD — "post-title-slug"

Strong: "[exact quote]" [Principle name — Author] — [why it landed]
Weak: "[exact quote]" [Principle name — Author] — [why it didn't]
Pattern: [pattern across recent entries, or single-line observation] [Principle name(s) if applicable]
Experiment from last post: [what was set] -> [hit / missed / partial] — [evidence quote]
Experiment for next post: Apply [Principle name — Author]: [specific instruction]
```

Order matters. `Experiment from last post` (accountability check) before `Experiment for next post` (forward dare).

### Step 4: Trend overview (only when due)

Trend fires only when `session_count > 0 && session_count % 14 == 0`. Otherwise skip this step.

When firing:
1. Read the last 14 posts from the local posts directory.
2. Read the last 14 entries from `coaching.md`.
3. `get_post_stats(post_id)` for each of the 14 (parallel calls).
4. Output six sections:

```
--- Bi-weekly Trend (last 14 posts) ---

Voice: [what's consistent across the 14, what's drifting. Reference specific lines. Cite principles from principles.md where the data supports it.]

Topics: [what you keep returning to, what's missing or under-explored.]

Openers/Closers: [which hook patterns land, which fall flat. Same for endings. Tie to principles where applicable.]

Stats signal:
  Top: "[title]" (X%), "[title]" (Y%), "[title]" (Z%)
  Bottom: "[title]" (X%), "[title]" (Y%), "[title]" (Z%)
  Pattern: [what the top 3 share that the bottom 3 don't — cite principles where the data points clearly.]

Coaching arc: [which principles from principles.md have improved over 14 sessions, which keep recurring as weaknesses, what to commit to for next 14]

Experiment: [one specific thing to try over the next 14 posts, tied to a named principle from principles.md]
```

### Step 5: First-run handling

If `coaching.md` was missing at start of session:
- Create `coaching.md` with the single entry generated for the last post.
- On the entry, skip pattern detection and skip the `Experiment from last post` line (no prior experiment exists).
- In the brief, prepend this notice:
  ```
  Coaching log starting today — no prior entries, so no pattern detection or previous-experiment check yet.
  ```

### Step 6: Output the brief

Format:

```
[trend block, only if firing]

Last post: "[title]" ([N] days ago)
Strong: "[quote]" — [why]
Weak: "[quote]" — [why]
Pattern: [...]
Experiment from last post: [...] → [hit / missed / partial]

Today's experiment: [the new experiment instruction from the just-written entry]

Sparks:
1. [Story angle] — "What happened when..." [pull from config context]
2. [Reflection angle] — "What did you realize about..." [internal shift]
3. [Tension angle] — "What's the thing you haven't said yet about..." [friction]
```

No total length cap on the brief. Each section has its own one-line-or-so constraint. Specificity over brevity.

Then wait for the user to write and paste the draft.

---

## Process — Phase B: Grammar pass

When the user pastes the draft, parse the format:

1. **Title parsing**:
   - Primary: `Title: xyz\nBody: xyz` — split on those keys.
   - Fallback: markdown H1 at top of paste — treat as title, strip from body.
   - Last resort: ask "Title?" once.

2. **Grammar fixes** — auto-fix only:
   - Typos
   - Grammar mistakes
   - Punctuation errors

   Do NOT touch: word choice, phrasing, sentence structure, voice, or anything stylistic.

3. **Output format**:

   ```
   Fixed: [typo 'recieve'→'receive', missing comma para 2, ...]

   [cleaned draft body]
   ```

   If nothing to fix: `clean, ready to post` then show the draft.

4. **Major-issue flag** — only if a post-breaking issue exists, surface it separately. Tight definition:
   - Sentence that doesn't parse / missing word that breaks meaning
   - Factual contradiction or a claim that's clearly wrong
   - Something that reads as the opposite of what was meant

   Explicitly NOT flagged: voice drift, weak ending, "could be tighter," redundancy, repetition. Those are coaching — they go in tomorrow's `coaching.md` write, not today's draft pass.

   Output format if flagging:
   ```
   Major issue (paragraph N): "[quote]" — [what's broken]
   ```

---

## Process — Phase C: Confirm and publish

1. Show:
   ```
   Title: [title]
   First line: [first line of body]

   Good to go?
   ```

2. On explicit yes (require actual confirmation, don't auto-parse): call `publish_post(title, markdown_content)` with the cleaned body.

3. On publish success:
   - Save local copy to the configured posts directory as `<YYYY-MM-DD>-<slug>.md` with a `<!-- Source: <url> -->` header line.
   - Increment `session_count` in `state.json`. Update `last_session_date` to today.
   - Return the live URL.

4. On publish failure:
   - Do NOT save local copy.
   - Do NOT increment counter.
   - Surface the error.

---

## Constraints

- Never publish without explicit confirmation.
- Never return a stylistically-rewritten draft. Grammar-only auto-fix is the only exception.
- Quote-based coaching always uses exact quotes — no paraphrasing.
- `coaching.md` is append-only — never edit past entries.
- Coaching entry generation is idempotent: skip if entry for last published post already exists.
- Counter increments only on successful publish. Abandoned sessions don't count toward the bi-weekly trend.
