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

**Current setup version: 3.** (Bump this and add a row whenever a new setup module ships.)

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

## Idempotency rule

Every setup module must be safe to run standalone and more than once: **check current state first, write only what's missing, never clobber existing user data or duplicate rules.** This is exactly what lets a module run identically at onboarding or via migration.
