---
type: convention
title: Agent roles and communication
status: active
related: [run-layout, context-pointer, frontier, 0007-models-by-role, 0008-architecture-before-tickets]
supersedes:
source_run: 2026-11-03-architect-step
tags: [agents, economy]
updated: 2026-11-03
---

# Agent roles and communication

f-chief runs in the main thread on Fable, high effort. It does not read codebases and it does not implement. It grills, routes, dispatches, merges reports and writes the wiki. Everything else is a subagent.

## Roles

| Role | Model | Effort | Input | Output |
|---|---|---|---|---|
| explorer | Sonnet | medium | A question and pointers | One note under `notes/`, one report |
| architect | Opus | high | The approved spec path, wiki slugs, note paths | `architecture.md`, one report |
| implementer | Opus | high | One ticket path, the spec and architecture paths, note paths, wiki page slugs | Code and tests red-green, one report |
| merger | Sonnet | low | A branch name and the integration branch | A fast-forward or merge commit, one report |
| reviewer | Opus | high | The integration branch, the spec and architecture paths, convention slugs | One report whose Open section lists findings |

Models and effort are fixed per role (see [[0007-models-by-role]]; the architect row comes from [[0008-architecture-before-tickets]]). Skills run inline on the session model: f-chief, f-spec, f-arch and f-tickets at high effort, f-build and f-wiki at medium.

## Communication

- Prompts carry [[context-pointer]]s: paths and slugs, never pasted content. A prompt fits on one screen.
- Agents answer through their report file. The text they return to f-chief is one line: the report path and the status.
- Agents write under `.fstack/runs/` only, each its own note and report. f-chief alone writes `wiki/`.
- Implementers read what their prompt names (ticket, spec sections, architecture sections, notes, earlier reports) and grep from there. The [[architecture]] binds them: a conflict goes to `## Open`, not to a redesign. A ticket that needs exploration gets an explorer note first.
- Run files (`.fstack/runs/<run>/`) are committed by f-chief at the close of a run or a distillation, not by agents, except each agent's own report.

## Parallelism

Tickets form a graph through `blocked_by`. The [[frontier]] is the set of tickets whose blockers have all landed on the integration branch. Every ticket on the frontier is dispatched at once, each in its own worktree. The frontier is recomputed from merged branches, not from ticket status fields.

Without git the frontier is still computed, but tickets run one at a time in the project directory. The mode is re-detected at each frontier: a toolchain ticket that runs `git init` switches the rest of the run to parallel mode.
