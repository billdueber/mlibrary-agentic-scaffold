# ADR-008: Gradle Kotlin DSL for JVM Projects

## Status

Superseded — JVM is no longer a first-class concern of this scaffold (see `decision:0004`,
2026-05-01). If JVM support is added in the future, Gradle Kotlin DSL remains the
preferred choice and this ADR should be reinstated.

## Date

2026-04-30

## Context

JVM library projects governed by this scaffold need a build tool. The main
options for Java/Kotlin library builds in 2026 are:

- **Gradle with Kotlin DSL** (`build.gradle.kts`) — statically typed build
  scripts, IDE completion, better refactoring support, consistent with Kotlin
  as the primary JVM language.
- **Gradle with Groovy DSL** (`build.gradle`) — historically the default;
  dynamically typed; less clear tooling support as the Kotlin DSL has become
  the recommended approach.
- **Maven** — XML-based; verbose; no scripting flexibility; still common in
  enterprise Java but declining for new projects.
- **Bazel / Buck** — powerful for monorepos at scale; significant setup and
  learning cost; overkill for standalone library projects.

This scaffold targets library authors, not application developers. Library
projects are typically single-module or small multi-module builds, not large
monorepos.

The primary language is Kotlin. Using Kotlin DSL for Gradle means the build
language matches the project language — one language to reason about.

## Decision

JVM library projects use **Gradle with the Kotlin DSL** (`build.gradle.kts`).

New JVM projects must not use Groovy DSL `build.gradle` files. Maven is
acceptable only for projects with an existing Maven history or an explicit
requirement to publish Maven-compatible POMs that Gradle cannot satisfy (which
is rare — Gradle's `maven-publish` plugin handles standard Maven Central
publication correctly).

The minimum JVM target is Java 21 LTS. No JVM 8/11 compatibility shims.

## Consequences

### Positive

- Build scripts are statically typed; agents get better error messages when
  scripts are malformed.
- Consistent language across source and build: both use Kotlin.
- Gradle Kotlin DSL is now the recommended default in Gradle's own documentation.
- `maven-publish` plugin enables standard Maven Central publication without
  switching to Maven.

### Negative

- Kotlin DSL compile times are higher than Groovy DSL for large projects
  (not a concern at library scale).
- Agents or humans familiar only with Groovy DSL face a small learning curve.
- Some older Gradle plugins may have incomplete Kotlin DSL support (rare in 2026,
  but possible for niche plugins).

### Neutral

- Multi-module JaCoCo aggregation uses the `jacoco-report-aggregation` plugin
  with a dedicated `:build-tools` subproject. See `instructions/testing.md`.
- The `jvm.md` instruction file is the authoritative reference for Gradle
  conventions.

## Alternatives Considered

**Gradle Groovy DSL**: Dynamically typed; weaker IDE and agent tooling support
as the ecosystem has shifted toward Kotlin DSL. Rejected for new projects.

**Maven**: Verbose XML build files; limited scripting. Acceptable for existing
projects with Maven history, but not the default for new projects. Rejected as
default.

**Bazel / Buck**: Overkill for standalone library projects. Rejected.

## Open Questions

None at adoption time.
