# Permissions Setup — let automatic runs work without prompts

By default every tool call that isn't pre-allowed prompts for permission, and "Allow once" doesn't persist across runs. Work Buddy adds allow-list rules to the user's `~/.claude/settings.json` so routine actions stop prompting. This is the **`permissions`** setup module (introduced setup version 2) and a **standard, non-optional part of setup** — without it, Work Buddy prompts for approval on nearly every action, which is the single biggest source of day-to-day friction. Do **not** frame it as a skippable extra. The only gate is showing the exact rules and confirming before writing to `settings.json` (normal practice for touching a config file) — that's a consent step for the write, not an invitation to skip the feature.

It also matters for any **non-interactive/automatic run**: those can't answer permission prompts, so without these allow-list rules they'd stall.

---

## What gets allowed

**Portable (identical for everyone):**
- `Bash(date:*)` — the skill runs `date` on every time-aware response.
- `Read(~/.claude/work-buddy/**)`, `Edit(~/.claude/work-buddy/**)`, `Write(~/.claude/work-buddy/**)` — the data files (config, context, triage-heuristics, tasks, logs, recaps, meetings).

**Per-install (must be detected — see below):**
- The connected connector tools: **Microsoft 365**, **Slack**, **BigQuery**.

---

## Detecting the connector tool names (the key step)

The connectors' MCP tool names embed a **per-install server ID**, so they **cannot be hardcoded** in the skill. To get them, **inspect the MCP tools actually available in the current session** and take each connector's real `mcp__<server>__…` prefix — whatever the server segment is on *this* machine is what goes in the rule.

- **Microsoft 365** → the server prefix of the available M365 calendar/email/chat-search tools.
- **Slack** → the server prefix of the available Slack search/read tools.
- **BigQuery** → the server prefix of the available BigQuery query tool.

For each **connected** connector, add a rule for its server prefix (e.g. `mcp__<server>__*` to allow that connector's tools). If a connector isn't connected, **skip its rule** and note it can be added later once connected (the module is re-runnable).

---

## Writing the rules (idempotent)

1. Read `~/.claude/settings.json` (if absent, start from `{ "permissions": { "allow": [] } }`).
2. Build the rule list: the portable rules above + one rule per detected, connected connector.
3. **Merge, don't clobber:** add only rules not already present in `permissions.allow`. Never remove or rewrite the user's existing rules.
4. **Show the user the exact rules** about to be added and confirm before writing.
5. Write the merged `settings.json`.
6. Mark the `permissions` module complete in `config.md` (per `references/setup-migration.md`).

---

## Notes
- Use `~/.claude/settings.json` (user scope) so the rules apply across all the user's projects and surfaces. (`settings.local.json` is the project-local alternative if the user prefers per-project scoping.)
- Re-runnable by design: detect-and-merge means running it again only fills gaps — e.g. after the user connects a new connector, run it again to add that connector's rule.
- This writes to a config file outside the work-buddy data dir, so it is the one setup step that always requires explicit confirmation, even when the user has opted into other automatic behavior.
