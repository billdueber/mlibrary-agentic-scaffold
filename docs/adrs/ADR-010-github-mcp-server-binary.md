# ADR-010: github-mcp-server Go Binary over @modelcontextprotocol/server-github

## Status

Accepted

## Date

2026-04-30

## Context

OpenCode agents in this scaffold need GitHub integration to work with issues,
pull requests, releases, and repository metadata. The original MCP server for
this was `@modelcontextprotocol/server-github`, installed and invoked via `npx`.

That package was deprecated upstream by GitHub in early 2026. GitHub replaced it
with a purpose-built Go binary: `github/github-mcp-server`. The new binary is
distributed through `brew` (macOS) and as a Go module, with MIT license.

The key behavioral difference is in how the binary is invoked. The npx package
used a JSON command array like `["npx", "--yes", "@modelcontextprotocol/server-github"]`.
The new binary is invoked as `["github-mcp-server", "stdio"]`.

Both require a `GITHUB_TOKEN` environment variable.

## Decision

Replace `@modelcontextprotocol/server-github` (npx) with the `github-mcp-server`
Go binary in all scaffold artifacts:

- `opencode.json` (project-level MCP config)
- `mcp/recommended-servers.md` (install instructions)
- `bin/initialize_for_opencode` (interactive setup script)

The command array used in opencode.json is `["github-mcp-server", "stdio"]`.

Installation:
- macOS: `brew install github-mcp-server`
- Linux: `go install github.com/github/github-mcp-server/cmd/github-mcp-server@latest`

## Consequences

- Agents using the scaffold will get the actively maintained GitHub MCP server.
- The binary must be on `PATH`; it is not a zero-install tool like the npx approach.
- The `bin/initialize_for_opencode` script handles the platform-aware install (brew
  → go install → tell user) so end-users are guided through this automatically.
- Any project that previously had `@modelcontextprotocol/server-github` in its
  `opencode.json` must update its command array manually. No migration helper is
  provided; the change is a one-line JSON edit.

## Alternatives Considered

**Keep the npx package.** The upstream repository is deprecated and no longer
receives updates. This was rejected as it leaves projects on a dead package.

**Use the GitHub CLI (`gh`) directly.** The `gh` CLI is not an MCP server and
would require prompt engineering rather than native tool calls. Rejected.

## References

- https://github.com/github/github-mcp-server (new upstream)
- Deprecated: https://github.com/modelcontextprotocol/servers/tree/main/src/github
