# fstack wiki index

Read this first. One line per page. Updated: 2026-10-31.

## Overview
- [[fstack]] — what the plugin is, its missions, routing, surface, plugin layout

## Conventions
- [[wiki-format]] — page types, frontmatter schema, links, index rules
- [[run-layout]] — `.fstack/runs/` structure, ticket frontmatter, 30-line report format
- [[agent-roles]] — roles, models, context-pointer communication, frontier parallelism
- [[commits]] — plugin-wide: Conventional Commits 1.0.0 plus Ticket/Run footers, overridable per project
- [[code-quality]] — plugin-wide: four gates (format, lint, analyse, test), no silenced rules, baselines on adoption
- [[quality-catalog]] — plugin-wide, bootstrap only: default tools per stack signal, project toolchain page layout

## Decisions
- [[0001-inspired-not-dependent]] — reuse Pocock's ideas, depend on nothing
- [[0002-single-writer-wiki]] — only f-chief writes the wiki
- [[0003-runs-committed]] — run directories are committed
- [[0004-approval-before-full-chain]] — announce every route, ask only before full
- [[0005-english-everywhere]] — plugin and wiki in English, replies in the user's language
- [[0006-sequential-without-git]] — no git means one implementer at a time
- [[0007-models-by-role]] — Fable chief at high, Opus builders and reviewers at high, Sonnet explorer at medium, merger at low

## Terms
- [[run]] — one pass on one request
- [[route]] — answer, quick or full
- [[ticket]] — one vertical slice with blockers
- [[frontier]] — tickets ready to start
- [[report]] — an agent's single bounded output
- [[context-pointer]] — a path instead of content
- [[seam]] — where behaviour is tested
- [[distillation]] — reports into wiki pages
- [[bootstrap]] — mission zero

## Runs
- [[2026-10-31-fstack-brief]] — brief to spec, wiki bootstrapped, plugin v0.1.0 written
