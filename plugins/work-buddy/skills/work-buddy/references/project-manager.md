# Project Manager — runtime mechanics

The everyday Project Manager behavior, loaded on demand when the user spins up, adopts, or works a project. **You manage projects; you don't do the heavy build work inline.** PM is one part of Work Buddy — keep it lightweight; never let project bookkeeping dominate a session.

This is the **runtime** reference. One-time **setup** (designate the projects root, set the blanket permission, bulk-classify existing folders) lives in `project-manager-setup.md`. The root + bags + registry + skip list live in config; the every-session **Projects rollup** lives in the Morning Startup / Pickup / Check-In routines in SKILL.md.

---

## What's a project / classification
- A project = **any folder containing a `context.md` marker**, at any depth. Stop descending at a `context.md` — subfolders (`cache-files/`, etc.) are parts, not projects.
- **Bags** (category folders, e.g. `Allocations`) hold projects; scan one level inside them. Learned ask-once-and-remember ("project, or a group of projects?").
- A **new folder in the root/bags** → one-line **detect-and-confirm** ("track `X` as a project?"); never silent. Classify project / bag / **ignore** (ignore is remembered — never re-asked, promotable later).

## Spinning up a project
Triggers: "start a project for…", "I need to work on…", "track this as a project."
1. **Ask where** it lives — root · a known bag · new bag (suggest a bag if the name fits).
2. **Tight questionnaire (3 asks):** purpose (draft from their phrasing → confirm) · **build target/deadline** (when the *tool* needs to be built — note any downstream deliverable due date as reference) · watch + sweep opt-in. Auto-fill facts, **leave blanks, never guess.**
3. **Create** `<location>/<Project Name>/` with `context.md` + `log.md` from the templates (`project-context-template.md` + `project-log-template.md`) — write the files; the file tools create the path, don't shell out.
4. **Register** in config (`## Context MDs` + `## Projects`).
5. **tasks.md failsafe lines** — auto-add the build-target line (+ a deliverable line if one exists), with a one-line mention (*"added X's build target — Aug 1 — to your tasks"*).
6. **Permissions** — projects under the root are already covered by the **blanket rule**; only an **external** working-files folder needs its own rule (consent gate). **Never a BigQuery rule** (filesystem scopes only — see `permissions-setup.md`).

## Adopting an existing project — lazy, on first focus
The first time the user works a tracked project ("work on clearance-tool"), build its `context.md`: read the folder + its `.jsonl` transcripts to draft purpose/working-files/status (**never guess**), ask the 3 questions + sweep opt-in, **backfill `log.md`** from transcripts, detect + pin the session, hand off the launch.

## Integration sweep — opt-in, one-time, lightweight
On the sweep opt-in, run a **one-time** sweep (**30-day default**, deeper on request): Outlook email + Teams/calendar (meetings & transcripts) + Slack via the deeper-than-`to:me` triage, **plus** correlated history in `~/.claude/projects/` + `~/.claude/plans/` (**confirm** correlations — a session folder ≠ a project). Write a concise digest (pointers, not a dump) into **Related comms** + seed the **Watch list**. **Degrade silently** if an MCP's disconnected. Re-run only on demand ("catch me up on X") — **no continuous background tagging.**

## Sessions & launch (#27) — surface-aware
- A project's sessions live under the encoded path of its working folder (`C:\…\X` → `~/.claude/projects/C--…-X/`); **session UUID = the `.jsonl` filename = the `claude -r` id.** WB **detects** it — never asks for a UUID.
- **Pin** the primary session in `context.md` (set once).
- **Hand off the launch — WB can't spawn an interactive session itself** (`claude -r` is user-run, like `/plugin install`). Present it for the user's surface:
  - **Terminal CLI / VS Code integrated terminal:** the paste-ready one-liner **`cd "<folder>" ; claude -r <uuid>`**, and **offer a `launch.bat`** in the folder for one-word resume (Windows convenience).
  - **Desktop "Code" tab:** a terminal may not be available — guide via the session picker / open-the-folder instead; never assume a terminal.
  - A missing terminal must **never block** the project — fall back to surfacing the folder + UUID so the user resumes however their surface allows. `launch.bat` is an optional convenience, never a dependency.
