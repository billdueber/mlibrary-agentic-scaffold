# MCP Server Recommendations

Model Context Protocol (MCP) servers extend an AI agent with additional tools.
These are the servers most useful for library development work.

---

## How to Install MCP Servers in OpenCode

Add servers to your project's `opencode.json` (in the project root, safe to commit to git).
They can also go in the global `~/.config/opencode/opencode.json`; OpenCode merges both.
Project-level config is preferred — it makes the project's MCP dependencies explicit and
version-controlled.

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "server-name": {
      "type": "local",
      "command": ["npx", "-y", "@scope/mcp-server-name"],
      "enabled": true
    }
  }
}
```

> **Note**: The config key is `"environment"` (not `"env"`) for passing environment
> variables to local MCP servers. The `"command"` value must be an array, not a
> string — `"command": ["npx", "-y", "pkg"]`, not `"command": "npx", "args": [...]`.
> Both are common mistakes.

---

## Recommended Servers

### 1. `github` — GitHub API

Access GitHub issues, PRs, releases, and repository metadata.

Install: `brew install github-mcp-server`

```json
"github": {
  "type": "local",
  "command": ["github-mcp-server", "stdio"],
  "environment": {
    "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
  },
  "enabled": true
}
```

**Use for**: Reading issues, creating PRs, checking CI status, creating releases.

### 2. `context-mode` — In-Session Context Management

Prevents context-window overflow by routing tool output through a subprocess sandbox
(only stdout enters context) and providing a BM25/FTS5 in-session knowledge base for
indexed, searchable retrieval instead of raw content injection.

Install globally (not per-project — it integrates with OpenCode lifecycle hooks):

```bash
npm install -g context-mode
```

```json
"context-mode": {
  "type": "local",
  "command": ["context-mode"],
  "enabled": true
}
```

**Key tools**: `ctx_execute` (sandboxed shell/code, stdout-only), `ctx_index` / `ctx_search`
(BM25 knowledge base), `ctx_fetch_and_index` (fetch URL → index → search; never dumps raw HTML),
`ctx_batch_execute` (parallel commands + search in one round trip), `compress` (summarize
conversation ranges to reclaim context).

**License**: Elastic License v2 (ELv2). Restriction applies only to hosting the software as
a service for third parties. Internal and personal use is unrestricted.

**Replaces `fetch`**: `ctx_fetch_and_index` is strictly better for retrieving documentation —
raw page content never enters context; only BM25-matched snippets are returned. There is no
reason to install the `fetch` MCP alongside context-mode; use `ctx_fetch_and_index` instead.

**No open-source equivalent found**: Searched npm, PyPI, pkg.go.dev, crates.io, GitHub (all
languages), and HackerNews Show HN posts through Apr 2026. No other MCP server combines
transparent output sandboxing with in-session FTS indexing. See `decision:0003`.

### 3. `fetch` — URL Fetching (fallback, only if context-mode is not installed)

Fetch web pages and API documentation. Prefer `ctx_fetch_and_index` from `context-mode`
for larger documents; use this for simple one-off URL reads where indexing is unnecessary.

```json
"fetch": {
  "type": "local",
  "command": ["uvx", "mcp-server-fetch"],
  "enabled": true
}
```

Requires `uv` (Python): `brew install uv`

**Use for**: Quick single-URL fetches. Reading RubyGems/Maven API docs, gem/artifact documentation, RFC references.

**Do not install alongside context-mode.** context-mode's `ctx_fetch_and_index` does the
same job better. Install `fetch` only if you have declined or cannot use context-mode.

### 4. `sequential-thinking` — Structured Reasoning

Forces the agent to break complex problems into sequential steps before acting.

```json
"sequential-thinking": {
  "type": "local",
  "command": ["npx", "-y", "@modelcontextprotocol/server-sequential-thinking"],
  "enabled": true
}
```

**Use for**: Complex refactors, architectural decisions, debugging subtle bugs. Particularly
valuable when you want the agent to reason before writing code.

**Note with Loom active**: Loom's outer-loop and packet discipline already impose structured
reasoning before execution. `sequential-thinking` is complementary — it operates at the
LLM inference layer; Loom operates at the workflow/record layer. Both can be active.

### 5. `memory` — Cross-Session Memory (Tier 2)

Stores and retrieves facts across agent sessions using a local knowledge graph.
Official Anthropic MCP reference implementation.

```json
"memory": {
  "type": "local",
  "command": ["npx", "-y", "@modelcontextprotocol/server-memory"],
  "environment": {
    "MEMORY_FILE_PATH": "/path/to/your/project/memory.jsonl"
  },
  "enabled": true
}
```

No API key, no database, no Docker required. Persists to a local `.jsonl` file.

Set `MEMORY_FILE_PATH` to a project-specific path (e.g., `/Users/you/devel/myproject/memory.jsonl`)
so each project has its own memory store. You can also use a single global file if you prefer
a shared store across projects — but per-project files are easier to inspect and discard.

**Use for**: Storing design decisions, project conventions, architecture context,
and anything the agent should recall in future sessions without re-reading source files.

**Typical agent workflow**:
1. At the start of a session, the agent calls `memory_search` with the current task topic
   to surface relevant prior context.
2. After resolving a non-obvious design question, the agent calls `memory_create_entities`
   or `memory_add_observations` to persist the decision.
3. End-of-session: store any context that would otherwise need re-explaining next time.

**Limitation**: Retrieval is by entity name lookup — no fuzzy/semantic search.
For most library-project context, this is sufficient.

**Note on Loom**: Loom's `.loom/memory/` is a grep-based, within-session scratchpad —
intentionally minimal and pruned. It does not substitute for this server. Use
`@modelcontextprotocol/server-memory` for durable, cross-session recall by entity name.
Use Loom's `.loom/memory/` for transient, in-session support context that is expected
to be promoted into canonical Loom records or discarded. They serve adjacent, non-overlapping
roles. See `decision:0001` and `research:agentic-memory` for the full rationale.

**Note on memU**: An earlier version of these docs recommended memU as Tier 2.
That recommendation was based on an incorrect characterization of memU as
"Node.js, zero-infra." Current memU requires Python 3.13+, an OpenAI API key,
and optionally PostgreSQL+pgvector. See `decision:0001` for the full record.

### 6. `filesystem` — Extended File Access

Allows the agent to read files outside the project root (e.g., these mlibrary-agentic-scaffold
instruction files).

```json
"filesystem": {
  "type": "local",
  "command": [
    "npx",
    "-y",
    "@modelcontextprotocol/server-filesystem",
    "/path/to/your/projects",
    "/path/to/mlibrary-agentic-scaffold"
  ],
  "enabled": true
}
```

**Customize these paths** for your machine: replace `/path/to/your/projects` with your
projects root (e.g., `~/devel`) and `/path/to/mlibrary-agentic-scaffold` with the actual
location of this scaffold directory.

**Use for**: Allowing the agent to read shared instruction files from `mlibrary-agentic-scaffold/`
without you having to copy them into every project.

---

### 7. `mem0` — Semantic Memory (Tier 3, cloud, conditional)

Richer cross-session memory with vector + graph retrieval via the official mem0
cloud service. Only worth the API key if `@modelcontextprotocol/server-memory`
(Tier 2) proves insufficient for semantic recall.

**Note**: The self-hosted `mem0-mcp` npm package was archived March 2026. mem0 is
now cloud-only for MCP. Requires a free or paid API key from `app.mem0.ai`.

```json
"mem0": {
  "type": "http",
  "url": "https://mcp.mem0.ai/mcp",
  "headers": {
    "Authorization": "Bearer ${MEM0_API_KEY}"
  },
  "enabled": false
}
```

**Infrastructure required**: mem0 account + API key (free tier available at
`app.mem0.ai`). No local Docker required.

**Use when**: The agent is repeatedly re-explaining the same design context and
`@modelcontextprotocol/server-memory` entity-lookup isn't surfacing it reliably.

---

## Installation Order / Priority

For a minimal, useful setup, install in this order:

1. `context-mode` — global install; prevents context overflow and replaces `fetch` entirely
2. `github` — essential for release workflows
3. `sequential-thinking` — helps on hard problems
4. `filesystem` — useful if you reference mlibrary-agentic-scaffold files from projects
5. `memory` — zero-infra cross-session memory; recommended for all active projects
6. `mem0` — only if `@modelcontextprotocol/server-memory` (item 5) repeatedly fails to surface relevant context; mem0 adds semantic/fuzzy retrieval via cloud API (free tier at app.mem0.ai; API key required; no local Docker needed)
7. `fetch` — **only** if context-mode is not installed; fallback URL fetcher with no overflow protection

---

## Watch Later

These projects are too early to recommend but worth revisiting:

| Project | What it does | Why interesting | Check again |
|---------|-------------|----------------|-------------|
| `asd412id/mcp-context-manager` ([GitHub](https://github.com/asd412id/mcp-context-manager)) | Agent-invoked context tools: summarizer, memory store, checkpoints, smart file loader. MIT. | Right problem space (context overrun prevention), actively maintained (v1.0.17 Mar 2026). | ~Sep 2026 — needs adoption signal (currently 1 star) |
| `wuphf` (HN Show HN Apr 2026, author: najmuzzaman) | Markdown+git wiki substrate for multi-agent context compounding. BM25+SQLite, MCP retrieval tool. Draft-to-wiki promotion flow. | Very close to Loom's philosophy; could complement or partially replace `.loom/wiki/` | Repo not publicly findable at time of research. Check again ~Jul 2026 |

---

## What These Servers Cannot Do

- They cannot replace a project-level `AGENTS.md` and Loom records — those are the primary truth layer.
- The `github` server cannot push code or trigger CI — it can only read/create issues and PRs.
- `mem0` memories are only as good as what was stored; verify important facts against the codebase.
