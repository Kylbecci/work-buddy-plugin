# Active Tasks

Tasks are the single source of truth for what's currently open. Completed or dropped tasks are removed — the daily log keeps the historical record.

**Format:**
- `- [ ] Task description — **due M/DD** — added M/DD — optional context/project`
- `- [ ] Task description — **blocked** (reason) — added M/DD`
- `- [ ] Task description — **no deadline** — added M/DD`

Keep roughly ordered: dated items first (soonest on top), then blocked, then no-deadline. Stale tracking uses the `added` date — no-deadline items 3+ business days old get a "set a deadline, push, or drop?" prompt.

**Project failsafe lines (Project Manager).** Each tracked project auto-adds 1–2 lines here so its deadlines ride the normal surfacing — marked with a `project:<name>` tag:
- `- [ ] 🛠 <Project> — build target — **due M/DD** — added M/DD — project:<name>`
- `- [ ] <Deliverable> — **due M/DD** — added M/DD — project:<name>` *(only if a downstream deliverable exists)*

These stay in sync with the project's `context.md` build target. `tasks.md` is the **only** place project deadlines are surfaced (the Projects rollup is status-only). Lifecycle: project `done` → its build line clears and the **deliverable line moves to `## Deliverables`** below; `set aside` → its lines **park** (exempt from stale prompts). Don't hand-edit project lines — tell your Work Buddy.

<!-- Your tasks appear below. This file starts empty until your first captured task. -->

---

## Deliverables

Outputs whose tool is **built** but that still need to be produced/delivered. A project's deliverable line moves here when the build is marked `done`. Same format; `project:<name>` tag retained until delivered, then removed.

<!-- Deliverables appear here once their project's build is done. -->
