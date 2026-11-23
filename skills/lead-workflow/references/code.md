# Code guide

## Orient

- Read CLAUDE.md, AGENTS.md, README and any architecture docs.
- Find the real verification commands in package.json scripts, Makefile, or CI config: lint, typecheck, tests, build. Use exactly these commands from here on.
- Note the conventions: folder structure, naming, state management, error handling, test style. New code follows them.

## Level examples

- **Trivial:** a typo or copy fix; a rename done with IDE refactoring or a codemod, even across many files; changing a config value; an obvious one-line fix where the cause is already known.
- **Medium:** a bug fix that needs investigation inside one feature; a new component, hook or endpoint that follows an existing pattern; a refactor within one module.
- **Large:** a feature spanning frontend and backend, or several modules; introducing a new pattern or library; migrating from one approach to another; performance work across modules.

## Risk examples

- **High:** authentication and authorization, payments, data migrations and schema changes, public API contracts, major dependency upgrades, CI/CD and infrastructure, personal data, anything irreversible.
- **Medium:** user-facing behavior, state management, caching, concurrency, error-handling paths.
- **Low:** styling, copy, internal naming, test-only changes, anything the typecheck and tests fully cover.

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

## Finish

Commit only when asked, following the repo's commit conventions.
