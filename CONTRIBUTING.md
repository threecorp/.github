# Contributing to Extremo repositories

This file is inherited by every repo in the `threecorp` organization that does not ship its own `CONTRIBUTING.md`.

## Before you file an issue

Read [`ISSUE_GUIDELINES.md`](./ISSUE_GUIDELINES.md). Every issue must have:

- A Type (Task / Bug / Feature) — set via the native Issue Types sidebar, not via labels.
- An Area — set on the Project; must match the `[area]` tag in the title.
- A label set — pick from the shared taxonomy (security / tech-debt / architecture / performance / ui-ux / breaking-change / needs-triage / blocked + GitHub defaults).
- Links to any parent issue, sub-issues, blockers, and related PRs.
- Project linkage to [Extremo Platform](https://github.com/orgs/threecorp/projects/1).

## Before you open a PR

- Branch name: `<type>/<issue-number>-<slug>` (e.g. `feat/75-scope-books-list`).
- PR body: include `Closes #<n>` (or `Closes threecorp/<repo>#<n>` for cross-repo).
- Run the project-specific verification commands listed in that repo's `CLAUDE.md` or `README.md` before requesting review.
- CI must pass.

## Repository-specific conventions

- **extremo-db** changes land first for any schema work.
- **extremo-proto** changes land before any consumer (api / view / mobile) starts its work.
- **extremo-api** requires `make build format linter test integration-test` before PR.
- **extremo-view** requires `npm run pre-commit` (format + lint + typecheck) before PR.
- **extremo** (Flutter) requires `dart run build_runner build --delete-conflicting-outputs` after generated-code changes.

## AI coding agents

Human and AI authors follow the same rules. If an agent cannot verify a change end-to-end (e.g., testing tools fail), it must say so explicitly in the PR description rather than claim completion.
