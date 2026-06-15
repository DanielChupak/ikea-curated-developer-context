# Step 4 — Golden-Path Scorecard

**Goal:** Turn the API standards into **checkable rules** so "standardised context" is
measurable, and so Port can tell a developer what "good" looks like.

## Problem
Standards as documents are easy to ignore. A scorecard makes the golden path concrete:
each API gets graded Bronze / Silver / Gold, and the developer sees the bar to clear.

## Artifact
- Scorecard `ikea_api_golden_path` on blueprint `ikea_api`.

> **Scorecard rules:** can only be assigned to **Bronze / Silver / Gold** (never Basic).
> Boolean conditions must use `true` / `false` (not 1/0).

## Do together (MCP — run only this step)

```
CallMcpTool: server="user-port-eu", toolName="upsert_scorecard", arguments={
  "blueprintIdentifier": "ikea_api",
  "scorecard": {
    "identifier": "ikea_api_golden_path",
    "title": "API Golden Path",
    "rules": [
      {
        "identifier": "ikea_has_docs",
        "title": "Has documentation",
        "level": "Bronze",
        "query": { "combinator": "and", "conditions": [
          { "property": "has_documentation", "operator": "=", "value": true }
        ] }
      },
      {
        "identifier": "ikea_has_version",
        "title": "Version is set",
        "level": "Bronze",
        "query": { "combinator": "and", "conditions": [
          { "property": "version", "operator": "isNotEmpty" }
        ] }
      },
      {
        "identifier": "ikea_secure_auth",
        "title": "Uses standard auth (not API Key / None)",
        "level": "Silver",
        "query": { "combinator": "and", "conditions": [
          { "property": "auth_method", "operator": "!=", "value": "None" },
          { "property": "auth_method", "operator": "!=", "value": "API Key" }
        ] }
      },
      {
        "identifier": "ikea_has_base_path",
        "title": "Base path is defined",
        "level": "Silver",
        "query": { "combinator": "and", "conditions": [
          { "property": "base_path", "operator": "isNotEmpty" }
        ] }
      },
      {
        "identifier": "ikea_has_openapi",
        "title": "Has an OpenAPI spec",
        "level": "Gold",
        "query": { "combinator": "and", "conditions": [
          { "property": "openapi_spec", "operator": "isNotEmpty" }
        ] }
      }
    ]
  }
}
```

## Checkpoint
```
CallMcpTool: server="user-port-eu", toolName="list_scorecards", arguments={}
```
- [ ] `ikea_api_golden_path` appears in the list, bound to blueprint `ikea_api`.
- [ ] Review the seeded APIs: `ikea-orders-api`/`ikea-payments-api` should score well;
      `ikea-inventory-api` (API Key, no docs) should fail Silver/Bronze rules — a nice
      teaching example of "what not to repeat."

## Pause
Say **"Ready for Step 5?"** and wait.
