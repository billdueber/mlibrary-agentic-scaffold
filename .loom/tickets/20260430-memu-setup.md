---
id: ticket:memu-setup
kind: ticket
status: cancelled
change_class: documentation-explanation
risk_class: low
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-04-30T00:00:00Z
scope:
  kind: workspace
links:
  plan:
    - plan:mlibrary-agentic-scaffold-stabilize
  research:
    - research:agentic-memory
  decision:
    - decision:0001
depends_on:
  - ticket:audit-instructions
---

# Summary

Document memU setup (installation, MCP configuration, OpenCode integration) in
`mlibrary-agentic-scaffold/mcp/recommended-servers.md`.

# Acceptance Criteria

1. memU listed in `recommended-servers.md` with install command, MCP server
   config snippet, and usage notes.
2. Instructions verified to work (or annotated with known caveats).

# Blockers

None.

# Next Move / Next Route

Local edit — research memU current install docs, then update `recommended-servers.md`.

# Journal

- 2026-04-30: Ticket created as Wave 2 stub.
- 2026-04-30: Cancelled. Prior research characterization of memU as "Node.js, zero-infra" was incorrect. memU requires Python 3.13+, OpenAI API key, optionally PostgreSQL+pgvector. Decision:0001 amended to adopt @modelcontextprotocol/server-memory as Tier 2 instead. See ticket:memory-server-setup.
