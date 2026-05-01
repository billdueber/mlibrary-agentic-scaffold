# Agentic Memory

Cross-session memory for AI coding agents. This file covers what to use and how to set it up.
For system profiles and full tradeoff analysis, see `docs/research/agentic-memory-research.md`.

---

## Three-Tier Model

**Tier 1 (always)**: `CONTEXT.md` + Loom records

The file-based approach is transparent, version-controlled, and requires zero infrastructure.
Sufficient for most single-developer library work.

**Tier 2 (when CONTEXT.md isn't enough)**: `@modelcontextprotocol/server-memory`

- Zero infrastructure — persists a knowledge graph to a local `.jsonl` file
- No API key, no Python, no Docker
- Works with GitHub Copilot via OpenCode
- Limitation: entity name lookup only; no fuzzy/semantic search

**Tier 3 (when semantic retrieval matters)**: `mem0` (cloud, API key required)

- Vector + graph semantic memory via cloud service
- No local Docker required — the self-hosted MCP server was archived March 2026
- Free tier available at `app.mem0.ai`; requires API key

---

## Setup: Tier 2 (`@modelcontextprotocol/server-memory`)

No installation required — `npx` handles it on first run.

In your project's `opencode.json`:

```json
{
  "mcp": {
    "memory": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-memory"],
      "environment": {
        "MEMORY_FILE_PATH": "/absolute/path/to/your/project/memory.jsonl"
      }
    }
  }
}
```

Set `MEMORY_FILE_PATH` to a project-specific path. The file is created automatically
on first use. It is plain JSON Lines — human-readable and git-committable.

**Typical agent usage**:
- Start of session: search memory for the current task topic
- After resolving a design question: create entities or add observations
- After any decision you'd normally put in a sticky note for next session

---

## Setup: Tier 3 (mem0)

**Infrastructure**: Cloud service at `mcp.mem0.ai`. No local Docker needed. Get
a free API key at `app.mem0.ai`.

In your project's `opencode.json`:

```json
{
  "mcp": {
    "mem0": {
      "type": "http",
      "url": "https://mcp.mem0.ai/mcp",
      "headers": {
        "Authorization": "Bearer ${MEM0_API_KEY}"
      }
    }
  }
}
```

Set `MEM0_API_KEY` in your shell environment. mem0's cloud service handles
per-user isolation automatically.

---

## Decision Record

See `decision:0001` in `.loom/constitution/decisions/` for the full decision history,
including the amended note about memU's infrastructure requirements.
