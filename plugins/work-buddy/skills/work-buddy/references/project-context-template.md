# {{Project Name}} — Project Context

The durable context file for one tracked project. Dropped into the project folder as `context.md` when the project is adopted/spun up, and re-read in briefings so the project's status and build target stay surfaced. Your Work Buddy keeps it current — you rarely edit it by hand.

This file holds **project-level state** (a *build*: the tool/work being built). The chronological narrative lives beside it in **`log.md`**. Granular action items live in `tasks.md` — this project only adds 1–2 deadline failsafe lines there.

---

## Snapshot

The scannable top block — kept tight; this is what gets read every briefing.

```
Status:            not-started | active | testing | done | blocked (reason) | set aside
Location:          <path to this project folder>   (bag: <category folder, or —>)
Primary session:   <session UUID, or — if none yet>
Launch:            cd "<project folder>" ; claude -r <session UUID>
Synced-through:    <ISO timestamp — log sync checkpoint; auto-managed>
```

- **Status** is the *build* lifecycle. `done` = done being built (still tracked, just silent until you raise it). `set aside` = deprioritized, not finished. WB never marks `done` on its own and never untracks a project on its own.
- **Primary session** is pinned (set once on first detection/spin-up). The launcher targets it; WB only re-points it when it detects a fresh session and you confirm.

---

## Purpose / what we're building

One or two lines: what this tool/work is and what it produces. (Drafted from your spin-up phrasing; you confirm.)

---

## Build target / milestones

When the **build** needs to be done (the build-completion target — ideally ahead of any downstream deliverable, with room to test), plus any dated checkpoints. If there's a downstream deliverable with its own due date, note it here as reference — but the build target is what drives tracking.

---

## Working files

Paths to the actual code/data this project runs on. Usually inside this folder; may live elsewhere (OneDrive, a repo) — record the real paths. External folders get their own allow-rule.

---

## Stakeholders

Who needs the output / who this is for. (Blank if not applicable.)

---

## Watch list

People + keywords that identify this project — used to seed the integration sweep **and** to route related meetings to this project (with your confirmation).

- **People:**
- **Keywords:**

---

## Related comms / background

Key people, emails, threads, and background surfaced by the integration sweep (pointers, not pasted content). Grows over time.

---

## Related meetings

Links to meeting folders (in `~/work-buddy/meetings/`) confirmed as related to this project.

---

## Open items / Notes

Project-level open questions, decisions pending, things to remember. (Not a task list — actionable items live in `tasks.md`.)

---

## → Log

Chronological activity lives in **`log.md`** beside this file (one dated entry per day of activity; kept current by your Work Buddy from session transcripts).
