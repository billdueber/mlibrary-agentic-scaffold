---
id: ticket:verify-lint-fix
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
depends_on:
  - ticket:resolve-open-questions
---

# Summary

Exercise the `lint-fix` skill end-to-end against a real or mock project.
Record pass/fail and any issues found. File follow-up tickets for bugs or
missing guidance.

# Acceptance Criteria

1. Skill invoked and observed to complete without errors, OR issues filed.
2. Any gaps in the skill's SKILL.md corrected or filed as follow-up tickets.
3. Evidence note in journal with outcome.

# Blockers

None.

# Journal

- 2026-04-30: Ticket created as Wave 3 stub.
- 2026-04-30: Closed. One issue found and fixed:
  Skill referenced SpotBugs alongside Checkstyle for Java, but SpotBugs is not
  documented in instructions/jvm.md. Skill updated to list Checkstyle only for Java.
  Ruby (StandardRB) and Kotlin (ktlint + Detekt) sections correct and consistent.
