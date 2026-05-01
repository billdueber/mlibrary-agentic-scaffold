---
id: decision:0003
kind: decision
status: active
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-04-30T00:00:00Z
scope:
  kind: workspace
links: {}
---

# Decision

`context-mode` is the recommended MCP server for in-session context-window management in
this scaffold. It is installed globally (not per-project) and is listed first in
`mcp/recommended-servers.md`.

## What Was Decided

- Add `context-mode` to `mcp/recommended-servers.md` as a top-tier recommendation.
- Demote `fetch` to "fallback" status; `ctx_fetch_and_index` is the preferred method for
  retrieving documentation.
- Record two "too green" candidates for future revisit: `asd412id/mcp-context-manager`
  and `wuphf`.

## Rationale

`context-mode` provides a capability not available elsewhere in the MCP ecosystem:

1. **Subprocess sandbox with stdout-only context injection** — large tool outputs (build
   logs, test output, file reads) are processed in a subprocess; only the summary stdout
   enters the agent context window.
2. **BM25/FTS5 in-session knowledge base** — content is indexed into SQLite at invocation
   time and retrieved via ranked search. Raw pages and files never enter context.
3. **OpenCode lifecycle hook integration** — hooks enforce routing patterns within the
   OpenCode harness. This is harness-specific; the general MCP tools work with any client.
4. **`ctx_fetch_and_index` strictly dominates `fetch`** for documentation retrieval —
   the raw HTML/markdown never enters context; only matched snippets are returned.

## Search Scope

A thorough search was conducted across npm, PyPI, pkg.go.dev (Go), crates.io (Rust),
GitHub (language-filtered for Go, Rust, Java, Python, TS), and HackerNews Show HN posts
through April 2026. No open-source alternative was found that addresses context-window
overflow at the MCP layer with comparable transparency and capability.

Candidates reviewed and rejected:

| Candidate | Language | Reason not adopted |
|-----------|----------|--------------------|
| `capsule` (`@capsule-run/mcp-server`) | Rust/WASM | Sandboxed code execution only; no context overflow protection, no FTS |
| `pydantic/mcp-run-python` | Python | Archived Jan 2026 |
| `@e2b/mcp-server` | TypeScript | Deprecated; requires cloud API |
| `mcp-server-code-runner` | TypeScript | Dumps output directly to context — no overflow protection |
| `mkreyman/mcp-memory-keeper` | TypeScript | Cross-session persistence, not in-session output filtering; different problem |
| `asd412id/mcp-context-manager` | TypeScript | Right problem space but 1 star, 1 contributor — too early |

## License

Elastic License v2 (ELv2). The restriction (no hosting as a service for third parties)
does not apply to internal or personal use. This scaffold is used by dueberb for personal
library development work; ELv2 is acceptable.

## Watch Later

- `asd412id/mcp-context-manager` — revisit ~Sep 2026 if adoption signal grows
- `wuphf` (HN Show HN Apr 2026, author najmuzzaman) — markdown+git+BM25 wiki for agents;
  repo not publicly findable at time of research. Revisit ~Jul 2026.
