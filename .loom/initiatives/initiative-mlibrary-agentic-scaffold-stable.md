---
id: initiative:mlibrary-agentic-scaffold-stable
kind: initiative
status: completed
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-04-30T00:00:00Z
scope:
  kind: workspace
links:
  constitution:
    - constitution:main
---

# Objective

Deliver a stable, complete, and Loom-governed personal agentic library system
(`mlibrary-agentic-scaffold/`) that any future AI agent can use to work effectively on Ruby,
Kotlin, and Java library projects — covering the full lifecycle from project setup
through release — without requiring the owner to re-explain context.

# Why Now

A substantial first-pass system already exists in `mlibrary-agentic-scaffold/`. It has
instructions, skills, templates, and research. However:

- No Loom governance exists yet (being created now).
- Several known gaps remain (type checking, multi-module JaCoCo, semantic release,
  memU setup).
- Existing content has not been audited for completeness or accuracy.
- The skills are not all verified to work end-to-end.

Without Loom governance and a systematic audit, future agents will find partial
guidance and no reliable way to determine what is stable vs. speculative.

# In Scope

- All content in `mlibrary-agentic-scaffold/`: instructions, skills, templates, MCP guidance
- Loom records governing this workspace (constitution, initiatives, plans, tickets, wiki)
- `OPEN_QUESTIONS.md` resolution and promotion into decision records
- Skills verification and gap-fill
- AGENTS.md template quality and usability
- memU setup documentation
- Cross-session memory instrumentation (CONTEXT.md convention + memU)

# Out Of Scope

- Rails, frontend, full-stack, or web application guidance
- Python, TypeScript, JavaScript library guidance
- Third-party library projects (this is about the tooling, not the libraries
  built with the tooling)
- Replacing OpenCode or GitHub Copilot with other tooling

# Success Metrics

1. All instruction files (`instructions/*.md`) reviewed and validated accurate.
2. All skills (`adr-create`, `ruby-gem-release`, `integration-test`, `lint-fix`,
   `docker-services`) end-to-end verified.
3. All items in `OPEN_QUESTIONS.md` resolved into decision records or confirmed
   as future-work tickets.
4. AGENTS-ruby.md template reviewed and usable for a new project without amendment.
5. MCP memory server setup documented in `mcp/recommended-servers.md` and
   `instructions/agentic-memory.md`. (`@modelcontextprotocol/server-memory` adopted
   as Tier 2; memU original proposal superseded — see `decision:0001`.)
6. At least one wiki page per major concept area (memory, release, testing,
   CI, ADRs) exists in `.loom/wiki/`.
7. Loom records truthfully reflect work state at any point in time.

# Milestones

1. **Loom initialized** — `.loom/` tree bootstrapped, constitution written,
   initiative/plan/ticket chain live. *(In progress — current session)*
2. **Audit complete** — all instruction files reviewed; gaps filed as tickets.
3. **Skills verified** — all skills exercised against a real or mock project.
4. **Open questions resolved** — all `OPEN_QUESTIONS.md` items promoted into
   decision records or future-work tickets.
5. **Template usable** — AGENTS-ruby.md template reviewed and polished.
6. **Wiki seeded** — initial wiki pages written for key concept areas.
7. **Stable** — no open high-priority tickets; system is ready to use in a
   new project without modification.

# Dependencies

- OpenCode + GitHub Copilot (toolchain; assumed stable)
- Loom package (`open-loom@latest`; already installed)
- Node.js / npx (for `@modelcontextprotocol/server-memory`, Tier 2 MCP memory)

# Risks

- **Scope creep**: temptation to add language coverage (e.g., Python, Go) before
  the core system is stable. Mitigate: strict "libraries, not applications, Ruby
  + JVM only" constraint in constitution.
- **Stale research**: agentic memory landscape evolves fast; `agentic-memory.md`
  may become inaccurate. Mitigate: quarterly review cadence in constitution.
- **Unverified skills**: skills were written speculatively and may not work
  end-to-end. Mitigate: dedicated skills-verification tickets before declaring stable.

# Linked Work

- `plan:mlibrary-agentic-scaffold-stabilize` — execution sequencing
- `research:agentic-memory` — agentic memory system evaluation
- `decision:0001` — memory tier decision
- Tickets: all 15 original + 7 audit-derived tickets filed, worked, and closed or deferred

# Status Summary

Completed 2026-04-30. All seven success metrics met:
1. All 10 instruction files reviewed; 5 gap tickets filed and all resolved.
2. All 5 skills verified; 2 corrected (lint-fix SpotBugs removed; jvm-release removed — see decision:0004).
3. All OPEN_QUESTIONS.md items triaged: Sorbet→decision:0002, multi-module JaCoCo→testing.md,
   GraalVM+Semantic Release→future-work.
4. AGENTS-ruby.md template polished and usable. AGENTS-jvm.md removed (decision:0004).
5. `@modelcontextprotocol/server-memory` setup documented (memU superseded per decision:0001).
6. 8 wiki pages cover memory, ruby-release, testing+coverage, CI, overview,
   new-project-setup, loom-orientation (jvm-release-workflow marked stale per decision:0004).
7. All Loom records truthful at close.

2026-05-01 update: scope pulled back to Ruby + general-purpose. JVM coverage withdrawn.
See decision:0004.

# Completion Basis

All seven success metrics met. No open high-priority tickets under
`plan:mlibrary-agentic-scaffold-stabilize`. System is ready to use in a new project with
only `AGENTS.md` as the entry point. `plan:mlibrary-agentic-scaffold-stabilize` updated to
`completed`.

2026-05-01 session update: Substantial second-pass improvements completed after initial close.
Key changes: `bin/initialize_for_opencode` and `bin/update_mlibrary_scaffold` scripts created;
`opencode.json` instructions array is now the primary instruction-file mechanism (not AGENTS.md);
11 ADRs written (ADR-001 through ADR-011); README restructured around installer as primary path;
agentic memory research doc created; instruction files slimmed (testing.md, changelog-automation.md,
docker-integration.md, ci-github-actions.md, core.md); Loom checkpoint policy added to AGENTS.md
and ss skill; wiki pages updated for installer, scope pullback, and jvm supersession.
