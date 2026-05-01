# AGENTS.md — [Gem Name]

<!-- 
  TEMPLATE: Replace all [bracketed] placeholders. Remove comment blocks when done.
  This file gives AI agents the context they need to work on this Ruby gem.
-->

## Project Identity

**Name**: [gem-name]  
**Version**: [x.y.z]  
**Description**: [One sentence: what this gem does and who uses it]  
**RubyGems**: https://rubygems.org/gems/[gem-name]  
**GitHub**: https://github.com/[owner]/[gem-name]  

This is a Ruby library gem (no Rails dependency). It is intended to be used as a
dependency by other Ruby programs and libraries.

---

## First Things First

At the start of every session:
1. Read `CONTEXT.md` if it exists. Treat it as the authoritative project memory.
2. Check `git status` to understand the current state of the working tree.
3. If working on a specific issue or PR, read the relevant context before writing code.

---

## Shared Instructions

<!--
  Replace [SCAFFOLD_DIR] with the actual path to your mlibrary-agentic-scaffold
  installation, e.g. ~/devel/mlibrary-agentic-scaffold
-->
This project follows the mlibrary-agentic-scaffold conventions. Key instruction files:

- Core conventions: `[SCAFFOLD_DIR]/instructions/core.md`
- Ruby specifics: `[SCAFFOLD_DIR]/instructions/ruby.md`
- Testing: `[SCAFFOLD_DIR]/instructions/testing.md`
- Documentation: `[SCAFFOLD_DIR]/instructions/documentation.md`
- ADRs: `[SCAFFOLD_DIR]/instructions/adrs.md`
- Docker/integration tests: `[SCAFFOLD_DIR]/instructions/docker-integration.md`
- CI/GitHub Actions: `[SCAFFOLD_DIR]/instructions/ci-github-actions.md`
- Agentic memory: `[SCAFFOLD_DIR]/instructions/agentic-memory.md`

<!--
  Preferred: list these paths in your project's opencode.json "instructions" array
  so OpenCode loads them automatically at session start:

  {
    "instructions": [
      "AGENTS.md",
      "[SCAFFOLD_DIR]/instructions/core.md",
      "[SCAFFOLD_DIR]/instructions/ruby.md",
      "[SCAFFOLD_DIR]/instructions/testing.md"
    ]
  }

  The bin/initialize_for_opencode script can set this up automatically.
  Fallback: symlink files into .opencode/ or copy sections inline.
-->

---

## Available Skills

The following skills are available globally (if installed per README):

- `adr-create` — scaffold a new ADR
- `ruby-gem-release` — guided release to RubyGems.org
- `integration-test` — start Docker services and run integration tests
- `docker-services` — manage Docker Compose services
- `lint-fix` — run StandardRB and auto-fix

---

## Project-Specific Context

<!-- Fill in what makes this project unique. Remove sections that don't apply. -->

### Language and Toolchain

- **Ruby version**: 3.2+ (see `ruby.md`)
- **Type checking**: Sorbet + Tapioca (see `ruby.md` and `decision:0002`)
- **Test framework**: RSpec
- **Linter**: StandardRB

### Services This Gem Integrates With

<!-- List the external services this gem connects to -->
- [e.g., PostgreSQL — for the persistence adapter]
- [e.g., OpenSearch — for the search adapter]

### Key Design Decisions

<!-- List the most important architectural decisions already made -->
- See `docs/adrs/` for the full ADR history

### Public API Surface

<!-- Brief tour of the most important public classes/methods -->
- `[ClassName]` — [what it does]
- `[ClassName]` — [what it does]

### Known Gotchas

<!-- Sharp edges that will bite an agent working on this codebase -->
- [e.g., "The connection pool is not thread-safe; tests must not share a pool instance"]

---

## What Agents Must NOT Do Without Explicit Approval

- Bump major version
- Publish to RubyGems.org (`gem push`)
- Push to `main`
- Add new runtime dependencies without proposing alternatives
- Modify `.github/workflows/` without explaining the change

---

## Running Things

```bash
# Install dependencies
bundle install

# Unit tests (fast; no Docker)
bundle exec rspec --exclude-pattern "spec/integration/**/*_spec.rb"

# All tests (requires Docker services; use integration-test skill)
bundle exec rspec

# Lint
bundle exec standardrb

# Lint + auto-fix
bundle exec standardrb --fix

# Generate docs
bundle exec yard doc

# Interactive console
bin/console
```

---

## Integration Test Services

<!-- List only the services this project actually uses -->
Services required for integration tests (see `docker/docker-compose.yml`):
- [e.g., postgres — port 5432]
- [e.g., opensearch — port 9200]

Start with: `docker compose -f docker/docker-compose.yml up -d`  
Stop with: `docker compose -f docker/docker-compose.yml down`

---

## Project Setup Checklist

When first bootstrapping this project, ensure these files exist:

- `.github/dependabot.yml` — keeps GitHub Actions SHAs and bundler deps current
- `cliff.toml` — git-cliff config for changelog generation
- `CONTEXT.md` — agent memory file (see `core.md` for template)

### `.github/dependabot.yml`

```yaml
version: 2
updates:
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"

  - package-ecosystem: "bundler"
    directory: "/"
    schedule:
      interval: "weekly"
```
