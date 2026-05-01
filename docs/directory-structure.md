# Directory Structure

```
mlibrary-agentic-scaffold/
├── README.md                        # Overview and how to use
├── OPEN_QUESTIONS.md                # Assumptions made; revisit with owner
├── opencode.json                    # Project-level MCP config (copy/adapt to your project)
│
├── bin/
│   ├── initialize_for_opencode      # Interactive setup script for new projects
│   └── update_mlibrary_scaffold     # Check for new skills/MCPs/instructions after scaffold updates
│
├── instructions/                    # Markdown instruction files for opencode.json inclusion
│   ├── core.md                      # Language-agnostic library conventions (incl. GPG setup)
│   ├── documentation.md             # Inline & external docs standards
│   ├── testing.md                   # Testing philosophy, standards, SimpleCov coverage
│   ├── docker-integration.md        # Docker Compose for integration tests
│   ├── ci-github-actions.md         # GitHub Actions CI conventions
│   ├── adrs.md                      # ADR workflow
│   ├── agentic-memory.md            # Cross-session memory: recommendations + setup (concise)
│   ├── changelog-automation.md      # Conventional Commits format + agent rules
│   └── ruby.md                      # Ruby gem specifics
│
├── docs/
│   ├── directory-structure.md       # This file
│   ├── adrs/                        # Architecture Decision Records for the scaffold itself
│   │   ├── ADR-000-template.md
│   │   └── ADR-001 … ADR-011
│   └── research/
│       └── agentic-memory-research.md  # Full research: system profiles + tradeoff analysis
│
├── skills/                          # OpenCode SKILL.md modules (loaded on demand)
│   ├── adr-create/SKILL.md          # Scaffold a new ADR
│   ├── ruby-gem-release/SKILL.md    # Gem versioning + rubygems.org release
│   ├── integration-test/SKILL.md    # Stand up Docker services + run integration tests
│   ├── lint-fix/SKILL.md            # Run linters and auto-fix
│   ├── docker-services/SKILL.md     # Manage Docker Compose service lifecycle
│   ├── worth-it/SKILL.md            # Assess effort before acting; wait for go-ahead
│   └── ss/SKILL.md                  # Self-sufficient execution mode
│
├── templates/
│   ├── ADR-000-template.md          # ADR template (your preferred format)
│   └── AGENTS-ruby.md               # Drop-in AGENTS.md for a new Ruby gem project
│
└── mcp/
    └── recommended-servers.md       # MCP server recommendations + setup notes
```
