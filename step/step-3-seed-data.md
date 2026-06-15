# Step 3 — Seed Sample Context

**Goal:** Populate the catalog with realistic `ikea-` data so Port has actual context to serve.

## Problem
Empty blueprints serve empty answers. We seed teams, domains, standards, security
requirements, a set of existing APIs (so the developer has something to *reuse*), and a couple
of recent incidents.

> **Order matters:** teams → domains → standards/security → APIs → incidents (so relations resolve).
> Tip: you can pass `"createMissingRelatedEntities": true` to be safe.

## Do together (MCP — run only this step)

### 1. Teams
```
upsert_entity blueprintIdentifier="ikea_team" entity={ "identifier": "ikea-checkout-team", "title": "Checkout Team", "properties": { "description": "Owns cart, payments, returns", "slack_channel": "#team-checkout" } }
upsert_entity blueprintIdentifier="ikea_team" entity={ "identifier": "ikea-catalog-team",  "title": "Catalog Team",  "properties": { "description": "Owns product & inventory APIs", "slack_channel": "#team-catalog" } }
upsert_entity blueprintIdentifier="ikea_team" entity={ "identifier": "ikea-platform-team", "title": "Platform Team", "properties": { "description": "Owns shared platform & standards", "slack_channel": "#team-platform" } }
```

### 2. Domains
```
upsert_entity blueprintIdentifier="ikea_domain" entity={ "identifier": "ikea-checkout", "title": "Checkout", "properties": { "description": "Cart, payments, order placement and returns" }, "relations": { "owning_team": "ikea-checkout-team" } }
upsert_entity blueprintIdentifier="ikea_domain" entity={ "identifier": "ikea-catalog",  "title": "Catalog",  "properties": { "description": "Products, inventory, availability" }, "relations": { "owning_team": "ikea-catalog-team" } }
```

### 3. API Standards (the golden path, curated)
```
upsert_entity blueprintIdentifier="ikea_api_standard" entity={ "identifier": "ikea-std-naming",     "title": "Resource Naming",   "properties": { "category": "Naming",        "severity": "Required",    "content": "Use plural, kebab-case nouns: /return-orders. No verbs in paths.", "doc_url": "https://standards.ikea.dev/naming" } }
upsert_entity blueprintIdentifier="ikea_api_standard" entity={ "identifier": "ikea-std-versioning", "title": "API Versioning",    "properties": { "category": "Versioning",    "severity": "Required",    "content": "Version in the path: /v1/. Breaking changes require a new major version.", "doc_url": "https://standards.ikea.dev/versioning" } }
upsert_entity blueprintIdentifier="ikea_api_standard" entity={ "identifier": "ikea-std-auth",       "title": "Authentication",    "properties": { "category": "Auth",          "severity": "Required",    "content": "All external APIs use OAuth2 (client credentials). No API keys for new APIs.", "doc_url": "https://standards.ikea.dev/auth" } }
upsert_entity blueprintIdentifier="ikea_api_standard" entity={ "identifier": "ikea-std-errors",     "title": "Error Handling",    "properties": { "category": "Error Handling","severity": "Required",    "content": "Return RFC 7807 problem+json. Never leak stack traces.", "doc_url": "https://standards.ikea.dev/errors" } }
upsert_entity blueprintIdentifier="ikea_api_standard" entity={ "identifier": "ikea-std-pagination", "title": "Pagination",        "properties": { "category": "Pagination",    "severity": "Recommended", "content": "Cursor-based pagination with `page[size]` and `page[after]`.", "doc_url": "https://standards.ikea.dev/pagination" } }
```

### 4. Security requirements
```
upsert_entity blueprintIdentifier="ikea_security_requirement" entity={ "identifier": "ikea-sec-pii-encryption", "title": "PII Encryption",   "properties": { "severity": "Critical", "description": "All PII encrypted at rest (AES-256) and in transit (TLS 1.2+).", "doc_url": "https://security.ikea.dev/pii" } }
upsert_entity blueprintIdentifier="ikea_security_requirement" entity={ "identifier": "ikea-sec-oauth2",         "title": "OAuth2 Required", "properties": { "severity": "High",     "description": "External endpoints require OAuth2 with short-lived tokens.", "doc_url": "https://security.ikea.dev/oauth2" } }
upsert_entity blueprintIdentifier="ikea_security_requirement" entity={ "identifier": "ikea-sec-rate-limiting",  "title": "Rate Limiting",   "properties": { "severity": "Medium",   "description": "Enforce per-client rate limits at the gateway.", "doc_url": "https://security.ikea.dev/rate-limits" } }
upsert_entity blueprintIdentifier="ikea_security_requirement" entity={ "identifier": "ikea-sec-audit-logging",  "title": "Audit Logging",   "properties": { "severity": "High",     "description": "Log all writes to PII with actor, timestamp, and resource id.", "doc_url": "https://security.ikea.dev/audit" } }
```

