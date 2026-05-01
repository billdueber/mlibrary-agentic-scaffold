---
id: ticket:jvm-java21-minimum
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
  constitution:
    - constitution:main
depends_on:
  - ticket:audit-instructions
---

# Summary

Fix contradiction between `instructions/jvm.md` and `constitution:main` on the
JVM minimum target. The instruction says "Java 17 LTS minimum; Java 21 LTS
preferred for new projects." The constitution says "JVM guidance targets Java 21
LTS minimum." These must agree.

# Acceptance Criteria

1. `instructions/jvm.md` Assumptions section updated to state Java 21 LTS as the
   minimum (matching constitution constraint).
2. Any other references to "Java 17" as a minimum in `jvm.md` removed or updated.
3. Maven `pom.xml` skeleton in `jvm.md` updated: `maven.compiler.source` and
   `maven.compiler.target` changed from `21` (already set) — verify consistency.
4. Gradle `jvmToolchain(21)` already correct — confirm it matches the new stated
   minimum.

# Non-goals

- Changing the constitutional constraint itself (Java 21 min is correct policy)
- Addressing Java 17 projects that may already exist (not in scope here)

# Journal

- 2026-04-30: Created during ticket:audit-instructions audit. Instruction says
  "17 min / 21 preferred"; constitution says "21 min". Constitution wins.
