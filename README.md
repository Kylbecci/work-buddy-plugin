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

1. Add the marketplace (one time):
   ```
   /plugin marketplace add YOUR-GH-ORG/work-buddy
   ```
2. Install the plugin (one time):
   ```
   /plugin install work-buddy@work-buddy-marketplace
   ```
3. Run it the first time — it walks you through a ~2-minute setup (your name/role, tone, and a few questions to tailor it to your job):
   ```
   /work-buddy
   ```

> Replace `YOUR-GH-ORG/work-buddy` with the actual repo path you're given.

## Use every day

Just type:
```
/work-buddy
```
That's it — no plugin commands needed again. It'll ask if you want a morning startup or to pick up where you left off. Mention tasks, ask for a check-in, or say "wrap up" whenever you're done for the day.

---

## Integrations (optional, but recommended)

Work Buddy works fully on its own. It gets **much** more useful with these connected:

| Connect | And you get |
|---|---|
| **Microsoft 365** | Calendar meeting briefs + email triage |
| **Slack** | Message triage — surfaces what needs your attention |
| **BigQuery** | On-demand reports |

It'll point these out once during setup. Connect whichever you use; skip the rest.

---

## Updating

When a new version ships, pull it with:
```
/plugin marketplace update work-buddy-marketplace
```

## Feedback

This is a tool I built and use daily, shared as-is — still improving it. **Hit a bug or have an idea? Message Kyle Becci.** Your notes drive what gets fixed next.
