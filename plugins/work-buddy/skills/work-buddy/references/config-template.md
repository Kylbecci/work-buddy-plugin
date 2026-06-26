# Work Buddy Config

This file is your personal configuration for the Work Buddy skill. It was created during onboarding and can be updated anytime — either by editing directly or by telling your Work Buddy to change a setting.

Your daily logs are saved to `~/work-buddy/logs/` and weekly recaps to `~/work-buddy/recaps/`. These paths are managed by the skill and don't need to be configured.

---

## User

```
Name:
Role:
```

---

## Tone Preference

Options: `concise`, `conversational`, `detailed`

```
Style:
```

---

## Transcript Paths

Where Claude stores conversation transcripts on your machine. The Work Buddy reads these during wrap-ups and recalibration to capture all work done across sessions.

**All** Claude Code surfaces — terminal CLI, the VS Code extension, and the desktop app's "Code" tab — write standard JSONL transcripts under `~/.claude/projects/`, in per-working-directory subfolders. A single all-folders glob therefore covers every surface; the Work Buddy scans all subfolders by modification time. (This field is informational — transcript reading scans `~/.claude/projects/` directly regardless.)

See [transcript-locations.md](transcript-locations.md) for details.

```
Transcript root: ~/.claude/projects/**/*.jsonl
```

---

## Projects Workspace

The folder where the Work Buddy tracks and creates projects (`<root>/<Project Name>/` or `<root>/<bag>/<Project Name>/`, each with its own `context.md` + `log.md`).

**Must be a normal, writable path inside your user folder** (e.g. `~/kylbecci-workspace`) — **not** under `~/.claude/` (sensitive) and **not** OneDrive (sync issues). At designation, a **single blanket allow-rule** on the root covers every project beneath it (any depth) so creating/editing projects never prompts.

```
Workspace root:
```

### Project bags (category folders)
Container folders *inside* the root that hold multiple projects (e.g. `Allocations`). WB scans one level inside these for projects. Learned by asking once ("project or a group of projects?") and remembered here.

```
```

### Ignored folders (skip list)
Folders the user said *not* to track (scratch, or a project they don't want managed). Never surfaced, never re-asked; promotable later via "track X".

```
```

---

## Context MDs

The **context-file registry** — context files the Work Buddy loads and stays aware of every briefing. Each tracked project's `context.md` is registered here (alongside any standalone context files discovered during onboarding or added manually). (Paired with the **project status registry** in `## Projects` below — this section is the *files to load*; that one is the *status line per project*.)

```
```

---

## Named Locations

Bookmarked folders for quick file saving. Add paths here or tell your Work Buddy "save it to [name]" and it will use these.

```
downloads: ~/Downloads
```

---

## Email / Slack Filters

Senders or keywords to skip when triaging email and Slack. These grow over time as you tell the Work Buddy what's not worth surfacing.

### Skip senders:
```
```

### Skip keywords:
```
```

---

## Save History

Tracks how often you save to unregistered paths. After 3 saves to the same path, the Work Buddy offers to bookmark it. Managed automatically — no need to edit.

```
```

---

## Projects

The **project status registry** — one line per tracked project: name · folder (under the root/bag, or external) · status · build target · pinned primary session UUID. Each entry pairs with that project's `context.md` in `## Context MDs`. Managed automatically on spin-up/adoption. A project whose working files live **outside** the root carries its own external-folder allow-rule (noted on its line); the root blanket covers everything else.

```
```

---

## Meeting → Project routing

Remembered meeting-series → project links, so a recurring project meeting auto-links after you confirm it once (no re-asking each occurrence). Auto-managed.

```
```

---

## Setup

Tracks which one-time setup steps you've completed, so features added later can be offered to you without redoing setup. Auto-managed — don't hand-edit. (See `references/setup-migration.md`.)

```
Setup version: (set during onboarding)
Completed modules: (set during onboarding)
Declined modules:
```
