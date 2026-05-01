---
id: ticket:memory-revisit
kind: ticket
status: proposed
change_class: research-review
risk_class: low
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-04-30T00:00:00Z
not_before: 2026-06-25
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

Revisit the agentic memory Tier 2 decision. Evaluate `memory-mcp-lite`
(SQLite + FTS5, coding-agent-focused schema) now that ~8 weeks have passed
since its April 2026 launch. Determine whether it should replace or supplement
`@modelcontextprotocol/server-memory` as Tier 2.

# Not Before

2026-06-25 (8 weeks from decision date)

# Acceptance Criteria

1. `memory-mcp-lite` current state assessed: npm downloads, GitHub stars,
   issues, whether semantic search has landed.
2. Comparison against `@modelcontextprotocol/server-memory` updated in
   `research:agentic-memory`.
3. Either: decision:0001 amended to adopt memory-mcp-lite as Tier 2, OR
   research:agentic-memory notes that @modelcontextprotocol/server-memory
   remains the correct Tier 2 with rationale.

# Blockers

Not before 2026-06-25.

# Next Move / Next Route

Deferred. Do not start until not_before date. When ready: fetch current
memory-mcp-lite README and npm stats, compare to @modelcontextprotocol/server-memory,
update research and decision as warranted.

# Journal

- 2026-04-30: Created as part of decision:0001 amendment. User requested 8-week
  revisit to assess memory-mcp-lite maturity.
