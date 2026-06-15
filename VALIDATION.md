# VALIDATION — IKEA Curated Developer Context

Run this end-to-end checklist after completing the workshop. All identifiers are namespaced
`ikea_` (blueprints) / `ikea-` (entities) so checks never touch existing org resources.

## Step 0 — Connect & Inspect
- [ ] `list_blueprints` on `user-port-eu` succeeds (MCP connected).
- [ ] Namespacing decision confirmed (`ikea_` / `ikea-`).

## Step 1 — Catalog foundations
- [ ] Blueprints exist: `ikea_team`, `ikea_domain`, `ikea_api`.
- [ ] `ikea_api` relations: `owning_team` → `ikea_team`, `domain` → `ikea_domain`, `depends_on` → `ikea_api` (many).

## Step 2 — Context blueprints
- [ ] Blueprints exist: `ikea_api_standard`, `ikea_security_requirement`, `ikea_incident`.
- [ ] `ikea_api` has `security_requirements` → `ikea_security_requirement` (many).
- [ ] `ikea_incident` has `affected_api` (many) and `domain`.

## Step 3 — Seed data
- [ ] Teams: `ikea-checkout-team`, `ikea-catalog-team`, `ikea-platform-team`.
- [ ] Domains: `ikea-checkout`, `ikea-catalog` (with `owning_team`).
- [ ] Standards: 5 `ikea-std-*` entities.
- [ ] Security: 4 `ikea-sec-*` entities.
- [ ] APIs: `ikea-orders-api`, `ikea-cart-api`, `ikea-payments-api`, `ikea-inventory-api` (relations populated).
- [ ] Incidents: `ikea-inc-2026-payments-timeout`, `ikea-inc-2026-cart-overload` (resolve to APIs + domain).

## Step 4 — Golden-path scorecard
- [ ] Scorecard `ikea_api_golden_path` on `ikea_api` exists.
- [ ] Rules only on Bronze/Silver/Gold (none on Basic).
- [ ] `ikea-inventory-api` visibly fails rules (teaching example); Orders/Payments score well.

## Step 5 — Serve via MCP
- [ ] Checkout-domain API query returns Orders / Cart / Payments.
- [ ] Curated-context prompt saved for reuse.

## Step 6 — Demo (the user story)
- [ ] Trigger: "new Returns API for Checkout" produces a briefing.
- [ ] Briefing cites concrete `ikea-` entities for **all five** context categories:
  - [ ] Standards / golden path
  - [ ] Owning team / domain
  - [ ] Reusable existing APIs
  - [ ] Security requirements
  - [ ] Recent incidents

## Safety
- [ ] No existing (non-`ikea`) blueprint, entity, or scorecard was modified or deleted.
- [ ] No destructive operation ran without explicit user approval.

## Behavior (skill quality)
- [ ] Skill previews the journey and waits before Step 0.
- [ ] Each step has a checkpoint + "Ready for Step N?" pause.
- [ ] No turn batched MCP calls across multiple steps.

---

## Optional — Phase 2 (only if IKEA wants it)
- [ ] **AI agent:** wrap the Step 5 queries in a Port AI agent ("New API Context Assistant")
      with conversation starters like "What do I need to build a new API?".
- [ ] **Real data:** connect the GitHub integration and relate `ikea_api` to `githubRepository`
      / `service` so ownership, PRs, and history are live (README → "Make it real").
- [ ] **Dashboard:** a "Developer Hub" page surfacing standards + golden-path scores (the story
      chose MCP-in-IDE as the surface, so this is additive).

## Teardown (optional)
To remove the workshop data later (explicit action only): delete `ikea-*` entities, then the
scorecard `ikea_api_golden_path`, then the `ikea_*` blueprints (children before parents:
`ikea_incident` → `ikea_api` → `ikea_domain` → `ikea_team`, plus `ikea_api_standard`,
`ikea_security_requirement`).
