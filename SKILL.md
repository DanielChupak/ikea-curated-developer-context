---
name: ikea-curated-developer-context
description: >-
  Guided Port onboarding for IKEA developers. Builds a curated, standardised
  context layer in Port so that when a developer starts a task (e.g. building a
  new API), Port serves up the relevant standards, ownership, reusable APIs,
  security requirements, and recent history straight into the IDE via the Port
  MCP server. Use when an IKEA developer wants Port to surface task-relevant
  context, or to set up the "curated developer context" workshop step by step.
---

# IKEA — Curated Developer Context

Turn Port into a **context engine for developers**. When a developer starts a task — for
example *"I'm about to build a new Returns API"* — Port serves up everything relevant to
that task: the API standards and golden path, who owns the surrounding domain, existing
APIs they can reuse, the security requirements they must meet, and recent incidents in
the area. All of it reachable from the IDE through the **Port MCP server**.

This skill guides you (the IKEA developer / platform engineer) through building that layer
in your own Port org, one step at a time.

- **Region:** EU — MCP server `user-port-eu`
- **Persona:** Developer
- **Surface:** Port MCP in the IDE (you ask your agent, Port answers)
- **Data:** Namespaced dummy data prefixed `ikea-` (swap for real GitHub data later — see README)

---

## Behavior — CRITICAL

These rules are non-negotiable. Read them before doing anything.

1. **This is an interactive workshop, not a batch installer.** Build **one step at a time**.
2. **On activation:** show the welcome + journey table + step count, then **STOP** and wait
   for the user to say "let's go" (or similar) before starting Step 0.
3. **Never run MCP calls for more than the current step in a single turn.** Do not pre-build
   future steps. Do not "get ahead."
4. After each step: run a **Checkpoint** (verify what was built), then **PAUSE** and ask
   *"Ready for Step N?"*. Wait for confirmation.
5. **Escape hatch:** if the user says *"create it for me"*, run the MCP calls for the
   **current step only**, then pause. Never chain into the next step.
6. **Manual fallback:** if the user prefers not to use MCP, give them the JSON / UI steps
   from the matching file in `steps/` to paste into Port.
7. **Inspect before create.** Call `list_blueprints` first. **Adapt, don't overwrite** — never
   modify or delete existing org resources. All new artifacts use the `ikea_` / `ikea-` prefix.
8. **Never delete** anything in the customer org without explicit approval.
9. Keep the step details in `steps/` — read the relevant step file when that step is active;
   do not dump every step's MCP payloads at activation.

---

## Activation Script

