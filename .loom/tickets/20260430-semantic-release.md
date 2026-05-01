---
id: ticket:semantic-release
kind: ticket
status: cancelled
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
---

# Summary

Evaluate Semantic Release (or similar automated semver tooling) and, if adopted,
write guidance for integrating it into the mlibrary-agentic-scaffold release workflow.

This ticket is future-work — activate only if manual releases become a
recurring burden.

# Acceptance Criteria

1. A research note (`research:semantic-release`) covers: options evaluated
   (semantic-release, release-please, standard-version), tradeoffs, recommendation.
2. If adopted: `instructions/changelog-automation.md` and the `ruby-gem-release` skill updated to reflect automated semver.
3. If not adopted: constitution open question updated to "evaluated and rejected"
   with rationale.

# Blockers

No burden signal yet from manual releases. Do not activate until the manual
workflow causes repeated friction.

# Next Move / Next Route

Proposed — do not start until manual releases become a burden.

# Journal

- 2026-04-30: Created as future-work ticket during ticket:resolve-open-questions.
  Manual release workflow currently sufficient; automated semver not evaluated.
