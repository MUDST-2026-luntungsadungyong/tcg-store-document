# TCG Project Current Status

> **Purpose:** one shared, evidence-based project snapshot for the team and AI assistants.
>
> **Last verified:** 2026-09-13 (ICT)  
> **Status owner:** team-maintained; the editor must identify themselves in the update log.  
> **Do not store:** passwords, tokens, `.env` values, customer data, database dumps, or private URLs with credentials.

## Read this first

This document is a navigation and handoff layer, not a replacement for the delivery systems below.

| Question | Source of truth |
|---|---|
| Who owns a story, its Jira status, priority, acceptance criteria? | [SCRUM board](https://nunkantinan.atlassian.net/jira/software/projects/SCRUM/boards/1?filter=&groupBy=none) |
| Was code reviewed, merged, or did remote CI pass? | GitHub PR and Actions for that repository |
| Does the current checkout build/test? | Command output from that checkout, with commit SHA |
| Is the application usable right now? | Current Docker/service health plus browser/API smoke evidence |

Never infer one row from another. For example, merged code does not prove the stack is running, and a running container does not prove the latest commit passed CI.

## Delivery snapshot

| Area | Delivery status | Evidence snapshot | Current gap / next validation |
|---|---|---|---|
| Backend API | **Implemented; review and runtime repair pending** | Spring Boot API covers auth, profile, seller, catalog, cart, checkout, orders, and collection. Backend Jira work is mainly `In Review`. | Apply a safe migration for existing DB volumes, then run health and checkout smoke tests. |
| Frontend | **Implemented; integration hardening pending** | React/Vite UI covers auth, marketplace, cart/checkout, orders, listings, profile, store, and collection. | Run browser smoke tests against current backend API; remove/defer mock-only UX explicitly. |
| PostgreSQL | **Available, but schema drift blocks backend** | Compose PostgreSQL service was healthy on 2026-09-13. | Existing volume lacks `orders.idempotency_key`; init scripts do not repair an existing volume. |
| Full stack | **Not yet verified** | Backend Compose contains backend + PostgreSQL, not frontend. | Create a reproducible frontend + backend + DB stack and test critical flow. |
| CI | **Present, latest-run evidence must be checked per PR/commit** | Backend workflow runs Maven verification and PostgreSQL concurrency tests; frontend workflow runs lint, test, build. | Record exact successful run URLs/SHAs for the delivery candidate. |
| E2E / release | **Partially complete (Frontend Mock)** | Cypress E2E tests cover core journeys (Auth, Discovery, Cart, Admin, Seller Onboarding, Public Store, Error Handling) using mock API. | Add full-stack E2E tests against real backend, security checks. |

## Current technical blockers

1. **Backend cannot become healthy against the current persistent database volume.** Hibernate schema validation reports missing `orders.idempotency_key`; the container restarts. Implement and document a repeatable, non-destructive migration before declaring backend runtime ready.
2. **Local checkouts are stale after remote fetch.** At the snapshot time, `tcg-store-backend` was 3 commits behind `origin/main`; `tcg-store-frontend` was 4 commits behind `origin/main`. Sync deliberately and revalidate; do not report local results as remote-main results.
3. **Frontend runtime/browser proof is missing.** The Vite server was not accepting connections during the final probe. API integration needs a real browser smoke test.
4. **Two user-facing frontend paths are not production complete.** Forgot/reset password UI calls backend endpoints that are not implemented; leaderboard uses mock storage and notifications is a placeholder. Jira SCRUM-41 and SCRUM-42 own those decisions.

## Feature and Jira map

### Done in Jira

| Scope | Jira |
|---|---|
| Database schema and local PostgreSQL Compose | SCRUM-5, SCRUM-19 |
| Checkout and stock reservation | SCRUM-12 |
| Frontend seller inventory, marketplace, auth, profile, cart/checkout | SCRUM-13 to SCRUM-17 |
| Buyer history, seller orders, personal collection frontend | SCRUM-25, SCRUM-27, SCRUM-29 |
| Initial backend structure and shipping-address product decision | SCRUM-35, SCRUM-39 |
| Backend Dockerfile, Docker build pipeline, full-stack Compose (Rev 5) | SCRUM-21, SCRUM-23, SCRUM-24 |

### In review

| Backend/API scope | Jira |
|---|---|
| Profile/contact, seller verification, authentication | SCRUM-6, SCRUM-8, SCRUM-9 |
| Seller inventory, browse products, cart, checkout CI | SCRUM-7, SCRUM-10, SCRUM-11, SCRUM-20 |
| Seller orders/profit, collection, buyer order history | SCRUM-28, SCRUM-38 |

### Delivery work still open or planned

| Scope | Jira |
|---|---|
| Frontend environment configuration | SCRUM-18 |
| Error/retry UX, responsive/accessibility, frontend test setup | SCRUM-30, SCRUM-31, SCRUM-32 |
| Dependency audit and Playwright E2E | SCRUM-33, SCRUM-34 |
| Final validation | SCRUM-36 |
| User-flow clarification and collection ownership decision | SCRUM-37, SCRUM-40 |
| Password recovery and leaderboard/notification decisions | SCRUM-41, SCRUM-42 |

## Repositories and current checkout state

| Repository | Role | Snapshot note |
|---|---|---|
| `tcg-store-backend` | Main Spring Boot API + PostgreSQL Compose | Local `main` was behind remote by 3 commits at snapshot time. |
| `tcg-store-frontend` | React/Vite application | Local `main` was behind remote by 4 commits at snapshot time. |
| `tcg-store-document` | Shared documentation | This document's home. |
| `User-FlowANDFigma-Progress` | User-flow and design progress | Separate documentation repository. |
| `tcg-profile-backend` | Legacy/duplicate backend worktree | Detached HEAD with uncommitted schema files observed. Do not treat it as canonical backend without an explicit team decision. |

## Verified API surface (not browser-E2E proof)

- Auth: register, login, current user
- User profile/contact and seller verification/store profile
- Product/catalog browse and seller product management
- Cart and atomic checkout
- Buyer order history/detail and seller order management/profit
- Personal collection

## Definition of delivery-ready

A feature becomes **delivery-ready** only when all applicable evidence exists:

- Jira acceptance criteria and owner/status are current.
- The intended branch is merged or the PR is explicitly identified.
- Required CI checks pass on the intended commit.
- Backend/database migrations work on a clean database and documented existing-volume upgrade path.
- The real frontend uses the API contract, including loading, empty, error, and unauthorized states.
- Critical user flow is proven in a browser or E2E test.
- Deployment/runtime evidence exists if the feature is claimed as deployed.

## Update protocol

Update this file after any of these events: merge, Jira transition, new blocker, migration, verified CI run, browser/E2E validation, or release attempt.

1. Verify the claim in the source-of-truth system.
2. Change only the affected row/section; do not rewrite unrelated history.
3. Add the evidence link or command + commit SHA.
4. Use **Unverified**, **Blocked**, **In review**, or **Done** precisely.
5. Commit the document change in the same PR, or link its PR from the implementation PR.

## Update log

| Date (ICT) | Editor | Change | Evidence |
|---|---|---|---|
| 2026-09-13 | Codex audit | Created the shared status baseline. | Jira project snapshot, repository fetch/state, Compose logs, API/browser probes. |
| 2026-09-24 | DevOps Agent | Implemented CI/CD Quality Gates across backend & frontend, verified full-stack Docker Compose & Rev 5 DB schema, transitioned SCRUM-21, SCRUM-23, SCRUM-24 to Done. Added `ci_cd_pipelines_overview.md`. | Backend PR #22 (6/6 checks pass), Frontend PR #92 (5/5 checks pass), Jira API transitions. |
| 2026-10-02 | DevOps Agent | Implemented comprehensive Frontend E2E tests (Cypress) covering Auth, Discovery, Cart, Admin Dashboard, Seller Onboarding, Public Store Browsing, and Error Handling/Feedback. | All Cypress specs pass 100% locally and on CI (Mock API). |
| 2026-10-14 | DevOps Agent | Implemented comprehensive E2E Cypress test suite for Notification System (US-11-1 & US-11-2) covering Navbar Bell, Unread Badge, Action links, Read/Delete lifecycle, Pagination, and Error/Auth guards. | 14/14 Cypress specs pass (100%) locally via `npm run test:e2e:notifications`. |


