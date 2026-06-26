# Weekly Recap

Loaded on demand — when the user asks ("weekly recap," "what did I do this week," "weekly summary") or when offered after a Friday wrap-up.

## Process
1. Read all daily logs from the current work week (Mon–Fri).
2. **Deduplicate and roll up** into a weekly view. The same open item often carries across multiple daily logs — without dedup the recap lists it 3–4 times and buries what actually happened.

## Output format
```
## Weekly Recap — Week Ending [Date]

### Summary
2–3 sentence narrative of the week.

### Completed This Week
Grouped by project — from daily Work Completed sections.

### Open Items
Deduplicated — only items still open (cross-check against tasks.md).

### Next Week
Inferred from carry-overs and open-item due dates.
```

3. **Display in chat for review before saving.** This is the user's record of their week — let them correct or add context before it becomes permanent.
4. **Save** to `~/work-buddy/recaps/Week Ending YYYY-MM-DD.md`.
