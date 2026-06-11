# Transcript Locations by OS

Claude Code stores conversation transcripts as JSONL/JSON files. The Work Buddy reads these during wrap-ups and recalibration to capture all work done across sessions. Below are the common locations by OS and client.

---

## Windows

### CLI / VS Code
```
C:\Users\[username]\.claude\projects\C--Users-[username]\*.jsonl
```

### Desktop App
```
C:\Users\[username]\AppData\Local\Packages\Claude_[package-id]\LocalCache\Roaming\Claude\claude-code-sessions\**\*.json
```

Note: The Desktop app package ID (`Claude_[package-id]`) may vary. Look for a folder starting with `Claude_` in the `Packages` directory. The session files are nested in subdirectories within `claude-code-sessions`.

---

## macOS

### CLI / VS Code
```
~/.claude/projects/[hashed-working-directory]/*.jsonl
```

The hashed directory name is based on your working directory path. Look for folders in `~/.claude/projects/` and check modification dates to find active ones.

### Desktop App
```
~/Library/Application Support/Claude/claude-code-sessions/**/*.json
```

---

## Linux

### CLI / VS Code
```
~/.claude/projects/[hashed-working-directory]/*.jsonl
```

### Desktop App
```
~/.config/Claude/claude-code-sessions/**/*.json
```

---

## How to Find Your Paths

If you're not sure where your transcripts are:

1. **CLI / VS Code:** Look in `~/.claude/projects/`. The subfolder names are your working directory paths with slashes replaced by dashes. Find the one that matches where you typically work.

2. **Desktop App:** Search your system for a folder named `claude-code-sessions`. On Windows, check `AppData\Local\Packages\` for a folder starting with `Claude_`.

3. **Check modification dates:** Your active transcript files will have recent modification timestamps. Sort by date modified to find current sessions.

---

## Adding Paths to Your Config

Once you've found your transcript locations, add them to your config file (`~/.claude/work-buddy/config.md`) under the Transcript Paths section:

```
CLI / VS Code: C:\Users\yourname\.claude\projects\C--Users-yourname\*.jsonl
Desktop App: C:\Users\yourname\AppData\Local\Packages\Claude_abc123\LocalCache\Roaming\Claude\claude-code-sessions\**\*.json
```

Use glob patterns (`*` and `**`) so the Work Buddy can find all transcript files in those directories.
