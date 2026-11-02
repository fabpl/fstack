---
type: decision
title: Inspired by Pocock, dependent on nothing
status: active
related: [fstack]
supersedes:
source_run: 2026-10-31-fstack-brief
tags: [architecture]
updated: 2026-10-31
---

# Inspired by Pocock, dependent on nothing

## Context
Matt Pocock's skills already cover grilling, spec, tickets, parallel implementation, TDD and review. Depending on the installed plugin would save work. Its skill names have changed twice (to-prd → to-spec, to-issues → to-tickets) and its behaviours are tied to its own tracker setup and doc layout.

## Decision
fstack reuses the ideas and rewrites every behaviour. No skill in fstack invokes a `mattpocock-skills` skill. No file in fstack is copied from it.

## Consequences
More to write and maintain. In exchange, one memory system (the wiki) instead of three, stable names, and a plugin that works with nothing else installed.
