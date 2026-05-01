# ADR-002: OpenCode + GitHub Copilot as the AI Harness

## Status

Accepted

## Date

2026-04-30

## Context

This scaffold exists to make AI agents useful when building Ruby and JVM libraries.
That requires choosing a specific AI harness and model provider — the choice
affects which instruction file conventions work, which tool invocation patterns are
available, which models can be used, and what authentication infrastructure is
required.

The owner's AI access is through a GitHub Copilot subscription. Copilot's model
roster (including Claude Sonnet variants) covers the capabilities needed for
library-building tasks. Direct API access to Anthropic or OpenAI requires separate
billing, separate API keys, and separate tooling.

OpenCode is a CLI-native AI coding agent that:
- Works with GitHub Copilot as a model provider (no separate API key)
- Reads `AGENTS.md` for project-level instructions
- Supports skills (invokable `.md` workflow modules)
- Integrates with MCP servers
- Works in a terminal without an IDE dependency

Alternative harnesses considered:
- **Claude CLI (claude.ai/code)** — Anthropic API key required; not available
  under Copilot subscription.
- **Cursor** — IDE-bound; billing separate from Copilot; less transparent about
  instruction file conventions.
- **GitHub Copilot in VS Code** — IDE-bound; does not support a skills/workflow
  layer comparable to OpenCode; instruction conventions are less agent-native.
- **Aider** — strong for code editing; weaker skill/workflow protocol support;
  would require separate API keys.

## Decision

We use **OpenCode** as the AI harness and **GitHub Copilot** as the model
provider.

All instruction files, skill files, `AGENTS.md` conventions, and MCP
configuration in this scaffold target OpenCode's capabilities and conventions.
Suggestions requiring direct Anthropic or OpenAI API access must be flagged as
unsupported unless the owner explicitly adds that access.

## Consequences

### Positive

- No separate API key infrastructure beyond a GitHub Copilot subscription.
- OpenCode's `AGENTS.md` + skills system is a first-class instruction surface
  with well-defined loading semantics.
- Terminal-native; works in any environment without IDE dependency.
- MCP server integration is available for extending agent capabilities.

### Negative

- If OpenCode is discontinued or changes conventions materially, all instruction
  files and skill conventions may need migration.
- GitHub Copilot's model roster is determined by GitHub; the owner cannot freely
  choose arbitrary models.
- Some Loom workflow features may work better with models that have larger context
  windows than Copilot currently offers.

### Neutral

- Instruction files use OpenCode-style `AGENTS.md` conventions; projects using
  other harnesses would need to adapt or rename these files.
- MCP server configuration uses `"environment"` (not `"env"`) per OpenCode's
  config schema.

## Alternatives Considered

**Claude CLI**: Requires Anthropic API key and separate billing. Not available
under a standard Copilot subscription. Rejected on infrastructure grounds.

**Cursor**: IDE-bound, separate billing, weaker agent-native instruction
conventions. Rejected.

**GitHub Copilot in VS Code**: No comparable skill/workflow layer. Instruction
file conventions are less expressive. Rejected in favor of OpenCode's richer
protocol.

**Aider**: Strong editing agent but weaker workflow protocol. Requires separate
API keys. Rejected.

## Open Questions

None at adoption time.
