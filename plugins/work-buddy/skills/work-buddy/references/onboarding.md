# Onboarding — First Run

Loaded only when no config exists at `~/.claude/work-buddy/config.md`. The goal: get the user set up quickly **and** leave them knowing how to make the assistant genuinely theirs. Keep it conversational and skippable — never a wall — but don't skip the personalization, because that's what makes Work Buddy useful instead of generic.

---

## Steps

1. **Greet + frame (one line):** "I'm your Work Buddy — a daily assistant that keeps you organized: morning briefings, task tracking, wrap-ups, weekly recaps, meeting prep. Let's take ~2 minutes to set me up for how *you* work."

2. **Essentials (quick):**
   - **Name + role** — one line each.
   - **Tone** — `concise`, `conversational`, or `detailed`.

3. **Auto-detect, don't ask:**
   - **Transcript paths** — all Claude Code surfaces (CLI, VS Code, desktop "Code" tab) write standard JSONL to `~/.claude/projects/` in per-working-directory subfolders, so there's nothing per-surface to resolve. Confirm `~/.claude/projects/` exists (it will, if they've used Claude Code) and write the single all-folders glob `~/.claude/projects/**/*.jsonl` to config's *Transcript root*. The field is informational — transcript reading scans all `~/.claude/projects/` subfolders by mod time regardless (see `references/transcript-reading.md`). Do **not** chase the desktop app's app-data dir (`Claude_*`) — that store is metadata-only, not the transcript.
   - **Context MDs** — scan `~/.claude/projects/` for `*-context.md`; if any, ask "are these your active projects?" and save the confirmed ones.

4. **Personalize to their work (invited, skippable — but ask).** This is the high-value part. Walk through these conversationally; let them answer what's easy and skip the rest with "we can add more anytime":
   - **Weekly routines** — "Anything you do on a regular cadence? A report every Monday, a standing meeting, a Friday submission? I'll remind you on the right days and plan around them."
   - **Key contacts** — "Who do you work with most, and on what? That helps me prep you for meetings and surface the right messages in triage."
   - **Recurring workflows** — "Any multi-step process you repeat — a month-end close, a pipeline you run? Tell me the steps and I can walk you through it next time."
   Write their answers into the matching `context.md` sections. Whatever they skip stays blank for later.

5. **Create the data directory + files** at `~/.claude/work-buddy/`:
   - Subfolders: `logs/`, `recaps/`, `meetings/`
   - Copy templates: `config.md`, `context.md`, `triage-heuristics.md`, `tasks.md` (from `references/*-template.md`), filled in with the user's answers.

6. **Connect the tools (MCP setup) — standard, walk through all of them:** Set up every MCP Work Buddy uses — **Microsoft 365**, **Slack**, and **BigQuery** — per `references/mcp-setup.md`. This is not pick-and-choose: connecting them is what makes Work Buddy fully useful. Frame it as part of getting set up, not optional. The user runs the commands/authentications themselves (the assistant can't). Detect what's already connected and only walk through what's missing.

7. **Permissions (standard setup — not optional):** add allow-list rules so routine tool calls don't prompt for approval every time. This is core to Work Buddy not constantly interrupting the user — run it as a normal part of setup, not a skippable extra. Per `references/permissions-setup.md` (it detects the user's connected connector IDs, shows the exact rules, and confirms before writing to `settings.json` — that confirmation is the only gate). Note the outcome for the Setup markers (step 10).

8. **Orient + README (one line per file):** "I've set up four files you never edit directly — just talk to me and I keep them current: **config** (settings), **context** (teaches me your job), **triage-heuristics** (email/Slack filters), **tasks** (your open items). The README has the full tour if you want it."

9. **Next steps — how to make it yours (leave them with this).** Personalization is ongoing, not a one-time form. Give concrete examples they can say anytime:
   - *"Add to my weekly routine: I run the inventory report every Tuesday."*
   - *"I work with Sarah on pricing."*
   - *"Here's how I do month-end close: first… then…"*
   - *"Track this project doc for me: [path]."*
   - *"Stop surfacing emails from X" / "always show me messages about Y."*
   "The more you tell me about your role and how you work, the better I get. Don't worry about getting it all in now — just mention things as they come up."

10. **Record setup state:** write the **## Setup** markers in `config.md` (per `references/setup-migration.md`) — `Setup version` = current, `Completed modules` = every module just run, `Declined modules` = any the user skipped. This is what lets features added later be offered to this user without redoing setup.

11. **Done:** "You're set. Type `/work-buddy` any morning and I'll have your day ready."

---

## Notes
- The data directory (`~/.claude/work-buddy/`) is the user's private, per-machine state. The skill never ships or reads another user's data.
- If a user re-runs onboarding when a config already exists, don't clobber — ask before overwriting.
- Onboarding triggers strictly on the **absence of `~/.claude/work-buddy/config.md`**. Do not adopt a differently-named nearby folder (e.g. `work-buddy-backup`) as the data dir — if `config.md` isn't at the canonical path, onboard fresh.
- **Onboarding is interactive.** Even if you can infer details from the user's broader Claude context (global memory, CLAUDE.md), still *ask* name/role/tone and the personalization questions, and let the user confirm what gets stored. Don't silently auto-fill the whole context from ambient knowledge — the user should know what their assistant knows.
