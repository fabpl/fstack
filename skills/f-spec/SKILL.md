---
name: f-spec
description: Writes the spec of a run from the current conversation, the wiki and the code. Records decisions already made, invents nothing, names the test seams first. Argument is the run id. Invoked by f-chief as a step of a run, or by the user to replay that step alone.
argument-hint: <run id>
effort: high
---

Write `.fstack/runs/$0/spec.md`. The deciding is already done, in the conversation, in `.fstack/runs/$0/interview.md` and in the wiki. You synthesise. You do not ask a new round of questions, and you do not decide anything the user did not decide.

Format rules: `${CLAUDE_PLUGIN_ROOT}/wiki/conventions/run-layout.md`.

## 1. Gather

- Read `.fstack/runs/$0/brief.md` and `interview.md`.
- Read `wiki/index.md`, then the `overview`, `term`, `convention` and `decision` pages the change touches. Use the project's terms from the `term` pages, never generic product words.
- If a fact about the code is missing (where a seam lives, what a module exposes), spawn one `fstack:explorer` with the question and the run directory. Explorers read the code for you.

## 2. Seams first

Before writing prose, list the seams where the change will be tested: module boundary, API, CLI, UI entry. Prefer seams that already exist and have tests. Pick the highest seam that still lets a failing test say what is wrong. The ideal count is one.

Show the seams to the user in a numbered list, one line each, with your reason. Wait for approval or corrections. Implementers test only at these seams; the reviewer flags any other.

## 3. Write

```yaml
---
run: <run id>
kind: spec
status: draft
date: YYYY-MM-DD
---
```

Sections, in this order, all present:

1. **Goal** — one paragraph: what changes for whom, in the project's words.
2. **Context** — wiki slugs and code paths an implementer must read. Pointers, no copies.
3. **Decisions** — one line per decision from the interview, with the answer as given. Precise values survive here: numbers, defaults, ordering guarantees, negative requirements ("never ..."). Do not soften them.
4. **Seams** — the approved list.
5. **Behaviour** — what the system does after the change, as checkable statements. Each statement is testable at one of the seams.
6. **Implementation decisions** — structure, names, modules, only where the user decided them. Mark anything left to the implementer as "implementer's choice".
7. **Testing decisions** — what is tested where, fixtures, and the gate commands from the project's `toolchain` page (format, lint, analyse, test). A gate the page marks `missing` stays missing here: the spec does not invent tooling. On a greenfield project the page reads `missing — chosen: <tool>`: the spec names those tools and makes their installation the first behaviour to deliver.
8. **Out of scope** — what was refused or deferred. The most useful lines on the page. On a greenfield project, state that the spec covers the first demoable increment only and list what the product will need later.
9. **Open** — questions no one answered, to be asked before build if they block.

Under 150 lines. If a section would state something the user never said, leave the line out rather than fill it.

## 4. Check

Re-read `interview.md` against the spec: every answer appears in Decisions or Out of scope.

Then two separate rounds:
1. The questions from Open, when there are any, in one round with your recommendation first. Fold the answers into the spec.
2. Approval, as a single question on its own, with nothing else in the round. Tell the user what to read first: Seams and Out of scope. `status: approved` on the user's yes; otherwise the spec stays `draft` and you report what is missing.

Return one line: the spec path and its status.
