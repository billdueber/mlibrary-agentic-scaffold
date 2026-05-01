---
id: ticket:jvm-ossrh-legacy-clarify
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

`instructions/jvm.md` contains two Maven Central publishing sections:

1. "Publishing to Maven Central" (old OSSRH portal: `s01.oss.sonatype.org`)
2. "Publishing to Maven Central (New Portal)" (`central.sonatype.com` + nmcp plugin)

An agent reading the file may not know which to use. The old OSSRH section uses
`OSSRH_USERNAME`/`OSSRH_PASSWORD` secret names; the new portal section uses
`MAVEN_CENTRAL_USERNAME`/`MAVEN_CENTRAL_PASSWORD`. The secrets table in
`ci-github-actions.md` only lists the new-portal names, creating confusion.

# Acceptance Criteria

1. Old OSSRH section in `jvm.md` clearly labelled as **legacy only** (for projects
   registered before mid-2024 that cannot migrate).
2. New portal section is the primary, recommended path.
3. A brief note at the top of the publishing section tells agents: "Use the New
   Portal section unless the project was registered on OSSRH before mid-2024."
4. `ci-github-actions.md` secrets table consistent with new-portal secret names
   (already correct; verify no stale `OSSRH_*` references in the primary workflow
   examples).

# Journal

- 2026-04-30: Created during ticket:audit-instructions audit. Dual publishing
  paths without clear routing caused by Sonatype's mid-2024 portal migration.
