# ADR-005: GitHub Actions as the Sole CI Target

## Status

Accepted

## Date

2026-04-30

## Context

Library projects governed by this scaffold need CI. The scaffold must choose
which CI system(s) to target for its instruction files, workflow templates, and
agent guidance. Supporting multiple CI systems multiplies maintenance surface
without obvious benefit for a solo-developer workflow.

The owner's projects are hosted on GitHub. GitHub Actions is the native CI
system for GitHub-hosted repositories — no additional account, separate
authentication, or billing required. The free tier covers typical open-source
library build workloads.

Alternative CI systems (Jenkins, CircleCI, GitLab CI, Travis CI) require either
self-hosting, a separate account, or integration steps that add overhead for no
gain given GitHub hosting.

## Decision

This scaffold targets **GitHub Actions exclusively**. All CI instruction files,
workflow templates, and agent guidance assume GitHub Actions.

Guidance for Jenkins, CircleCI, GitLab CI, Travis CI, or other CI systems is
out of scope and must not appear in scaffold instruction files.

## Consequences

### Positive

- No additional account or billing beyond GitHub hosting.
- Native integration with GitHub PRs, branch protections, and Dependabot.
- A single CI system means a single set of conventions for agents to learn.
- Workflow files (`.github/workflows/*.yml`) are version-controlled alongside
  the project source.

### Negative

- Projects not hosted on GitHub cannot directly reuse the CI guidance without
  adaptation.
- GitHub Actions pricing or availability changes could force migration.

### Neutral

- Agents must not modify `.github/workflows/` without explaining the change —
  this constraint is enforced at the AGENTS.md level regardless of CI system.
- The `ci-github-actions.md` instruction file is the authoritative guide;
  agents should read it before generating or modifying workflow files.

## Alternatives Considered

**CircleCI / Travis CI**: Separate account and billing. No native GitHub
integration advantage. Rejected.

**GitLab CI**: Requires GitLab hosting or mirroring. Out of scope. Rejected.

**Jenkins**: Self-hosting required. Infrastructure burden outweighs benefit for
a solo dev. Rejected.

**Supporting multiple CI systems**: Multiplies maintenance surface and
instruction file complexity with no clear benefit for the owner's use case.
Rejected.

## Open Questions

None at adoption time.
