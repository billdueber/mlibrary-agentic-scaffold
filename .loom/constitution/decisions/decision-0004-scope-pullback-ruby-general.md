---
id: decision:0004
kind: decision
status: accepted
created_at: 2026-05-01T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
scope:
  kind: workspace
links:
  supersedes: [adr:ADR-008]
---

# decision:0004 — Scope Pullback: Ruby + General-Purpose

## Decision

The scaffold's scope is narrowed from "Ruby + JVM (Kotlin/Java)" to
**"Ruby + general-purpose"**.

- Ruby gem coverage remains deep and is the primary audience.
- General-purpose conventions (Loom, git, changelog, CI, testing philosophy,
  agentic memory, security posture) apply to any project regardless of language.
- JVM (Kotlin/Java) is withdrawn as a first-class concern. The existing
  `instructions/jvm.md` file was half-baked and potentially misleading.
  It has been removed.

## Rationale

The JVM content was never at the same quality bar as the Ruby content. Claiming
equal first-class coverage for both was a lie the scaffold was telling. Half-baked
instructions are potentially worse than no instructions: an agent will follow them
with false confidence.

Pulling back to Ruby + general is more honest, produces a coherent product, and
leaves room for proper JVM (or any other platform) coverage to be added as a
deliberate future expansion rather than an unmet promise.

## What Was Removed

- `instructions/jvm.md` — deleted
- `templates/AGENTS-jvm.md` — deleted
- 6 JVM split tickets (all `proposed`) — deleted
- `adr:ADR-008` (Gradle Kotlin DSL) — marked Superseded

## What Was Retained

- Java 21 LTS minimum and no-shims constraint remain in the constitution as
  guidance that activates if JVM integration services are used (e.g., for
  integration testing data stores) or if JVM coverage is added in the future.
- ADR-008 reasoning is preserved; Gradle Kotlin DSL remains the preferred choice
  if JVM support is reinstated.

## Future Path

If JVM coverage is added, it should start from the existing Loom ticket structure
(recreate tickets, write kotlin.md and java.md from scratch, add templates) and
the ADR-008 decision should be reinstated. Do not resurrect `jvm.md` from git
history without a full quality review.
