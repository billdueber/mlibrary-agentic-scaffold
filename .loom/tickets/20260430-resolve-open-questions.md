---
id: ticket:resolve-open-questions
kind: ticket
status: closed
change_class: record-hygiene
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
  - ticket:ruby-type-checking
  - ticket:multimodule-jacoco
---

# Summary

Review all remaining items in `OPEN_QUESTIONS.md` and the constitution's open
questions section. Promote each into a decision record or confirm as future-work
ticket. Close out `OPEN_QUESTIONS.md` items that are now resolved.

# Acceptance Criteria

1. Every item in `OPEN_QUESTIONS.md` is either: (a) resolved with a decision
   record reference, or (b) filed as a future-work ticket.
2. Constitutional open questions section updated to reflect resolved items.
3. `OPEN_QUESTIONS.md` reflects current state (no stale "resolved" items in
   the open section).

# Journal

- 2026-04-30: Ticket created as Wave 2 stub.
- 2026-04-30: Closed. All OPEN_QUESTIONS.md items triaged:
  (1) Sorbet/RBS — resolved; see decision:0002 and ruby.md.
  (2) GraalVM — deferred; ticket:graalvm-guidance filed (proposed).
  (3) Semantic Release — deferred; ticket:semantic-release filed (proposed).
  (4) Multi-module JaCoCo — resolved; guidance added to testing.md per ticket:multimodule-jacoco.
  OPEN_QUESTIONS.md updated; constitution open questions #3/#4/#5 updated; plan updated with future-work section.
