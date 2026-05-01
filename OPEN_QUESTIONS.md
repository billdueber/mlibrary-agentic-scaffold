# Open Questions and Assumptions

This file records assumptions made when building the mlibrary-agentic-scaffold system, and
questions that should be revisited with the project owner.

---

## Resolved Assumptions and Questions

### Ruby

- **RuboCop vs StandardRB**: ✅ **StandardRB confirmed.** No existing `.rubocop.yml` files
  to preserve. All Ruby projects use StandardRB.

- **RSpec only**: ✅ **Confirmed.** All Ruby projects use RSpec. No minitest support needed.

- **Ruby 3.2+ minimum**: ✅ **Assumed correct.** No older projects to worry about.

- **No C extensions**: ✅ **Confirmed.** No native gems; no `rake-compiler` needed.

### Java / Kotlin

- **JVM coverage**: ✅ **Withdrawn (2026-05-01).** JVM is no longer a first-class concern
  of this scaffold. See `decision:0004`. If JVM coverage is added in future: Gradle Kotlin
  DSL preferred, Java 21 LTS minimum, no shims.

### CI

- **GitHub Actions only**: ✅ **Confirmed.** No other CI systems in use.

### Agentic Memory

- **CONTEXT.md vs mem0**: ✅ **Resolved (2026-04-30).** Three-tier model adopted.
  Tier 1: `CONTEXT.md` + Loom records (mandatory, zero infra). Tier 2:
  `@modelcontextprotocol/server-memory` (official Anthropic MCP reference, zero infra,
  `npx` install). Tier 3: mem0 (conditional, Python + Docker or cloud). `memory-mcp-lite`
  deferred for review late June 2026. See `decision:0001` and `instructions/agentic-memory.md`.

### GPG Commit Signing

- ✅ **One-line note adopted.** Setup is a human task; full walkthrough removed from
  `instructions/core.md`. See GitHub's GPG signing guide for setup instructions.

### Code Coverage

- ✅ **Yes, enforce coverage.** SimpleCov for Ruby (≥90%). JVM coverage enforcement
  not currently covered (JVM coverage withdrawn — see `decision:0004`).

---

## Still Open

1. **Sorbet / RBS**: ✅ **Resolved (2026-04-30).** Sorbet adopted for AI-agent-authored
   type annotations. Rationale: inline `sig` blocks reduce context cost, Tapioca generates
   stubs, `srb tc` gives clean CLI feedback. See `decision:0002` and `instructions/ruby.md`.

2. **Semantic Release**: Deferred — manual release workflow sufficient. Activate if manual
   releases become a burden.

---

## Things to Revisit

- Update CI action SHA hashes when Dependabot flags them
- Review `agentic-memory.md` quarterly — this space evolves rapidly
- Assess `memory-mcp-lite` maturity late June 2026 (ticket:memory-revisit)
- Check mem0's cloud API pricing if Tier 3 becomes relevant
