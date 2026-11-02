---
type: convention
title: Code quality gates
status: active
related: [quality-catalog, commits, agent-roles, run-layout]
supersedes:
source_run: 2026-10-31-fstack-brief
tags: [quality, plugin-wide]
updated: 2026-10-31
---

# Code quality gates

Plugin-wide convention: applies in every project, whatever its stack. A project page with the same slug overrides it.

## The four gates

Every project has four gates. Each gate is one command, recorded in the project's `wiki/conventions/toolchain.md` page (written at bootstrap, see [[quality-catalog]]).

| Gate | Checks | Example |
|---|---|---|
| format | Code layout, mechanically fixable | `vp fmt --check`, `pint --test` |
| lint | Rules on code shape and known mistakes | `vp lint`, `php-cs-fixer check` |
| analyse | Types and static analysis | `tsc --noEmit`, `phpstan analyse` |
| test | The project's test suite | `vp test`, `phpunit` |

A gate the project lacks is recorded as `missing` in `toolchain.md`, with the catalog's recommendation. A missing gate is skipped, never invented on the fly.

## Rules for implementers

- Run format (in fix mode), then lint, analyse and test before writing the report. A red gate means `status: partial`, never `done`. Name the failing gate in `## Open`.
- Never silence a rule to go green: no inline disable comment, no file exclusion, no lowered level, no deleted test. If a rule is wrong for this code, keep the gate red and write the reason in `## Decisions`; the user decides.
- Never change a tool's config file unless the ticket asks for it.

## Rules for adoption on an existing project

- A new tool is adopted with a baseline (PHPStan baseline, ESLint ignore list, a `--diff` scope) so that all four gates are green on adoption day.
- New or touched code must not add to the baseline. The reviewer compares the baseline file before and after.

## Rules for the reviewer

- Run the four gates on the integration branch. Each failure is a finding: `[convention:code-quality] <gate> — <first error line>`.
- Diff every tool config and baseline file between base and integration. Any loosening without a `## Decisions` line in a report is a finding.

## Rules for bootstrap

- Detect existing tools before recommending: a tool already configured in the project always wins over the catalog.
- Confirm each command against the installed tool's `--help` before writing it into `toolchain.md`.
- Install nothing. Setting up missing gates is a run of its own that the user starts.
