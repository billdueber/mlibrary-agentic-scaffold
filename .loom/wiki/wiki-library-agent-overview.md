---
id: wiki:mlibrary-agentic-scaffold-overview
kind: wiki
page_type: concept
status: accepted
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
scope:
  kind: workspace
links:
  constitution:
    - constitution:main
  initiative:
    - initiative:mlibrary-agentic-scaffold-stable
---

# Summary

`mlibrary-agentic-scaffold/` is a personal agentic library system: a collection of
instruction files, OpenCode skills, AGENTS.md templates, and setup scripts that
give AI agents the context they need to work effectively on Ruby gem and general
library projects.

# Why This Concept Exists

AI agents are stateless and generic. Without domain-specific guidance, an agent
working on a Ruby gem will make Rails assumptions, forget project conventions,
and ask the same questions every session. `mlibrary-agentic-scaffold/` solves this by
encoding the owner's conventions, toolchain preferences, and workflow patterns
into files that agents can read.

# Core Idea

The system has five layers:

**1. Setup scripts** (`bin/`)
- `bin/initialize_for_opencode` — interactive installer: configures `opencode.json`,
  symlinks skills, initializes Loom, writes AGENTS.md
- `bin/update_mlibrary_scaffold` — drift checker: reports new skills, MCP servers,
  or instruction files not yet in the project config. Fails fast if installer
  was never run.

**2. Instruction files** (`instructions/*.md`)
Language-agnostic and language-specific guidance loaded via `opencode.json`
`instructions[]` array. Topics: project layout, CONTEXT.md convention,
documentation, testing, Docker-based integration tests, CI, linting, ADRs,
agentic memory, Ruby specifics, changelog automation.

**3. Skills** (`skills/*/SKILL.md`)
OpenCode skills for repeatable bounded workflows. Each skill is loaded on demand:
- `adr-create` — scaffold a new ADR
- `ruby-gem-release` — version bump + RubyGems.org release
- `integration-test` — stand up Docker services + run integration tests
- `lint-fix` — run linters and auto-fix
- `docker-services` — manage Docker Compose service lifecycle
- `worth-it` — assess effort before acting; wait for go-ahead
- `ss` — self-sufficient execution mode (with Loom checkpoint auto-run)

**4. AGENTS.md templates** (`templates/`)
Drop-in starting point for new projects. Copy `AGENTS-ruby.md` to a new project
root as `AGENTS.md`, fill in the project-specific blanks.

**5. MCP recommendations** (`mcp/recommended-servers.md`)
Which MCP servers are useful for library work, tradeoffs, and installation notes.
The installer offers these interactively.

# Important Boundaries

- This system targets **Ruby gem authors** primarily, with general conventions
  applicable to any library project. See `decision:0004`.
- JVM (Kotlin/Java) coverage is **not currently available** — scope was pulled
  back in decision:0004.
- Frontend, data-pipeline, and full-stack projects are out of scope.
- Toolchain: **OpenCode + GitHub Copilot**. Other AI providers are not supported.
- CI: **GitHub Actions only**.

# How It Connects

- Every project using this system should have an `opencode.json` with the
  `instructions[]` array wired to scaffold files, and an `AGENTS.md` with the
  Loom bootstrap directive.
- A `CONTEXT.md` at the project root serves as the agent's working memory across
  sessions (Tier 1 memory).
- The Loom workspace in `.loom/` governs the `mlibrary-agentic-scaffold/` system itself —
  it is the meta-layer for tracking improvement work on the tooling.
- Skills are installed globally via `~/.config/opencode/skills/` (handled by the
  installer) and are available in all projects.

# Sources

- `mlibrary-agentic-scaffold/README.md`
- `constitution:main`
- `decision:0004` — scope pullback to Ruby + general
- `initiative:mlibrary-agentic-scaffold-stable`

# Related Pages

- `wiki:cross-session-memory` — how agents maintain context between sessions
- `wiki:new-project-setup` — workflow for starting a new project
- `wiki:loom-workspace-orientation` — how to enter this workspace cold

# Change Notes

- 2026-04-30: Initial page created during Loom workspace bootstrap.
- 2026-05-01: Updated for scope pullback (decision:0004): JVM removed. Added
  `bin/` scripts as a first-class layer. Updated boundary constraints.
  `opencode.json` instructions array documented as primary instruction mechanism.
