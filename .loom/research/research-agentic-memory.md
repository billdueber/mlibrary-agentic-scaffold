---
id: research:agentic-memory
kind: research
status: active
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-04-30T00:00:00Z
scope:
  kind: workspace
links:
  initiative:
    - initiative:mlibrary-agentic-scaffold-stable
  decision:
    - decision:0001
external_refs:
  mem0: https://github.com/mem0ai/mem0
  letta: https://github.com/letta-ai/letta
  graphiti: https://github.com/getzep/graphiti
  memu: https://github.com/NevaMind-AI/memU
  beads: https://github.com/nicholasgasior/beads
  mcp-server-memory: https://github.com/modelcontextprotocol/servers/tree/main/src/memory
  memory-mcp-lite: https://www.npmjs.com/package/memory-mcp-lite
---

# Question

Which cross-session memory approach is appropriate for a solo developer using
OpenCode + GitHub Copilot to build Ruby/JVM libraries?

# Why This Matters

AI coding agents are stateless. Each session starts cold. Without a memory
layer, agents repeatedly rediscover context, re-explain design decisions, and
lose awareness of in-progress multi-session work. For library development —
where accumulated decisions (API shape, dependency choices, testing conventions)
matter — this is a real productivity cost.

# Scope

Eight systems evaluated (April 2026 re-review): mem0, Letta (MemGPT), Graphiti,
memU, Beads, sqlite-memory, `@modelcontextprotocol/server-memory`,
`memory-mcp-lite`. Constraint: must be compatible with GitHub Copilot via MCP
or file-based convention. Full prose analysis in
`mlibrary-agentic-scaffold/instructions/agentic-memory.md`.

# Method

GitHub README review (current as of April 2026) + npm/pip install verification.
Each system evaluated on: (1) actual infrastructure requirement, (2) MCP
compatibility, (3) GitHub Copilot compatibility, (4) memory quality,
(5) maintenance risk.

**Note**: A prior version of this research (April 2026) incorrectly characterized
memU as "Node.js only, no database." Current README shows memU requires Python
3.13+, an OpenAI API key, and optionally PostgreSQL+pgvector. The prior
characterization was based on a summary that did not match the actual project.
This version corrects that error.

# Sources

- `mlibrary-agentic-scaffold/instructions/agentic-memory.md` — initial prose analysis
- GitHub READMEs verified April 2026 for each system
- npm `@modelcontextprotocol/server-memory` package page
- npm `memory-mcp-lite` package page (published April 2026)

# Evidence

| System | Stars (Apr 2026) | Infrastructure (actual) | MCP | Copilot-compat | Notes |
|--------|-----------------|------------------------|-----|----------------|-------|
| `@modelcontextprotocol/server-memory` | Official Anthropic | None — local JSONL file | Yes (`npx`) | Yes | Entity graph; no semantic search |
| mem0 | 54.5k | Python + Qdrant (Docker); cloud API option | Yes (cloud primarily) | Yes (via MCP) | OpenMemory sunset early 2025 |
| Graphiti | 25.6k | Python + Neo4j or FalkorDB (Docker, mandatory) | Yes | Indirect | Temporal knowledge graph |
| memU | ~13.5k | **Python 3.13+, OpenAI API key, optionally PostgreSQL+pgvector** | Yes | Yes | Prior notes were wrong — not Node.js |
| Letta | 22k | Python server (stateful agent runtime) | No (own API) | No | Not Copilot-compatible |
| Beads | Small | SQLite + Node.js CLI | CLI only | Yes | Task tracker, not memory system |
| sqlite-memory | 44 | SQLite C extension | None (no MCP server) | No | No agent-facing MCP; not actionable |
| memory-mcp-lite | New (Apr 2026) | None — SQLite (libSQL) | Yes (`npx`) | Yes | FTS5 only; no semantic search yet |

Key findings:
- Procedural memory is already solved by `AGENTS.md` + skills.
- `CONTEXT.md` (git-native, zero-infra) adequately solves working memory at this scale.
- The original rationale for memU (Node.js, no DB) was factually incorrect.
  memU is a Python project with similar infrastructure requirements to mem0.
