# TCG Store / TCG Trading Platform — Project Overview

## What this is

Group project for ITDS323 Practical DevOps and Applications, worth 30% of grade.
7 checkpoints (CP1–CP7), 15 Aug – 14 Nov 2026. 8-person team, 4 pairs:
frontend x2, backend API, backend DevOps/test.

The product: a TCG (trading card game) marketplace. Originally scoped as a simple
single-seller store, later pivoted into a "One Piece Hub" middleman/escrow platform —
buyers and sellers trade through a hub that holds payment in escrow, runs quality
control (QC) on shipped cards, then releases payout. The pivoted scope is what's
being built now.

## Current status (2026-08-24)

- Docs-only — no application code written yet.
- Figma design done: Seller side (9 screens), Platform Admin (14 screens), Buyer side
  (5 screens).
- GitHub org set up with two empty repos ready for code: frontend and backend
  (created today, nothing pushed yet).
- A docs/links repo exists holding a Miro user-story board and the Figma link.

## Product scope

- Guest browsing allowed; login only required to buy, like, or sell.
- Escrow flow: buyer pays in, funds held, payout to seller only after QC pass.
  Simulated internally — no real payment gateway.
- Shipping is 2-leg: seller ships to the Hub, Hub does QC, then Hub ships to buyer.
  Dual tracking codes.
- Admin CRUD for products, cost/profit calculator, stock display, order fulfillment
  status — all still required from the original course spec.

## Stack

- Frontend: Vue.js or React (pick one) + Axios/Fetch, designed in Figma
- Backend: Spring Boot + Spring Security + Spring Data JPA
- Database: PostgreSQL (self-hosted, no cloud DB service) or MySQL; H2 for tests
- Testing: JUnit 5 + Mockito (backend, mandatory); Jest/Vitest (frontend);
  Cypress/Playwright optional for E2E
- Git/GitHub with GitHub Actions for CI/CD (mandatory)
- Docker + Docker Compose (mandatory); Kubernetes via Minikube or GCP (mandatory
  by checkpoint 5)

## Repo strategy

Frontend and backend live in separate repositories (polyrepo, not monorepo). Each
repo gets its own CI/CD pipeline, own Dockerfile, and its own independent version
number (SemVer: Major.Minor.Patch) — the two repos don't need to share a version.
How local dev will run both together (Docker Compose spanning two repos) isn't
decided yet.

## Hard rules

- No external APIs/services (no ChatGPT API, Firebase, Auth0, real payment
  gateways, etc.) — everything must run fully local/offline, including the escrow
  simulation.
- Every team member must have commits in git log — a team where one person
  commits everything loses points.
- Never push directly to main — PR only, one branch per feature, delete branch
  after merge.
- Always pull main before starting new work.
- Top grade requires: working Kubernetes deployment, full CI/CD, automated
  unit + integration tests, and every member committing.

## Open items

- Original course-spec docs (requirements list, user stories) still need
  rewriting to match the escrow/hub scope — not done yet.
- Where Docker Compose and Kubernetes manifests live is unresolved now that
  frontend/backend are split into separate repos.
