# Step 1 — Catalog Foundations

**Goal:** Model the backbone Port needs to answer "what exists and who owns it": teams,
domains, and APIs.

## Problem
Port can't serve task context if it doesn't know what an API is, which domain it belongs to,
or which team owns it. We create three blueprints with the relations that connect them.

## Artifacts
- `ikea_team` — owning team
- `ikea_domain` — business domain (e.g. Checkout, Catalog), owned by a team
- `ikea_api` — the API itself, with the properties a developer needs as context, and
  relations to team, domain, and other APIs (for reuse / dependency)

> **Order matters:** create `ikea_team` → `ikea_domain` → `ikea_api` so relation targets exist.

## Do together (MCP — run only this step)

### 1. `ikea_team`
```
CallMcpTool: server="user-port-eu", toolName="upsert_blueprint", arguments={
  "blueprint": {
    "identifier": "ikea_team",
    "title": "IKEA Team",
    "icon": "Team",
    "schema": {
      "properties": {
        "description": { "title": "Description", "type": "string" },
        "slack_channel": { "title": "Slack Channel", "type": "string" }
      },
      "required": []
    },
    "calculationProperties": {},
    "aggregationProperties": {},
    "mirrorProperties": {},
    "relations": {}
  }
}
```

### 2. `ikea_domain`
```
CallMcpTool: server="user-port-eu", toolName="upsert_blueprint", arguments={
  "blueprint": {
    "identifier": "ikea_domain",
    "title": "IKEA Domain",
    "icon": "Folder",
    "schema": {
      "properties": {
        "description": { "title": "Description", "type": "string", "format": "markdown" }
      },
      "required": []
    },
    "calculationProperties": {},
    "aggregationProperties": {},
    "mirrorProperties": {},
    "relations": {
      "owning_team": { "title": "Owning Team", "target": "ikea_team", "required": false, "many": false }
    }
  }
}
```

### 3. `ikea_api`
```
CallMcpTool: server="user-port-eu", toolName="upsert_blueprint", arguments={
  "blueprint": {
    "identifier": "ikea_api",
    "title": "IKEA API",
    "icon": "Microservice",
    "schema": {
      "properties": {
        "description":   { "title": "Description", "type": "string", "format": "markdown" },
        "lifecycle":     { "title": "Lifecycle", "type": "string",
                           "enum": ["Idea", "In Development", "Production", "Deprecated"],
                           "enumColors": { "Idea": "lightGray", "In Development": "yellow", "Production": "green", "Deprecated": "red" } },
        "api_type":      { "title": "API Type", "type": "string",
                           "enum": ["REST", "GraphQL", "gRPC", "AsyncAPI"] },
        "version":       { "title": "Version", "type": "string" },
        "base_path":     { "title": "Base Path", "type": "string" },
        "auth_method":   { "title": "Auth Method", "type": "string",
                           "enum": ["OAuth2", "API Key", "mTLS", "None"],
                           "enumColors": { "OAuth2": "green", "API Key": "yellow", "mTLS": "blue", "None": "red" } },
        "has_documentation": { "title": "Has Documentation", "type": "boolean" },
        "openapi_spec":  { "title": "OpenAPI Spec", "type": "string", "spec": "open-api" }
      },
      "required": []
    },
    "calculationProperties": {},
    "aggregationProperties": {},
    "mirrorProperties": {},
    "relations": {
      "owning_team": { "title": "Owning Team", "target": "ikea_team", "required": false, "many": false },
      "domain":      { "title": "Domain", "target": "ikea_domain", "required": false, "many": false },
      "depends_on":  { "title": "Depends On / Reuses", "target": "ikea_api", "required": false, "many": true }
    }
  }
}
```

## Checkpoint
```
CallMcpTool: server="user-port-eu", toolName="list_blueprints", arguments={ "identifiers": ["ikea_team", "ikea_domain", "ikea_api"] }
```
- [ ] All three blueprints exist.
- [ ] `ikea_api` has relations `owning_team`, `domain`, `depends_on`.

## Manual fallback (UI)
Builder → **+ Blueprint** → paste each JSON above (the `blueprint` object) into the JSON editor.

## Pause
Say **"Ready for Step 2?"** and wait.
