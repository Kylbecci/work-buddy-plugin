# Transcript Reading Procedure

Shared procedure used by **Daily Wrap-Up, Midday Check-In, and missed/retroactive wrap-ups.** Transcripts are the ground truth of what actually happened across all Claude sessions — the daily log only holds checkpoints and tasks captured mid-day. Never rely solely on emails, context changes, or log checkpoints; they capture communications and final state, not the work itself.

## Lookup procedure
Transcript files are named with UUIDs, not dates — you can't find a day's work by filename. Every time:
1. **List transcript files by modification time** from each path in config (CLI/VS Code and Desktop App). Use `ls -lt` to see mod dates + sizes.
2. **Identify files modified on the target day** — include long-running sessions created earlier but modified that day.
3. **Read candidate files directly** (use offset/limit for large files). Do not delegate to agents.
4. **Filter by timestamp** — each entry has a `timestamp`; for multi-day files, only synthesize entries that fall on the target day.
5. **Cover all paths** — CLI/VS Code *and* Desktop App; the user may have worked in multiple clients.

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
