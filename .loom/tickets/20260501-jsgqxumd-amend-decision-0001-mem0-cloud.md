---
id: ticket:20260501-jsgqxumd-amend-decision-0001-mem0-cloud
kind: ticket
status: proposed
created_at: 2026-05-01T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
title: "Amend decision:0001 — Tier 3 mem0 is cloud-only, not Docker self-hosted"
priority: medium
links:
  related:
    - decision:0001
    - wiki:cross-session-memory
    - instructions:agentic-memory
---

## Why

`decision:0001` (agentic memory tiers) was recorded when mem0 was self-hostable via
Docker and offered a local `mem0-mcp` npm package. That premise changed:

- `mem0-mcp` npm package was archived March 2026
- mem0 is now cloud-only, accessed via `https://mcp.mem0.ai/mcp`
- Docker self-hosting is no longer a supported path for the MCP integration

The content files were updated during the loom-critique session (FIND-005, critique
`20260501-ewtdxi6z`), but `decision:0001` itself was noted as needing amendment and
never updated. This is the ticket to close that residual.

## Scope

- Read `decision:0001` at `.loom/constitution/decisions/`
- Append an amendment section documenting:
  - What changed (mem0-mcp archived, cloud-only as of March 2026)
  - What the new recommended Tier 3 setup is (HTTP MCP at mcp.mem0.ai, requires API key)
  - That the original Docker-based rationale no longer applies
- Do not rewrite the original decision body — append an amendment with date

## Out of scope

- Re-evaluating whether mem0 is still the right Tier 3 choice (that is a separate
  research question; the ticket `memory-revisit` covers it, not_before 2026-06-25)
- Fixing `"type":"http"` → `"type":"remote"` (that is ticket `20260501-pz0qzejg`)

## Acceptance

- `decision:0001` contains an amendment section dated 2026-05-01
- Amendment notes the Docker/npm premise change and the cloud-only replacement
- No content file still describes mem0 as Docker-based (verify with `rg -r 'mem0.*docker\|docker.*mem0' instructions/ .loom/wiki/` case-insensitive)

## Execution notes

Residual first noted at end of loom-critique session 2026-05-01. Left in session prose
rather than a ticket — this ticket exists to close that gap, per Rule D in AGENTS.md
(Critique Discipline).
