## Working with our repositories

Each repository ships its own `README.md`, `CLAUDE.md` (agent instructions), and stack-specific setup notes. Start there before asking questions.

### Org-wide defaults

This `.github` repository ships organization-level defaults inherited by all repos that don't override them:

- [`CONTRIBUTING.md`](https://github.com/threecorp/.github/blob/main/CONTRIBUTING.md) — branching, review, and verification conventions
- [`.github/ISSUE_TEMPLATE/`](https://github.com/threecorp/.github/tree/main/.github/ISSUE_TEMPLATE) — Bug / Feature / Task issue forms

### Product-specific issue authoring

Some products coordinate work across multiple repositories via an org-level [GitHub Project](https://github.com/orgs/threecorp/projects). When a product has its own authoring guide, link to it from the repo's `README.md`.

- **Extremo**: [`ISSUE_GUIDELINES.md`](https://github.com/threecorp/.github/blob/main/ISSUE_GUIDELINES.md) — required reading for anyone (human or AI agent) filing issues against `extremo*` repos.

## Contributing

See [`CONTRIBUTING.md`](https://github.com/threecorp/.github/blob/main/CONTRIBUTING.md). TL;DR:

- Write code and issues in **English**.
- Follow the product-specific rules in each repo's `CLAUDE.md`.
- AI coding agents must state explicitly when they cannot verify a change end-to-end, rather than claim completion.

<p align="center">
  <img src="./assets/site-height.png" alt="Astronaut on Mars holding a rubber duck float" width="320">
</p>
