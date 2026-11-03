---
type: decision
title: Architecture step between spec and tickets
status: active
related: [architecture, agent-roles, run-layout, 0007-models-by-role]
supersedes:
source_run: 2026-11-03-architect-step
tags: [agents, build]
updated: 2026-11-03
---

# Architecture step between spec and tickets

## Context
The spec records only what the user decided and left the rest as "implementer's choice". Implementers run in parallel worktrees, so two tickets on the same frontier could pick incompatible structures, found only at merge or review. f-tickets also needed the structure to draw its blocking edges and name shared files, and rediscovered it through explorers. Options were to let the spec decide the how, to run an architect per ticket before each implementer, or to add one step after the spec.

## Decision
A full run gains `/f-arch` between `/f-spec` and `/f-tickets`. One `architect` agent (Opus, high effort) reads the approved spec, the wiki and the code, and writes `architecture.md`: structure, interfaces, data, shared files, proposed decisions, risks, open questions. f-arch asks the user only the decisions that pass the three decision gates, gets the page approved, then records those decisions as `decision` pages. The spec stays limited to the user's decisions and seams; the architect never amends it, and a gap it finds goes back to the user, who alone may change the spec. Quick runs have no architecture. The full chain stops at a spec, architecture or ticket step that does not finish.

## Consequences
f-tickets slices along the architecture and takes its shared files from it. Implementers treat it as binding and report a conflict in `## Open` instead of redesigning. The reviewer gains an architecture axis. One more approval for the user per full run, and one Opus agent more. Grilling no longer asks how the change is built. Complements [[0007-models-by-role]] with the architect row in [[agent-roles]].
