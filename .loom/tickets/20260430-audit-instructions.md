---
id: ticket:audit-instructions
kind: ticket
status: closed
change_class: documentation-explanation
risk_class: low
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-04-30T00:00:00Z
scope:
  kind: workspace
links:
  plan:
    - plan:mlibrary-agentic-scaffold-stabilize
  initiative:
    - initiative:mlibrary-agentic-scaffold-stable
depends_on:
  - ticket:loom-init
---

# Summary

Audit all nine instruction files in `mlibrary-agentic-scaffold/instructions/` for accuracy,
completeness, and consistency. File a follow-up ticket for each significant gap
or inaccuracy found.

# Context

The instruction files were written speculatively in a single session. They
represent a solid first pass but have not been reviewed against real project
experience. Gaps and inaccuracies should be found before skills verification
depends on correct guidance.

# Why Now

Wave 1 — first real work after Loom initialization.

# Scope

- `mlibrary-agentic-scaffold/instructions/core.md`
- `mlibrary-agentic-scaffold/instructions/documentation.md`
- `mlibrary-agentic-scaffold/instructions/testing.md`
- `mlibrary-agentic-scaffold/instructions/docker-integration.md`
- `mlibrary-agentic-scaffold/instructions/ci-github-actions.md`
- `mlibrary-agentic-scaffold/instructions/adrs.md`
- `mlibrary-agentic-scaffold/instructions/agentic-memory.md`
- `mlibrary-agentic-scaffold/instructions/ruby.md`
- `mlibrary-agentic-scaffold/instructions/jvm.md`
- `mlibrary-agentic-scaffold/instructions/changelog-automation.md`

# Non-goals

- Rewriting skills (separate tickets)
- Resolving open questions (ticket:resolve-open-questions)
- Making major content changes — audit findings become tickets, not inline rewrites

# Acceptance Criteria

1. All ten instruction files read and assessed.
2. Each significant gap, inaccuracy, or missing section is filed as a follow-up
   ticket or noted in `OPEN_QUESTIONS.md`.
3. Minor style/wording issues fixed inline.
4. A summary comment in this ticket's journal lists the overall finding per file.

# Coverage

None - ticket-local criteria only.

# Claim Matrix

None - no spec claim IDs apply.

# Blockers

None.

# Next Move / Next Route

Local edit pass — read each file, note findings, create follow-up tickets for
significant gaps.

# Evidence

Journal entry listing per-file assessment findings.

# Critique Disposition

Risk class: low
Critique policy: optional
Policy rationale: Documentation review with no behavior changes.
Required critique profiles: None.
Findings: None - no critique yet.
Disposition status: not_required

# Wiki Disposition

If audit reveals a recurring pattern worth documenting (e.g., "how agents
should read instruction files"), promote to wiki.

# Journal

- 2026-04-30: Ticket created during Loom initialization. Status: ready.
- 2026-04-30: Audit completed. All 10 files read. Findings:
  - core.md: good; no gaps
  - ruby.md: missing Sorbet type-checking section → ticket:ruby-type-checking-instructions
  - jvm.md: (1) JVM minimum says 17; constitution says 21 → ticket:jvm-java21-minimum;
    (2) dual Maven Central publishing paths confusing → ticket:jvm-ossrh-legacy-clarify
  - testing.md: good; JaCoCo multi-module gap already tracked
  - ci-github-actions.md: floating action tag in example → ticket:ci-floating-action-tags
  - docker-integration.md: deprecated `version:` key in skeleton → ticket:docker-compose-version-key
  - adrs.md: good
  - documentation.md: good
  - changelog-automation.md: floating action tag same as ci.md → same ticket:ci-floating-action-tags
  - agentic-memory.md: minor star count discrepancy vs research record; superseded by
    research:agentic-memory + decision:0001 but still useful agent-readable context; no action needed
  - 5 follow-up tickets filed; all set to ready
