---
type: decision
title: Run directories are committed
status: active
related: [run-layout]
supersedes:
source_run: 2026-10-31-fstack-brief
tags: [runs, memory]
updated: 2026-10-31
---

# Run directories are committed

## Context
`.fstack/runs/` holds specs, tickets, notes and reports. They could be gitignored once distilled into the wiki, keeping the repository lean.

## Decision
Run directories are committed with the project.

## Consequences
The wiki can always be rebuilt or audited from its sources, and a future run can read how an earlier one went. The repository grows by a few small markdown files per run. Reports are bounded, so the growth stays small.
