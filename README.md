# fstack

> [!WARNING]
> **Beta, work in progress.** fstack is at v0.2.0 and still being shaped by live runs. Skills, agents, file layouts and conventions may change without notice or migration path. Parallel mode (worktrees, merger, integration branch) has not yet been exercised on a real project.

A Claude Code plugin that gives a project a chief of staff.

`/fstack:f-chief <request>` routes the request, grills you until the understanding is shared, delegates the work to parallel subagents, and keeps a markdown wiki in the project as its memory. Inspired by Matt Pocock's engineering skills, dependent on none of them.

## What you get

| Command | Does |
|---|---|
| `/fstack:f-chief <request>` | Routes to answer, quick or full, and runs it end to end |
| `/fstack:f-spec <run>` | Writes the spec of a run from the conversation |
| `/fstack:f-arch <run>` | Has the architect decide how an approved spec is built, asks you the hard choices |
| `/fstack:f-tickets <run>` | Splits a spec into tickets with blocking edges, along its architecture |
| `/fstack:f-build <run>` | Frontier-parallel build, test-first, one review, one fix pass |
| `/fstack:f-wiki bootstrap` or `distil <run>` | Creates the wiki, or turns a run's reports into wiki pages |

Five agents do the work: `explorer` (medium effort) and `merger` (low) on Sonnet, `architect`, `implementer` and `reviewer` on Opus at high effort. f-chief itself runs on Fable at high effort; f-build and f-wiki step down to medium.

## Routes

- **answer**: a question. Wiki first, one explorer if the wiki is not enough. Nothing written.
- **quick**: one change, one implementer, no spec. Proceeds without asking.
- **full**: grill → spec → architecture → tickets → build → review → distil. Announced, then waits for your yes.

## What it leaves in your project

```
wiki/                 durable memory, committed, written by f-chief only
  index.md            read on every request
  overview/  decisions/  terms/  conventions/  runs/
.fstack/runs/<date>-<slug>/   working memory of one run, committed
  brief.md  interview.md  spec.md  architecture.md  tickets/  notes/  reports/
```

Two plugin-wide quality rules ship with it: Conventional Commits on every commit, and four quality gates (format, lint, analyse, test) that implementers must pass and the reviewer re-runs. Bootstrap detects the project's stack and records the gate commands in a `toolchain` page, recommending tools from a catalog for the gaps without installing them.

Every agent ends with one report: frontmatter plus Done, Decisions, Observations, Open, thirty lines maximum. The wiki and the final report are built from those reports.

## Conventions

The rules f-chief applies to every project are pages of this plugin's own wiki, under `wiki/conventions/`. The plugin is its own first user: `wiki/` and `.fstack/` here hold fstack's spec, decisions and glossary.

## Install

```bash
claude plugin validate .
```

The repository is its own marketplace (`.claude-plugin/marketplace.json`), so a local install is:

```bash
claude plugin marketplace add /path/to/fstack
```

```bash
claude plugin install fstack@fstack
```

Claude Code reads the plugin from that folder directly, so edits are live at the next session. Requires Claude Code with plugin support, git for parallel builds (sequential without it), and access to the Fable, Opus and Sonnet models.

## Status

v0.2.0: architect agent and f-arch step added; not yet exercised on a real project.
