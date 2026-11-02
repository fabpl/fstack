---
type: convention
title: Commit messages
status: active
related: [agent-roles, run-layout, wiki-format, quality-catalog]
supersedes:
source_run: 2026-10-31-fstack-brief
tags: [git, quality, plugin-wide]
updated: 2026-10-31
---

# Commit messages

Plugin-wide convention: applies to every commit an fstack agent makes in any project. A project page with the same slug overrides it (see [[wiki-format]], Precedence). Reference: Conventional Commits 1.0.0, https://www.conventionalcommits.org/en/v1.0.0/.

## Format

```
<type>(<scope>)!: <description>

<body>

<footer>
```

- `type` is required, lowercase, one of: `feat`, `fix`, `refactor`, `test`, `docs`, `build`, `ci`, `chore`, `perf`, `style`, `revert`.
- `scope` is optional, lowercase, a module or area name from the project's `overview` pages. No spaces.
- `!` before the colon marks a breaking change. A `BREAKING CHANGE: <text>` footer is then required.
- `description` is imperative, lowercase first letter, no trailing period, under 72 characters in total with the prefix.
- `body` is optional, separated by one blank line, explains why, not what. Wrap at 72.
- Footers are `Token: value` lines in one block at the end of the message, one blank line after the body and no blank line inside the block: `Ticket:`, `Run:` and any `Co-Authored-By:` sit on adjacent lines, so `git interpret-trailers` and `%(trailers)` see them all.

## Types in practice

| Change | Type |
|---|---|
| New behaviour visible at a seam | `feat` |
| Wrong behaviour corrected | `fix` |
| Code moved or reshaped, behaviour unchanged, tests green before and after | `refactor` |
| Tests only | `test` |
| Wiki, README, comments | `docs` |
| Dependencies, build scripts, tooling | `build` or `ci` |
| Prefactoring ticket that prepares a feature | `refactor` |

## fstack footers

Every commit made during a run carries:

```
Ticket: <NN-slug>
Run: <run id>
```

A fix-pass commit after review uses `Ticket: review`. A bootstrap, distil or run-close commit uses `Ticket: none`.

## Merge commits

The merger writes non-fast-forward merges as `chore(merge): land <NN-slug>` with the same two footers. Fast-forwards need no message.

## Checks

- The reviewer reads `git log <base>..<integration> --format=%B` and lists each non-conforming message in `## Open` as `[convention:commits] <short sha> — <rule broken>`, and checks with `%(trailers:key=Ticket)` and `%(trailers:key=Run)` that the footers are real trailers.
- Projects where every commit comes from a run can enforce the footers with commitlint's `trailer-exists` rule (see [[quality-catalog]], Commit tooling). Elsewhere the reviewer's check is the guard, since the user's own commits carry no footers.
- At bootstrap, the conventions explorer looks for `commitlint.config.*`, `.commitlintrc*`, a `commit-msg` hook under `.husky/` or `.git/hooks/`, and the dominant pattern of the last fifty subjects in `git log`. When the project has its own rules, f-wiki writes a project `commits` page from them. When it has none, f-wiki tells the user once that a `commit-msg` hook with commitlint would enforce this page mechanically, and does not install it.