- `@modelcontextprotocol/server-memory` (official Anthropic reference implementation)
  is the correct zero-infra Tier 2: no Python, no API key, no database, `npx` install.
  Limitation: entity-name lookup only, no fuzzy/semantic search.
- `memory-mcp-lite` is a promising coding-agent-focused alternative (SQLite + FTS5,
  decisions/architecture/gotchas schema) but is 12 days old at time of review.
  Treat as a deferred candidate; revisit ~late June 2026.

# Rejected Options

**memU**: Initial characterization ("Node.js only, no database") was incorrect.
Current project requires Python 3.13+, OpenAI API key, and optionally Docker +
PostgreSQL. At that infrastructure level, mem0 (54.5k stars, more mature) is
the better semantic-memory choice. memU is rejected as Tier 2 on grounds of
incorrect prior assessment; the real-infra version does not beat its alternatives.

**mem0**: Viable but Cloud API is the practical path; self-hosted requires
Python + Qdrant + Docker. Retained as Tier 3 (conditional on semantic need).

**Letta**: Requires stateful Letta-compatible agent. GitHub Copilot is stateless.
Incompatible.

**Graphiti**: Knowledge graph is compelling but Docker + Neo4j is heavy for solo
dev. Rejected as too much infra.

**Beads**: Task tracker, not a general memory system.

**sqlite-memory**: No MCP server; not agent-facing. Cannot use today.

# Null Results

- mem0's OpenMemory (local-first variant) was sunset in early 2025.
- memU's prior characterization as "Node.js, zero-infra" was incorrect. The
  actual project requires Python, an OpenAI API key, and optional PostgreSQL.
- No zero-infra semantic search MCP server with significant adoption exists
  as of April 2026.

# Conclusions

1. `CONTEXT.md` + Loom records is the correct mandatory tier. Zero
   infrastructure, transparent, git-versionable.
2. `@modelcontextprotocol/server-memory` is the correct Tier 2 (replaces memU):
   zero infrastructure, `npx` install, works with any LLM including Copilot.
   Accepts entity-name lookup without semantic search as an acceptable tradeoff
   at this project scale.
3. mem0 is viable as Tier 3 when semantic retrieval becomes clearly needed and
   Docker infrastructure is acceptable.
4. `memory-mcp-lite` is a deferred candidate. Review ~late June 2026 (8 weeks
   from initial tier decision).
5. This landscape evolves fast. Research should be reviewed quarterly.

# Recommendations

- Adopt revised three-tier model as recorded in `decision:0001` (amended April 2026).
- Document `@modelcontextprotocol/server-memory` setup in `mcp/recommended-servers.md`.
- Set revisit for `memory-mcp-lite` at ~late June 2026.

# Open Questions

- Will `memory-mcp-lite` mature into a viable semantic-search alternative by June 2026?
- Does OpenCode gain native cross-session memory at any point?
- Has a viable local mem0 replacement emerged since OpenMemory was sunset?

# Audit Note (April 2026)

MCP config syntax audit against live `~/.config/opencode/opencode.json` revealed:

- OpenCode uses `"environment"` (not `"env"`) for local MCP server env vars.
- OpenCode requires `"command"` as an array (`["npx", "-y", "pkg"]`), not
  `"command"/"args"` split. `recommended-servers.md` had both bugs in all snippets.
- All code blocks corrected in `recommended-servers.md`.

Loom coexistence assessment: Loom `.loom/memory/` is a grep-based scratchpad for
in-session support context. `@modelcontextprotocol/server-memory` provides durable
cross-session entity-graph recall. They serve adjacent, non-overlapping roles.
The three-tier model in `decision:0001` remains valid with Loom active.

`sequential-thinking` MCP server assessed as complementary to Loom, not redundant:
Loom structures workflow/record discipline; sequential-thinking structures LLM
inference reasoning. Both can coexist.

# Linked Work

- `initiative:mlibrary-agentic-scaffold-stable`
- `decision:0001` — tier decision derived from this research (amended April 2026)
- `ticket:memory-server-setup` — document `@modelcontextprotocol/server-memory` in recommended-servers.md
- `ticket:memory-revisit` — revisit memory-mcp-lite ~late June 2026
