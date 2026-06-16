# Work Buddy Config

This file is your personal configuration for the Work Buddy skill. It was created during onboarding and can be updated anytime — either by editing directly or by telling your Work Buddy to change a setting.

Your daily logs are saved to `~/.claude/work-buddy/logs/` and weekly recaps to `~/.claude/work-buddy/recaps/`. These paths are managed by the skill and don't need to be configured.

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

Paths where Claude stores conversation transcripts on your machine. The Work Buddy reads these during wrap-ups and recalibration to capture all work done across sessions.

See [transcript-locations.md](../references/transcript-locations.md) for help finding yours.

```
CLI / VS Code:
Desktop App:
```

---

## Context MDs

Project context files the Work Buddy should load and stay aware of. These were discovered during onboarding or added manually.

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

Active projects and their named sessions (if any). Managed automatically from context MD discovery.

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
