# Permissions Setup — let automatic runs work without prompts

By default every tool call that isn't pre-allowed prompts for permission, and "Allow once" doesn't persist across runs. Work Buddy adds allow-list rules to the user's `~/.claude/settings.json` so routine actions stop prompting. This is the **`permissions`** setup module (introduced setup version 2) and a **standard, non-optional part of setup** — without it, Work Buddy prompts for approval on nearly every action, which is the single biggest source of day-to-day friction. Do **not** frame it as a skippable extra. The only gate is showing the exact rules and confirming before writing to `settings.json` (normal practice for touching a config file) — that's a consent step for the write, not an invitation to skip the feature.

It also matters for any **non-interactive/automatic run**: those can't answer permission prompts, so without these allow-list rules they'd stall.

---

## What gets allowed

**Portable (identical for everyone):**
- `Bash(date:*)` — the skill runs `date` on every time-aware response.
- **Read-only Bash commands** — `Bash(ls:*)`, `Bash(find:*)`, `Bash(cat:*)`, `Bash(grep:*)`, `Bash(wc:*)`, `Bash(head:*)`, `Bash(tail:*)`, `Bash(diff:*)`, `Bash(md5sum:*)`. These inspect files and state without changing anything, so they shouldn't prompt — the user asking Work Buddy to do something *is* the authorization for the read-only steps it takes to do it (listing logs, finding transcripts by modification time, verifying a copy, etc.). **Prefer the dedicated read tools (Read/Glob/Grep) over Bash for reading** — they're built for this and reading a file the user pointed at is implicitly authorized; these Bash allows cover the cases where no native tool fits (e.g. `find ... -newermt` for transcript deltas). **Deliberately NOT allowed:** interpreters (`python`, `node`) and anything that mutates state (`git commit`/`push`, `rm`, `mv`, file writes outside the work-buddy dir, sending email/Slack) — those stay gated. The goal is to eliminate friction on routine read-only asks while keeping a confirmation step on anything consequential.
  - **Leading-program rule (critical for it to actually work):** the permission matcher credits only the **first program** in the command string. A command that **leads with `cd`** or **chains** (`cd ~/work-buddy && find …`, `… && …`, `;`, loops) matches **none** of these per-program allows and **prompts anyway** — which also **stalls a headless/scheduled run**. So the skill must construct read-only Bash as a **single allow-listed program with absolute paths** (e.g. `find ~/work-buddy/logs -newermt …`), never `cd`-led or compound. (Stated in SKILL.md *Agent Usage*.) The allow-list cannot fix a compound command — only command construction can.
- The data-file rules (`Read`/`Edit`/`Write` over `~/work-buddy/**`) — config, context, triage-heuristics, tasks, logs, recaps, meetings. **On Windows the `~/...` form alone does not reliably match** — see *Path form by OS* below; emit the OS-correct forms there, not just the tilde.

### Path form by OS (Windows gotcha — read before writing the file rules)

The file-path rules above must be written in a form that the running OS's permission matcher actually compares against — otherwise the rule is present but never matches, the module reports success, and the user still gets prompted on **every** work-buddy file write (the exact friction this module exists to remove). On macOS/Linux the `~` expands and matches; on Windows it does **not** reliably match the normalized absolute path Claude Code evaluates.

So when writing the data-file rules, branch on OS:

- **macOS / Linux** — the tilde form is enough:
  - `Read(~/work-buddy/**)`, `Edit(~/work-buddy/**)`, `Write(~/work-buddy/**)`
- **Windows** — Claude Code matches the path as the **tilde form with backslashes** (`~\work-buddy\...`). The forward-slash tilde form (`~/work-buddy/**`) and absolute drive forms (`C:/Users/...`, `//c/c/Users/...`) do **not** match on Windows — don't bother emitting them. **Emit BOTH a double-star and a single-star form for each of Read / Edit / Write:**
  - `Read(~\work-buddy\**)`, `Edit(~\work-buddy\**)`, `Write(~\work-buddy\**)` — the `\**` form matches files in **subfolders** (`logs\…`, `recaps\…`, `meetings\…`).
  - `Read(~\work-buddy\*)`, `Edit(~\work-buddy\*)`, `Write(~\work-buddy\*)` — the `\*` form matches files **directly in** the work-buddy folder (`tasks.md`, `config.md`, `context.md`, `triage-heuristics.md`). **Required** — on Windows the `\**` form does **not** cover direct children, so without the `\*` form the top-level files still prompt on every write.
  - **In `settings.json` (JSON), each backslash must be escaped** — write `"Edit(~\\work-buddy\\**)"` and `"Edit(~\\work-buddy\\*)"`, which store as `Edit(~\work-buddy\**)` and `Edit(~\work-buddy\*)`.
  - Optionally also keep the forward-slash tilde form as a harmless cross-surface fallback, but the backslash forms are the ones that work.
  - **Provenance:** the both-forms-per-OS requirement was proven on a live Windows install (2026-06-17) for the *old* `~\.claude\work-buddy\` path. The matcher behavior is path-agnostic, so it carries to `~\work-buddy\` — but re-verify on the new path at the next test cycle.

> **Existing installs / data-dir move:** the data dir moved out of `~/.claude/` to **`~/work-buddy/`** (so Bash file-ops there are no longer hard-gated — see SKILL.md *Agent Usage*). Existing users' old rules point at `~\.claude\work-buddy\` and won't match the new location, so the **`data-dir-move` migration** (see `setup-migration.md`) re-runs this permissions step for `~/work-buddy/` as part of relocating their data. (Updating the plugin does not rewrite already-written rules — the migration does.)

**Per-install (must be detected — see below):**
- The connected connector tools: **Microsoft 365** and **Slack**.
- **Projects workspace blanket (project-manager module).** When the user designates a projects root (`config.md` `## Projects Workspace`), add a **single blanket** `Read`/`Edit`/`Write` rule for the root in the OS-correct form (Windows: **both** `~\<root>\**` and `~\<root>\*`, escaped in JSON; macOS/Linux: the forward-slash tilde form). `**` covers descendants at any depth and `*` covers direct children, so **one rule pair covers every project under the root — current and future — with no per-project rule.** The root must be inside the user folder (never `~/.claude/`, never OneDrive). Same consent gate + merge-don't-clobber.
  - **Per-project external backup (only):** a project whose *working files* live **outside** the root (OneDrive, a repo elsewhere) needs its own folder+subtree rule (both `\**` and `\*` on Windows), added on spin-up/adoption, same gate. In-tree projects need nothing beyond the root blanket.
  - **Still filesystem-path scopes only — never a BigQuery rule** (see below).

