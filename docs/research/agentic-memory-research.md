# Agentic Memory: Research and System Profiles

> This document contains the full research behind the three-tier memory recommendation.
> For the actionable summary and setup instructions, see `instructions/agentic-memory.md`.
>
> Last substantive review: April 2026. The memory tooling space moves fast; re-review quarterly.

---

## The Core Problem

AI coding agents are stateless. Every session starts cold. The agent does not remember:
- Why a particular design was chosen
- What was tried and failed last week
- The current state of in-progress work
- Conventions that are project-specific

This is fine for simple "fix this bug" tasks. It becomes a real problem for:
- Multi-session features spanning days or weeks
- Projects with significant accumulated decisions and context
- Situations where the agent repeatedly rediscovers the same dead ends

---

## Taxonomy of Memory Approaches

| Type | What it stores | Example |
|------|---------------|---------|
| **Semantic** | Facts, preferences, design decisions | "We use msgpack because JSON was too slow" |
| **Episodic** | History of past interactions | "Last session we tried approach X; it didn't work because Y" |
| **Procedural** | How to do things | AGENTS.md, skills, instructions |
| **Working** | Current task state, in-progress work | Issues, TODOs, plan documents |

Procedural memory is solved by `AGENTS.md` + the scaffold instruction files.
The interesting gap is **semantic** and **working** memory across sessions.

---

## Systems Surveyed

### 1. mem0 — Vector + Graph Hybrid

**GitHub**: https://github.com/mem0ai/mem0 (54k+ stars)

**How it works**: Extracts facts from conversation (via LLM), stores them in a vector database
(Qdrant by default) and a graph database (Neo4j optional). On session start, relevant memories
are retrieved by semantic similarity and injected into context.

**MCP support**: Yes — official cloud MCP at `https://mcp.mem0.ai/mcp` (requires API key).
**Note**: The `mem0-mcp` npm package was archived March 2026. The self-hosted MCP server
no longer exists; mem0 is cloud-only for MCP access.

**Infrastructure required**: Cloud API key from `app.mem0.ai` (free tier available). No local
Docker required for MCP usage. OpenMemory (the local-first version) was sunset early 2025.

**Multi-project isolation**: Cloud service handles isolation automatically per API key/user.

**Tradeoffs**:
- Pro: Mature, widely used, good retrieval quality
- Pro: MCP server; works with any MCP-capable agent
- Pro: No local Docker needed anymore (cloud API)
- Con: Cloud dependency; requires API key
- Con: Memory extraction is LLM-dependent; hallucinated or wrong memories are hard to detect
- Con: Paid tiers for heavy usage

**Verdict**: Best option for semantic retrieval if you accept cloud dependency. Free tier is
usable for solo developer workloads.

---

### 2. `@modelcontextprotocol/server-memory` — Official Anthropic Reference

**GitHub**: https://github.com/modelcontextprotocol/servers

**How it works**: Stores a knowledge graph (entities + relations + observations) in a local
`.jsonl` file. Retrieval is by entity name lookup — not fuzzy/semantic. Zero infrastructure.

**MCP support**: Yes — this is itself an MCP server.

**Infrastructure required**: Node.js (npx). No database, no services.

**Multi-project isolation**: Point each project at a different `MEMORY_FILE_PATH`.

**Tradeoffs**:
- Pro: Zero infrastructure
- Pro: Human-readable, git-committable file
- Pro: Official Anthropic reference implementation
- Con: Entity name lookup only — no semantic similarity search
- Con: Knowledge graph schema may not fit all use cases

**Verdict**: Best Tier 2 option. Start here before investing in Qdrant.

---

### 3. Letta (formerly MemGPT) — Stateful Agent Platform

**GitHub**: https://github.com/letta-ai/letta (22k+ stars)

**How it works**: Agents have explicit memory "blocks" that they can rewrite using tool calls.
Core memory (always in context) + archival memory (retrieved when needed).

**MCP support**: No — requires a Letta-compatible agent, not a generic LLM call.

**GitHub Copilot compatible**: No.

**Verdict**: Not applicable for this setup. Architecturally most sophisticated; useful if
you ever switch to an agent platform that supports it.

---

### 4. Graphiti (Zep) — Temporal Knowledge Graph

