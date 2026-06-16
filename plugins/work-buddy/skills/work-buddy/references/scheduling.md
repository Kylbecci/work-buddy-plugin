# Scheduling — autonomous Work Buddy runs (push, not pull)

Lets Work Buddy fire its routines automatically on a schedule instead of only when the user types `/work-buddy`. Opt-in, per-user, fully configurable. This is the **`scheduling`** setup module (setup version 3).

> **Prerequisites — both are set up during standard setup, so they should already be in place:**
> 1. **The `scheduled-tasks` MCP** (the engine for scheduled runs) — connected as part of `mcp-setup` (`references/mcp-setup.md`). If it's somehow missing, set it up first; don't enable a schedule without it.
> 2. **The `permissions` module** (`references/permissions-setup.md`) — scheduled runs are non-interactive and stall on permission prompts without the allow-list rules. If not done, run it first.
>
> This module is the one place the user genuinely **chooses** — whether to enable autonomous runs and which routines fire when. The dependencies above are set up regardless (so a later "turn it on" just works); only the *enabling* is the user's call.

---

## Mechanism

Use the **local scheduling tool on the user's machine** — the `scheduled-tasks` MCP (`create_scheduled_task` / `list_scheduled_tasks` / `update_scheduled_task`), which stores each task under `~/.claude/scheduled-tasks/{taskId}/`.

This is **local, NOT the cloud `/schedule` routine** — cloud runs execute on remote infrastructure and can't reach the user's local `~/.claude/work-buddy/` files or their interactively-authed connectors, so they're the wrong fit. (Verified decision; see work-buddy-improvements.md.)

**Gotchas to honor:**
- Cron is evaluated in the user's **local timezone**.
- Tasks run **only while the Claude app is open**; a missed run fires once at the next launch.
- Expect **~3–9 min jitter** (a 9:00 task may fire ~9:09).
- **Verify a task by its `cronExpression`, not the human-readable label** — labels can mis-render (e.g. `1-4` shown as "Monday only").

---

## The default schedule (offer these — all editable)

| Task | Cron | Fires | Runs |
|---|---|---|---|
| `wb-morning` | `0 9 * * 1-5` | weekdays 9:00a | Morning Startup (Monday auto-includes the Weekly Outline) |
| `wb-midday` | `0 12 * * 1-5` | weekdays 12:00p | Midday Check-In |
| `wb-urgent` | `0 16 * * 1-4` | **Mon–Thu** 4:00p | lightweight urgent triage sweep |
| `wb-friday-recap` | `0 16 * * 5` | Fri 4:00p | Daily Wrap-Up + Weekly Recap |

Urgent is **Mon–Thu** so it doesn't double-fire with the Friday recap; Monday's overview folds into the morning run. These are **defaults** — let the user change times, change days, or drop any of them.

---

## Cadence lives in context.md (per-user)

Store the user's chosen schedule in `context.md` under **## Scheduled Runs** (auto-managed) — which tasks are enabled and each cron — so it's personalized, visible, and editable. The actual scheduled tasks are kept in sync with this section. When the user says "move my morning brief to 8" or "turn off the midday check-in," update **both** this section and the scheduled task.

---

## Setting it up

1. **Confirm prerequisites** (both should already be done from standard setup): the **`scheduled-tasks` MCP** is connected and the **`permissions`** module is done. If either is somehow missing, set it up first (`references/mcp-setup.md` / `references/permissions-setup.md`).
2. **Offer the defaults**, let the user adjust times/days/which tasks they want.
3. For each enabled task, create it via the local scheduling tool with its cron (local tz) and the **self-contained prompt** for that routine (below).
4. Write the chosen schedule to **## Scheduled Runs** in `context.md`.
5. Mark the `scheduling` module complete in `config.md` (per `references/setup-migration.md`).

**Re-runnable / editable:** to change the schedule, update the task **and** the context.md section; match by task name so you never create duplicates. To list what exists, use `list_scheduled_tasks`.

---

## Self-contained run prompts (critical)

Each scheduled run starts **fresh, with no conversation memory**, so its prompt must be **fully self-contained** — it can assume no prior context. Every task prompt must tell Work Buddy to:

1. **Invoke the work-buddy skill** — this is a real `/work-buddy` session, running headless.
2. Run `date` first (Time Awareness).
3. Read the data it needs: `config.md`, `context.md`, `triage-heuristics.md`, `tasks.md`, the last 5 business days of logs + today's log, and the context MDs.
4. **Run the specific routine end to end** (Morning Startup / Midday Check-In / urgent sweep / Wrap-Up + Recap) exactly as defined in SKILL.md, including writing its log/checkpoint.
5. Honor **Voice & Discretion** — surface results, not process; never mention the skill, the schedule, or that this was an automatic run.

**Prompt template (fill `[ROUTINE]`):**

> "Autonomous Work Buddy run. Invoke the work-buddy skill and run the **[ROUTINE]** for today. Start by running `date`. Read `config.md`, `context.md`, `triage-heuristics.md`, `tasks.md`, the last 5 business days of logs + today's log, and the context MDs. Then execute [ROUTINE] exactly as defined in the skill, including writing the log/checkpoint. Follow Voice & Discretion: surface results only — never mention the skill, the schedule, or that this ran automatically. Produce the briefing as if I'd asked for it."

Fill `[ROUTINE]` per task:
- `wb-morning` → **Morning Startup**
- `wb-midday` → **Midday Check-In**
- `wb-urgent` → **a lightweight urgent triage sweep** (email + Slack only; surface just Needs-Action items since the last sweep; skip the full briefing)
- `wb-friday-recap` → **the Daily Wrap-Up, then the Weekly Recap**

---

## Delivery

A scheduled run produces its briefing in that headless session; where the user sees it depends on their setup (e.g. the scheduled task's output in the app). If a notification/delivery channel is configured, send the summary there; otherwise it waits in the task output. Don't assume a delivery channel that isn't set up.
