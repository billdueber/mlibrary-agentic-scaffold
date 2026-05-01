---
id: ticket:ruby-type-checking-instructions
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
  decision:
    - decision:0002
depends_on:
  - ticket:audit-instructions
---

# Summary

Add Sorbet type-checking guidance to `mlibrary-agentic-scaffold/instructions/ruby.md`.
`decision:0002` chose Sorbet for AI-agent-authored type annotations; the
instruction file currently has no type-checking section.

# Acceptance Criteria

1. `instructions/ruby.md` has a `## Type Checking` section covering:
   - Gemfile placement (`group :development, :test` only; not in gemspec)
   - `# typed:` sigil convention (`strict` for new files, `true` as baseline)
   - `bundle exec tapioca gems` for stub generation
   - `bundle exec srb tc` as the check command and CI gate
   - `sig` block syntax with a brief example
2. Section references `decision:0002` for rationale.
3. CI section in `ruby.md` (or a note in the file) mentions `srb tc` in the
   lint/check step.

# Non-goals

- Updating `ci-github-actions.md` (separate ticket if needed)
- Sorbet integration test guidance

# Journal

- 2026-04-30: Created during ticket:audit-instructions audit pass. Gap identified
  because decision:0002 was resolved in this session.
