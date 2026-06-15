# Step 2 — Context Blueprints

**Goal:** Model the *curated* context a developer needs: standards (golden path), security
requirements, and recent incident history — and connect them to APIs and domains.

## Problem
Standards, security rules, and incidents are exactly the things a developer should see before
starting a task. They need their own blueprints and relations so Port can serve them in context.

## Artifacts
- `ikea_api_standard` — a curated standard / golden-path rule (naming, versioning, auth, …)
- `ikea_security_requirement` — a security control APIs must meet
- `ikea_incident` — recent incident, related to affected APIs and a domain
- New relations on `ikea_api`: `security_requirements` (many)

## Do together (MCP — run only this step)

### 1. `ikea_api_standard`
```
CallMcpTool: server="user-port-eu", toolName="upsert_blueprint", arguments={
  "blueprint": {
    "identifier": "ikea_api_standard",
    "title": "IKEA API Standard",
    "icon": "Book",
    "schema": {
      "properties": {
        "category": { "title": "Category", "type": "string",
                      "enum": ["Naming", "Versioning", "Auth", "Error Handling", "Pagination", "Observability", "Security"] },
        "severity": { "title": "Severity", "type": "string",
                      "enum": ["Required", "Recommended"],
                      "enumColors": { "Required": "red", "Recommended": "yellow" } },
        "content":  { "title": "Guidance", "type": "string", "format": "markdown" },
        "doc_url":  { "title": "Docs", "type": "string", "format": "url" }
      },
      "required": []
    },
    "calculationProperties": {},
    "aggregationProperties": {},
    "mirrorProperties": {},
    "relations": {
      "domain": { "title": "Domain (if scoped)", "target": "ikea_domain", "required": false, "many": false }
    }
  }
}
```

### 2. `ikea_security_requirement`
```
CallMcpTool: server="user-port-eu", toolName="upsert_blueprint", arguments={
  "blueprint": {
    "identifier": "ikea_security_requirement",
    "title": "Security Requirement",
    "icon": "Lock",
    "schema": {
      "properties": {
        "description": { "title": "Description", "type": "string", "format": "markdown" },
        "severity":    { "title": "Severity", "type": "string",
                         "enum": ["Critical", "High", "Medium"],
                         "enumColors": { "Critical": "red", "High": "orange", "Medium": "yellow" } },
        "doc_url":     { "title": "Policy Link", "type": "string", "format": "url" }
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

### 3. `ikea_incident`
```
CallMcpTool: server="user-port-eu", toolName="upsert_blueprint", arguments={
  "blueprint": {
    "identifier": "ikea_incident",
    "title": "IKEA Incident",
    "icon": "Alert",
    "schema": {
      "properties": {
        "status":      { "title": "Status", "type": "string",
                         "enum": ["Open", "Mitigated", "Resolved"],
                         "enumColors": { "Open": "red", "Mitigated": "yellow", "Resolved": "green" } },
        "severity":    { "title": "Severity", "type": "string",
                         "enum": ["SEV1", "SEV2", "SEV3"],
                         "enumColors": { "SEV1": "red", "SEV2": "orange", "SEV3": "yellow" } },
        "summary":     { "title": "Summary", "type": "string", "format": "markdown" },
        "occurred_at": { "title": "Occurred At", "type": "string", "format": "date-time" }
      },
      "required": []
    },
    "calculationProperties": {},
    "aggregationProperties": {},
    "mirrorProperties": {},
    "relations": {
      "affected_api": { "title": "Affected API", "target": "ikea_api", "required": false, "many": true },
      "domain":       { "title": "Domain", "target": "ikea_domain", "required": false, "many": false }
    }
  }
}
```

### 4. Add the `security_requirements` relation onto `ikea_api`
`upsert_blueprint` merges, but relations must be sent as the full relations map. Re-send
`ikea_api` with **all** relations (the three from Step 1 + the new one):

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
        "api_type":      { "title": "API Type", "type": "string", "enum": ["REST", "GraphQL", "gRPC", "AsyncAPI"] },
        "version":       { "title": "Version", "type": "string" },
        "base_path":     { "title": "Base Path", "type": "string" },
        "auth_method":   { "title": "Auth Method", "type": "string", "enum": ["OAuth2", "API Key", "mTLS", "None"],
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
      "owning_team":           { "title": "Owning Team", "target": "ikea_team", "required": false, "many": false },
      "domain":                { "title": "Domain", "target": "ikea_domain", "required": false, "many": false },
      "depends_on":            { "title": "Depends On / Reuses", "target": "ikea_api", "required": false, "many": true },
      "security_requirements": { "title": "Security Requirements", "target": "ikea_security_requirement", "required": false, "many": true }
    }
  }
}
```

## Checkpoint
```
CallMcpTool: server="user-port-eu", toolName="list_blueprints", arguments={ "identifiers": ["ikea_api", "ikea_incident", "ikea_api_standard", "ikea_security_requirement"] }
```
- [ ] All four blueprints exist.
- [ ] `ikea_api` now has a `security_requirements` relation.
- [ ] `ikea_incident` has `affected_api` and `domain` relations.

## Pause
Say **"Ready for Step 3?"** and wait.
