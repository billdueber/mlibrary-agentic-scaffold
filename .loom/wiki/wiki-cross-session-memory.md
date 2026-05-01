---
id: wiki:cross-session-memory
kind: wiki
page_type: concept
status: accepted
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-05-01T12:00:00Z
scope:
  kind: workspace
links:
  research:
    - research:agentic-memory
  decision:
    - decision:0001
  constitution:
    - constitution:main
---

# Summary

This workspace uses a three-tier approach to cross-session agent memory:
`CONTEXT.md` (mandatory, zero infra), `@modelcontextprotocol/server-memory`
(recommended, zero infra), and mem0 (conditional, cloud service, API key required).

# Why This Concept Exists

AI agents are stateless. Each session starts cold. Without explicit memory
mechanisms, agents re-explain context, re-derive decisions, and lose awareness
of in-progress work. For library development — where accumulated decisions,
sharp edges, and conventions matter — this is a genuine productivity cost.

# Core Idea

## Tier 1: CONTEXT.md (mandatory)

Every library project maintains a `CONTEXT.md` at the project root.

**At session start**: agent reads `CONTEXT.md` and treats its content as
authoritative project context.

**At session end**: agent proactively updates `CONTEXT.md` with any decisions
made, gotchas discovered, in-progress work state, or corrections to prior
understanding.

This is git-versionable, transparent, zero-infrastructure. It covers **working
memory** (current state, in-progress work) and **semantic memory** (design
decisions, conventions, sharp edges) at the level a competent human would write.

Always start here. Richer tiers add recall at the cost of infrastructure.

## Tier 2: @modelcontextprotocol/server-memory (recommended)

A local entity knowledge graph stored as a `.jsonl` file. Zero infrastructure —
runs via `npx`. Exposes a `memory` MCP server that agents can use to store and
retrieve named entities and facts across sessions.

**When to adopt**: when you find yourself re-explaining the same project context
repeatedly at session start. Tier 2 can catch what CONTEXT.md misses.

**Setup**: added to `opencode.json` `mcp` section. The installer offers this.
See `instructions/agentic-memory.md` for the config snippet.

One installation handles all projects; per-project isolation is via `user_id`
in the memory file path.

## Tier 3: mem0 (conditional, cloud)

mem0 is a vector + graph hybrid memory system. It extracts facts from
conversation (via LLM) and retrieves by semantic similarity — much richer recall
than Tier 2 but requires an API key for the mem0 cloud service.

**When to adopt**: only if CONTEXT.md + server-memory are insufficient and the
owner is willing to use a cloud service with an API key.

**Infrastructure**: Cloud service at `mcp.mem0.ai`. Free tier available at
`app.mem0.ai`. No local Docker required — the self-hosted MCP package was
archived March 2026.

Config:
```json
{
  "mcp": {
    "mem0": {
      "type": "remote",
      "url": "https://mcp.mem0.ai/mcp",
      "headers": { "Authorization": "Bearer ${MEM0_API_KEY}" }
    }
  }
}
```

Set `MEM0_API_KEY` in your shell environment.

# Important Boundaries

- Loom records (`constitution`, `tickets`, `research`, `wiki`) are **canonical
  project truth**. Memory systems are support context only — they must never
  silently override Loom records.
- CONTEXT.md is per-project. Loom records are per-workspace.
- Tiers 2 and 3 are optional; a project is fully functional with Tier 1 alone.

# How It Connects

- `instructions/agentic-memory.md` — concise setup for all three tiers
- `docs/research/agentic-memory-research.md` — full research: system profiles + tradeoff analysis
- `decision:0001` — the binding decision on memory tier adoption
- `mcp/recommended-servers.md` — MCP server config reference

# Sources

- `research:agentic-memory`
- `decision:0001`
- `instructions/agentic-memory.md`

# Related Pages

- `wiki:mlibrary-agentic-scaffold-overview` — what the system is
- `wiki:new-project-setup` — where CONTEXT.md fits in new project setup

# Change Notes

- 2026-05-01: Updated Tier 2 from memU → @modelcontextprotocol/server-memory
  (per decision:0001; memU was cancelled as memu-setup ticket). Removed stub
  reference to cancelled ticket:memu-setup.
- 2026-05-01 (critique:bin-scripts-critique#FIND-005): Updated Tier 3 from
  Docker-local → cloud-only. mem0-mcp npm package archived March 2026;
  official MCP is now cloud service at mcp.mem0.ai. Research doc reference updated.
