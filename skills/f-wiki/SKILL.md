---
name: f-wiki
description: Keeps the project wiki. `bootstrap` scans a project without a wiki and writes the first pages; `greenfield` writes the minimal wiki of an empty project; `distil <run id>` turns a run's agent reports into the run page, wiki updates and the final report. Only f-chief writes the wiki. Invoked by f-chief as a step of a run, or by the user to replay that step alone.
argument-hint: bootstrap | greenfield | distil <run id>
effort: medium
---

You are the wiki's only writer. Page types, frontmatter, links and index rules: `${CLAUDE_PLUGIN_ROOT}/wiki/conventions/wiki-format.md`. Read it before writing a page for the first time in a session.

Mode: $0. Run id, when given: $1.

## Mode: bootstrap

The project has no `wiki/index.md`. Create the first wiki from the code, without reading the code yourself.

1. Create run `YYYY-MM-DD-bootstrap` with a `brief.md` (`route: bootstrap`).
2. Spawn three `fstack:explorer` agents in parallel, each writing a note under the run's `notes/`:
   - **structure**: what the project is, its entry points, modules and their responsibilities, how to run and test it.
   - **conventions**: coding rules in force, from config files (linters, formatters, CI), CONTRIBUTING, existing docs, and recurring patterns in the code. Quality gates in particular: read the catalog at the absolute path you give it (resolve `${CLAUDE_PLUGIN_ROOT}/wiki/conventions/quality-catalog.md` before spawning) first, detect the stack signals it lists, find each gate's command in the project's configs, scripts and CI, confirm it against the installed tool's `--help`, and record which gates are missing with the catalog's recommendation. The setup command as well: what CI or the README runs to install dependencies and prepare the environment before the gates, else the catalog's Setup row. Commit rules too: `commitlint.config.*`, `.commitlintrc*`, a `commit-msg` hook under `.husky/` or `.git/hooks/`, and the dominant pattern of the last fifty `git log` subjects. Facts only.
   - **terms**: the project's own vocabulary: names that appear in code, docs and tests with a specific meaning.
3. From the reports and notes, write:
   - `wiki/overview/<project>.md` — what it is, module map with paths, how to run and test.
   - `wiki/overview/<module>.md` — one per major module when the project has more than three. Otherwise fold them into the project page.
   - `wiki/conventions/toolchain.md` — the four gate commands per stack block, in the layout the catalog's Project toolchain page section gives, with `missing — recommended: <tool>` for gaps and the `setup` row. Install nothing.
   - `wiki/conventions/<topic>.md` — one page per rule family found (style, tests, layout, commits). Rules only, each one checkable. Write a project `commits` page only when the project has its own commit rules; it then overrides the plugin-wide page. When the project has no commit tooling, tell the user once that a `commit-msg` hook with commitlint would enforce the plugin convention mechanically. Do not install it.
   - `wiki/terms/<term>.md` — one per term the notes found. Tight definition, no implementation detail.
   - `wiki/index.md`.
4. Write the run page `wiki/runs/<run id>.md` and add it to the index. When the project is a git repository, commit the wiki and run files in one commit `docs(wiki): bootstrap` with footers `Ticket: none` and `Run: <run id>`.
5. Tell the user what the wiki now holds, in under ten lines, which pages looked thin, and which gates are missing. When gates are missing, end with one proposed run, `set up the missing quality gates`, that the user may start with f-chief. Do not start it.

No `decision` pages at bootstrap: decisions need a human who made them. Record candidate decisions found in the code under the run page's `## Open`.

## Mode: greenfield

The project folder is empty. There is nothing to explore, so spawn no agent and create no run.

1. Write `wiki/overview/<folder name>.md` with `status: draft`, body: `Greenfield project. Nothing built yet. The first interview settles purpose, first increment, stack, constraints and quality gates; this page is rewritten when the first run lands.`
2. Write `wiki/index.md` with that one page.
3. Return one line: `greenfield wiki written — <overview path>`.

No `toolchain.md` yet: f-chief writes it during the interview when the stack resolves.

## Mode: distil

Turn `.fstack/runs/$1/` into memory.

1. Read every file under `.fstack/runs/$1/reports/`. Read `brief.md`, and `interview.md`, `spec.md` and `architecture.md` when they exist. Do not read notes unless a report points to one for a fact you must record.
2. Write `wiki/runs/$1.md`: Goal, Outcome, Decisions made, Observations, Open. Under twenty lines. Every line traces to a report or a run file.
3. Promote report lines:
   - `## Decisions` lines that pass all three gates (hard to reverse, surprising without context, a real trade-off) become a `decision` page, numbered after the last one, unless a `decision` page with `source_run: $1` already records it (f-arch writes them during the run). The rest stay in the run page.
   - `## Observations` lines about structure update the relevant `overview` page in place. Lines about a rule become or extend a `convention` page. A recurring name with a specific meaning becomes a `term` page.
   - A report whose `## Done` installs or configures a gate tool updates the project's `toolchain.md`: the gate's `missing — chosen: <tool>` becomes the real command, `source: project`. A report whose `## Done` creates the project skeleton or changes how dependencies install sets the `setup` row the same way.
   - On a greenfield project, the first landed run rewrites the draft overview page from the reports and sets `status: active`.
   - `## Open` lines go to the run page's Open section. An open item that blocks future work also goes to the relevant overview page under an `## Open` heading.
   - A page that a report shows to be wrong is corrected now. A superseded decision gets `status: superseded` and the new page names it in `supersedes`.
4. Set `source_run` and `updated` on every page you touched. Rewrite `wiki/index.md`. When the project is a git repository, commit the pages and run files you touched in one commit `docs(wiki): distil $1` with footers `Ticket: none` and `Run: $1`.
5. Produce the final report for the user, under fifteen lines, from the reports only: what landed, what was decided, what is open, where the branch or files are. Say what was not verified.

Return one line: pages created, pages updated, and the run page path.
