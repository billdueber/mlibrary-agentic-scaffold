---
id: ticket:add-security-md
kind: ticket
status: cancelled
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

# Add instructions/security.md

## Why

`instructions/core.md` covers GPG signing briefly, but there is no dedicated security
instruction file for common library and service security concerns. Ruby gem authors
and JVM library maintainers benefit from explicit agent guidance on topics like
dependency auditing, secret scanning, SBOM generation, and CVE triage that currently
have no home in the scaffold.

## In Scope

- Create `instructions/security.md`
- Cover: dependency auditing (`bundle-audit` for Ruby, `gradle dependencyCheckAnalyze`
  or `trivy` for JVM), secret scanning (Gitleaks or `git-secrets`), SBOM generation
  (Syft or CycloneDX plugin), GitHub security features (Dependabot, code scanning,
  secret scanning alerts)
- Agent rule callouts: do not silently suppress audit findings; report CVEs with CVSS
  score and upstream status; do not commit secrets
- Add `security.md` to the `instructions` array in `opencode.json` template and
  `bin/initialize_for_opencode`
- Cross-reference from `core.md` and relevant templates

## Out of Scope

- Application-layer auth (login, RBAC, session management)
- Runtime WAF or network security
- Penetration testing

## Acceptance

- `instructions/security.md` exists and covers the in-scope topics
- File is referenced in `opencode.json` instructions array in templates
- `bin/initialize_for_opencode` offers or includes the file

## Execution Notes

None yet.
