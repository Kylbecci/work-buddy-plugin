# MCP Setup — connect the tools Work Buddy uses

Work Buddy is far more useful with its tools connected, so setup walks the user through **all** of them — this is **not** pick-and-choose. Connect every one the user has access to. (At runtime the skill still degrades silently if one is temporarily unavailable — see *Integrations* in SKILL.md — but setup's job is to get them all connected so the user gets the full benefit and is ready for any pushed feature.)

This is the **`mcp-setup`** module (setup version 3). The user runs these commands / authentications **themselves** — the assistant can't run `claude mcp add` or authenticate a connector for them.

---

## Microsoft 365 (calendar + email) and Slack (messages)

These are **hosted Claude connectors** — set up through Claude, not `claude mcp add`:

1. Go to **https://claude.ai/customize/connectors** (or, in Claude Code, run `/mcp`).
2. Add **Microsoft 365** and **Slack**; click each to authenticate — OAuth opens in the browser.
3. Back in Claude Code, run `/mcp` to confirm each shows **connected**. If they don't appear, check `/status` shows your Claude.ai account (not an API key) — unset `ANTHROPIC_API_KEY` if it's set.

Do **Microsoft 365 first** — it's the single biggest upgrade (real meetings with context + email triage).

---

## BigQuery (reports)

A local MCP server added from the terminal. **Windows:**

```
claude mcp add --scope user bigquery -- cmd /c npx -y @ergut/mcp-bigquery-server --project-id backcountry-data-team --location US
```

**Mac/Linux** (drop the `cmd /c` wrapper):

```
claude mcp add --scope user bigquery -- npx -y @ergut/mcp-bigquery-server --project-id backcountry-data-team --location US
```

- **Authenticate to Google Cloud first:** install the gcloud SDK, then run `gcloud auth application-default login` and confirm you have access to the `backcountry-data-team` project. The server uses Application Default Credentials.
- `--project-id backcountry-data-team` is the shared Backcountry data project — same for everyone on the team.
- Verify with `claude mcp list` → `bigquery` shows connected, then `/mcp` in a session to confirm the tools load.

---

## scheduled-tasks (engine for automatic runs)

Set this up as part of standard setup — **not** only when enabling the schedule — so scheduled runs work the moment they're turned on (now, or whenever a future update enables them). It's a local MCP, added the same way:

```
claude mcp add --scope user scheduled-tasks -- <the scheduled-tasks MCP server command>
```

> ⚠️ **OWNER TODO — fill in before rollout.** Backend is **decided: Ryan's `scheduled-tasks` MCP** (tools `create_scheduled_task` / `list_scheduled_tasks` / `update_scheduled_task`). What's left is to capture the exact `claude mcp add` command from Ryan's setup and drop it in here + the go-live email — handled during email finalization. Until it's filled in, scheduling only works for users who already have the `scheduled-tasks` MCP connected.

---

## Verify everything

- `claude mcp list` (terminal) — each MCP shows ✓ connected.
- `/mcp` (inside a session) — shows tool counts; authenticate anything still pending.

## Scope

Use `--scope user` so the MCPs are available across all the user's projects and surfaces — matching the user-scope plugin install (`~/.claude/`).
