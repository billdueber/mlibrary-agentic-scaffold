---
id: ticket:multimodule-jacoco
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
  - ticket:audit-instructions
---

# Summary

Write multi-module Gradle/JaCoCo coverage aggregation guidance and add it to
`instructions/testing.md`.

# Acceptance Criteria

1. `instructions/testing.md` includes a multi-module JaCoCo section with
   `:build-tools:jacocoAggregateReport` configuration.
2. Guidance tested or annotated with known caveats.

# Blockers

None — can be written from JaCoCo docs without a real project.

# Journal

- 2026-04-30: Ticket created as Wave 2 stub.
- 2026-04-30: Closed. Added "JVM — JaCoCo Multi-Module Aggregation (Gradle)" subsection
  to `instructions/testing.md` after the existing single-module JaCoCo (Gradle) block.
  Covers: jacoco-report-aggregation plugin setup, :build-tools aggregation module config,
  coverage verification task, CI snippet, caveats (integration test source sets, Android/KMP,
  Gradle version requirements, :bom exclusion), and agent rules.
