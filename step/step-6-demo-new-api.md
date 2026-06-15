# Step 6 — Demo: Build a New "Returns API" with Full Context

**Goal:** Prove the user story end to end. A developer starts a new task and Port serves up
all the relevant, curated context — without leaving the IDE.

## The trigger (say this in Cursor)
> **"I'm about to build a new Returns API for the Checkout domain. Give me the curated context
> from Port."**

This is the single moment the whole workshop builds toward. The agent runs the Step 5 queries
against `user-port-eu` (current task only) and returns a briefing.

## Expected outcome — the agent returns a briefing like:

> **Context for: new Returns API (Checkout domain)**
>
> **Standards you must follow (golden path):**
> - *Resource Naming* (Required): plural kebab-case nouns → `/return-orders`
> - *API Versioning* (Required): version in path → `/v1/`
> - *Authentication* (Required): OAuth2 client credentials (no API keys for new APIs)
> - *Error Handling* (Required): RFC 7807 problem+json
> - Golden-path scorecard bar: Bronze needs docs + version; Silver needs OAuth2/mTLS + base path; Gold needs an OpenAPI spec.
>
> **Ownership / domain:**
> - Domain **Checkout** is owned by the **Checkout Team** (`#team-checkout`). Loop them in.
>
> **Reuse — existing APIs in Checkout you should build on:**
> - `ikea-orders-api` (`/v2/orders`, OAuth2) — returns are created against an order; depend on this.
> - `ikea-cart-api`, `ikea-payments-api` — refunds likely call Payments.
> - Pattern to copy: model the new API on Orders (OAuth2 + docs → already Gold-track).
>
> **Security requirements that apply:**
> - *PII Encryption* (Critical), *OAuth2 Required* (High), *Audit Logging* (High) — Returns handle PII + refunds.
>
> **Recent incidents to learn from (Checkout):**
> - SEV2 *Payments timeouts under load* (2026-05-12) → add circuit breakers / pool sizing if you call Payments.
> - SEV3 *Cart API 5xx spike* (2026-06-01) → enforce rate limits from day one.
>
> **Suggested next step:** scaffold `ikea-returns-api` as REST, `/v1/return-orders`, OAuth2, depends_on `ikea-orders-api`.

## Checkpoint
- [ ] The agent's answer cites **concrete `ikea-` entities** in every category:
      standards, owning team, reusable APIs, security requirements, incidents.
- [ ] The developer could start the task immediately from this briefing.

## (Optional) Register the new API in the catalog
To close the loop, add the new API so the next developer inherits the context:
```
upsert_entity blueprintIdentifier="ikea_api" entity={ "identifier": "ikea-returns-api", "title": "Returns API", "properties": { "lifecycle": "In Development", "api_type": "REST", "version": "v1", "base_path": "/v1/return-orders", "auth_method": "OAuth2", "has_documentation": true }, "relations": { "owning_team": "ikea-checkout-team", "domain": "ikea-checkout", "depends_on": ["ikea-orders-api"], "security_requirements": ["ikea-sec-pii-encryption", "ikea-sec-oauth2", "ikea-sec-audit-logging"] } }
```

## Wrap up
You've made Port a context engine for developers. To go further:
- **Make it real:** connect GitHub (README → "Make it real") so ownership, PRs, and history are live.
- **One-click:** turn the Step 5 query into a Port AI agent (VALIDATION.md → Optional).
- Run `VALIDATION.md` to confirm every piece is in place.
