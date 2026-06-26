# Daily Log Format

Each daily log lives at `~/work-buddy/logs/YYYY-MM-DD.md`. One file per business day (Monday through Friday). Logs persist permanently.

---

## Structure

```markdown
# Work Buddy Log — YYYY-MM-DD

## Checkpoint — H:MM AM/PM
(Silent — written during recalibration. Not visible to the user. Multiple checkpoints may exist if recalibration happens more than once.)
- Brief bullets of work done since last checkpoint or start of day
- Any tasks captured since last checkpoint
- Current open items at time of checkpoint

## Tasks
(Running section — tasks written here the moment they're captured during the day.)
- [ ] Task description — due [date/time or "no deadline"] — captured [time]
- [x] Completed task — due [date] — completed [time]

## Daily Summary
(Written at wrap-up. A brief narrative paragraph of what the day looked like overall.)

## Work Completed
(Written at wrap-up. Grouped by project when possible.)
- Project A: what was done
- Project B: what was done
- Ad-hoc: anything not tied to a specific project

## Open Items
(Written at wrap-up. Checkbox format. Due dates required when known. These are what the morning startup scans.)
- [ ] Item description — source/context — due [date]
- [ ] Item description — source/context — no deadline

## Carry-Overs
(Written at wrap-up. Items that need to continue tomorrow with brief context on where they left off.)
- Item and what needs to happen next
```

---

## Rules

1. **Checkpoints are silent.** They are written by the skill during recalibration (compaction or 30-message threshold). The user never sees them. They exist so the wrap-up can recover context that would otherwise be lost.

2. **Tasks are written immediately.** When the user drops a task mid-conversation, it goes into the Tasks section right away. This persists through compaction.

3. **Wrap-up writes the final sections.** Daily Summary, Work Completed, Open Items, and Carry-Overs are generated at wrap-up by synthesizing all JSONL transcripts from the day plus any checkpoints and tasks already in the log.

4. **Open Items use checkboxes.** `- [ ]` for open, `- [x]` for completed. **These are historical snapshots** — they record what was open at the end of the day. The source of truth for current open tasks is `~/work-buddy/tasks.md`. Morning startup reads `tasks.md`, not daily log Open Items. **Carry-Overs use prose** — they describe what needs to continue and where it left off. Both sections are still written at wrap-up for historical context and weekly recaps, but they do not drive the morning task list.

5. **Due dates are explicit.** Every open item either has a date or says "no deadline." This enables the stale item tracking (3 business day threshold for items with no deadline).

6. **One log per day.** If a wrap-up is missed and generated retroactively the next morning, it still writes to the original day's file (e.g., Monday morning generates Friday's missed wrap-up into `2026-04-18.md`, not into Monday's file).

7. **Never overwrite checkpoints.** The wrap-up appends the final sections below existing checkpoints and tasks. Checkpoints are historical record — they stay.

8. **Second wrap-up replaces, not duplicates.** If the user wraps up, continues working, and wraps up again, the second wrap-up should replace the existing wrap-up sections (Daily Summary, Work Completed, Open Items, Carry-Overs) with a fresh version that covers the full day. The new version incorporates everything from the earlier wrap-up plus any work done after it. Checkpoints and Tasks sections above the wrap-up are always preserved. This prevents duplicate or conflicting summaries in the same log.
