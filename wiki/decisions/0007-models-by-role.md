---
type: decision
title: Models and effort fixed by role
status: active
related: [agent-roles]
supersedes:
source_run: 2026-10-31-fstack-brief
tags: [agents, economy]
updated: 2026-10-31
---

# Models and effort fixed by role

## Context
Inheriting the session model would run every agent on Fable. Running everything on Opus costs more than exploration and merging need. Reasoning effort is a second cost lever: skill and agent frontmatter both accept `effort`, and a long dispatch loop at high effort burns context for bookkeeping.

## Decision
Models and effort are set in each skill and agent definition, not chosen per run.

| Component | Model | Effort | Reason |
|---|---|---|---|
| f-chief | Fable | high | Routing and grilling are judgement |
| f-spec | Fable (inline) | high | Must not invent or soften a decision |
| f-tickets | Fable (inline) | high | A wrong graph costs hours at merge time |
| f-build | Fable (inline) | medium | Bookkeeping and report reading |
| f-wiki | Fable (inline) | medium | Bounded synthesis from structured reports |
| implementer | Opus | high | TDD and convention conformance |
| reviewer | Opus | high | One pass, so findings must be sure, not exhaustive |
| explorer | Sonnet | medium | One targeted question at a time |
| merger | Sonnet | low | Mechanical merge, run tests, report |

## Consequences
Judgment-heavy work gets the strongest models at high effort. Search and bookkeeping stay cheap. Changing a role's model or effort is one line in one file. Two caveats: a skill's `effort` is ignored when `CLAUDE_CODE_EFFORT_LEVEL` is set in the environment, and the docs do not say which effort wins when f-chief at high invokes f-build at medium in the same turn. The first real run settles the second point.
