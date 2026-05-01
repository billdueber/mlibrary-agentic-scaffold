---
id: decision:0001
kind: decision
status: active
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
scope:
  kind: workspace
links:
  research:
    - research:agentic-memory
---

# Decision

Cross-session agentic memory for this workspace uses a three-tier approach:

- **Tier 1 (mandatory)**: `CONTEXT.md` at the project root + Loom records. Git-tracked,
  transparent, zero infrastructure.
- **Tier 2 (recommended)**: `@modelcontextprotocol/server-memory` (official Anthropic MCP
  reference implementation). Zero infrastructure — persists a knowledge graph as a local
  JSONL file. Install: `npx -y @modelcontextprotocol/server-memory`. No API key, no
  database, works with any LLM including GitHub Copilot.
- **Tier 3 (conditional)**: mem0 (vector + graph MCP memory). Python + Docker required.
  Adopt only when semantic retrieval becomes clearly valuable and infrastructure cost is
  acceptable.

**Deferred candidate**: `memory-mcp-lite` (SQLite + FTS5, coding-agent-focused schema).
Published April 2026; too new to adopt now. Revisit ~late June 2026. If it has matured,
consider replacing Tier 2 for better semantic fit.

# Why This Decision Exists

The owner is a solo developer building libraries. Context window exhaustion causes
agents to lose state across sessions. A lightweight, git-native memory layer
(`CONTEXT.md`) is always valuable. A richer system is only worth the infrastructure
cost if it demonstrably improves recall quality beyond what a well-maintained
CONTEXT.md provides.

Eight systems were evaluated: mem0, Letta, Graphiti, memU, Beads, sqlite-memory,
`@modelcontextprotocol/server-memory`, and `memory-mcp-lite`. See
`research:agentic-memory` for full evidence.

# Amendment Note (April 2026)

An initial version of this decision adopted memU as Tier 2 based on a characterization
of memU as "Node.js only, no database." That characterization was incorrect. Current
memU requires Python 3.13+, an OpenAI API key, and optionally PostgreSQL+pgvector
(Docker). When the correct requirements were verified, the rationale for preferring
memU over mem0 collapsed. `@modelcontextprotocol/server-memory` was identified as the
actual zero-infrastructure option and adopted in its place.

# Alternatives Considered

| System | Reason Not Chosen As Tier 2 |
|--------|------------------------------|
| memU | Initial characterization was wrong; actual requirements are Python 3.13+, OpenAI API key, optional PostgreSQL. Not zero-infra. |
| mem0 | Requires Python + Docker; infrastructure burden outweighs benefit at this scale. Retained as Tier 3. |
| memory-mcp-lite | 12 days old at decision time; too new to adopt. Deferred to late June 2026 review. |
| Letta | Full agent runtime — too heavy; not Copilot-compatible |
| Graphiti | Requires Neo4j (Docker); overkill for solo dev |
| Beads | Task tracker, not a memory system |
| sqlite-memory | No MCP server; not agent-facing |

`@modelcontextprotocol/server-memory` was selected as Tier 2 because it is the official
Anthropic MCP reference implementation: zero infrastructure, `npx` install, works with
any LLM, persists to a local JSONL file. Limitation accepted: entity-name lookup only,
no fuzzy/semantic search.

# Consequences

- Every library project this workspace governs should maintain `CONTEXT.md`.
- The `mlibrary-agentic-scaffold/instructions/core.md` CONTEXT.md convention is the authoritative
  guide for how agents should read and update cross-session context.
- `@modelcontextprotocol/server-memory` setup instructions should be in
  `mcp/recommended-servers.md`.
- `memory-mcp-lite` should be reviewed ~late June 2026 (`ticket:memory-revisit`).
- mem0's OpenMemory was sunset in early 2025; `agentic-memory.md` should be reviewed
  quarterly as the landscape evolves.

# Revisit Conditions

- `memory-mcp-lite` matures significantly by late June 2026 → consider replacing Tier 2
- A zero-infrastructure semantic search MCP option emerges
- The owner's workflow generates enough repeated re-explanation to justify mem0 infra
- OpenCode gains native cross-session memory support

# Supersession

This record supersedes the initial April 2026 version that adopted memU as Tier 2.
The prior version contained a factual error about memU's infrastructure requirements.

# Amendment — May 2026: Tier 3 mem0 is now cloud-only

The original decision described Tier 3 (mem0) as requiring "Python + Docker" for
self-hosted operation. That premise is no longer accurate:

- The `mem0-mcp` npm package was **archived in March 2026** and is no longer maintained.
- mem0 no longer supports a self-hosted MCP integration. The product is **cloud-only**.
- The current integration point is the HTTP endpoint `https://mcp.mem0.ai/mcp`,
  which requires a mem0 API key. There is no Docker self-hosting path for MCP.

**Impact on this decision:**
- The original rationale for Tier 3 ("Python + Docker; infrastructure burden outweighs
  benefit") still holds — but the nature of the burden changed from Docker infra to
  cloud API dependency and data residency.
- Tier 3 adoption conditions remain unchanged: adopt only when semantic retrieval is
  clearly valuable. The infrastructure concern is now API key management and data
  leaving the local machine, not Docker overhead.
- `mcp/recommended-servers.md`, `instructions/agentic-memory.md`, and
  `wiki-cross-session-memory` have been updated to reflect the cloud-only status.

**Note on MCP config type:** The installer (`bin/initialize_for_opencode`) currently
emits `"type":"http"` for the mem0 entry. The OpenCode schema only defines `"local"`
and `"remote"` as valid types. The correct value is likely `"remote"`. This is tracked
in `ticket:20260501-pz0qzejg-mem0-type-http-nonstandard` and has not yet been fixed.
