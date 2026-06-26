# Project Manager Setup — workspace + classify (lightweight, lazy)

The **`project-manager`** setup module (setup version 4). It does two light things and defers the heavy part:
1. **Designate the projects workspace root** + set its blanket allow-rule.
2. **Bulk-classify** what's already in the root (project / bag / ignore).

It does **not** build out every project up front — full per-project `context.md` adoption is **lazy** (happens the first time the user works a project from WB; see *Lazy adoption* below). Goal: setup is a couple of minutes, not a marathon. Runs at first-run onboarding **or** later via the migration offer (`setup-migration.md`). **Idempotent** — re-running only fills gaps.

> **Consent-first stays the rule for *external* material.** The designated root is the user's explicit project home (placement = opt-in), so folders there are *adopted via classify*. But existing `~/.claude/` context files / plans / session folders are **weak, always-confirmed hints only** — never auto-converted (Step 3).

---

## Step 1 — Designate the projects root + blanket permission

1. **If a root is already set** in `config.md` `## Projects Workspace` → confirm it, **don't re-prompt or clobber**. Only change on request.
2. **If not set,** ask where projects live. **Requirements:** a normal, writable path **inside the user folder** (e.g. `~/kylbecci-workspace`); **never** under `~/.claude/` (sensitive) and **not OneDrive** (sync issues). Confirm it exists (offer to create).
3. Store it under `## Projects Workspace` → `Workspace root:`.

### Blanket allow-rule (set once, covers all projects forever)
A **single** allow-rule set on the root covers every project beneath it, at any depth, present and future — so creating a project never prompts and never needs a per-project rule. Add `Read` / `Edit` / `Write` for the root in the **OS-correct form** (per `permissions-setup.md`):
- Windows: **both** `~\<root>\**` (descendants, any depth) **and** `~\<root>\*` (direct children), escaped in JSON.
- macOS/Linux: the forward-slash tilde form.
- Same consent gate as any `settings.json` write: name the file, show the exact rules, write only on an explicit yes. **Merge, don't clobber.**

> **Hard guardrail — never weaken the read-only BigQuery rule.** These are **filesystem path scopes only**; never add a BigQuery MCP rule or anything permitting a BigQuery write/DDL, in any context. Mirrors `permissions-setup.md`.

---

## Step 2 — Bulk classify the root (project / bag / ignore)

Scan the root (read-only) and have the user classify each folder. **Three choices, no separate "track" step:**
- **Project → tracked.** Calling it a project *is* opting in. (Its `context.md` is **not** built now — that's lazy, below.) Registered under `## Projects` / `## Context MDs`.
- **Bag/category → container.** Record under the bags list; **then ask about each folder inside it** (project/bag/ignore), recursing into sub-bags. **Never descend into a project's internals** (stop at a folder classified as a project / containing a `context.md`).
- **Ignore → skip list.** Covers both "not a project / scratch" and "a project I don't want managed." Remembered, **never re-asked**; promotable later via "track X."

Persist all three states in `config.md` so future scans are stable and non-nagging (bags list · projects registry · ignore/skip list).

**Ongoing detection** (outside setup): at Session Start / Morning Startup / refresh, a read-only scan of the root + known bags flags any *new* untracked folder with one lightweight **detect-and-confirm** line ("New folder `X` — track it as a project?"). No silent adoption.

---

## Step 3 — Existing-user hints (consent-first, confirm — never assume)

For already-onboarded users, also surface **weak hints** and let the user opt in per item — **never auto-convert**:
- `~/.claude/projects/` standalone `*-context.md` files + session folders (a session folder ≠ a project — it's just a dir Claude was launched from).
- `~/.claude/plans/` plan files.

Offer: "I found these existing context files / plans / sessions — want to start any as a tracked project, or pull any into a project's context?" Only on an explicit yes does WB create/seed a project. Skipped items stay untouched.

---

## Lazy adoption (the deferred heavy part)

Full per-project adoption is **lazy** — it happens the first time the user works a tracked project, **not at setup**. The procedure (tight questionnaire, transcript-drafted `context.md`, optional integration sweep, session detect/pin + launch hand-off, the 1–2 `tasks.md` failsafe lines, `log.md` backfill) is the **runtime** reference: see **`project-manager.md`**. Setup's job is only to designate the root, set the blanket permission, and classify what's already there — it never builds a project's `context.md`.

---

## Record setup state + idempotency

- Mark `project-manager` complete in `config.md` `## Setup`; bump `Setup version` toward current (per `setup-migration.md`).
- **Run-twice-safe:** root confirmed (not overwritten); classifications/skip-list remembered (no re-ask); registrations merged (no duplicates); blanket rule merged (never clobbered); no per-project `context.md` created until lazy adoption.
