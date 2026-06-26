# Meeting Prep & Meeting Folders

Two related capabilities — on-demand prep, and a per-meeting folder structure for context that persists across recurring meetings.

## On-demand prep
Triggered by "prep me for [meeting/person]," "what do I need for the [X] call," "prep me for my next meeting."

Search daily logs and email/Slack for the attendees, the topic, or related project context. Present a brief:
- What the user has been working on related to this meeting
- Recent communications with the attendees
- Open items or decisions relevant to the agenda

(Morning Startup already gives lightweight inline context per meeting. This is the deeper, on-request version.)

## Meeting folders
Each meeting gets a folder at `~/work-buddy/meetings/YYYY-MM-DD - Meeting Name/` containing:
- **`prep.md`** — built before the meeting
- **`transcription.md`** — the meeting transcript (auto-pulled from Teams when available — see below — or pasted/uploaded by the user)
- **`next-steps.md`** — forward-looking summary: decisions, open items/owners, and any action items

**When a meeting appears on the calendar, check this folder.** Look for past meetings with the same name / topic / attendees — they often hold prep already built and `next-steps.md` from the prior session. Build on those rather than starting cold; a recurring meeting should feel continuous.

Create the folder by writing the first file into it (the file tools create the parent path) rather than shelling out, so it stays within the pre-allowed work-buddy data dir and doesn't prompt.

## After a meeting — pull the transcript (Teams)
When a meeting on the calendar has passed (M365 connected), proactively retrieve and file its transcript — don't wait to be asked.

1. Find the event (`outlook_calendar_search`), then read the full event record (`read_resource` on the event URI) and take its **`meetingTranscriptUrl`** field.
   - **URI-encoding gotcha:** Outlook event IDs contain `/` and `+`. Passing the raw or single-encoded ID truncates it at the first slash and 400s ("Id is invalid"). **Double-encode slashes (`/` → `%252F`) and encode `+` → `%2B`** in the event URI.
2. If `meetingTranscriptUrl` is present, read it (`read_resource`) for the WEBVTT transcript. **No transcript field = the meeting wasn't recorded/transcribed → skip silently.**
3. Save into the meeting folder:
   - **`transcription.md`** — the cleaned transcript. If room-device labels (e.g. a conference-room name) mask multiple in-room speakers, add a one-line speaker-attribution note at the top.
   - **`next-steps.md`** — a TL;DR, key decisions, an open-items/bugs table with owners, and a user-specific action-items section.
4. Surface a one-line "saved" note. **Not every meeting produces follow-ups** — a demo or info session may have zero action items; say so rather than inventing tasks. Only write a task to `tasks.md` if it's genuinely the user's to own (confirm if unsure).

## Linking a meeting to a project (Project Manager)
Meetings always live **centrally** in `~/work-buddy/meetings/` — linking never moves them, it just associates a meeting with a tracked project. **Confirm-first, never silent** (watch-list overlap makes false attribution easy):

1. Match the meeting (attendees / title / topic) against tracked projects' **watch lists** (in each project's `context.md`).
2. **No match → ask nothing.** Leave it central, unlinked.
3. **A match → confirm once:** *"This looks related to [project] — link it?"* On yes, add the meeting folder under that project's `context.md` **Related meetings**. On no, skip and don't re-ask that series.
4. **Remember the series:** once confirmed (or declined) for a recurring meeting, store the series → project decision in config (`## Meeting → Project routing`) so future occurrences auto-link (or stay unlinked) **without re-asking** — keeps it light for heavy calendars.
5. **Manual override always:** "this meeting is for [project]" links it immediately; then *offer* to add the missed person/keyword/series to that project's watch list (tightens future matching).
6. When a meeting is linked to a project and produced follow-ups, drop a **one-line entry into that project's `log.md`** (decisions / next steps) so meeting outcomes flow into the project narrative.
