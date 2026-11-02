---
name: f-tickets
description: Splits a run's spec into tracer-bullet tickets with blocking edges, one markdown file per ticket in the run directory, after the user approves the breakdown. Argument is the run id. Invoked by f-chief as a step of a run, or by the user to replay that step alone.
argument-hint: <run id>
effort: high
---

Split `.fstack/runs/$0/spec.md` into tickets under `.fstack/runs/$0/tickets/`. Ticket frontmatter and sizing rules: `${CLAUDE_PLUGIN_ROOT}/wiki/conventions/run-layout.md`.

## 1. Gather

Read the spec. Read the wiki `convention` pages it names. If the spec's Context leaves a code question open (what exists today at a seam, which files a change will fan into), spawn one `fstack:explorer` per question, in parallel, writing notes under `.fstack/runs/$0/notes/`. Explorers read the code for you.

## 2. Draft slices

Each ticket is a tracer bullet: a narrow but complete path through every layer the behaviour needs, demoable or verifiable on its own, sized for one fresh context window. A slice of one layer alone is a task inside a ticket, not a ticket.

Aim for the widest frontier the rules below allow. A chain where each ticket blocks the next is a sign that a shared file belongs in an earlier ticket.

- Prefactoring that makes the change easy comes first, as its own ticket.
- Greenfield: ticket `01-toolchain` creates the project skeleton, runs `git init` when the folder has no repository, installs the gates chosen in `toolchain.md` with one trivial test green through all four gates, and declares every file the later tickets would otherwise fight over (content schemas, route or collection registry, shared config), each with its entries named so a later ticket fills its own. Every other ticket is blocked by it, and by nothing else unless the spec forces an order.
- A wide mechanical refactor (rename a shared symbol, retype a column) is the exception: sequence it as expand, migrate in batches, contract. Each batch is a ticket blocked by the expand; the contract is blocked by every batch.
- Two tickets that will touch the same shared file (a registry, a message catalogue, a type) get a blocking edge between them, or the note fixes the exact names each one adds. Worktrees postpone collisions to merge time; they do not remove them.
- Each ticket names its seams, from the spec. A ticket needing a seam the spec lacks goes back to the user.

## 3. Quiz the user

Show the breakdown as a numbered list. For each ticket: title, blocked by, delivers (one sentence). Ask, with your recommendation first:

- Is the granularity right?
- Does each edge gate the ticket for real?
- Should any ticket be merged or split?

Iterate until approved.

## 4. Write

One file per ticket, `.fstack/runs/$0/tickets/NN-slug.md`, numbered in a valid topological order so that a sequential build can follow the numbers.

```yaml
---
ticket: NN-slug
title: Short descriptive name
blocked_by: [NN-slug]
delivers: one sentence
seams: [path or symbol]
status: todo
---
```

Body, under thirty lines: what to build, in the project's terms; read first (spec sections, note paths, wiki slugs); acceptance criteria as checkable lines; what this ticket must not touch.

Return one line: the ticket count, how many have no blockers, and the directory path.
