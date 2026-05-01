---
id: ticket:docker-compose-version-key
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

The `docker/docker-compose.yml` skeleton in `docker-integration.md` includes
`version: "3.9"`. This top-level `version` key is deprecated in Docker Compose
v2 and generates a warning. Modern Docker Compose ignores it; the file spec
version is determined by the Compose binary, not the key.

# Acceptance Criteria

1. `version: "3.9"` line removed from the docker-compose.yml skeleton in
   `docker-integration.md`.
2. A brief comment added to the skeleton noting that the `version` key is
   obsolete in Compose v2+ and should be omitted.

# Journal

- 2026-04-30: Created during ticket:audit-instructions audit. Minor correctness
  issue; affects agent-generated docker-compose files if the skeleton is copied.
