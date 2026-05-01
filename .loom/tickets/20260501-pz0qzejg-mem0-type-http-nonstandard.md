---
id: ticket:20260501-pz0qzejg-mem0-type-http-nonstandard
kind: ticket
status: proposed
created_at: 2026-05-01T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
title: "mem0 MCP config uses non-standard type: 'http'"
priority: low
links:
  related:
    - wiki:cross-session-memory
    - decision:0001
---

## Why

The OpenCode config schema (`https://opencode.ai/config.json`) defines exactly two valid MCP
`type` values: `"local"` (for stdio subprocess servers) and `"remote"` (for remote servers
with a URL). There is no `"http"` type.

The mem0 MCP entry written by `bin/initialize_for_opencode` and documented in
`mcp/recommended-servers.md` currently uses:

```json
{"type":"http","url":"https://mcp.mem0.ai/mcp","headers":{"Authorization":"Bearer ${MEM0_API_KEY}"},"enabled":false}
```

`"http"` is not in the schema — the correct type for a URL-based server is `"remote"`.

## Scope

- Update `bin/initialize_for_opencode` line ~560: `"type":"http"` → `"type":"remote"`
- Update `mcp/recommended-servers.md` mem0 install snippet
- Update `instructions/agentic-memory.md` Tier 3 opencode.json example
- Update `.loom/wiki/wiki-cross-session-memory.md` Tier 3 example

## Out of scope

Investigating whether mem0's actual MCP endpoint (`mcp.mem0.ai`) truly works with
the `remote` type — that requires a live mem0 API key to test.

## Acceptance

- All four locations above use `"type":"remote"` for the mem0 entry
- `bash -n bin/initialize_for_opencode` passes
- No other `"type":"http"` values remain in the repo

## Execution notes

Discovered during OpenCode schema review (`https://opencode.ai/config.json`) on 2026-05-01.
The schema was fetched directly and confirmed only `local` and `remote` are valid const values
for the `type` field in MCP config.
