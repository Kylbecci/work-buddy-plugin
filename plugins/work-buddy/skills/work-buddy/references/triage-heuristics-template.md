# Email & Slack Triage Heuristics

This file controls how your Work Buddy filters email and Slack during morning startup and wrap-up sweeps. It starts with sensible defaults and grows as you teach it what matters and what doesn't.

You can update this anytime by telling your Work Buddy — "stop surfacing emails from X," "always show me messages about Y," etc. Or edit it directly.

---

## Surfacing Rules

These determine what gets pulled into your triage. Messages matching these criteria are candidates for surfacing.

- Messages from human senders with direct requests or questions aimed at you
- Messages mentioning you by name or tagging you directly
- Messages containing deadlines, due dates, or time-sensitive language
- Messages from key contacts listed in your context file
- Threads you previously participated in where someone replied

---

## Grouping

Surfaced messages are grouped into two buckets:

- **Needs Action** — Direct requests, open questions, deadlines, approvals needed, items waiting on your response
- **FYI** — Status updates, decisions made, changes announced, information shared that may affect your work but doesn't require a response

---

## Skip Rules

Messages matching these are filtered out automatically. Add senders or keywords as you learn what's noise.

### Always skip:
- Automated system notifications (build alerts, deployment notifications, monitoring)
- Calendar accepts, declines, and tentative responses
- Out-of-office auto-replies
- Newsletter and marketing emails
- Emails from `noreply@` addresses

### Skip senders:
```
```

### Skip keywords:
```
```

---

## Priority Senders

Messages from these senders always surface, even if they'd otherwise be filtered. Add people whose messages you never want to miss.

```
```

---

## Notes

Anything else about how you want triage to work — time windows to scan, channels to watch, threads to ignore, etc.

```
```
