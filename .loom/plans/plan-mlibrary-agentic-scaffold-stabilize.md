---
id: plan:mlibrary-agentic-scaffold-stabilize
kind: plan
status: completed
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-04-30T00:00:00Z
scope:
  kind: workspace
links:
  initiative:
    - initiative:mlibrary-agentic-scaffold-stable
  constitution:
    - constitution:main
---

# Purpose

Sequence the work required to bring `mlibrary-agentic-scaffold/` from a solid first-pass
to a stable, audited, verified system that any agent can use on a new project
without amendment.

# Strategy

Work in four waves:

1. **Foundation** — Loom initialization and audit of existing content
2. **Gap-fill** — Resolve open questions, document missing pieces
3. **Verification** — Exercise all skills end-to-end
4. **Polish and release** — Templates, wiki, and declare stable

Each wave's tickets must be substantially complete before the next wave begins.
Waves 1 and 2 overlap slightly (Loom init → audit can proceed immediately; gap-fill follows audit findings).

# Strategy Snapshot

Wave 1 is in progress. Loom workspace bootstrap is complete in this session.
Audit has not yet started. All subsequent waves are pending.

# Workstreams

**A. Governance** — Loom records (this plan; constitution; initiative; tickets)

**B. Audit** — Review each instruction file and skill for accuracy and completeness

**C. Gap-fill** — memU documentation; open-questions resolution; Ruby type
checking decision; multi-module JaCoCo guidance

**D. Skills verification** — Exercise each skill against a real or mock project

**E. Templates** — Audit and polish AGENTS-ruby.md

**F. Wiki** — Write initial wiki pages for key concept areas

# Milestones

1. Loom workspace fully initialized *(Wave 1 — in progress)*
2. All instruction files audited; gaps filed as tickets
3. All open questions resolved into decision records
4. memU setup documented
5. All skills verified
6. AGENTS-ruby.md template reviewed and usable
7. Wiki seeded (5+ pages)
8. No open high-priority tickets → declare stable

# Sequencing

Wave 1 (Foundation) must finish before Wave 3 (Verification), because
verification tickets need properly scoped tickets to own them.

Wave 2 (Gap-fill) can begin as soon as audit findings are filed — does not need
Wave 1 fully closed, only Loom initialized.

Wave 3 (Verification) requires Wave 2 to be substantially done, so verified
skills reflect the corrected/completed guidance.

Wave 4 (Polish) is the final pass; requires Wave 3 done.

# Execution Waves

## Wave 1 — Foundation (current)

| Ticket | Description | Scope |
|--------|-------------|-------|
| ticket:loom-init | Loom workspace initialization | `.loom/` tree, all seed records |
| ticket:audit-instructions | Audit all instruction files | `mlibrary-agentic-scaffold/instructions/*.md` |

## Wave 2 — Gap-fill

| Ticket | Description | Scope |
|--------|-------------|-------|
| ticket:memu-setup | Document memU in `mcp/recommended-servers.md` | `mcp/recommended-servers.md` |
| ticket:ruby-type-checking | Research + decide Sorbet vs RBS (decision made: Sorbet) | `instructions/ruby.md`, `constitution/decisions/` |
| ticket:ruby-type-checking-instructions | Add Sorbet guidance to ruby.md | `instructions/ruby.md` |
| ticket:multimodule-jacoco | Write multi-module JaCoCo guidance | `instructions/testing.md` |
| ticket:resolve-open-questions | Promote remaining open questions | `constitution/decisions/`, `OPEN_QUESTIONS.md` |
| ticket:ci-floating-action-tags | Fix floating action tags in examples | `instructions/ci-github-actions.md`, `changelog-automation.md` |
| ticket:docker-compose-version-key | Remove deprecated `version:` key from skeleton | `instructions/docker-integration.md` |

## Wave 3 — Verification

| Ticket | Description | Scope |
|--------|-------------|-------|
| ticket:verify-adr-create | Exercise adr-create skill | `skills/adr-create/` |
| ticket:verify-ruby-release | Exercise ruby-gem-release skill | `skills/ruby-gem-release/` |
| ticket:verify-integration-test | Exercise integration-test skill | `skills/integration-test/` |
| ticket:verify-lint-fix | Exercise lint-fix skill | `skills/lint-fix/` |
| ticket:verify-docker-services | Exercise docker-services skill | `skills/docker-services/` |

## Wave 4 — Polish and declare stable

| Ticket | Description | Scope |
|--------|-------------|-------|
| ticket:templates-audit | Audit and polish AGENTS-ruby.md template | `templates/` |
| ticket:wiki-seed | Write initial wiki pages (5+ pages) | `.loom/wiki/` |
| ticket:declare-stable | Final check against success metrics | `initiative:mlibrary-agentic-scaffold-stable` |

## Future-Work (Proposed, no activation condition met yet)

| Ticket | Description | Activate when |
|--------|-------------|---------------|
| ticket:semantic-release | Evaluate + adopt automated semver tooling | Manual releases become a burden |
| ticket:memory-revisit | Assess `memory-mcp-lite` maturity | Late June 2026 |

# Risks

- **Skills may have real bugs**: skills were written speculatively. Verification
  may reveal issues requiring significant rework — budget extra time for Wave 3.
- **Audit may reveal large instruction gaps**: if audit finds major gaps,
  Wave 2 gap-fill tickets may expand significantly.
- **Ruby type checking is genuinely undecided**: ~~the owner has not formed a preference. This ticket may need to remain open as a deferred question.~~ **Resolved**: Sorbet adopted per decision:0002.

# Evidence Strategy

- Instruction audits: record findings in tickets; update instruction files directly.
- Skills verification: record pass/fail and any issues found as evidence notes
  in the ticket or `.loom/evidence/`.
- Template audit: manually check each template against a fresh project scenario.
- Wiki: accepted on creation; no separate review gate unless content is disputed.

# Plan Readiness Review

Spec / acceptance coverage: initiative `mlibrary-agentic-scaffold-stable` has seven explicit
success metrics. Each maps to one or more tickets in this plan.

Placeholder scan: no placeholder tokens remain in this plan.

Ticket-sized slices: each ticket in Waves 1–4 is bounded to one file domain
or one skill.

Likely write scopes: instruction files, skills, templates, `.loom/` records.
No database migrations. No code under test.

Likely verification posture: `observation-first` for audit and doc tickets;
`test-first` is not applicable (no executable test suite for markdown).
Skills verification uses `observation-first` (manual exercise).

Evidence and critique route: low-risk doc changes need no critique. Skills
verification results logged as evidence. Template audit results logged in ticket.

Stop / loopback conditions: if audit reveals the instruction files are
fundamentally wrong (not just incomplete), escalate to constitution for
principles review before gap-fill continues.

# Exit Criteria

1. `initiative:mlibrary-agentic-scaffold-stable` all seven success metrics met.
2. No open high-priority tickets under this plan.
3. `ticket:declare-stable` closed.

# Completion Basis

Completed 2026-04-30. All four waves executed in sequence:
- Wave 1: Loom init + instruction audit — both tickets closed.
- Wave 2: 9 gap-fill tickets — all closed or cancelled (memu-setup cancelled; replaced
  by memory-server-setup which is closed). 2 future-work tickets filed (proposed).
- Wave 3: All 6 skills verified — 2 corrected, all verify tickets closed.
- Wave 4: Templates audited, 4 wiki pages written (8 total), initiative and plan
  declared stable.

All exit criteria met: initiative success metrics verified, no open high-priority
tickets, ticket:declare-stable closed.
