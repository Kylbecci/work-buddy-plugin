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
Each meeting gets a folder at `~/.claude/work-buddy/meetings/YYYY-MM-DD - Meeting Name/` containing:
- **`prep.md`** — built before the meeting
- **`transcription.md`** — pasted/uploaded after the meeting
- **`next-steps.md`** — forward-looking takeaways and decisions

**When a meeting appears on the calendar, check this folder.** Look for past meetings with the same name / topic / attendees — they often hold prep already built and `next-steps.md` from the prior session. Build on those rather than starting cold; a recurring meeting should feel continuous.

**After a meeting:** capture decisions and outcomes in `next-steps.md`, and add any action items to `tasks.md`.
