# Code guide

## Orient

- Read CLAUDE.md, AGENTS.md, README and any architecture docs.
- Find the real verification commands in package.json scripts, Makefile, or CI config: lint, typecheck, tests, build. Use exactly these commands from here on.
- Note the conventions: folder structure, naming, state management, error handling, test style. New code follows them.

## Trivial examples

A typo or copy fix, a one-line bug fix, renaming a variable in one file, a small config change. Roughly 20 lines or less, in 1-2 files, with no change to behavior other than the intended one.

## Constraints for briefs

- Follow the existing architecture and conventions.
- No comments that narrate what was changed. Only comments that are genuine best practice (explaining a non-obvious why).
- Change only the files you own. No drive-by refactors.
- Do not commit.
- For parallel subagents, use worktree isolation where available.

## Checks

1. Lint
2. Typecheck
3. Tests that cover the changed code (the full suite for Large tasks)
4. Build, if the change touches build config, dependencies, or entry points

## Review criteria

- Does it meet the acceptance criteria?
- Correctness: edge cases, error handling, null and empty states, async and race conditions.
- Fits the existing architecture and conventions.
- No unrelated changes, no leftover debug code, no narrating comments.
- New behavior has tests; changed behavior has updated tests.
- No secrets, no unsafe input handling, no new injection or XSS paths.

## Risky (always add an `opus` reviewer)

Authentication and authorization, payments, data migrations and schema changes, public API contracts, dependency upgrades, CI/CD and infrastructure, anything touching personal data.

## Finish

Commit only when asked, following the repo's commit conventions.
