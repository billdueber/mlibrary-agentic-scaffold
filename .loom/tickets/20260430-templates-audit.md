---
id: ticket:templates-audit
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
  - ticket:verify-adr-create
  - ticket:verify-ruby-release
  - ticket:verify-jvm-release
---

# Summary

Review and polish both AGENTS.md templates (`templates/AGENTS-ruby.md` and
`templates/AGENTS-jvm.md`). Verify they reference current instruction file
paths and produce a usable AGENTS.md for a new project without amendment.

# Acceptance Criteria

1. Both templates manually walked through a "new project" scenario.
2. All instruction file references point to valid paths.
3. Any stale or missing sections corrected.

# Journal

- 2026-04-30: Ticket created as Wave 4 stub.
- 2026-04-30: Closed. Both templates reviewed. Changes made:
  AGENTS-ruby.md: added agentic-memory.md to shared instructions list; added Language
  and Toolchain section (Ruby 3.2+, Sorbet, RSpec, StandardRB).
  AGENTS-jvm.md: added agentic-memory.md to shared instructions list; fixed JVM target
  from "[17 | 21]" to "21 (minimum)" per constitution constraint.
  All instruction file paths verified valid. Templates usable for a new project.
