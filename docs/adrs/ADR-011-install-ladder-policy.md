# ADR-011: Install Ladder Policy for bin/initialize_for_opencode

## Status

Accepted

## Date

2026-04-30

## Context

The scaffold's `bin/initialize_for_opencode` script installs external tools and
MCP servers on behalf of the user. This requires a policy for how to handle
installations when multiple package managers or install methods are available,
and when none work.

The key forces:

1. **Security**: The `curl | sh` pattern is widely used in tool installers but
   is a security risk — it executes arbitrary remote code without inspection.
   An agent should never do this autonomously.

2. **Cross-platform reach**: The scaffold targets macOS-primary, Linux-secondary
   users. Any given tool may be available via `brew`, `apt`, `go install`, `pip`,
   `pipx`, `npm`, etc. — with varying availability per platform.

3. **Graceful degradation**: If every install method fails, the script must not
   silently proceed with a broken state. It should give the user a concrete
   manual fallback and continue without erroring out.

4. **Auditability**: Users should know what command was run and why. Hidden install
   attempts are worse than failed ones.

## Decision

All tool installs in `bin/initialize_for_opencode` follow this priority ladder:

1. **Platform-native package manager** (brew on macOS, apt on Linux when the
   package exists in the official repos)
2. **Language ecosystem tool** (`go install`, `pipx`, `pip`, `npm -g`) — only
   where the tool's upstream officially supports this
3. **Print manual instructions** with the exact command(s) the user should run,
   including a URL to the releases page or official install docs

The script **never** runs `curl | sh` or equivalent autonomously. If the only
upstream install method is a curl-pipe script, the script prints that command
so the user can inspect and run it themselves.

### Per-tool ladder (current)

| Tool | Ladder |
|------|--------|
| `uv` | brew → apt → pipx → pip → tell user (docs URL) |
| `github-mcp-server` | brew → go install → tell user (releases URL) |
| `context-mode` | npm install -g → tell user |
| `@modelcontextprotocol/server-memory` | npm install -g → tell user |
| `npx`-based servers | npx at runtime (no pre-install needed) |

New tools added to the script must fit into this ladder model. A tool that
cannot be installed without `curl | sh` gets a "tell user" entry only.

## Consequences

- Users on edge-case platforms may need to install tools manually. This is
  intentional: correctness and security over convenience.
- The script stays auditable: every attempted command is printed before running.
- Future additions to the script must follow this pattern. Deviating requires
  updating this ADR.

## Alternatives Considered

**Allow curl | sh with a confirmation prompt.** Rejected. A user can misread or
rush past a prompt. The risk is asymmetric: a mistake is hard to undo. Better
to print the command and let the user run it in a shell they control.

**Use a single universal package manager (e.g., mise, asdf).** Rejected. Adding
a dependency on a version manager as a bootstrap prerequisite increases friction.
The scaffold targets developers who already have standard tools on their machines.

**Attempt all ladders silently and only report failure.** Rejected. Silent
attempts make it hard to diagnose problems and hide what the script did.
