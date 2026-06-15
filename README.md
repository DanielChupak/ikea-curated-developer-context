# IKEA — Curated Developer Context (Cursor + Port setup)

This skill guides an IKEA developer through building a **curated context layer** in Port so
that Port serves task-relevant context (standards, ownership, reusable APIs, security
requirements, recent incidents) straight into the IDE via the **Port MCP server**.

You run the skill yourself — it walks you through the build one step at a time.

---

## 1. Prerequisites

- **Cursor** (latest).
- A **Port account** in the **EU** region (`https://app.getport.io`). EU is assumed throughout;
  if you're on US, see "Region note" below.
- Permission to create blueprints, entities, and scorecards in your Port org.

## 2. Connect the Port MCP server to Cursor

The skill talks to Port through the `user-port-eu` MCP server. Add it to Cursor:

1. Open **Cursor → Settings → MCP → Add new MCP server** (this edits `~/.cursor/mcp.json`).
2. Add the Port server. Port's hosted MCP uses OAuth — the recommended config is:

```json
{
  "mcpServers": {
    "user-port-eu": {
      "url": "https://mcp.getport.io/v1"
    }
  }
}
```

3. Reload Cursor. When prompted, complete the **OAuth login** to your Port org.
4. Verify the tools are available (you should see Port tools like `list_blueprints`).

> **Region note:** This skill targets the **EU** region (`user-port-eu`). If your Port org is
> in the **US**, point the server at the US MCP endpoint and rename the server to `user-port-us`,
> then do a find-and-replace of `user-port-eu` → `user-port-us` in this skill's step files.

> Authoritative setup docs: search Port's docs for **"Port MCP server"** / **"Model Context
> Protocol"**, or use the in-skill `search_port_knowledge_sources` tool.

## 3. Install the skill

Copy this folder into your Cursor skills directory:

```bash
cp -R ikea-curated-developer-context ~/.cursor/skills/
```

## 4. Run it

Open Cursor in any project and say:

> **"Start the IKEA curated developer context onboarding."**

The skill previews the journey, then waits for you to say **"let's go"** before Step 0.
It builds **one step at a time** and pauses for you after each step.

---

## Data strategy

The workshop seeds **namespaced dummy data** (everything prefixed `ikea-`) so it never
collides with or overwrites anything already in your Port org. You get a realistic catalog
(teams, domains, APIs, standards, security requirements, incidents) to prove the flow.

## Make it real (optional, after the workshop)

Swap dummy data for live GitHub data:

1. In Port, install the **GitHub integration** (Builder → Data sources → GitHub).
2. Map repositories to your `ikea_api` blueprint (or relate `ikea_api` to the existing
   `githubRepository` / `service` blueprints already in your org).
3. Re-run the Step 5 context query — Port now serves real ownership, PRs, and history.

The GitHub-backed MCP tools (`git_hub_list_pull_requests`, `git_hub_search_code`,
`git_hub_get_file_content`) can then enrich answers with live code context.

---

## Package contents

```
ikea-curated-developer-context/
├── SKILL.md          # Orchestrator — the workshop logic
├── README.md         # This file
├── VALIDATION.md     # End-to-end checklist
└── steps/
    ├── step-0-connect-and-inspect.md
    ├── step-1-catalog-foundations.md
    ├── step-2-context-blueprints.md
    ├── step-3-seed-data.md
    ├── step-4-golden-path-scorecard.md
    ├── step-5-serve-via-mcp.md
    └── step-6-demo-new-api.md
```