**Never allowed — BigQuery is deliberately excluded:**
- **Do not add any BigQuery (`mcp__<bigquery-server>__*`) rule, for any user.** BigQuery must stay manually gated so every query is reviewed before it runs (read-only posture; write/DDL tools must never be silently pre-approved). Skip the BigQuery MCP entirely when building permission rules — even if it's connected. If a user already has explicit BigQuery allow rules in their `settings.json`, leave them as-is; never add new ones and never add a wildcard.

---

## Detecting the connector tool names (the key step)

The connectors' MCP tool names embed a **per-install server ID**, so they **cannot be hardcoded** in the skill. To get them, **inspect the MCP tools actually available in the current session** and take each connector's real `mcp__<server>__…` prefix — whatever the server segment is on *this* machine is what goes in the rule.

- **Microsoft 365** → the server prefix of the available M365 calendar/email/chat-search tools.
- **Slack** → the server prefix of the available Slack search/read tools.

**BigQuery is intentionally not in this list** — never detect it or build a rule for it (see *What gets allowed* above). It stays manually gated for every user.

For each **connected** connector (M365, Slack), add a rule for its server prefix (e.g. `mcp__<server>__*` to allow that connector's tools). If a connector isn't connected, **skip its rule** and note it can be added later once connected (the module is re-runnable).

---

## Writing the rules (idempotent)

1. Read `~/.claude/settings.json` (if absent, start from `{ "permissions": { "allow": [] } }`).
2. Build the rule list: `Bash(date:*)` + the read-only Bash allows (`Bash(ls:*)`, `Bash(find:*)`, `Bash(cat:*)`, `Bash(grep:*)`, `Bash(wc:*)`, `Bash(head:*)`, `Bash(tail:*)`, `Bash(diff:*)`, `Bash(md5sum:*)`) + the data-file rules in the **OS-correct form** (see *Path form by OS* — on Windows BOTH the `~\work-buddy\**` and `~\work-buddy\*` backslash tilde forms, escaped as `~\\work-buddy\\**` and `~\\work-buddy\\*` in JSON; on macOS/Linux the forward-slash tilde form) + one rule per detected, connected connector.
3. **Merge, don't clobber:** add only rules not already present in `permissions.allow`. Never remove or rewrite the user's existing rules.
4. **Give a clear, explicit notice and get a yes.** State plainly that this will **modify the user's global Claude Code config at `~/.claude/settings.json`** — a file *outside* the work-buddy data directory and the **only** file Work Buddy ever touches outside `~/work-buddy/`. Show the **exact rules** about to be added, say in one line what they do (stop routine work-buddy actions from prompting every time), and wait for explicit confirmation. Make the file being changed unmissable — never bury it. Example framing: *"To stop Work Buddy from asking permission on every routine action, I'd add these rules to your global Claude config at `~/.claude/settings.json` (the one file I touch outside the work-buddy folder). Here's exactly what I'd add: […]. Add them?"*
5. Only on an explicit yes, write the merged `settings.json`. If the user declines, skip it (the module is re-runnable later) — never write without the confirmation.
6. Mark the `permissions` module complete in `config.md` (per `references/setup-migration.md`).

---

## Notes
- Use `~/.claude/settings.json` (user scope) so the rules apply across all the user's projects and surfaces. (`settings.local.json` is the project-local alternative if the user prefers per-project scoping.)
- Re-runnable by design: detect-and-merge means running it again only fills gaps — e.g. after the user connects a new connector, run it again to add that connector's rule.
- This writes to a config file outside the work-buddy data dir, so it is the one setup step that always requires explicit confirmation, even when the user has opted into other automatic behavior.
