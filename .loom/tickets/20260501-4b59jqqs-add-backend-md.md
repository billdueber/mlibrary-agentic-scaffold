---
id: ticket:add-backend-md
kind: ticket
status: proposed
priority: low
created_at: 2026-05-01T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
scope:
  kind: repository
  repositories:
    - repo:root
links:
  parent: []
  blocks: []
  blocked_by: []
---

# Add instructions/backend.md (or record explicit deferral)

## Why

The scaffold constitution was softened from "libraries only" to "library-first,
backend-friendly." Backend services (Sinatra/Rack apps, Spring Boot services, etc.)
are welcomed but currently have no coverage in the instruction files. The gap is
acknowledged but unaddressed: agents working on a backend project get library-centric
guidance and no service-specific rules.

## Decision Point

Before writing the file, decide whether to:

a) Write `instructions/backend.md` covering service-specific concerns (health checks,
   configuration management, graceful shutdown, Docker packaging, service-to-service
   auth patterns, structured logging)

b) Record an explicit deferral in `OPEN_QUESTIONS.md` with rationale ("backend is
   welcomed but the scaffold maintainer has not authored backend instructions yet;
   do not synthesize from library rules")

Either outcome closes this ticket. The deferral option is valid and honest.

## In Scope (if option a)

- Ruby: Sinatra/Rack conventions, Puma config, health endpoint, environment-based config
- JVM: Spring Boot or Ktor basics, Dockerfile pattern, graceful shutdown hook
- Cross-language: structured logging (JSON), /health and /metrics endpoints, 12-factor config
- Agent rule: flag when library-centric patterns are being applied to a service context

## Out of Scope

- Full framework tutorials
- Frontend (explicitly out of scope in constitution)
- Data pipelines

## Acceptance

- Either `instructions/backend.md` exists and covers the in-scope topics, OR
- `OPEN_QUESTIONS.md` contains an explicit deferral entry with date and rationale

## Execution Notes

None yet.
