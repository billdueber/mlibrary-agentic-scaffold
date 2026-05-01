---
id: ticket:memory-server-setup
kind: ticket
status: closed
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
---

# Summary

Document `@modelcontextprotocol/server-memory` setup (installation, OpenCode MCP
configuration, usage notes) in `mlibrary-agentic-scaffold/mcp/recommended-servers.md`.

The initial stub for `memory` server entry has already been added to
`recommended-servers.md` as part of the decision:0001 amendment. This ticket
covers verification that the entry is complete and accurate, and adds any
missing usage guidance.

# Acceptance Criteria

1. `@modelcontextprotocol/server-memory` entry in `recommended-servers.md` is
   accurate and complete: install command, OpenCode JSON config snippet, `MEMORY_FILE_PATH`
   env var documentation, usage notes, and limitation (entity-name lookup, no semantic search).
2. The `agentic-memory.md` instructions file reflects the Tier 2 change (currently still
   describes memU — needs updating to point to the MCP server-memory tool).
3. A CONTEXT.md template entry or note is added so agents know to populate it before
   relying on the MCP memory server.

# Blockers

None.

# Next Move / Next Route

Local edit — verify recommended-servers.md entry, update agentic-memory.md prose,
add CONTEXT.md guidance note.

# Journal

- 2026-04-30: Created to replace ticket:memu-setup after decision:0001 was amended.
  memU rejected (incorrect prior characterization); @modelcontextprotocol/server-memory
  adopted as Tier 2.
- 2026-04-30: Closed. All three acceptance criteria complete:
  (1) recommended-servers.md entry verified complete with MEMORY_FILE_PATH guidance and agent workflow;
  (2) agentic-memory.md updated — comparison table corrected, Recommendation and Setup sections
      rewritten to replace memU with server-memory;
  (3) core.md CONTEXT.md section updated with note on populating both CONTEXT.md and MCP memory store.
