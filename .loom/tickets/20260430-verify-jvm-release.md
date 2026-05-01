---
id: ticket:verify-jvm-release
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

Exercise the `jvm-release` skill end-to-end against a real or mock project.
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
- 2026-04-30: Closed. Two issues found and fixed in the skill:
  (1) "Determine Target Repository" section only listed OSSRH as the Maven Central
      path — updated to distinguish new portal (central.sonatype.com, default) from
      legacy OSSRH (existing projects only).
  (2) Required secrets table listed only OSSRH_USERNAME/PASSWORD — updated to include
      MAVEN_CENTRAL_USERNAME/PASSWORD for new portal; OSSRH creds retained as legacy-only row.
  Skill now consistent with jvm.md after the jvm-ossrh-legacy-clarify fix.
