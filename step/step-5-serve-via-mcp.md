# Step 5 — Serve Context via MCP

**Goal:** Make the curated context reachable from the IDE on demand. The developer asks a
single natural-language question; the agent fans out to Port MCP calls and returns a briefing.

## Problem
The context now lives in Port. The last mile is turning "I'm starting task X" into the right
set of MCP queries so the developer never has to hunt across tabs.

## The curated context query (the "serving" pattern)

Save this prompt — it's what a developer runs in Cursor when starting an API task:

> **"I'm about to build a new `<API name>` in the `<domain>` domain. Using the Port MCP
> (`user-port-eu`), give me the curated context: the required API standards and golden path,
> the owning team, existing APIs in this domain I should reuse or depend on, the security
> requirements that apply, and any recent incidents in this domain."**

When that prompt runs, the agent should execute (current task only):

### 1. Standards / golden path
```
list_entities blueprintIdentifier="ikea_api_standard" include=["$title","category","severity","content","doc_url"]
list_scorecards   # to surface the ikea_api_golden_path bar
```

### 2. Domain + owning team
```
list_entities blueprintIdentifier="ikea_domain" identifiers=["ikea-checkout"] include=["$title","description","owning_team"]
```

### 3. Existing / reusable APIs in the domain
```
list_entities blueprintIdentifier="ikea_api" include=["$title","lifecycle","api_type","base_path","auth_method","depends_on"] query={
  "combinator": "and",
  "rules": [ { "relation": "domain", "operator": "=", "value": "ikea-checkout" } ]
}
```

### 4. Security requirements (what similar APIs in the domain must meet)
```
list_entities blueprintIdentifier="ikea_security_requirement" include=["$title","severity","description","doc_url"]
```

### 5. Recent incidents in the domain
```
list_entities blueprintIdentifier="ikea_incident" include=["$title","severity","status","summary","occurred_at"] query={
  "combinator": "and",
  "rules": [ { "relation": "domain", "operator": "=", "value": "ikea-checkout" } ]
} sort=[{ "property": "occurred_at", "order": "desc" }]
```

The agent then synthesizes these into one briefing (see Step 6 for the expected shape).

## Do together (MCP — run only this step)
Dry-run query #3 (reusable APIs in Checkout) to confirm the data flows:
```
CallMcpTool: server="user-port-eu", toolName="list_entities", arguments={ "blueprintIdentifier": "ikea_api", "include": ["$title","lifecycle","auth_method","domain"], "query": { "combinator": "and", "rules": [ { "relation": "domain", "operator": "=", "value": "ikea-checkout" } ] } }
```

## Checkpoint
- [ ] The query returns the Checkout-domain APIs (`ikea-orders-api`, `ikea-cart-api`, `ikea-payments-api`).
- [ ] You've saved the curated-context prompt somewhere reusable (e.g. a Cursor rule or snippet).

## Optional — make it a Port AI agent (Phase 2)
This skill ships the MCP-query pattern. If IKEA later wants a one-click assistant, the same
queries can back a Port **AI agent** ("New API Context Assistant"). See VALIDATION.md → Optional.

## Pause
Say **"Ready for the demo?"** and wait.
