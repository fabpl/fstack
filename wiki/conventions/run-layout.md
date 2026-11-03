---
type: convention
title: Run layout and report format
status: active
related: [wiki-format, agent-roles, run, ticket, report, architecture]
supersedes:
source_run: 2026-11-03-architect-step
tags: [runs, memory, economy]
updated: 2026-11-03
---

# Run layout and report format

A [[run]] is one pass of f-chief on one request. Everything agents read and write during the run lives under `.fstack/runs/<YYYY-MM-DD>-<slug>/`, committed. The run directory is the working memory shared between agents. f-chief distils it into the wiki at the end.

## Layout

```
.fstack/runs/2026-10-31-add-export/
  brief.md          the user's request, verbatim, plus the chosen route
  interview.md      grilling log: one line per question and answer
  spec.md           the spec, written by /f-spec
  architecture.md   how the spec is built, written by the architect for /f-arch
  tickets/
    01-slug.md      one file per ticket
  notes/
    <topic>.md      explorer notes, read by implementers
  reports/
    architecture.md architect report
    01-slug.md      one report per implementer, named after its ticket
    merge-01.md     merger reports
    review.md       reviewer report
```

A `quick` route creates only `brief.md` and `reports/`: no spec, no [[architecture]]. An `answer` route creates no run.

## Ticket frontmatter

```yaml
ticket: 01-slug
title: Short descriptive name
blocked_by: []            # ticket ids that must land first
delivers: one sentence, the end-to-end behaviour this ticket makes work
seams: [path or symbol]   # where it is tested, from the spec
status: todo | building | landed | failed
```

Body: what to build, which wiki pages and notes to read first, acceptance criteria. Under thirty lines.

## Report format

Every agent writes exactly one report. Thirty lines maximum, frontmatter included. Four sections, all present even if empty.

```yaml
---
agent: implementer | explorer | architect | merger | reviewer
ticket: 01-slug            # or none
status: done | partial | failed
files: [path, path]        # touched or created
---
## Done
What landed, one line per item.
## Decisions
Choices made that the ticket did not dictate, with the reason.
## Observations
Facts about the codebase worth keeping: surprises, debt, conventions found.
## Open
What is not done, not verified, or needs a human.
```

Decisions and Observations are the lines f-chief promotes to the wiki. Write them as if they will be read in six months by someone who was not there.
