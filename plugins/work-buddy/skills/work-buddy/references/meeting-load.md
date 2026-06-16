# Meeting Load — personal baseline

"Heavy" / "light" meeting days are **person- and weekday-specific**. Two hours of meetings is a heavy day for one person and a light morning for a director who bridges teams. **Never judge meeting load on an absolute feel or a fixed hour count.** Express it **relative to the user's own normal for that day of the week** (their Mondays ≠ their Fridays).

---

## Where the baseline lives

In `~/.claude/work-buddy/context.md`, under the auto-managed **Meeting Load Baseline** section, as a fixed table:

| Weekday | Avg meeting hrs | Avg meeting count | Sample days |
|---|---|---|---|
| Monday | … | … | … |
| Tuesday | … | … | … |
| Wednesday | … | … | … |
| Thursday | … | … | … |
| Friday | … | … | … |

_Last computed: YYYY-MM-DD HH:MM (local)_

This is auto-computed state — populate and refresh it **silently**; never ask the user to fill it in, and don't hand-edit it. Rewrite the whole table each refresh.

---

## When to refresh

Driven by the **`Last computed` timestamp** in the table, not a fixed weekday. At the start of a Morning Startup — and any other time you're about to use the baseline — run `date` and compare:

- If the stamp is **≥ 6.5 days old**, or the baseline is **missing / not yet computed**, refresh it: a silent background step (compute, rewrite `context.md`, continue the briefing).
- Otherwise leave it as-is — don't rebuild a current baseline.

Why **6.5** days, not 7: a half-day buffer so the weekly refresh still fires when today's startup runs slightly earlier than last week's (a flat 7 would skip the week and let the refresh drift later and later). 6.5 also can't fire twice in one week. Net effect: refreshes ~weekly, on the first startup past the threshold, self-stabilizing around the user's earliest startup time. Stamp the table with date **and** time so the comparison is precise.

---

## Computing it

1. Pull the user's calendar for the trailing **4 weeks**.
2. Keep only **real, committed meetings**:
   - **Committed RSVP** — use `responseStatus` (NOT `showAs`; see *Calendar Data* in SKILL.md): count `accepted`, `organizer` (the user's own meetings), and `tentativelyAccepted`. **Exclude** `declined` and `notResponded` / `none`. The calendar search doesn't return `responseStatus`, so read each event's live record — this runs only once a week, so the extra reads are fine.
   - **Real meetings only** — require at least one **other attendee**. Exclude solo holds, focus time, and personal busy blocks.
   - **Exclude** all-day events, cancelled events, and OOO / leave blocks.
3. **Skip days the user was off.** When averaging a weekday, drop any date the user did not work — a day with an all-day OOO/PTO entry, or a known recurring day off (e.g. summer Fridays, from `context.md` Weekly Routines). Never let an off day deflate that weekday's average.
4. Group the remaining meetings by **day of week**. For each weekday compute avg meeting **hours/day** and avg meeting **count/day**, averaged across the *worked* occurrences of that weekday in the window. "Sample days" = how many worked days went into that weekday's average.
5. Rewrite the Meeting Load Baseline table in `context.md`, stamped with the current date **and time** (local).

(Overlapping meetings: sum their durations — two stacked 30-min meetings count as 1 hr of load. Simple and fine for a relative gauge.)

---

## Using it

When characterizing a day's load (Morning Startup *Suggested Focus*, the Monday *Weekly Outline*, or a Check-In):

- Compare the day's committed meeting hours to the baseline **for that same weekday**.
- **"Heavy"** = meaningfully above their typical [weekday] — roughly ~30%+ over, or clearly more meetings than usual. **"Light"** = meaningfully below. If the day is about normal, don't editorialize on load at all.
- If **no baseline exists yet**, state the day's load in plain hours/count without a heavy/light judgment, and compute the baseline in the background so it's ready next time.
