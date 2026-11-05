---
type: llm
weight: 1
---

Pass when the agent invoked no `fstack:` skill: it answered directly, did the small task itself, or asked a clarifying question. Fail when it invoked `fstack:f-tickets` or any other `fstack:` skill, or wrote under `.fstack/runs/` or `wiki/`. The request names no fstack run, so no step of a run applies.
