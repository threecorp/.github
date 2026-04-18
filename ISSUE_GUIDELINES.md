# Extremo Issue Authoring Guidelines

> **Audience:** human developers and AI coding agents who file issues in any `threecorp/extremo*` repository.
> **Scope:** `extremo`, `extremo-api`, `extremo-db`, `extremo-proto`, `extremo-view`.
> **Central project board:** [Extremo Platform (org-level Project #1)](https://github.com/orgs/threecorp/projects/1).

All issues filed in these repositories MUST follow this guide. The rules exist so that the cross-repo project board stays usable — search, filter, roadmap views, sub-issue hierarchies, and automations all depend on consistent metadata.

---

## TL;DR — mandatory checklist for every new issue

| # | Metadata | Rule |
|---|---|---|
| 1 | **Title** | Start with a `[Area]` tag, e.g. `[booking] Fix double-booking race`. Use English. |
| 2 | **Type** (GitHub Issue Type) | One of `Task`, `Bug`, `Feature`. Never leave empty. |
| 3 | **Labels** | Pick from the shared taxonomy in §3. At least one label beyond the type. |
| 4 | **Project** | Link to `threecorp/projects/1` (Extremo Platform) — auto if Auto-add workflow is on. |
| 5 | **Status** | Set in Project. New items default to `Backlog`. |
| 6 | **Priority** | Set in Project (`P0`–`P3`). Required if Status ≥ `Ready`. |
| 7 | **Size** | Set in Project (`XS`–`XL`). Required if Status ≥ `Ready`. |
| 8 | **Area** | Set in Project single-select. Should match the title tag. |
| 9 | **Iteration** | Set only when the issue is committed to a sprint. |
| 10 | **Milestone** | Use release or epic-level milestones when applicable. See §5. |
| 11 | **Parent / sub-issues** | If this is part of a larger initiative, link as a sub-issue of the tracking parent. See §6. |
| 12 | **Development** | Link related PRs, branches, and commits via the native Development sidebar. See §7. |
| 13 | **Body** | Use the template for the chosen Type (Task/Bug/Feature). See §8. |

---

## 1. Repository scope

| Repository | Stack | Responsibility |
|---|---|---|
| `extremo` | Flutter/Dart | Mobile app (iOS / Android / macOS / Web) |
| `extremo-api` | Go + gRPC | Backend API server |
| `extremo-db` | MySQL + sqldef | Database schema |
| `extremo-proto` | Protobuf + Buf | Shared API definitions |
| `extremo-view` | Remix + React + TS | Web frontend |

**Cross-project changes** must be ordered DB → proto → api → view → mobile. If an issue touches multiple repos, create a **parent issue** in the most-upstream repo and **sub-issues** in each downstream repo. Sub-issues can be cross-repo (see §6).

---

## 2. Issue Types (GitHub-native)

The `threecorp` organization uses the three default issue types. Select exactly one when creating any issue.

| Type | When to use |
|---|---|
| **Task** | A defined unit of work with clear acceptance criteria: refactors, chores, tech-debt cleanup, migrations, test additions, DB schema changes, proto updates, build/CI changes. |
| **Bug** | Unexpected behavior in existing functionality. Includes regressions, crashes, wrong output, performance regressions, security defects. |
| **Feature** | New user-visible capability or API. Anything that extends the product surface area. |

Type is set via the Issue **sidebar → Type** field, not via labels. The legacy `bug`/`enhancement` labels are kept for backward compatibility but should be considered deprecated — set the Type field instead.

---

## 3. Label taxonomy

Labels are **orthogonal** to the Type field and to the Project `Area`. Use as many as apply. Every label listed here exists in all 5 repos.

### 3.1 Shared cross-cutting labels (mandatory vocabulary)

| Label | Meaning |
|---|---|
| `security` | Security-related: vulnerability, hardening, auth/authz concern, secrets, privacy leak. |
| `tech-debt` | Refactoring needed. Does not change behavior. |
| `architecture` | Affects module boundaries, layering, DI, cross-service contract. |
| `performance` | Latency, throughput, memory, bundle size, query cost. |
| `ui-ux` | Frontend layout, interaction, accessibility, copy. |
| `breaking-change` | Changes an API/behavior in an incompatible way. Requires coordinated rollout. |
| `needs-triage` | Not yet groomed. Blocks nothing but flags the issue for a maintainer pass. |
| `blocked` | Cannot proceed until an external dependency resolves. Must reference the blocker in the body. |

### 3.2 GitHub default labels (kept)

`documentation`, `duplicate`, `good first issue`, `help wanted`, `invalid`, `question`, `wontfix` — use as per their standard meaning. `bug` and `enhancement` still exist but prefer the Type field.

### 3.3 When to apply multiple labels

Stack labels to describe the issue fully:

- A security bug that also indicates refactoring: `security` + `tech-debt`.
- A cross-service breaking change in the booking domain: `breaking-change` + `architecture`.
- A documentation-only PR: `documentation`.

### 3.4 Do NOT add priority/size/status as labels

Priority, Size, and Status are **Project fields** — set them there, not as labels. Having two sources of truth breaks filtered views.

---

## 4. Project field reference

All items on the `Extremo Platform` project have these fields.

| Field | Type | Values | Who sets |
|---|---|---|---|
| Status | single-select | Backlog / Ready / In Progress / In Review / Blocked / Done | Author sets `Backlog`. Automation advances through `In Progress`/`In Review`/`Done`. |
| Priority | single-select | P0-Urgent / P1-High / P2-Medium / P3-Low | Triage. Required before `Ready`. |
| Size | single-select | XS / S / M / L / XL | Triage / author estimate. |
| Area | single-select | auth / booking / attendance / schedule / tenant / chat / notification / infra / ci / docs / security / testing | Author. Must match the `[Area]` tag in the title. |
| Iteration | iteration | 2-week sprints | Sprint planning. Leave empty if backlog. |
| Start date | date | — | Set when `Status = In Progress`. |
| Target date | date | — | Set when committed to an iteration or milestone. |

### 4.1 Priority definitions

| Value | Definition |
|---|---|
| `P0-Urgent` | Production outage, data loss, security breach in progress. Stops the current sprint. |
| `P1-High` | Blocks a release, affects many users, or a critical path dependency. Must ship this iteration. |
| `P2-Medium` | Should ship soon but the release can proceed without it. Default for most new work. |
| `P3-Low` | Nice-to-have. No deadline. |

### 4.2 Size definitions (effort estimate, not calendar time)

| Value | Typical effort |
|---|---|
| `XS` | <1 hour. Config change, copy fix, single-line bug. |
| `S` | Half-day. One file or one test. |
| `M` | 1–2 days. Contained change within one sub-project. |
| `L` | ~1 week. Multi-file, possibly multi-repo but no schema change. |
| `XL` | >1 week. Must be broken into sub-issues before starting. |

**Rule:** an `XL` issue must be split into sub-issues and tracked as a parent.

### 4.3 Area definitions

| Area | Covers |
|---|---|
| `auth` | Login, identity, magic links, phone OTP, permission/authz, session, cookies. |
| `booking` | Reservation CRUD, draft→reserve→order lifecycle, booking search. |
| `attendance` | Staff shifts, availability, calendar sync for staff. |
| `schedule` | Slot/coops/hybrid scheduling algorithms, calendar rendering. |
| `tenant` | Tenant/store lifecycle, settings, profiles, addresses, branding. |
| `chat` | Messaging between tenants and clients. |
| `notification` | Email, push, in-app notifications. |
| `infra` | Deploy, Docker, Nix, flake.nix, env config, observability, Sentry. |
| `ci` | GitHub Actions, build pipelines, release automation. |
| `docs` | README, CLAUDE.md, this file, inline docs. |
| `security` | Cross-cutting security work that is not auth-specific. |
| `testing` | Test infrastructure, fixtures, coverage, E2E harness. |

If no area matches, propose a new one in a triage issue before filing.

---

## 5. Milestones

Milestones track **releases** or **epics**. They are optional for individual issues but strongly recommended for features heading to a named release.

**Naming conventions:**

- Release milestones: `vX.Y.Z` (semver) or `YYYY-MM-sprint-N` if no semver tag exists yet.
- Epic milestones: `epic: <short-name>` — used when a body of work spans several iterations and cannot be captured as a single tracking issue with sub-issues.

Milestones live **per repository**. For cross-repo epics, prefer the **parent issue + sub-issue** pattern (§6) over duplicating a milestone in each repo.

---

## 6. Relationships — parent issues, sub-issues, blocked-by

### 6.1 Sub-issues (GA since 2025)

Use sub-issues to break down any issue that cannot be finished by one person in one iteration.

- Create the **parent** issue first. Write the acceptance criteria and the decomposition plan in the body.
- Add children via **Issue sidebar → Sub-issues → Add sub-issue**. Children can live in a **different repository** (e.g., parent in `extremo-api`, sub-issues in `extremo-view` and `extremo`).
- Limits: 50 sub-issues per parent, 8 levels deep.
- The Project board exposes `Parent issue` and `Sub-issues progress` fields; use them to filter/group.

**When to use sub-issues vs. task lists:** sub-issues have metadata (type, status, assignee, project linkage). Task lists (checkbox lists in the body) don't. Prefer sub-issues whenever each task deserves its own lifecycle.

### 6.2 Cross-repo parent pattern

For a feature that spans DB → proto → api → view → mobile:

1. Create the tracking issue in the **most-upstream repo that holds the contract change** (usually `extremo-proto` or `extremo-db`).
2. Set Type = `Feature`, label `architecture`, add to Project, set Area.
3. Add sub-issues in each affected repo with Type = `Task`. Link their PRs.
4. The parent closes when all sub-issues close.

### 6.3 Blocked-by

GitHub is rolling out typed dependencies; until then use:

- The `blocked` label on the dependent issue, plus a body line `Blocked by #123` (or `threecorp/other-repo#123` for cross-repo).
- Sub-issue relationships for decomposition, not for dependencies.

Set Project Status = `Blocked` while blocked; revert to `Ready` once unblocked.

---

## 7. Development linkage

The right-hand **Development** panel on an issue links branches and PRs. Use it — the project board's `Linked pull requests` column depends on it.

### 7.1 Branch naming

`<type>/<issue-number>-<slug>` — example: `feat/75-scope-books-list`, `fix/85-abort-signal-typing`.

Types: `feat`, `fix`, `refactor`, `chore`, `docs`, `test`, `perf`, `ci`.

### 7.2 Linking a branch to an issue

On the issue sidebar, **Development → Create a branch** — GitHub creates the correctly named branch and links it automatically. If the branch already exists, **Development → Link a branch**.

### 7.3 Linking a PR

The recommended way is to include one of the [GitHub closing keywords](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue) in the PR body:

```
Closes #75
Fixes threecorp/extremo-view#85
```

Cross-repo closes are supported. When the PR merges, the linked issue auto-closes and the Project `Item closed` workflow sets Status = Done.

### 7.4 Commit references

In commit messages, reference issues with `#<num>` (same repo) or `<owner>/<repo>#<num>` (cross-repo). Avoid closing keywords in commit messages — reserve those for the PR description so review comments gate the close.

---

## 8. Issue body templates

The repos ship issue forms at `.github/ISSUE_TEMPLATE/` that enforce the structure below. If you file an issue via the API, still follow the structure.

### 8.1 Feature

```markdown
## Summary
<one-paragraph user-facing description>

## Motivation / Why
<problem it solves, metric it moves, decision record link>

## Acceptance criteria
- [ ] <testable criterion>
- [ ] <testable criterion>

## Design notes
<API/schema/UX sketches, links to proto/diagrams/screenshots>

## Out of scope
<explicit non-goals>

## Related
- Parent: #<n>
- Sub-issues: #<n>, #<n>
- Blocks / Blocked by: #<n>
```

### 8.2 Bug

```markdown
## Summary
<what's broken, one sentence>

## Reproduction
1. <step>
2. <step>

## Expected behavior
<what should happen>

## Actual behavior
<what happens, with error text / stack / screenshot>

## Environment
- Repo: <extremo-api/view/etc>
- Commit: <sha>
- Env: <local/stg/prod>

## Suspected root cause
<optional; link to code>

## Related
- Blocked by: #<n>
```

### 8.3 Task

```markdown
## Objective
<what must be done>

## Scope
<files / modules / layers affected>

## Acceptance criteria
- [ ] <criterion>

## Notes
<references, prior art, migration considerations>

## Related
- Parent: #<n>
```

---

## 9. Workflow for creating an issue (AI-agent recipe)

Follow this exact sequence. It is idempotent and produces a fully-compliant issue.

1. **Pick the repository** based on where the change lands. Cross-repo work → file the parent in the upstream repo.
2. **Search for duplicates** first: `gh issue list -R threecorp/<repo> --search "<keywords>" --state all`. Link the found issue instead of creating a new one.
3. **Draft the body** using the §8 template for the chosen Type.
4. **Create the issue** with the `gh` CLI:

    ```bash
    gh issue create \
      -R threecorp/<repo> \
      --title "[<area>] <imperative summary>" \
      --body-file /tmp/issue.md \
      --label "<label1>,<label2>"
    ```

5. **Set the Type** (not settable via CLI yet; use the GraphQL mutation below or the UI):

    ```bash
    gh api graphql -f query='
    mutation($issueId: ID!, $typeId: ID!) {
      updateIssueIssueType(input: { issueId: $issueId, issueTypeId: $typeId }) {
        issue { number issueType { name } }
      }
    }' -f issueId=<issue-node-id> -f typeId=<type-id>
    ```

    Type IDs (org-level, stable):
    - `Task`: `IT_kwDOAuF21c4AmJJR`
    - `Bug`: `IT_kwDOAuF21c4AmJJT`
    - `Feature`: `IT_kwDOAuF21c4AmJJW`

6. **Add to Project #1** (if Auto-add isn't configured):

    ```bash
    gh project item-add 1 --owner threecorp --url <issue-url>
    ```

7. **Set Project fields** via `gh project item-edit` or the GraphQL `updateProjectV2ItemFieldValue` mutation. See §4 for field IDs.

8. **Link to a parent issue** (if applicable) via the sub-issue UI or the REST endpoint:

    ```bash
    gh api -X POST /repos/<owner>/<repo>/issues/<parent-number>/sub_issues \
      -f sub_issue_id=<child-issue-id>
    ```

9. **Create a branch from the issue** (Development panel) before opening a PR, or ensure the PR body includes `Closes #<n>`.

---

## 10. Project field and option IDs (for programmatic access)

> These IDs are stable but may change if a field is deleted and recreated. Re-fetch with `gh project field-list 1 --owner threecorp --format json` if a call returns `field not found`.

**Project:** `PVT_kwDOAuF21c4BU-OD`

| Field | Field ID | Option IDs |
|---|---|---|
| Status | `PVTSSF_lADOAuF21c4BU-ODzhPEq9M` | Backlog `3fc7321d` / Ready `7cd48b6b` / In Progress `377946c0` / In Review `eb363c6d` / Blocked `224a4dc5` / Done `fe7d264a` |
| Priority | `PVTSSF_lADOAuF21c4BU-ODzhPOq3U` | P0-Urgent `c917c49c` / P1-High `fa6b4dd8` / P2-Medium `42eefee0` / P3-Low `2f658f7b` |
| Size | `PVTSSF_lADOAuF21c4BU-ODzhPOr-A` | XS `e5c1c5c2` / S `b8135436` / M `6ee1db25` / L `bf18d7c1` / XL `e5d972ee` |
| Area | `PVTSSF_lADOAuF21c4BU-ODzhPOs8A` | auth `a0fbbbae` / booking `57bf83a1` / attendance `780327e3` / schedule `a9992512` / tenant `45636914` / chat `297e8bf5` / notification `9c82de90` / infra `536755d7` / ci `ab34fbb3` / docs `c37d7690` / security `029cd318` / testing `fe8538dc` |
| Start date | `PVTF_lADOAuF21c4BU-ODzhPOt_I` | — (date) |
| Target date | `PVTF_lADOAuF21c4BU-ODzhPOvDI` | — (date) |
| Iteration | `PVTIF_lADOAuF21c4BU-ODzhPO4Vo` | fetch via API |

---

## 11. Anti-patterns to avoid

- Filing an issue without a Type.
- Setting Priority/Size as labels instead of Project fields.
- Creating an `XL` issue without sub-issues.
- Cross-repo epics managed only by milestones (use parent/sub-issue).
- Closing an issue via commit message instead of via PR body.
- Duplicating a milestone across repos for the same epic.
- Using the body to hold acceptance criteria for multiple sub-features instead of creating sub-issues.
- Leaving Status = Backlog after work has started — always advance.

---

## 12. Maintenance

This document lives at [`ISSUE_GUIDELINES.md`](https://github.com/threecorp/.github/blob/main/ISSUE_GUIDELINES.md) in the [`threecorp/.github`](https://github.com/threecorp/.github) org-defaults repo. The org-level Project README links to it. When Project fields or labels change, update:

1. `ISSUE_GUIDELINES.md` (this file) in `threecorp/.github`
2. The Project README at `orgs/threecorp/projects/1`
3. `.github/ISSUE_TEMPLATE/*.yml` in `threecorp/.github` (org-level defaults; inherited by any extremo* repo that doesn't ship its own)
