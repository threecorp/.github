# threecorp

Threecorp builds **Extremo**, a multi-tenant booking / reservation SaaS platform.

## Extremo platform repositories

| Repo | Stack | Role |
|---|---|---|
| [extremo](https://github.com/threecorp/extremo) | Flutter / Dart | Mobile app (iOS / Android / macOS / Web) |
| [extremo-api](https://github.com/threecorp/extremo-api) | Go + gRPC | Backend API server |
| [extremo-db](https://github.com/threecorp/extremo-db) | MySQL + sqldef | Database schema |
| [extremo-proto](https://github.com/threecorp/extremo-proto) | Protobuf + Buf | Shared API definitions |
| [extremo-view](https://github.com/threecorp/extremo-view) | Remix + React + TS | Web frontend |

## Cross-repo coordination

- **Project board**: [Extremo Platform (org Project #1)](https://github.com/orgs/threecorp/projects/1) aggregates issues and PRs across all five repositories.
- **Issue authoring rules**: [`ISSUE_GUIDELINES.md`](https://github.com/threecorp/.github/blob/main/ISSUE_GUIDELINES.md). All humans and AI agents filing issues must read it.
- **Default issue forms**: this repo ships the org-level [`.github/ISSUE_TEMPLATE/`](https://github.com/threecorp/.github/tree/main/.github/ISSUE_TEMPLATE) forms that are inherited by any extremo* repo without its own.

## Cross-project change order

DB → proto → api → view → mobile. Never modify a downstream project before the upstream contract is in place.
