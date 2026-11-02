---
type: decision
title: Approval required before the full chain
status: active
related: [route, fstack]
supersedes:
source_run: 2026-10-31-fstack-brief
tags: [routing, economy]
updated: 2026-10-31
---

# Approval required before the full chain

## Context
The full chain (grill, spec, tickets, build, review, distil) spawns many Opus agents and can run for hours. Answer and quick routes cost little. Asking before every request would slow the chief down; never asking risks a costly misroute.

## Decision
f-chief announces its route in one line on every request. It proceeds unasked on answer and quick. It waits for an explicit yes before starting the full chain.

## Consequences
Cheap routes stay fluid. The expensive route is always a conscious choice. The user can override any route by saying so.