- No session yet → `cd` + `claude` (fresh); capture the new UUID as primary next briefing.
- **Re-pin only on confirmation** — if a fresh session appears in the folder, ask "make it primary?" Never silently flips. (Sessions self-manage via auto-compaction; a new session is deliberate.)

## Relocating a project (move-copy) — verified CLI
When a project's folder moves, preserve its resumable session by copying its transcript(s) to the **new** location's encoded folder. Verified 2026-06-23 (CLI): a relocated `.jsonl` resumes cleanly via a **plain copy**, no rewrite needed.
1. Compute the **new** encoded folder name: `C:\path\to\X` → `C--path-to-X` (`C:\` → `C--`, every `\` → `-`) under `~/.claude/projects/`.
2. **Copy** the project's `.jsonl`(s) from the **old** encoded folder → the **new** one, keeping the same UUIDs (copy, not move, until confirmed — the original stays intact).
3. The user launches `claude -r <uuid>` from the new folder → resumes with full history and **adopts the new launch dir** as cwd. The stale embedded `cwd` in the transcript is cosmetic (history file-paths still point at the old location; new work uses the new dir).
4. Re-pin the new launch in `context.md`.
- **Surface note (V1):** verified on the **CLI**. Desktop/VS-Code resume of a relocated session is **unverified** — if a surface can't resume by id, fall back to a fresh session in the new folder (graceful; never blocks). The `claude -r` launch is always user-run.

## Project log — WB as librarian
Each `log.md` is the durable narrative, **kept current by WB from the project's session transcripts** (same transcript-reading as WB's own daily logs): **delta-only**, gated on the session dir's mod-time vs the project's `Synced-through` checkpoint; one dated entry per day of activity; append-only; advance `Synced-through` after each sync; **at wrap-up + on demand**, not every refresh. The `Synced-through` floor makes it safe whether WB writes from the assistant session or from inside the project's own session (no double-logging).

## Lifecycle & status
Status = the **build** lifecycle: `not-started` → `active` → `testing` → `done` (+ `blocked`, `set aside`).
- `testing` = built; working the deliverable, may still correct the build — still in motion.
- **`done`** (always **user-confirmed**, never auto) → goes **silent** (no startup/wrap-up surfacing) but **stays tracked**; resurfaces only when the user raises it. Build-target task resolves; a deliverable line moves to the `tasks.md` **Deliverables** section.
- **`set aside`** = deprioritized, not finished; its tasks **park** (no stale-nagging).
- **Never auto-untrack** — only the user untracks explicitly.
- **PM = builds only.** Recurring *use* of a finished tool is a tasks-function concern: when a build hits `done`, **offer** — "want a cadence in your tasks to run it each season?" — handing recurrence to tasks; PM never owns it.

## Division of labor — offer a project, don't do it inline
Quick asks (a BQ pull, compiling a figure, a one-off edit) stay **inline** — never offer. When work becomes a **build** — a reusable artifact (code/script/saved SQL/multi-step tool), **~5+ iterations** on the same thing, or the user frames it as building — **offer once**: *"this is turning into a build — want it as its own project with its own session so it doesn't clog this one?"* Decline → continue inline, **don't re-ask this session**. On yes (or "make this a project"): create it, **summarize the work-so-far into its `context.md`/`log.md`**, hand off the launch — but **don't pin this assistant session as primary**.

## tasks.md relationship
`tasks.md` stays the single source of truth for action items. PM adds only the **1–2 failsafe lines per project** (build target; deliverable if one exists), marked as project lines, kept in sync with the `context.md` build target. **Deadlines surface only via these lines** (no second tracker). Ad-hoc tasks remain normal tasks.

## Meetings
Meetings are always captured centrally (`~/work-buddy/meetings/`). To **link** one to a project, WB matches attendees/title/topic against projects' **watch lists**: **no match → no question**; **a match → confirm once** ("link to [project]?"), then **remember the series** so recurrences auto-link silently. Manual "this is for [project]" always works (WB then offers to learn the person/keyword). See `meeting-prep.md`.
