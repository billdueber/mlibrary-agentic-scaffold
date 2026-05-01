---
id: ticket:graalvm-guidance
kind: ticket
status: cancelled
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
---

# Summary

Write GraalVM native image guidance for JVM library projects and add it to
`instructions/jvm.md`.

This ticket is future-work — activate only when a JVM library project in this
workspace requires a native binary.

# Acceptance Criteria

1. `instructions/jvm.md` includes a GraalVM native image section covering:
   - `org.graalvm.buildtools.native` Gradle plugin configuration
   - Reflection metadata (`reflect-config.json`) guidance
   - CI considerations (build time, platform matrix)
   - Agent rules for native-image compatibility
2. Known caveats documented (reflection, dynamic proxies, classpath scanning).

# Blockers

No concrete use case yet. Do not activate until a real project needs it.

# Next Move / Next Route

Proposed — do not start until a GraalVM native image build is actually needed.

# Journal

- 2026-05-01: Cancelled. `instructions/jvm.md` was deleted in decision:0004 (scope pullback to Ruby + general). GraalVM native image guidance is out of scope until JVM coverage is formally re-added.
