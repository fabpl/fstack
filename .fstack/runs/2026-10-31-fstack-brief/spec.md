---
run: 2026-10-31-fstack-brief
kind: spec
status: approved
date: 2026-10-31
---

# fstack specification

## 1. What fstack is

fstack is a Claude Code plugin. It targets Claude Code only: no other harness. It ships one chief-of-staff skill, `f-chief`, the step skills it orchestrates, and the subagent definitions those steps use.

`f-chief` behaves as the user's right hand. It runs in the main conversation on Fable at high effort. It protects its own context window by delegating everything that can be delegated, and it keeps the project's memory in a markdown wiki inside the project.

fstack is inspired by Matt Pocock's engineering skills (grilling, to-spec, to-tickets, implement-spec, tdd, code-review, retro). It depends on none of them. Every behaviour is rewritten inside fstack.

## 2. Missions

| # | Mission | Trigger |
|---|---|---|
| 0 | Bootstrap: scan the project and create the initial wiki. Greenfield when the folder is empty: minimal draft wiki, no exploration, toolchain chosen during the interview | First invocation in a project without `wiki/index.md` |
| 1 | Keep the wiki: record decisions, actions and facts worth remembering | Every run, at distillation time |
| 2 | Understand the request and deliver it by delegating | Any request that changes the project |
| 3 | Answer a plain question fast and precisely | Any request that does not change the project |

## 3. Routing

On every request, f-chief classifies it into one of three routes and says which one in a single line.

| Route | When | Approval | What happens |
|---|---|---|---|
| answer | The request asks for information, no change to the project | None | Read `wiki/index.md`, then the relevant pages. If insufficient, one explorer subagent (Sonnet) searches the code. Short, sourced reply. |
| quick | One change that fits one agent and needs no spec | None | One implementer subagent (Opus) with a context pointer to the relevant wiki pages. Report written to a run. |
| full | A change too large for one agent, or whose shape is still fuzzy | Required before starting | Grill → spec → tickets → build → review → distil. See section 4. |

The classification can be overridden by the user at any time. Pocock's rule applies: a spec is only worth its cost when the work spans more than one context window.

## 4. The full chain

Each step is a skill the user can also invoke on its own.

1. **Grill** (`f-chief`, main thread, not delegable). Rounds of questions until the chief and the user share one understanding. Every question carries a recommendation the user can accept with one word. Questions the code or the wiki can answer are answered by reading them, never asked. Resolved terms and hard decisions are written to the wiki as they resolve.
2. **Spec** (`/f-spec`). Writes `.fstack/runs/<run>/spec.md` from the conversation, the wiki and the code. Records decisions already made; invents nothing. Names the test seams before prose, as few as possible.
3. **Tickets** (`/f-tickets`). Splits the spec into tracer-bullet vertical slices, one file per ticket under `.fstack/runs/<run>/tickets/`, each declaring its blockers in frontmatter. Shown to the user as a numbered list for approval.
4. **Build** (`/f-build`). Reads the tickets as a task graph. Everything on the frontier runs at once. One implementer subagent per ticket, working red-green (TDD) at the seams the spec names. Each implementer writes its report to `.fstack/runs/<run>/reports/<ticket>.md`. A merger subagent lands each finished ticket on the integration branch and recomputes the frontier from what has merged, not from ticket status.
5. **Review**. Once every ticket has landed, one reviewer subagent (Opus) reviews the integration branch on two axes: does the code match the spec, and does it follow the conventions recorded in the wiki. Findings go to one implementer for a single fix pass. Then stop. No second broad review.
6. **Distil** (`/f-wiki`). f-chief reads the reports, writes the run page and updates or creates wiki pages. The final report to the user is generated from the reports, not from f-chief's memory.

## 5. Git modes

| Project state | Build behaviour |
|---|---|
| git repository | One worktree per implementer, one integration branch per run, fast-forward merges, frontier parallelism |
| no git | Tickets run sequentially in the project directory, one implementer at a time, no branch |

f-chief never creates a git repository on its own.

## 6. Memory, two levels

**Durable: `wiki/`** at the project root, committed. Written by f-chief only. Five page types, one fixed frontmatter schema, Obsidian-style `[[slug]]` links. Written for agents first, readable by humans. Defined in the convention page `wiki-format`.

**Working: `.fstack/runs/<YYYY-MM-DD>-<slug>/`** at the project root, committed. Holds the spec, the tickets and the agent reports of one run. Agents read and write here. f-chief distils it into the wiki at the end of the run. Defined in the convention page `run-layout`.

## 7. Agents

| Role | Model | Writes |
|---|---|---|
| explorer | Sonnet | Notes under `.fstack/runs/<run>/notes/` |
| implementer | Opus | Code, tests, one report |
| merger | Sonnet | Merge commits on the integration branch, one report |
| reviewer | Opus | One report with findings |

Communication to and from agents is sparse and goes through context pointers: paths to the spec, a ticket, a wiki page, a note, a commit. Never pasted summaries. Every report follows the bounded format defined in `run-layout`: frontmatter plus four sections, thirty lines maximum.

## 8. User-facing surface

| Command | Does |
|---|---|
| `/f-chief <request>` | Routes the request and runs the chosen route end to end |
| `/f-spec` | Writes the spec from the current conversation |
| `/f-tickets [spec path]` | Splits a spec into tickets |
| `/f-build [run]` | Builds a run's tickets |
| `/f-wiki` | Distils the current run into the wiki, or bootstraps the wiki |

Only `f-chief` carries `disable-model-invocation: true`: the user starts a run, the model never does. The four step skills stay model-invocable, because f-chief chains them through the Skill tool; their descriptions say they are invoked by f-chief or replayed by the user, so the model has no reason to reach for them on its own.

## 9. Economy rules

- Agents receive pointers, not content. Prompts stay under a screen.
- Reports are bounded at thirty lines and follow the fixed sections.
- Wiki pages are dense: facts, decisions, pointers. No narrative.
- One review, one fix pass, then stop.
- f-chief reads reports and the wiki index, never whole codebases.

## 10. Out of scope for v1

- SessionStart, Stop or PreCompact hooks.
- Automatic retrospective at the end of a run.
- GitHub Issues or any external tracker.
- Any harness other than Claude Code.
- Agents writing to the wiki.

## 11. Language

Plugin, skills, agents, wiki and runs are written in English regardless of the conversation language. f-chief replies to the user in the user's language.
