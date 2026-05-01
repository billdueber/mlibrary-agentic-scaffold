---
id: wiki:testing-and-coverage
kind: wiki
page_type: reference
status: accepted
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
scope:
  kind: workspace
sources:
  - instructions/testing.md
  - instructions/docker-integration.md
  - skills/integration-test/SKILL.md
links:
  wiki:
    - wiki:mlibrary-agentic-scaffold-overview
    - wiki:ci-conventions
---

# Testing and Coverage Conventions

## Philosophy

- Tests are not afterthoughts; they are written alongside or before production code.
- Unit tests run without external services; integration tests use Docker.
- ≥90% line coverage is enforced in CI for all Ruby projects.
- Prefer live Dockerized services over mocks for integration tests.

## Test Layers

| Layer | Scope | Speed | Services needed |
|---|---|---|---|
| Unit | One class/function in isolation | Fast (<1s each) | None |
| Integration | Across layers or with real service | Slower | Docker services |

## Ruby

### Running tests

```bash
# Unit tests only (no Docker)
bundle exec rspec --exclude-pattern "spec/integration/**/*_spec.rb"

# All tests (requires Docker services)
bundle exec rspec
```

### Coverage — SimpleCov

```ruby
# spec/spec_helper.rb — must be first lines
require "simplecov"
SimpleCov.start do
  add_filter "/spec/"
  minimum_coverage 90
  enable_coverage :branch
end
```

Add `coverage/` to `.gitignore`.

## Integration Tests with Docker

Use the `integration-test` skill for the full lifecycle:

1. `docker compose -f docker/docker-compose.yml up -d`
2. Wait for healthy status
3. Run tests
4. `docker compose -f docker/docker-compose.yml down`

Project convention: Docker Compose file lives at `docker/docker-compose.yml`.
No deprecated `version:` key.

**Prefer live services over mocks.** Acceptable exceptions: external APIs with
no local equivalent, unacceptable CI cost (>10 min), unavoidable flakiness.
When a mock is used, add a comment explaining why.

## Agent Rules

- Write failing tests before fixing bugs.
- Never mark a test as pending/skipped to make CI green.
- Integration test specs live in `spec/integration/`.
- When adding a Docker service, update `docker/docker-compose.yml` and add
  a CI `services:` block (or health-check equivalent) to `.github/workflows/ci.yml`.

# Sources

- `instructions/testing.md`
- `instructions/docker-integration.md`
- `skills/integration-test/SKILL.md`

# Related Pages

- `wiki:ci-conventions` — how tests run in CI
- `wiki:mlibrary-agentic-scaffold-overview` — system overview

# Change Notes

- 2026-04-30: Initial page created during Loom workspace bootstrap.
- 2026-05-01: Removed JVM JaCoCo and Maven coverage sections (decision:0004,
  JVM coverage withdrawn). Added "Mocks vs Live Services" note from testing.md.
