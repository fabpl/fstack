---
run: 2026-10-31-fstack-brief
kind: interview
date: 2026-10-31
---

# Interview log

Questions asked by the chief, answers given by the user. One line each.

| # | Question | Answer |
|---|---|---|
| 1 | Depend on the installed Pocock plugin, fork it, or hybrid? | Neither: inspired by Pocock, no dependency, everything rewritten. |
| 2 | Where do wiki and runs live, versioned? | `wiki/` + `.fstack/` at project root, both committed. |
| 3 | Who is the wiki written for? | Agents first, readable by humans, Obsidian-compatible links. |
| 4 | Does f-chief wait for approval before routing? | Only before the full chain. Questions and quick tasks run unasked. |
| 5 | Where do tickets live? | Local markdown in the run directory. |
| 6 | Subagent models? | Explorers in Sonnet, implementers and reviewers in Opus, f-chief in Fable. |
| 7 | Project without git? | Sequential fallback, no worktrees. Full parallelism with git. |
| 8 | User-facing commands? | `/f-chief` plus replayable steps `/f-spec`, `/f-tickets`, `/f-build`, `/f-wiki`. |
| 9 | Enrichment ideas kept? | Wiki bootstrap only. No SessionStart hook, no auto retro, no Stop hook. |
| 10 | Who writes the wiki? | f-chief alone. Agents write run reports only. |
| 11 | Grilling intensity? | Full Pocock-style rounds, each question carrying a recommendation. |
| 12 | Language? | Everything in English, plugin and wiki. |
| 13 | Wiki page types? | Five: overview, decision, term, convention, run. |
| 14 | Agent report format? | Frontmatter + Done/Decisions/Observations/Open, 30 lines max. |
| 15 | Implementation guarantees? | TDD per ticket, final two-axis review (spec, conventions), one fix pass. |
| 16 | How are plain questions answered? | Wiki first, Sonnet explorer if the wiki is not enough. |
