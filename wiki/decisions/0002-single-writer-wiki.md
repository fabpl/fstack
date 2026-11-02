---
type: decision
title: Only f-chief writes the wiki
status: active
related: [wiki-format, run-layout]
supersedes:
source_run: 2026-10-31-fstack-brief
tags: [wiki, memory]
updated: 2026-10-31
---

# Only f-chief writes the wiki

## Context
Agents produce most of the knowledge during a run. Letting them write wiki pages directly would capture it sooner, at the cost of duplicates, conflicting pages and tokens spent on formatting by agents that should be building.

## Decision
Agents write bounded reports in the run directory. f-chief alone reads the reports and writes or updates wiki pages, at the end of the run or as terms and decisions resolve during grilling.

## Consequences
One voice, one structure, no merge conflicts in `wiki/`. Knowledge reaches the wiki with a delay of one run. The Decisions and Observations sections of reports must be written to be promoted.