### 5. Existing APIs (so the new task has reusable context)
```
upsert_entity blueprintIdentifier="ikea_api" entity={ "identifier": "ikea-orders-api",    "title": "Orders API",    "properties": { "lifecycle": "Production", "api_type": "REST", "version": "v2", "base_path": "/v2/orders",    "auth_method": "OAuth2", "has_documentation": true,  "description": "Create and manage customer orders." },               "relations": { "owning_team": "ikea-checkout-team", "domain": "ikea-checkout", "security_requirements": ["ikea-sec-pii-encryption", "ikea-sec-oauth2", "ikea-sec-audit-logging"] } }
upsert_entity blueprintIdentifier="ikea_api" entity={ "identifier": "ikea-cart-api",      "title": "Cart API",      "properties": { "lifecycle": "Production", "api_type": "REST", "version": "v1", "base_path": "/v1/carts",     "auth_method": "OAuth2", "has_documentation": true,  "description": "Shopping cart lifecycle." },                          "relations": { "owning_team": "ikea-checkout-team", "domain": "ikea-checkout", "security_requirements": ["ikea-sec-oauth2", "ikea-sec-rate-limiting"] } }
upsert_entity blueprintIdentifier="ikea_api" entity={ "identifier": "ikea-payments-api",  "title": "Payments API",  "properties": { "lifecycle": "Production", "api_type": "REST", "version": "v3", "base_path": "/v3/payments",  "auth_method": "OAuth2", "has_documentation": true,  "description": "Payment authorization and capture." },               "relations": { "owning_team": "ikea-checkout-team", "domain": "ikea-checkout", "depends_on": ["ikea-orders-api"], "security_requirements": ["ikea-sec-pii-encryption", "ikea-sec-oauth2", "ikea-sec-audit-logging"] } }
upsert_entity blueprintIdentifier="ikea_api" entity={ "identifier": "ikea-inventory-api", "title": "Inventory API", "properties": { "lifecycle": "Production", "api_type": "REST", "version": "v1", "base_path": "/v1/inventory", "auth_method": "API Key", "has_documentation": false, "description": "Stock levels (legacy auth — pre-standard)." },        "relations": { "owning_team": "ikea-catalog-team", "domain": "ikea-catalog" } }
```

### 6. Recent incidents (history context)
```
upsert_entity blueprintIdentifier="ikea_incident" entity={ "identifier": "ikea-inc-2026-payments-timeout", "title": "Payments timeouts under load", "properties": { "status": "Resolved", "severity": "SEV2", "summary": "Payments API connection pool exhausted during peak. Fix: pool sizing + circuit breaker.", "occurred_at": "2026-05-12T14:30:00Z" }, "relations": { "affected_api": ["ikea-payments-api", "ikea-orders-api"], "domain": "ikea-checkout" } }
upsert_entity blueprintIdentifier="ikea_incident" entity={ "identifier": "ikea-inc-2026-cart-overload",     "title": "Cart API 5xx spike",          "properties": { "status": "Mitigated","severity": "SEV3", "summary": "Cart API returned 5xx due to missing rate limits on a noisy client.",                       "occurred_at": "2026-06-01T09:10:00Z" }, "relations": { "affected_api": ["ikea-cart-api"], "domain": "ikea-checkout" } }
```

## Checkpoint
```
CallMcpTool: server="user-port-eu", toolName="list_entities", arguments={ "blueprintIdentifier": "ikea_api", "include": ["$title", "owning_team", "domain", "security_requirements"] }
```
- [ ] APIs return with their owning team, domain, and security requirements populated.
- [ ] Incidents resolve to their affected APIs.

## Pause
Say **"Ready for Step 4?"** and wait.
