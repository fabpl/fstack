---
type: overview
title: fstack
status: active
related: [wiki-format, run-layout, agent-roles, route, 0001-inspired-not-dependent, 0008-architecture-before-tickets]
supersedes:
source_run: 2026-11-03-architect-step
tags: [plugin, architecture]
updated: 2026-11-05
---

# fstack

A Claude Code plugin, Claude Code only. It ships `f-chief`, a chief-of-staff skill that acts as the user's right hand, plus the step skills it orchestrates and the agents those steps use. Full specification: `.fstack/runs/2026-10-31-fstack-brief/spec.md`.

## Missions

0. [[bootstrap]] the wiki on first invocation in a project; greenfield mode on an empty folder (draft overview, toolchain chosen at the interview, first ticket installs the gates).
1. Keep `wiki/` as the project's memory: decisions, actions, facts. Format in [[wiki-format]].
2. Understand a request through grilling, produce a spec, its architecture and tickets, deliver by delegating to parallel agents.
3. Answer plain questions fast, from the wiki first.

## Routing

Every request gets a [[route]]: answer, quick or full. Full needs approval ([[0004-approval-before-full-chain]]). Full chain: grill → `/f-spec` → `/f-arch` → `/f-tickets` → `/f-build` → review → `/f-wiki` ([[0008-architecture-before-tickets]]).

## Surface

| Command | Does |
|---|---|
| `/fstack:f-chief <request>` | Routes and runs the request end to end |
| `/fstack:f-spec <run>` | Spec from the current conversation |
| `/fstack:f-arch <run>` | [[architecture]] of an approved spec, hard choices asked |
| `/fstack:f-tickets <run>` | Tickets with blocking edges |
| `/fstack:f-build <run>` | Frontier-parallel build, TDD, review, one fix pass |
| `/fstack:f-wiki bootstrap\|distil <run>` | Distil the run into the wiki, or bootstrap |

## Memory

Two levels. `wiki/` is durable, written by f-chief only ([[0002-single-writer-wiki]]). `.fstack/runs/<run>/` is working memory shared by agents, committed ([[0003-runs-committed]]), laid out per [[run-layout]]. Every agent ends with one bounded [[report]]. [[distillation]] turns reports into wiki pages and the final answer.

## Agents

Explorer (medium) and merger (low) on Sonnet, architect, implementer and reviewer on Opus at high, f-chief on Fable at high ([[0007-models-by-role]]). Rules in [[agent-roles]]. With git: worktrees and an integration branch. Without: sequential ([[0006-sequential-without-git]]).

## Plugin layout

```
fstack/
  .claude-plugin/plugin.json     name, version, description; components auto-discovered
  skills/f-chief/SKILL.md        router, grilling, chain driver (model: fable, effort: high)
  skills/f-spec/SKILL.md         spec from conversation, seams first
  skills/f-arch/SKILL.md         architect dispatch, gated decisions, approval
  skills/f-tickets/SKILL.md      vertical slices with blocked_by edges
  skills/f-build/SKILL.md        frontier dispatch, merge, one review, one fix
  skills/f-wiki/SKILL.md         bootstrap | distil <run>
  agents/explorer.md  architect.md  implementer.md  merger.md  reviewer.md
  wiki/   .fstack/                fstack's own memory, same format
  evals/                         trigger cases for `claude plugin eval`: each step skill fires on a replay, not on a near miss
```

Only f-chief is locked to user invocation; f-spec, f-arch, f-tickets, f-build and f-wiki stay model-invocable so f-chief can chain them. Skills read the format conventions from `${CLAUDE_PLUGIN_ROOT}/wiki/conventions/`, so this wiki is both fstack's memory and the rule book f-chief applies elsewhere. Pages tagged `plugin-wide` ([[commits]], [[code-quality]], [[security]], [[quality-catalog]]) are quality rules enforced in every project unless a project page of the same slug overrides them. Bootstrap writes a project `toolchain` page with the four gate commands; f-build passes them to every agent. Plugin agents are spawned as `fstack:<role>`; worktree isolation is passed per call so the no-git mode still works.

## Out of scope v1

Hooks, automatic retro, external trackers, other harnesses, agents writing the wiki.
