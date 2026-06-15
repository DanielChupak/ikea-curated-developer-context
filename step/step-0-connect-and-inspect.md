# Step 0 — Connect & Inspect

**Goal:** Confirm the Port MCP server works and understand what already exists, so we build
*additively* and never collide with the existing model.

## Problem
We're about to add blueprints and data. If we blindly create things, we could clash with an
existing catalog. Inspect first.

## Do together (MCP — run only this step)

1. Confirm connectivity and list the current model:

```
CallMcpTool: server="user-port-eu", toolName="list_blueprints", arguments={}
```

2. Skim the result. Things to note:
   - Is there an existing `service`, `_team`, `githubRepository` model? (Common in mature orgs.)
   - Any identifiers starting with `ikea_`? (A previous run of this workshop.)

3. Decide on namespacing. **Default: namespace everything with `ikea_` (blueprints) and
   `ikea-` (entities).** This keeps the workshop self-contained and reversible.

## Checkpoint
- [ ] `list_blueprints` returned a list with **no error**.
- [ ] You've confirmed we'll use the `ikea_` / `ikea-` namespace.

## Notes
- If `ikea_*` blueprints already exist from a prior run, that's fine — `upsert_*` is idempotent
  and will update rather than duplicate.
- **Do not** modify or delete any non-`ikea` resources.

## Pause
Say **"Ready for Step 1?"** and wait for confirmation before continuing.
