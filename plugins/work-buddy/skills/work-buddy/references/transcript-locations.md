# Transcript Locations

Claude Code stores conversation transcripts as JSONL files. The Work Buddy reads these during wrap-ups and recalibration to capture all work done across sessions.

---

## All surfaces write to one place

**Every** Claude Code surface — the **terminal CLI**, the **VS Code extension**, and the **desktop app's "Code" tab** — writes the **same standard JSONL transcript format** to:

```
~/.claude/projects/
```

(Windows: `C:\Users\<username>\.claude\projects\`.)

Inside, each session is filed in a **subfolder named after the session's working directory** (`cwd`), with path separators and other special characters replaced by dashes — e.g. a session run in `C:\Users\me\workspace` lands in `~/.claude/projects/C--Users-me-workspace/`. The file itself is `<sessionId>.jsonl`.

**Because the folder is keyed to the working directory, one person's sessions are usually spread across several folders** — the terminal opened in your home dir, the desktop app opened in a project/workspace dir, and so on. So the Work Buddy scans **all** subfolders of `~/.claude/projects/`, not just one.

A single glob covers everything:

```
~/.claude/projects/**/*.jsonl
```

---

## Desktop app — what NOT to read

The desktop app keeps some files under its own app-data directory (Windows: `…\AppData\Local\Packages\Claude_*\LocalCache\Roaming\Claude\`). **These are not the transcript:**

- `claude-code-sessions\…\local_<id>.json` — **metadata only** (session id, cwd, model, MCP config, title). **No messages.** Ignore it. Its `cliSessionId` field is the name of the real transcript `.jsonl` over in `~/.claude/projects/`.
- `local-agent-mode-sessions\…\audit.jsonl` — a **different surface** (agent-mode / Cowork), not the Code tab. Don't use it for Code-session wrap-ups.

The real transcript content for any Code session — CLI, VS Code, or desktop — is always the `.jsonl` in `~/.claude/projects/`.

---

## Finding active sessions

Transcript files are named by session UUID, not date — find a day's work by **modification time**, not filename:

```
find ~/.claude/projects -name '*.jsonl' -printf '%TY-%Tm-%Td %TH:%TM  %10s  %p\n' | sort
```

Sort by mod time to find current/recent sessions; each line entry inside carries an ISO `timestamp` for day-level filtering.
