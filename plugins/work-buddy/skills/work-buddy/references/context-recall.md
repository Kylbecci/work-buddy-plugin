# Context Recall

Loaded on demand — when the user asks about past work ("what did I do Tuesday," "what was that thing about the report," "what did [person] say last week"). Use a **layered search**: each layer trades speed/cost for depth. Answer as fast as possible, escalating only when needed.

1. **Daily logs first.** Search the relevant date range. Logs are structured summaries — fast, cheap, and enough for most questions. They answer "what happened."
2. **Offer to go deeper.** "Want more detail on any of this?"
3. **If yes — transcripts.** Read transcripts for the specific topic from the relevant dates (see `transcript-reading.md`). Full detail, timestamps, decision sequence — answers "how" and "why." Pull only for the asked topic, not the whole day.
4. **If still thin — email/Slack.** The widest net; best for person- or communication-based questions where the answer lives outside Claude sessions entirely.

Each layer fires only if the user needs it. Searching everything upfront feels thorough but buries the answer in noise — most questions are resolved at layer 1.
