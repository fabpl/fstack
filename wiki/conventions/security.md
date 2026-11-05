---
type: convention
title: Security rules
status: active
related: [code-quality, agent-roles]
supersedes:
source_run:
tags: [security, quality, plugin-wide]
updated: 2026-11-05
---

# Security rules

Plugin-wide convention: applies in every project, whatever its stack. A project page with the same slug overrides it. It is not a gate: no command runs. Implementers follow the rules; the reviewer checks the run's diff against them like any other convention page.

## Rules

- No secret in the diff: API key, token, password, private key, connection string that carries credentials. Configuration reads them from the environment; the committed example file holds placeholders.
- Untrusted input (request, file, queue message, CLI argument, third-party response) reaches a database query, a shell command, an HTML page, a file path or a redirect only through the mechanism the project already uses for it: bound parameters, escaping, an allow-list.
- A new route, command, job or webhook checks who is calling before it acts, unless the spec says it is public.
- A new dependency is named in the report's `## Decisions` with the reason it was needed.
- No secret and no personal data in a log line, an error message or a test fixture.

## Proof

A finding states what an attacker or a mistake does and what happens, not what might be possible.

- A secret finding names the file and line in the run's diff, or the commit. A file that is gitignored and absent from the run's commits is not a finding.
- An input finding names the path: where the value enters, where it lands, and that nothing on the way neutralises it.
- A pattern match alone is not a finding. Read the code around it first.

## Rules for the reviewer

Each violation is one finding: `[convention:security] path:line — defect — fix`. Security findings rank as the most severe in `## Open`: when the thirty lines run out, they are the ones kept.
