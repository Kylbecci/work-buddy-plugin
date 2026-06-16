# Work Buddy

A personal daily work assistant for Claude Code. It runs your **morning startup**, captures **tasks** as you mention them, writes **daily wrap-ups** and **weekly recaps** from what you actually worked on, preps you for **meetings**, and triages **email + Slack** — all while keeping a private running log of your work.

Think of it as a colleague who remembers everything you did and tells you what's next.

---

## What it does

- **Morning startup** — today's meetings (with context), your open tasks by priority, and a suggested focus for the day.
- **Task tracking** — drop a task in plain language ("remind me to send the report Friday") and it's tracked. One source of truth, never dropped.
- **Daily wrap-up** — a written summary of the day, pulled from your actual sessions, plus what carries to tomorrow.
- **Weekly recap** — a clean week-in-review, grouped by project.
- **Meeting prep** — what you've been working on, recent messages, and open items for any meeting.
- **Catches you up** after time off — surfaces the routines you missed while you were out.

It keeps all of this in private local files under `~/.claude/work-buddy/` on your machine. Nothing about your work leaves your computer.

---

## Requirements — where it runs

Work Buddy runs **anywhere Claude Code runs**:
- ✅ Claude Code in the **terminal (CLI)**
- ✅ Claude Code in the **VS Code extension**
- ✅ Claude Code mode **inside the Claude desktop app**

It does **not** run in the plain Claude chat app (the standalone chat with no file access) — Work Buddy needs to read and write local files, which only the Claude Code runtime provides. If you can run `/`-commands and Claude can edit files for you, you're in the right place.

---

## Set up once

**Install in the terminal, then use it anywhere.** Run the two install commands once in **Claude Code in your terminal** — you type these yourself (Claude can't run `/plugin` commands on your behalf). After that, Work Buddy is available in *every* Claude Code surface, because it installs to your user profile (`~/.claude/`).

1. Add the marketplace (one time):
   ```
   /plugin marketplace add Kylbecci/work-buddy-plugin
   ```
2. Install the plugin (one time):
   ```
   /plugin install work-buddy@work-buddy-marketplace
   ```
3. **(Recommended) Turn on auto-updates** so new versions arrive on their own: open `/plugin` → **Marketplaces** → `work-buddy-marketplace` → **Enable auto-update**. Third-party marketplaces are off by default, so this is a one-time toggle.
4. Run it the first time — it walks you through a ~2-minute setup (name/role, tone, and a few questions to tailor it to your job). Open your preferred Claude Code (terminal, VS Code, or the desktop app's **Code** section) and type:
   ```
   /work-buddy
   ```

> **If `/plugin` isn't recognized**, your Claude Code is out of date — update it (npm / Homebrew / native installer), restart, and try again.
>
> **Desktop app:** use the **Code** section. Work Buddy won't run in **Chat** or **Cowork** — those have no local file access.

### Fallback install (last resort)

If the marketplace route won't work for you, clone the repo and copy `plugins/work-buddy/skills/work-buddy` into `~/.claude/skills/work-buddy/`. This works but **loses auto-update** — you'd have to re-copy on every new version — so only use it if the marketplace install fails.

## Use every day

Just type:
```
/work-buddy
```
That's it — no plugin commands needed again. It'll ask if you want a morning startup or to pick up where you left off. Mention tasks, ask for a check-in, or say "wrap up" whenever you're done for the day.

---

## Connecting your tools

Setup walks you through connecting Work Buddy's tools — and it's worth doing all of them, since they're what make it genuinely useful:

| Connect | And you get |
|---|---|
| **Microsoft 365** | Calendar meeting briefs + email triage |
| **Slack** | Message triage — surfaces what needs your attention |
| **BigQuery** | On-demand reports |
| **`scheduled-tasks` MCP** | The engine for automatic scheduled runs — set up so it's ready whenever you turn scheduling on |

Setup helps you connect each one (you run the sign-ins/commands yourself). Core Work Buddy still runs if one is temporarily disconnected — it just uses what's available.

**Automatic scheduling.** Setup gets the pieces in place; whether you actually *turn on* scheduled runs — your morning brief, check-ins, and Friday recap firing on their own — is your call, and you can enable or change it anytime.

---

## Updating

If you enabled auto-update during setup, new versions arrive automatically at the next launch (you'll get a quick `/reload-plugins` prompt). To pull updates manually instead:
```
/plugin marketplace update work-buddy-marketplace
```

## Feedback

This is a tool I built and use daily, shared as-is — still improving it. **Hit a bug or have an idea? Message Kyle Becci.** Your notes drive what gets fixed next.
