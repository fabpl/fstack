---
name: f-build
description: Builds a run's tickets as a task graph, dispatching every ticket on the frontier to a parallel implementer in its own worktree, merging onto an integration branch, then one review and one fix pass. Sequential without git. Argument is the run id. Invoked by f-chief as a step of a run, or by the user to replay that step alone.
argument-hint: <run id>
effort: medium
---

Build every ticket under `.fstack/runs/$0/tickets/` and land the result on one integration branch. The architecture path is `.fstack/runs/$0/architecture.md` when that file exists with `status: approved`, `none` otherwise. A `draft` architecture on a `route: full` run means the architecture step did not finish: say so in one line and stop. Roles, models and communication rules: `${CLAUDE_PLUGIN_ROOT}/wiki/conventions/agent-roles.md`. Report format: `${CLAUDE_PLUGIN_ROOT}/wiki/conventions/run-layout.md`.

You dispatch and read reports. You do not read code and you do not implement.

## 1. Mode

Run `git rev-parse --is-inside-work-tree`. Run it again each time you recompute the frontier: a toolchain ticket may create the repository mid-run, and the mode follows it.

- **git**: integration branch `fstack/$0`, created from the current HEAD if absent. Each implementer runs in the background with `isolation: "worktree"`. Several run at once. When git appears mid-run, create the branch from the current HEAD and build the remaining tickets in this mode.
- **no git**: tell the user once that parallelism is off until a repository exists. Implementers run one at a time, in place, in ticket number order, with integration branch `none`. Skip every merger step.

Find the gate commands (format, lint, analyse, test) in the project's `wiki/conventions/toolchain.md`, then in the spec's Testing decisions. Pass them to every agent. A gate the page marks `missing` is passed as `missing`. When the page does not exist, pass every gate as `missing`, say so to the user once, and suggest a bootstrap.

Conventions to pass to every agent: the plugin-wide pages under `${CLAUDE_PLUGIN_ROOT}/wiki/conventions/` whose tags include `plugin-wide` (today: `commits`, `code-quality`; `quality-catalog` is for bootstrap only), plus the project's `wiki/conventions/` slugs the spec or tickets name. A project page with the same slug as a plugin page replaces it. Resolve `${CLAUDE_PLUGIN_ROOT}` to an absolute path before writing it into a prompt: agents receive absolute paths.

## 2. Graph

Read every ticket's frontmatter only. Build the graph from `blocked_by`. Keep a table in your reply as you go: ticket, status, branch, report path.

The frontier is the set of `todo` tickets whose blockers are all `landed`. Landed means merged on the integration branch (git) or reported `done` (no git): the frontier comes from those two facts alone.

## 3. Dispatch loop

Repeat until no ticket is `todo` or `building`:

1. For every ticket on the frontier, set `status: building` in its frontmatter and spawn one `fstack:implementer` with pointers only: ticket path, spec path, architecture path (or `none`), note paths the ticket names, wiki convention slugs, the paths of every report already under `reports/` (their Decisions and Observations are the map of what earlier tickets built), the project's `wiki/overview/` page when its status is `active`, integration branch, gate commands, run directory. Background and worktree in git mode.
2. When an implementer returns:
   - `failed` or `partial`: read its `## Open` before anything else. If one missing fact explains it, spawn an explorer for that fact and re-dispatch once with the note. Otherwise set `status: failed`, leave its dependents `todo` and unbuildable, and continue with the rest.
   - `done` in git mode: spawn one `fstack:merger` (foreground) with the ticket branch, the integration branch, the test gate command, the `commits` convention page path, the run directory. On merger `done`, set the ticket `landed`. On merger `failed`, set the ticket `failed` and report the conflicting files.
   - `done` without git: set the ticket `landed`.
3. Recompute the frontier and dispatch again. A ticket starts the moment its last blocker lands, not when the run ends.

## 4. Review, once

When every ticket is `landed` or `failed`, spawn one `fstack:reviewer` with: the integration branch and its base (or the working tree without git), the spec path, the architecture path (or `none`), the ticket directory, the wiki convention slugs, the gate commands, the run directory.

Read `.fstack/runs/$0/reports/review.md`. If `## Open` has findings, spawn one `fstack:implementer` with the review report path as its ticket, the spec path, the architecture path (or `none`), the integration branch, the gate commands. One pass. When it returns, run focused checks only on the fixed findings if needed, then close.

## 5. Close

- Git mode: run the test gate on the integration branch once. Remove implementer worktrees (`git worktree prune` after the harness cleans them). The integration branch stays where it is: merging into the user's branch and opening a pull request are the user's calls.
- Run files: during the build, ticket status edits and other changes under `.fstack/runs/$0/` stay uncommitted while implementers commit their own files. Now, when the project is a git repository, commit everything under `.fstack/runs/$0/` in one commit `chore(run): close $0` with footers `Ticket: none` and `Run: $0`, on the integration branch in git mode, on the current branch otherwise.
- Tell the user, in under ten lines: tickets landed and failed, integration branch, gate results, review finding count and how many were fixed. Every number comes from a report or a command output.

Return one line: `<landed>/<total> landed — <branch or 'in place'> — <review findings> findings, <fixed> fixed`.
