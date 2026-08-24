# TCG Store / TCG Trading Platform — Project Overview

> One-page snapshot. For full detail and decision history, see `docs/PROJECT_BRAIN.md`
> (source of truth for conflicts/pivots) and `CLAUDE.md` (working conventions).

## What this is

Group project for **ITDS323 Practical DevOps and Applications**, worth 30% of grade.
7 checkpoints (CP1–CP7), 15 Aug – 14 Nov 2026. 8-person team, 4 pairs:
frontend x2, backend API, backend DevOps/test.

## Current status (2026-08-24)

- **Docs-only** — no application code written yet.
- Figma design: Seller (9 screens), Platform Admin (14 screens), Buyer-side (5 screens) —
  all built. Full detail in `docs/figma_scope_todo.md`.
- GitHub: org `MUDST-2026-luntungsadungyong`.
  - `Document` — docs/links repo (Miro user-story board, Figma link). This overview's docs
    were just pushed there.
  - `demo-repository` — unused GitHub default template, ignorable.
  - `tcg-store-frontend`, `tcg-store-backend` — created 2026-08-24, **empty**, not started.

## Product scope — ⚠️ has an unresolved pivot

Two competing versions of what this product is:

| | Original scope | Pivoted scope (current canonical) |
|---|---|---|
| Model | Single-seller TCG store, direct CRUD | "One Piece Hub" — middleman/escrow marketplace |
| Payment | No real payment | Escrow: buyer pays in → held → payout to seller on QC pass (still simulated, no real gateway) |
| Login | Required to browse | Guest browse allowed; login gated on buy/like/sell |
| Fulfillment | Direct order, admin sets status | 2-leg shipping (seller→Hub, Hub→buyer) + QC step + dual tracking |
| Source docs | `tcg_project_guide.md`, `user_stories.md` | `📦 TCG Platform Architecture & Business Strategy.md`, `📊 Market Analysis Middleman.md` |

**The pivoted scope wins** on all product-direction questions (confirmed 2026-08-16). The
original docs stay authoritative only for course-admin facts: timeline, checkpoints, grading
rules, tool stack. Full conflict table: `docs/PROJECT_BRAIN.md`.

## Stack (mandatory unless noted)

- **Frontend:** Vue.js or React (pick one) + Axios/Fetch, Figma for design
- **Backend:** Spring Boot + Spring Security + Spring Data JPA
- **DB:** PostgreSQL (self-hosted, no Supabase/cloud DB) or MySQL; H2 for tests
- **Test:** JUnit 5 + Mockito (backend, mandatory); Jest/Vitest (frontend); Cypress/Playwright optional E2E
- **Git/CI:** GitHub, GitHub Actions mandatory CI/CD
- **Infra:** Docker + Docker Compose mandatory; Kubernetes (Minikube or GCP) mandatory by CP5

## Repo strategy — POLYREPO (decided 2026-08-24)

Separate repos for frontend and backend, not monorepo — per user instruction (no instructor
reference example seen, treated as a direct decision). Each repo: own CI/CD, own Dockerfile,
own SemVer version line (see `🏷️ Standard Semantic Versioning (SemVer 3-Digit) Guide.md`).
Local dev orchestration (`docker-compose.yml` spanning both repos) not yet decided.

## Hard rules

- No external APIs/services (no ChatGPT API, Firebase, Auth0, etc.) — must run fully local/offline
- Escrow/QC is self-built simulation, not a real payment gateway — the "no external services" rule still applies post-pivot
- Every team member must have commits — instructor checks git log
- Never push directly to `main` — PR only, 1 branch = 1 feature, delete branch after merge
- Always `git pull origin main` before starting new work
- A-grade (no quota) requires: K8s + full CI/CD + automated unit+integration tests + all members committing


