---
name: f-arch
description: Turns a run's approved spec into its architecture (structure, interfaces, shared files, decisions) through one architect agent, asks the user the hard-to-reverse choices, and gets the architecture approved before the tickets. Argument is the run id. Invoked by f-chief as a step of a run, or by the user to replay that step alone.
argument-hint: <run id>
effort: high
---

Get `.fstack/runs/$0/architecture.md` written and approved. The spec says what the change does; the architecture says how it is built. The architect agent reads the code and proposes; the user decides what is hard to reverse; you relay and record. You do not read code.

Format of the page: `${CLAUDE_PLUGIN_ROOT}/agents/architect.md`, section Output. Run layout: `${CLAUDE_PLUGIN_ROOT}/wiki/conventions/run-layout.md`.

## 1. Check

Read the frontmatter of `.fstack/runs/$0/spec.md`. If it is not `status: approved`, say so in one line and stop: the architecture builds on an approved spec only.

## 2. Dispatch

Spawn one `fstack:architect` (foreground) with pointers only: the spec path, the run directory, the wiki slugs the spec's Context names plus the active `overview` page and every active `decision` page, the project's `wiki/conventions/toolchain.md` when it exists, the note paths under `.fstack/runs/$0/notes/`. Resolve `${CLAUDE_PLUGIN_ROOT}` to an absolute path before writing it into the prompt.

Read its report. If `status` is not `done`, tell the user what is open, leave the architecture `draft`, and return.

The step allows two re-dispatches in all, across steps 3 to 5. Each one passes the same pointers plus the interview path and, when there are any, the slugs of the decision pages the user chose to supersede, marked as not binding.

## 3. Open

Read `architecture.md`. When its Open section has lines:

- A missing seam or a spec ambiguity goes back to the spec: show it to the user with your recommendation first. On the user's explicit yes, edit the spec's Seams or Behaviour, add the answer as a line in its Decisions, and append the question and answer to `interview.md`. Otherwise the architecture stays `draft`: say what blocks and return.
- A conflict with a `decision` or `convention` page: ask the user whether to keep the page (the architect redesigns) or supersede it. Note the slugs to supersede; their pages are written in step 6.

One round, at most four questions, your recommendation first in each.

## 4. Decisions

Ask the `gate: yes` lines of the Decisions section, in rounds of at most four questions. Each question carries the architect's recommendation first, marked as such, and the rejected options with their reason. Use the AskUserQuestion tool when it is available; otherwise number the questions. Append one line per question and answer to `interview.md`.

`gate: no` lines are not asked. The user can still challenge them at approval.

When an answer from step 3 or 4 departs from a recommendation, step 3 changed the spec, or a page is to be superseded, re-dispatch the architect. Otherwise mark each answered line in place: `recommended: <option>` becomes `chosen: <option> (user)`.

## 5. Approval

One question, alone in its round. Tell the user what to read first: Structure and Shared files. On the user's yes, set `status: approved` and go to step 6.

Otherwise append the corrections to `interview.md`. A correction that reshapes Structure, Interfaces or Data needs the architect: re-dispatch if one is left, then ask again. Any other correction you fold in yourself, then ask again. When a correction needs the architect and none is left, keep `status: draft`, tell the user what is missing, and return.

## 6. Record

Only after approval. Each `chosen: ... (user)` line marked `gate: yes`, and each page the user chose to supersede, becomes a `decision` page in `wiki/decisions/`, numbered after the last one, format per `${CLAUDE_PLUGIN_ROOT}/wiki/conventions/wiki-format.md`, with `source_run: $0`. A superseded page gets `status: superseded`, and the new page names it in `supersedes`. Add each page to `wiki/index.md`.

Return one line: the architecture path, its status, and the number of decision pages written.
