# Setup Migration — reaching already-onboarded users

New onboarding-gated setup (permissions, anything future) must also reach users who onboarded **before** that feature existed. They already have a `config.md`, so onboarding never re-runs for them. This is the mechanism that offers them the new setup — **once, with consent**.

(The updated skill *files* arrive on their own via marketplace update / auto-update. This handles the per-user *setup state* those files depend on.)

---

## Setup modules + version

Setup is tracked as discrete **modules**, each idempotent and runnable on its own — at first-run onboarding **or** later via migration. Every module declares the **setup version** it was introduced in.

| Module | Introduced @ | What it sets up | Run via |
|---|---|---|---|
| `essentials` | 1 | name, role, tone | onboarding step 2 |
| `transcripts` | 1 | transcript paths | onboarding step 3 |
| `context-mds` | 1 | project context files | onboarding step 3 |
| `personalize` | 1 | routines, contacts, workflows | onboarding step 4 |
| `integrations` | 1 | early connector awareness (superseded by `mcp-setup`) | onboarding (legacy) |
| `permissions` | 2 | allow-list rules so automatic runs don't prompt | `references/permissions-setup.md` |
| `mcp-setup` | 3 | connect ALL MCPs (M365, Slack, BigQuery) | `references/mcp-setup.md` |
| `project-manager` | 4 | projects root + blanket permission + bulk-classify folders (project/bag/ignore); lazy per-project adoption; consent-first `~/.claude` hints | `references/project-manager-setup.md` |
| `data-dir-move` | 5 | relocate the data dir `~/.claude/work-buddy/` → `~/work-buddy/` (existing users only — copy + verify + repoint + re-run `permissions` for the new path); new users onboard straight to `~/work-buddy/` and skip it | *Data-dir migration* below |

**Current setup version: 5.** (Bump this and add a row whenever a new setup module ships.)

**Everything is standard setup — get it all working.** Run every module; don't frame any as a skippable extra.

**Prerequisites:** none currently — every module is independent and can run on its own.

---

## How the markers are stored

In `config.md` under **## Setup** (auto-managed — never hand-edit):

- `Setup version: <N>` — the version this user is set up through.
- `Completed modules:` — module IDs done.
- `Declined modules:` — module IDs the user passed on, each with the date asked.

---

## The migration check (Session Start, when config exists)

Run **silently** right after reading config:

1. **Read the `## Setup` section.**
   - **If it's missing** (the user onboarded before module tracking existed): they have a working config, so treat all **version-1 core modules as already complete** — back-fill `Completed modules` with the core set and set `Setup version: 1`. Never re-offer core onboarding to an existing user.
2. **Compute what's new:** modules whose *Introduced @* version is **greater than the user's `Setup version`**, minus anything in `Declined modules`.
3. **If nothing is new** → done; say nothing.
4. **If there are new modules** → after the session's **first briefing** (never interrupt it), make **one concise, consented offer**. Voice & Discretion applies — never say "module"/"migration"; frame it as a new capability:
   > "Since you set me up, I've added a couple of things: [one-line pitch each]. Want me to set [that] up? (~1 min each.)"
   - **Accept** → run the module's procedure (its reference file), add it to `Completed modules`, bump the user's `Setup version` toward current.
   - **Decline** → add to `Declined modules` with today's date; don't auto-offer it again. The user can still ask later ("set that up"), which runs it on demand.
5. When all new modules are completed or declined, set `Setup version` to current.

**One offer per session, max.** If the user is mid-task, hold it to the end. Never nag across a briefing.

---

## Data-dir migration (the `data-dir-move` module)

The data dir moved from `~/.claude/work-buddy/` (inside the sensitive `~/.claude/` tree, which hard-gated Bash file-ops) to **`~/work-buddy/`** (a normal user folder). **New users** onboard straight to `~/work-buddy/` and never run this. **Existing users** (data still at `~/.claude/work-buddy/`) get it **once**, consent-gated.

**Trigger — this one is special: it runs from Session Start, NOT the post-briefing module offer.** Because the data dir itself is what's moving, an existing user's `~/work-buddy/config.md` doesn't exist yet, so Session Start's *config-missing* branch would otherwise mistake them for a new user. Per SKILL.md *Session Start*: when `~/work-buddy/config.md` is **missing** but `~/.claude/work-buddy/config.md` **exists**, run this migration **before** onboarding, then proceed as a normal existing user. (The standard post-briefing migration offer still handles every *other* new module.)

**Procedure — idempotent, copy-not-move, never lose data:**
1. **Detect.** Needed when old `~/.claude/work-buddy/config.md` exists **and** `~/work-buddy/config.md` does **not**. If the new path already has a config, treat as done — never clobber.
2. **Consent.** Explain plainly: *"I'm moving my data folder out of `~/.claude/` to `~/work-buddy/` so I stop hitting permission prompts. I'll copy everything over, leave the old folder as a backup, and update where I point."* Wait for a yes.
3. **Copy** the whole `~/.claude/work-buddy/` tree (config, context, triage-heuristics, tasks, `logs/`, `recaps/`, `meetings/`) to `~/work-buddy/`. Bash `cp` is fine — the **destination** `~/work-buddy/` isn't sensitive, and reading the source is read-only (no write-gate).
4. **Verify.** Confirm the key files + subfolders exist at `~/work-buddy/` and match (counts/sizes). If anything's missing, **stop and report — do not proceed**.
5. **Repoint.** Sweep the user's own config/context for absolute `~/.claude/work-buddy/...` paths (e.g. named locations) and update them to `~/work-buddy/...`. Most paths are managed/relative; catch stragglers.
6. **Re-run `permissions`** for the new path (`references/permissions-setup.md`) so `~/work-buddy/` gets its Read/Edit/Write rules; the old `~\.claude\work-buddy\` rules can stay (harmless) or be removed.
7. **Leave the old folder as a backup** — do **not** delete it automatically. Tell the user `~/.claude/work-buddy/` is safe to delete once they've confirmed everything works.
8. Mark `data-dir-move` complete and set `Setup version: 5`.

**Safety:** copy-not-move means a failure never loses data (the original stays put); re-running after a partial copy just fills gaps. Until migration completes, the old location remains the live data dir.

---

## Idempotency rule

Every setup module must be safe to run standalone and more than once: **check current state first, write only what's missing, never clobber existing user data or duplicate rules.** This is exactly what lets a module run identically at onboarding or via migration.
