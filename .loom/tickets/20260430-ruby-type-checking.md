---
id: ticket:ruby-type-checking
kind: ticket
status: closed
change_class: documentation-explanation
risk_class: low
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
scope:
  kind: workspace
links:
  plan:
    - plan:mlibrary-agentic-scaffold-stabilize
  constitution:
    - constitution:main
depends_on:
  - ticket:audit-instructions
---

# Summary

Research Sorbet vs RBS for Ruby type checking; record a decision; update
`instructions/ruby.md` with the adopted guidance.

# Acceptance Criteria

1. `decision:0002` (ruby-type-checking) created with both options evaluated and
   rejected alternatives named.
2. `instructions/ruby.md` updated with type-checking guidance consistent with
   the decision.
3. Constitutional open question #1 resolved.

# Blockers

None. Decision recorded as `decision:0002`.

# Next Move / Next Route

Update `mlibrary-agentic-scaffold/instructions/ruby.md` with Sorbet guidance:
- Add Sorbet + Tapioca setup instructions
- Document `sorbet-runtime` dev-only placement
- Document `srb tc` as the CI gate
- Document migration conditions to RBS

# Journal

- 2026-05-01: Closed. All acceptance criteria met: `decision:0002` written (Sorbet adopted), `instructions/ruby.md` updated with Sorbet + Tapioca setup, `srb tc` documented as CI gate. Constitutional open question #1 resolved.
