---
type: convention
title: Quality tool catalog
status: active
related: [code-quality, commits]
supersedes:
source_run: 2026-10-31-fstack-brief
tags: [quality, plugin-wide, bootstrap]
updated: 2026-10-31
---

# Quality tool catalog

Read at bootstrap by f-wiki to fill the gates of [[code-quality]] that a project lacks, and during a greenfield interview by f-chief to recommend the gates of a project that has no stack yet. Entries are defaults, not truths: what the project already uses wins, and the conventions explorer confirms every command against the installed version's `--help` before it is written into the project's `toolchain.md`.

## By stack signal

| Signal in the project | format | lint | analyse | test |
|---|---|---|---|---|
| `package.json` with `vite` or Vite+ (`vp`) | Vite+ `vp fmt` (oxfmt) | Vite+ `vp lint` (oxlint) | `tsc --noEmit`, or Vite+'s type check when the installed version has one | Vitest via `vp test` |
| `package.json`, TS/JS, no Vite | Biome `biome format`, else Prettier | Biome `biome lint`, else ESLint | `tsc --noEmit` | the `test` script |
| `composer.json` | PHP-CS-Fixer `php-cs-fixer fix` | PHP-CS-Fixer rule set (`check` mode) | PHPStan `phpstan analyse`, level 6 minimum on a new project, current level plus baseline on an existing one | PHPUnit or Pest |
| `composer.json` with `laravel/framework` | Pint `pint` | Pint `pint --test` | Larastan (PHPStan with the Larastan extension) | `php artisan test` |
| `pyproject.toml` or `requirements*.txt` | Ruff `ruff format` | Ruff `ruff check` | mypy, else pyright | pytest |
| `go.mod` | `gofmt`, `goimports` | golangci-lint | `go vet`, staticcheck | `go test ./...` |
| `Cargo.toml` | `rustfmt` | `cargo clippy` | `cargo check` | `cargo test` |

A project matching several rows (a Laravel API with a Vite front end) gets one `toolchain.md` with one block per stack, each block naming its working directory.

## Where to look for existing tools

- Config files at the root or under a `config/` folder: `biome.json`, `.eslintrc*`, `eslint.config.*`, `.prettierrc*`, `tsconfig.json`, `.php-cs-fixer*.php`, `pint.json`, `phpstan.neon*`, `ruff.toml`, `.golangci.yml`, `rustfmt.toml`, `clippy.toml`.
- Scripts: `package.json` `scripts`, `composer.json` `scripts`, `Makefile`, `justfile`, `Taskfile.yml`.
- CI: `.github/workflows/*.yml`, `.gitlab-ci.yml`: the commands CI runs are the project's real gates.
- Baselines: `phpstan-baseline.neon`, ESLint ignore files, Biome `ignore` entries.

## Project toolchain page

f-wiki writes `wiki/conventions/toolchain.md` in the project:

```yaml
---
type: convention
title: Toolchain
status: active
related: [code-quality]
tags: [quality, toolchain]
---
```

Body: one table per stack block with `gate`, `command`, `cwd`, `source` (`project`, `ci` or `catalog`). A gate with no command is written as `missing — recommended: <tool>`. Then one line: how to install the missing ones, as a proposed run, not an action.

## Commit tooling

- Node stacks: commitlint with `@commitlint/config-conventional` in a `commit-msg` hook (husky or lefthook). Settings that match [[commits]]: `header-max-length` 72, `body-max-line-length` 72, `footer-leading-blank` as an error. Where every commit comes from a run, add `'trailer-exists': [2, 'always', 'Run:']`; otherwise leave the footers to the reviewer.
- Other stacks: a `commit-msg` hook running `npx commitlint` when Node is present, else a shell check of the header pattern.

## Adding an entry

One row per stack signal, tools in the order the gates run, commands as installed by default. Keep it to tools with a stable CLI and a `--help`.
