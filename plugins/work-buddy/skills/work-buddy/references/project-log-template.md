# {{Project Name}} — Log

Chronological record of work on this project. Lives beside `context.md` in the project folder. **Your Work Buddy keeps this current** by reading the project's session transcripts — you don't write it by hand (though if you run `/work-buddy` inside the project's own session, it may append here live).

Why it exists: `context.md` holds *curated state*; this log holds the *narrative*. Together they let any session — even a fresh one after a compaction or restart — re-orient instantly. Append-only; never deleted.

---

## How it's maintained (for the Work Buddy)

- **One dated entry per day of activity** (`## YYYY-MM-DD`), newest at the top.
- **Synced from transcripts, delta-only.** Read the project's `.jsonl` sessions (under the encoded path of the project's working folder) for activity **after** the `Synced-through` checkpoint in `context.md`; summarize; append; then advance `Synced-through`.
- **Activity-gated:** only sync when the session dir's modification time is newer than `Synced-through` (skip untouched projects — keeps it cheap).
- **When:** at daily wrap-up, and on demand (when the user focuses the project, or on adoption — backfill from existing transcripts).
- **Both-writers-safe:** whether WB writes from the assistant session or from inside the project's own session, the `Synced-through` floor prevents double-logging. Append-only; never rewrite prior entries.
- Matched-meeting follow-ups (see `meeting-prep.md`) drop a one-line entry here too.

---

## Entries

### YYYY-MM-DD
- (what happened — decisions, progress, blockers, next steps)
