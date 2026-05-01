# ADR-006: Three-Tier Agentic Memory Model

## Status

Accepted

## Date

2026-04-30

## Context

See `.loom/constitution/decisions/decision-0001-agentic-memory-tier.md` for the
full evaluation of eight memory systems and the detailed rationale. This ADR
summarizes that decision in the ADR format for completeness of the `docs/adrs/`
record.

AI agents working on library projects across multiple sessions lose state at
context window and session boundaries. A memory strategy is needed. The key
constraints:

- Solo developer; infrastructure overhead must be minimal.
- GitHub Copilot via OpenCode; no direct Anthropic/OpenAI API key.
- The strategy must work without a persistent daemon process (constitution
  principle: minimal infrastructure, transparent state).
- Richer memory is only worth the infrastructure cost if it demonstrably
  improves recall beyond what a well-maintained file provides.

Eight systems evaluated: mem0, Letta, Graphiti, memU, Beads, sqlite-memory,
`@modelcontextprotocol/server-memory`, `memory-mcp-lite`. See `decision:0001`
for full comparison.

## Decision

Cross-session agentic memory uses a **three-tier model**:

- **Tier 1 (mandatory)**: `CONTEXT.md` at the project root + Loom records.
  Git-tracked, transparent, zero infrastructure. Every project must maintain
  this.
- **Tier 2 (recommended)**: `@modelcontextprotocol/server-memory` — the official
  MCP reference implementation. Zero infrastructure; installs via
  `npx -y @modelcontextprotocol/server-memory`; persists a knowledge graph as a
  local JSONL file; works with any LLM including GitHub Copilot.
- **Tier 3 (conditional)**: mem0 (Python + Docker). Adopt only when semantic
  retrieval becomes clearly valuable and infrastructure cost is acceptable.

`memory-mcp-lite` (SQLite + FTS5) is deferred for review ~late June 2026; it
was too new at decision time (12 days old).

## Consequences

### Positive

- Tier 1 requires no tooling installation; any project can start immediately.
- Tier 2 requires only `npx` (Node.js); no API key, no database.
- The tiers are additive — projects adopt Tier 2 and 3 when the value is clear,
  not upfront.

### Negative

- Tier 2 (`@modelcontextprotocol/server-memory`) supports entity-name lookup
  only, not fuzzy or semantic search.
- Tier 1 is only as good as how well the agent maintains `CONTEXT.md`; a stale
  file provides false confidence.
- If `memory-mcp-lite` matures significantly, the Tier 2 recommendation may
  need updating.

### Neutral

- Loom's `.loom/memory/` is in-session scratch only, not a substitute for Tier 2.
- `mcp/recommended-servers.md` contains the exact MCP server config for Tier 2
  and Tier 3.
- This decision should be revisited quarterly given the fast-moving agentic
  tooling landscape.

## Alternatives Considered

See `.loom/constitution/decisions/decision-0001-agentic-memory-tier.md` for the
full alternatives table.

Key rejections: memU (initial characterization was wrong; actually requires
Python 3.13+ and OpenAI API key), Letta (full agent runtime, too heavy),
Graphiti (Neo4j required).

## Open Questions

- `memory-mcp-lite` review: ~late June 2026. If matured, consider replacing
  Tier 2. See `ticket:memory-revisit`.