**GitHub**: https://github.com/getzep/graphiti (25k+ stars)

**How it works**: Extracts facts as graph edges with timestamps. Supports temporal queries
("what did we decide last week?"). The temporal aspect is its differentiator.

**MCP support**: Yes.

**Infrastructure required**: Python service + Neo4j (Docker).

**Tradeoffs**:
- Pro: Temporal queries are uniquely useful for long-lived team projects
- Con: Neo4j is heavy infrastructure
- Con: Overkill for solo single-developer library work

**Verdict**: Best for team projects with long history. Overengineered for solo use.

---

### 5. memU — Originally Described as File-Based

> **Note (April 2026)**: memU was initially evaluated as "Node.js only, zero infrastructure."
> Further investigation showed it actually requires Python 3.13+, an OpenAI API key, and
> optionally PostgreSQL+pgvector. The original description was wrong. It was removed from
> the Tier 2 recommendation. See `decision:0001` (amended).

---

### 6. Beads — Issue Tracker as Agent Memory (Steve Yegge)

**Blog post**: https://steve-yegge.medium.com/introducing-beads-a-coding-agent-memory-system-637d7d92514a

**How it works**: Not a memory system — a structured issue tracker for agents. Creates and
tracks issues in SQLite with a dependency graph. `bd ready --json` returns unblocked work
as structured data. Key insight: markdown TODO files are write-only for agents; Beads makes
state queryable.

**Infrastructure required**: SQLite + Node.js CLI.

**Tradeoffs**:
- Pro: Zero infrastructure; queryable task state
- Pro: `discovered-from` links let agents record work noticed during implementation
- Con: Focused on working/task memory, not semantic memory
- Con: Immature; Yegge wrote it for personal use

**Verdict**: Excellent complement to CONTEXT.md for multi-session feature work.
Solves task tracking, not semantic recall.

---

### 7. sqlite-memory — Markdown + SQLite + Semantic Search

**GitHub**: https://github.com/sqliteai/sqlite-memory

**How it works**: Stores agent memories as Markdown, with embeddings in SQLite (via
sqlite-vector extension). Supports semantic search, hybrid retrieval (keyword + semantic),
and offline-first sync.

**Infrastructure required**: SQLite + Node.js. Embedding model runs locally.

**Tradeoffs**:
- Pro: Single `.db` file — portable and git-committable
- Pro: Semantic search without a vector database service
- Con: Very new (2025); small community
- Con: On-device embedding requires local ML runtime

**Verdict**: Promising but immature as of early 2026. Worth watching.

---

### 8. memory-mcp-lite — SQLite + FTS5, Coding-Agent-Focused Schema

Published April 2026. Too new to evaluate. Revisit late June 2026 (see `ticket:memory-revisit`).

---

## Comparison Table

| System | Storage | Infrastructure | Semantic Search | MCP | Copilot Compatible | Maturity |
|--------|---------|----------------|-----------------|-----|--------------------|----------|
| `CONTEXT.md` | Markdown file | None | No | No | Yes | — |
| **`@modelcontextprotocol/server-memory`** | Local JSONL | None | No (entity lookup) | Yes | Yes | Official Anthropic |
| **mem0** | Qdrant + optional Neo4j | Python + Docker | Yes | Yes | Yes | High |
| **Graphiti** | Neo4j / FalkorDB | Python + Docker + graph DB | Yes | Yes | Indirect | Medium |
| **Beads** | SQLite | Node.js CLI | No (structured) | CLI only | Yes | Low |
| **sqlite-memory** | SQLite file | SQLite C extension | Yes (local embeddings) | None | No | Low |
| **memory-mcp-lite** | SQLite (libSQL) | None | FTS5 only | Yes | Yes | Very new |
| Letta | Letta server | Python | Yes | No | No | High |

---

## The Irreducible Tradeoff

Richer memory systems improve recall at the cost of:
1. Infrastructure overhead (services to run, maintain, potentially break)
2. Memory noise (incorrect or stale memories polluting reasoning)
3. Opacity (harder to inspect what the agent "knows")

The CONTEXT.md approach is maximally transparent: you can read, edit, and git-blame
every piece of context the agent has. A vector database cannot give you that. Choose
the tier that matches how often you're re-explaining things, not the most sophisticated
option available.
