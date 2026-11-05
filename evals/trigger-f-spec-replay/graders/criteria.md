---
type: llm
weight: 1
---

Pass when the agent invoked the `fstack:f-spec` skill for run `2026-11-05-csv-export`. Evidence that counts: a Skill tool call naming `fstack:f-spec`, or a reply that reports on `.fstack/runs/2026-11-05-csv-export/` (for instance that a file the step needs is missing). Fail when the agent did the step's work without the skill, invoked another `fstack:` skill, or invoked none.