When this skill starts, post this welcome (fill in the live numbers if you've inspected the org):

> **Welcome to the IKEA Curated Developer Context workshop.**
>
> We're going to make Port serve task-relevant context straight into your IDE. By the end,
> you'll be able to ask your agent *"I'm building a new X API — what do I need to know?"* and
> get back the standards, owners, reusable APIs, security rules, and recent incidents that matter.
>
> Here's the journey (7 steps, ~20–30 min):
>
> | Step | What we build | Why it matters |
> |------|---------------|----------------|
> | 0 | Connect & inspect your Port org | Make sure MCP works; adapt to what exists |
> | 1 | Catalog foundations: `ikea_team`, `ikea_domain`, `ikea_api` | The backbone of "what exists & who owns it" |
> | 2 | Context blueprints: standards, security, incidents | The curated context to serve |
> | 3 | Seed `ikea-` sample data | Give Port real context to answer with |
> | 4 | API golden-path scorecard | Encode the standards as checkable rules |
> | 5 | Wire up the Port MCP context query | The "serving" surface in your IDE |
> | 6 | Demo: build a new "Returns API" with full context | Prove the whole flow live |
>
> Everything is namespaced `ikea-` so nothing in your existing org is touched.
>
> **Ready for Step 0?**

Then STOP and wait.

---

## Step Orchestration

For each step, follow this loop. Detailed MCP payloads and manual JSON live in the linked
step files — read the file when you enter the step.

### Step 0 — Connect & Inspect → [`steps/step-0-connect-and-inspect.md`](steps/step-0-connect-and-inspect.md)
- **Problem:** Before building, confirm the Port MCP works and see what's already there.
- **What we build:** Nothing yet — we inspect.
- **Do together:** Run `list_blueprints` on `user-port-eu`. Note any existing `service`/`_team`
  model. Confirm we'll namespace with `ikea_` to avoid collisions.
- **Checkpoint:** MCP returned a blueprint list without error.
- **Pause:** "Ready for Step 1?"

### Step 1 — Catalog Foundations → [`steps/step-1-catalog-foundations.md`](steps/step-1-catalog-foundations.md)
- **Problem:** Port can't serve context about a task if it doesn't model APIs, teams, and domains.
- **What we build:** Blueprints `ikea_team`, `ikea_domain`, `ikea_api` (+ relations api→team, api→domain, api→api dependency).
- **Do together:** Upsert the three blueprints in order (team → domain → api, so relations resolve).
- **Checkpoint:** `list_blueprints` shows all three with the relations defined.
- **Pause:** "Ready for Step 2?"

### Step 2 — Context Blueprints → [`steps/step-2-context-blueprints.md`](steps/step-2-context-blueprints.md)
- **Problem:** Standards, security rules, and incident history are the *curated* context — they need a home.
- **What we build:** `ikea_api_standard`, `ikea_security_requirement`, `ikea_incident` (+ relations to `ikea_api`/`ikea_domain`).
- **Do together:** Upsert the three blueprints; add the relations onto `ikea_api`.
- **Checkpoint:** Relations `security_requirements` and (incident) `affected_api` exist.
- **Pause:** "Ready for Step 3?"

### Step 3 — Seed Sample Context → [`steps/step-3-seed-data.md`](steps/step-3-seed-data.md)
- **Problem:** An empty catalog serves empty context. We need representative data.
- **What we build:** `ikea-` teams, domains, existing APIs (for reuse), standards, security reqs, recent incidents.
- **Do together:** Upsert entities in dependency order (teams → domains → standards/security → APIs → incidents).
- **Checkpoint:** `list_entities` on `ikea_api` returns the seeded APIs with owners and domains.
- **Pause:** "Ready for Step 4?"

### Step 4 — Golden-Path Scorecard → [`steps/step-4-golden-path-scorecard.md`](steps/step-4-golden-path-scorecard.md)
- **Problem:** "Standards" are only useful if they're checkable. Encode them.
- **What we build:** Scorecard `ikea_api_golden_path` on `ikea_api` (Bronze/Silver/Gold rules).
- **Do together:** Upsert the scorecard; review which seeded APIs pass/fail.
- **Checkpoint:** `list_scorecards` shows `ikea_api_golden_path`.
- **Pause:** "Ready for Step 5?"

### Step 5 — Serve Context via MCP → [`steps/step-5-serve-via-mcp.md`](steps/step-5-serve-via-mcp.md)
- **Problem:** The context exists in Port — now make it reachable from the IDE on demand.
- **What we build:** A reusable **context-query prompt** the developer runs in Cursor, mapped to MCP calls.
- **Do together:** Confirm MCP connection; dry-run the curated query against the seeded data.
- **Checkpoint:** Agent returns standards + owners + reusable APIs + security + incidents for a domain.
- **Pause:** "Ready for the demo?"

### Step 6 — Demo: New "Returns API" → [`steps/step-6-demo-new-api.md`](steps/step-6-demo-new-api.md)
- **Problem:** Prove the user story end to end.
- **Trigger:** Developer says *"I'm about to build a new Returns API for the Checkout domain."*
- **Expected outcome:** Port serves: applicable API standards + golden path, owning team & domain
  context, related/reusable APIs (`ikea-orders-api`, `ikea-cart-api`), security requirements, and
  recent incidents in Checkout. Developer starts the task fully briefed.
- **Checkpoint:** The agent's answer cites concrete `ikea-` entities for every context category.
- **Wrap:** Point to VALIDATION.md and the "make it real" path in README.

---

## MCP Server & Tools

| | |
|---|---|
| Server | `user-port-eu` |
| Inspect | `list_blueprints`, `list_entities`, `list_scorecards` |
| Catalog | `upsert_blueprint`, `upsert_entity` |
| Standards | `upsert_scorecard` |

Always read the tool schema before calling. Full payloads are in the `steps/` files.

---

## Safety

- Namespace everything `ikea_` (blueprints) / `ikea-` (entities). Never touch existing org resources.
- Inspect before create. Never delete without explicit approval.
- Real GitHub data is optional and additive — see README "Make it real".
