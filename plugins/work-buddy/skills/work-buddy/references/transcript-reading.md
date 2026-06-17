# Transcript Reading Procedure

Shared procedure used by **Daily Wrap-Up, Midday Check-In, and missed/retroactive wrap-ups.** Transcripts are the ground truth of what actually happened across all Claude sessions — the daily log only holds checkpoints and tasks captured mid-day. Never rely solely on emails, context changes, or log checkpoints; they capture communications and final state, not the work itself.

## Where transcripts live
**All** Claude Code surfaces — the terminal CLI, the VS Code extension, **and the desktop app's "Code" tab** — write the **same standard JSONL transcript format** to `~/.claude/projects/`. (Confirmed across surfaces 2026-06-17.) Each session is filed in a subfolder whose name is derived from the session's working directory (`cwd`), with path separators and other special characters replaced by dashes — e.g. a session run in `C:\Users\me\workspace` lands in `~/.claude/projects/C--Users-me-workspace/` — and the file is named `<sessionId>.jsonl`.

Because the folder is keyed to the cwd, **one user's sessions are commonly spread across several project folders** — the terminal opened in the home dir, the desktop app opened in a project/workspace dir, etc. Never assume a single folder.

> **Desktop app note:** the desktop app *also* keeps a `local_<id>.json` per session under its own app-data dir (`…/Roaming/Claude/claude-code-sessions/…`). That file is **metadata only** — session id, cwd, model, MCP config, title — **no messages**. Ignore it. The real transcript content is always the `<cliSessionId>.jsonl` in `~/.claude/projects/` (the desktop session's `cliSessionId` field is the `.jsonl` filename). Do **not** chase `audit.jsonl` under `local-agent-mode-sessions/` — that's a different surface (agent-mode/Cowork), not the Code tab.

## Lookup procedure
Transcript files are named with UUIDs, not dates — you can't find a day's work by filename. Every time:
1. **Scan every project folder by modification time.** List `*.jsonl` across **all** subfolders of `~/.claude/projects/` — e.g. `find ~/.claude/projects -name '*.jsonl' -printf '%TY-%Tm-%Td %TH:%TM  %10s  %p\n' | sort`. Do **not** limit to one cwd-specific folder; the user may have worked in the CLI *and* the desktop app, which land in different folders.
2. **Identify files modified on the target day** — include long-running sessions created earlier but modified that day.
3. **Read candidate files directly** (use offset/limit for large files). Do not delegate to agents.
4. **Filter by timestamp** — each message entry has an ISO `timestamp` (`message.role` / `message.content` hold the content). For multi-day files, only synthesize entries that fall on the target day. A few control line types (`queue-operation`, `last-prompt`, `ai-title`) carry no `timestamp`/`message` — skip them.
5. **One reader handles all surfaces** — the format is identical across CLI / VS Code / desktop, so no per-surface parsing is needed; only the *folder scan* in step 1 has to be broad.

## Delta-only reading (mid-day check-ins)
After the first read, only read NEW content on later check-ins:
1. Note each transcript's file size from `ls -lt`.
2. Compare to the size recorded at the last check-in.
3. If it grew, read only from the previous offset to the end.
4. If unchanged, skip it.
5. Summarize the new content into the daily log checkpoint.

The daily log is the running summary — once content is captured in a checkpoint, the log is the source of truth. Re-reading already-summarized content wastes tokens. **Exception:** full wrap-ups (end-of-day or retroactive next morning) read the complete transcripts for the day to ensure nothing is missed.

## Tail-end thoroughness
The most important actions usually happen at the very *end* of a session — commits, pushes, deployments, final test results, status changes — and those are exactly what resolve carry-overs.
1. **Always read the end of every transcript** — at least the final ~200 lines, even if earlier portions were already summarized. Outcomes live at the end.
2. **Look for completion signals:** commits, pushes, "deployed," "pulled to prod," "done," "shipped," "tested," "live."
3. **Cross-reference carry-overs against the transcripts before writing them.** If a carry-over says work is pending but a transcript shows it finished, it's resolved — do not carry it forward. A stale carry-over that reports finished work as still-pending is the fastest way to erode trust in the morning briefing.
