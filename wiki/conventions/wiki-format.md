---
type: convention
title: Wiki format
status: active
related: [run-layout, 0002-single-writer-wiki]
supersedes:
source_run: 2026-10-31-fstack-brief
tags: [wiki, memory]
updated: 2026-11-05
---

# Wiki format

The wiki lives in `wiki/` at the project root and is committed. Only f-chief writes to it (see [[0002-single-writer-wiki]]). Pages are written for agents first: facts, decisions, pointers, no narrative. Humans read it in Obsidian or any markdown viewer.

## Layout

```
wiki/
  index.md            one line per page, grouped by type, kept by f-chief
  overview/           what the project is, its modules, how it runs
  decisions/          one hard decision per page, numbered NNNN-slug.md
  terms/              one project term per page
  conventions/        rules agents must follow: code, process, layout
  runs/               one synthesis page per run, named like the run
```

## Frontmatter, required on every page

```yaml
type: overview | decision | term | convention | run
title: Human title
status: active | draft | superseded
related: [slug, slug]        # other pages this one leans on
supersedes:                  # slug of the page this one replaces, or empty
source_run: YYYY-MM-DD-slug  # the run that produced or last changed it
tags: []
updated: YYYY-MM-DD
```

## Links

Inside the body, link pages with `[[slug]]` where slug is the file name without extension. Slugs are unique across the whole wiki, so no folder prefix is needed. A link to a page that does not exist yet is allowed and marks a page worth writing.

## Page rules

- **overview**: structure and behaviour of the project or a module. Points to code paths. Rewritten in place when the project changes.
- **decision**: one decision that is hard to reverse, surprising without context, and a real trade-off. All three or it is not a decision page. Sections: Context, Decision, Consequences. Never edited after acceptance: a new decision supersedes it.
- **term**: the project's own word for a thing. One tight definition, no implementation detail.
- **convention**: a rule agents must follow. Reviewers check code against these pages.
- **run**: synthesis of one run. Sections: Goal, Outcome, Decisions made, Observations, Open. Written from the run's reports, under twenty lines.

## Precedence

The plugin's own wiki, under `${CLAUDE_PLUGIN_ROOT}/wiki/conventions/`, holds plugin-wide conventions, tagged `plugin-wide`. They apply in every project. A project page in `wiki/conventions/` with the same slug overrides the plugin page entirely; agents are pointed at one or the other, never both. Today's plugin-wide pages: [[commits]], [[code-quality]], [[security]], [[quality-catalog]] (bootstrap only).

## Index

`wiki/index.md` is the only file f-chief reads on every request. It lists every page as `- [[slug]] — one-line hook`, grouped by type. It is rewritten whenever a page is added, renamed or superseded.
