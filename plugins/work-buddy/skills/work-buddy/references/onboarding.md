# Onboarding — First Run

Loaded only when no config exists at `~/.claude/work-buddy/config.md`. The goal: get the user set up quickly **and** leave them knowing how to make the assistant genuinely theirs. Keep it conversational and skippable — never a wall — but don't skip the personalization, because that's what makes Work Buddy useful instead of generic.

---

## Steps

1. **Greet + frame (one line):** "I'm your Work Buddy — a daily assistant that keeps you organized: morning briefings, task tracking, wrap-ups, weekly recaps, meeting prep. Let's take ~2 minutes to set me up for how *you* work."

2. **Essentials (quick):**
   - **Name + role** — one line each.
   - **Tone** — `concise`, `conversational`, or `detailed`.

3. **Auto-detect, don't ask:**
   - **Transcript paths** — glob the CLI path (`~/.claude/projects/…`) and enumerate the desktop-app location (Windows: `AppData\Local\Packages\Claude_*`; mac: `~/Library/Application Support/Claude/claude-code-sessions`; linux: `~/.config/Claude/claude-code-sessions`) to resolve the package ID automatically. Write what's found to config. **Only ask if detection fails** — then hand the user the exact `ls`/search command, not prose.
   - **Context MDs** — scan `~/.claude/projects/` for `*-context.md`; if any, ask "are these your active projects?" and save the confirmed ones.

4. **Personalize to their work (invited, skippable — but ask).** This is the high-value part. Walk through these conversationally; let them answer what's easy and skip the rest with "we can add more anytime":
   - **Weekly routines** — "Anything you do on a regular cadence? A report every Monday, a standing meeting, a Friday submission? I'll remind you on the right days and plan around them."
   - **Key contacts** — "Who do you work with most, and on what? That helps me prep you for meetings and surface the right messages in triage."
   - **Recurring workflows** — "Any multi-step process you repeat — a month-end close, a pipeline you run? Tell me the steps and I can walk you through it next time."
   Write their answers into the matching `context.md` sections. Whatever they skip stays blank for later.

5. **Create the data directory + files** at `~/.claude/work-buddy/`:
   - Subfolders: `logs/`, `recaps/`, `meetings/`
   - Copy templates: `config.md`, `context.md`, `triage-heuristics.md`, `tasks.md` (from `references/*-template.md`), filled in with the user's answers.

6. **MCP check — value-framed, mention once:** Detect which of the three integrations are connected (Microsoft 365, Slack, BigQuery). Then: "Work Buddy works fine without these, but it's **most useful** with them connected — **Microsoft 365** gives calendar briefs + email triage, **Slack** gives message triage, **BigQuery** lets me run reports. Want help connecting any?" If they decline, move on — do **not** nag in future sessions; only raise it again if they invoke a feature that needs one.

7. **Orient + README (one line per file):** "I've set up four files you never edit directly — just talk to me and I keep them current: **config** (settings), **context** (teaches me your job), **triage-heuristics** (email/Slack filters), **tasks** (your open items). The README has the full tour if you want it."

8. **Next steps — how to make it yours (leave them with this).** Personalization is ongoing, not a one-time form. Give concrete examples they can say anytime:
   - *"Add to my weekly routine: I run the inventory report every Tuesday."*
   - *"I work with Sarah on pricing."*
   - *"Here's how I do month-end close: first… then…"*
   - *"Track this project doc for me: [path]."*
   - *"Stop surfacing emails from X" / "always show me messages about Y."*
   "The more you tell me about your role and how you work, the better I get. Don't worry about getting it all in now — just mention things as they come up."

9. **Done:** "You're set. Type `/work-buddy` any morning and I'll have your day ready."

---

## Notes
- The data directory (`~/.claude/work-buddy/`) is the user's private, per-machine state. The skill never ships or reads another user's data.
- If a user re-runs onboarding when a config already exists, don't clobber — ask before overwriting.
- Onboarding triggers strictly on the **absence of `~/.claude/work-buddy/config.md`**. Do not adopt a differently-named nearby folder (e.g. `work-buddy-backup`) as the data dir — if `config.md` isn't at the canonical path, onboard fresh.
- **Onboarding is interactive.** Even if you can infer details from the user's broader Claude context (global memory, CLAUDE.md), still *ask* name/role/tone and the personalization questions, and let the user confirm what gets stored. Don't silently auto-fill the whole context from ambient knowledge — the user should know what their assistant knows.
