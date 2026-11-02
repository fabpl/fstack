---
type: decision
title: Sequential build without git
status: active
related: [agent-roles, frontier]
supersedes:
source_run: 2026-10-31-fstack-brief
tags: [git, build]
updated: 2026-10-31
---

# Sequential build without git

## Context
Parallel implementers need isolation: a worktree each and an integration branch. A project that is not a git repository offers neither. Options were to require git, to run in parallel in one directory, or to fall back.

## Decision
Without git, f-chief computes the same ticket graph but dispatches one implementer at a time in the project directory. It never runs `git init` on its own.

## Consequences
fstack works on any folder. Non-git projects lose parallelism, not correctness. The user is told once per run that parallelism is off and why.
