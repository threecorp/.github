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
| 14 | **Roadmap metadata** | If `epic:blueprinting`, add `tier:*` / `phase:*` / `feature:F-*` labels and `vX.Y-tier` milestone. See §9. |

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

### 3.5 Roadmap labels (epic / tier / phase / feature) — MANDATORY for any roadmap-tracked issue

Every issue that lives on the `Extremo Platform` project board (i.e. **all** issues filed in extremo* repos) MUST carry the four roadmap-axis labels in addition to the cross-cutting labels above. The org-level [Project #1 README](https://github.com/orgs/threecorp/projects/1) is the canonical source for the label values; this section pins the rules that govern *which* labels to set.

| Axis | Label prefix | Cardinality | Meaning |
|---|---|---|---|
| Epic | `epic:*` | exactly 1 | Roadmap epic. Pick from the list defined in the Project #1 README (e.g. `epic:blueprinting`). |
| Tier | `tier:梅` / `tier:竹` / `tier:松` | **1, 2, or 3** (cumulative — see below) | Release tier in which the work first becomes necessary. |
| Phase | `phase:A-foundation` … `phase:J-platform` | exactly 1 | One of the 10 phase epics. |
| Feature | `feature:F-*` | exactly 1 | Vertical/foundation feature group (e.g. `feature:F-Stripe`). The Project #1 README maintains the authoritative list. |

#### Tier label rule — 2 cases by feature type (the single most-violated rule)

The `tier:*` labeling follows **two distinct rules** depending on whether the issue belongs to a Foundation feature or a Vertical feature. Decide which case applies *first*, then label accordingly.

**Case 1 — Foundation features** (`F-Audit` / `F-EnumRollout` / `F-AttendanceBreak` / `F-NameMatching`, 4 features only)

Foundation features are cross-tier infrastructure (audit log / enum rollout / attendance break / name matching). They are *not* scoped to one release tier — every tier consumes them. Therefore **every Foundation issue carries all 3 tier labels regardless of milestone**:

```
tier:梅 + tier:竹 + tier:松   (always 3, no exceptions)
```

This is what makes the Project #1 `Foundation × 梅 / × 竹 / × 松` per-feature filters return the same issue set.

**Case 2 — Vertical features** (the other 29 features) — standard cumulative rule

Vertical features are scoped to a release tier of *first appearance*. Tier labels are **cumulative from that tier upward**, never single-tier. Picking a tier means "this work is needed *from this tier onward*", which implies it is also needed in every later tier. Therefore:

| If the Vertical work first appears in… | Add **all** these `tier:*` labels |
|---|---|
| Tier 梅 (foundation parity) | `tier:梅` + `tier:竹` + `tier:松` (3 labels) |
| Tier 竹 (beauty middleware) | `tier:竹` + `tier:松` (2 labels) |
| Tier 松 (full platform) | `tier:松` (1 label) |

The Project board's `Tier 梅 / Tier 竹 / Tier 松` sequential views rely on the cumulative semantics: "all issues that must be done by the time we ship 松" must include everything from 梅 and 竹 too. A Vertical tier 梅 issue with only `tier:梅` silently disappears from the 竹 and 松 filtered views.

> Note: a Vertical 梅 issue and a Foundation issue both end up with 3 tier labels, but the *reason* differs (Foundation = tier-agnostic by definition; Vertical 梅 = earliest tier cumulated upward). Determine the feature type first.

**Conversely**, the **Milestone** is set to the *single* tier you actually plan to ship the work in: `v0.1-梅` / `v0.2-竹` / `v0.3-松`. Tier labels = scope coverage; Milestone = ship target.

**Worked example — Foundation feature** (always 3 tier labels):

```bash
gh issue create -R threecorp/extremo-api \
  --title "[booking] F-Audit follow-up: add admin filter for noshow events" \
  --body-file /tmp/body.md \
  --label "epic:f-audit" \
  --label "phase:A-foundation" \
  --label "tier:梅" --label "tier:竹" --label "tier:松" \
  --label "feature:F-Audit" \
  --milestone "v0.2-竹"   # shipping in 竹, but tier coverage is all 3
```

**Worked example — Vertical 梅 feature** (cumulative — same 3 labels, different reason):

```bash
gh issue create -R threecorp/extremo-view \
  --title "[tenant] Add custom landing page slot" \
  --body-file /tmp/body.md \
  --label "epic:blueprinting" \
  --label "phase:J-platform" \
  --label "tier:梅" --label "tier:竹" --label "tier:松" \
  --label "feature:F-CustomCMS" \
  --milestone "v0.1-梅"
```

If the same Vertical work were instead scoped to first appear in tier 竹, the labels would be `tier:竹` + `tier:松` (no `tier:梅`) and the milestone `v0.2-竹`.

#### Quick self-check before submitting

- [ ] **Foundation or Vertical?** Foundation (4 features) → 3 tier labels always. Vertical → cumulative from first-appearance tier.
- [ ] If Vertical, is `tier:*` cumulative? (1 label only ⇒ likely wrong unless it's pure tier:松 work)
- [ ] Does the Milestone match exactly one of the tier labels? (`v0.1-梅` ↔ `tier:梅`, etc.)
- [ ] Is `feature:F-*` listed in the Project #1 README? (If not, file a master-tracking discussion before proceeding.)
- [ ] Is `phase:*` set? (Missing `phase:` is the second-most-violated rule.)

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
- Setting **only one `tier:*` label** for a Vertical feature when the work first appears in tier 梅 or tier 竹 — tier labels are cumulative (see §3.5). A Vertical tier 梅 issue must carry all 3 tier labels.
- Treating a **Foundation feature** issue with single-tier or partial-tier labels — Foundation issues always carry all 3 tier labels regardless of milestone (see §3.5 Case 1).
- Filing an issue without `phase:*` / `feature:F-*` / `epic:*` / `tier:*` — all four roadmap axes are mandatory for any roadmap-tracked work (see §3.5).
- Adding a brand-new `feature:F-*` value without first updating the [Project #1 README](https://github.com/orgs/threecorp/projects/1) feature-vertical table and the existing-features list. The README is the canonical source.

---

## 12. Maintenance

This document lives at [`ISSUE_GUIDELINES.md`](https://github.com/threecorp/.github/blob/main/ISSUE_GUIDELINES.md) in the [`threecorp/.github`](https://github.com/threecorp/.github) org-defaults repo. The org-level Project README links to it. When Project fields or labels change, update:

1. `ISSUE_GUIDELINES.md` (this file) in `threecorp/.github`
2. The Project README at `orgs/threecorp/projects/1`
3. `.github/ISSUE_TEMPLATE/*.yml` in `threecorp/.github` (org-level defaults; inherited by any extremo* repo that doesn't ship its own)

---

## 9. Extremo Roadmap metadata (epic / tier / phase / feature / milestone)

The Extremo Platform roadmap (松/竹/梅 tier × Phase A–J × 32 features) is tracked via additional metadata on top of §3 / §4 / §5. Each issue **must** carry these when it belongs to the roadmap (= `epic:blueprinting`).

### 9.1 Required label set for roadmap issues

| Label series | Values | Purpose |
|---|---|---|
| `epic:*` | `epic:blueprinting` | Roadmap epic identifier (typically only this one) |
| `tier:*` | `tier:梅` / `tier:竹` / `tier:松` (cumulative) | "Required from this tier onward". 梅 issue carries all 3, 竹 issue carries `tier:竹,松`, 松 issue carries `tier:松` only |
| `phase:*` | `phase:A-foundation` / `phase:B-payment-cal` / `phase:C-pc-chart` / `phase:D-line-push` / `phase:E-public-api` / `phase:F-beauty` / `phase:G-google` / `phase:H-pos-acc` / `phase:I-multichannel` / `phase:J-platform` | One of 10 phase epics |
| `feature:F-*` | 32 features (see 9.4) | Vertical feature group for parallel session targeting |

### 9.2 Milestone scheme

Each sub-project repo carries 3 milestones aligned with tier:

- `v0.1-梅` — Foundation parity with Freee 予約
- `v0.2-竹` — Beauty middleware (recommended target)
- `v0.3-松` — Full beauty platform

**An issue belongs to exactly one milestone** (= the tier where the work is delivered). Tier labels are cumulative; milestone is singular.

### 9.3 Body template (roadmap issue)

```markdown
## Context
(Why this is needed, citing TODO.txt / TODO-talk.txt / ROADMAP.md)

## Acceptance
- (Concrete completion criteria, 3–7 bullets)
- ...

## 親
extremo-db#10 (Phase epic issue number; e.g. Phase A = extremo-db#10, Phase B = extremo-api#87, ...)
```

### 9.4 Feature catalog (32 features)

| Tier classification | Features |
|---|---|
| Foundation (梅, cross-feature shared) | `F-Audit`, `F-EnumRollout`, `F-AttendanceBreak`, `F-NameMatching` |
| Vertical 梅 | `F-AuthP0`, `F-BookingCluster`, `F-ServiceConfig`, `F-Stripe`, `F-GoogleCal`, `F-LINE`, `F-AccountHub`, `F-CalendarPages`, `F-PCScheduler`, `F-MobileAttendance` |
| Vertical 竹 | `F-Chart`, `F-Push`, `F-LIFF`, `F-LINESegment`, `F-PublicAPI`, `F-Tickets`, `F-ReserveWithGoogle`, `F-Accounting`, `F-POS` |
| Vertical 松 | `F-MaterialPurchase`, `F-Kanzashi`, `F-CustomDomain`, `F-CustomCMS`, `F-MultiStoreDashboard`, `F-Instagram`, `F-TwilioSMS`, `F-AIBookingPage`, `F-BIPredict` |

A new feature label requires creation in all 6 repos (5 sub-projects + `extremo-spec`) plus Project #1 README update.

### 9.5 PR labeling

PRs implementing a roadmap issue must inherit:

- `feature:F-*` (which feature this PR delivers part of)
- `tier:*` (matching the linked issue's tier set)
- `phase:*` (matching the linked issue)

Do **not** add `epic:blueprinting` to PRs (issue-only). Use `Closes #<num>` in PR body so Project automation moves Status to `Done` on merge.

### 9.6 Cross-references

- Project board cheat-sheet: https://github.com/orgs/threecorp/projects/1 (README → "Filing follow-up Issues / PRs" section)
- Master tracking + roadmap: https://github.com/threecorp/extremo-spec/issues/1
- ROADMAP source of truth: https://github.com/threecorp/extremo-spec/blob/main/ROADMAP.md
- Parallel session onboarding: https://github.com/threecorp/extremo-spec/blob/main/docs/onboarding-tier-ume.md
- Phase / feature dependency map: https://github.com/threecorp/extremo-spec/blob/main/docs/dependencies.md
