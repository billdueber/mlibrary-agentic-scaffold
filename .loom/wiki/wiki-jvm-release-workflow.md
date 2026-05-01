---
id: wiki:jvm-release-workflow
kind: wiki
page_type: workflow
status: superseded
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
scope:
  kind: workspace
sources:
  - instructions/jvm.md
  - instructions/changelog-automation.md
  - instructions/ci-github-actions.md
  - skills/jvm-release/SKILL.md
links:
  wiki:
    - wiki:mlibrary-agentic-scaffold-overview
    - wiki:new-project-setup
---

> **Stale (2026-05-01)**: JVM coverage withdrawn from scaffold scope. See `decision:0004`. This page is preserved for reference only.

# JVM Library Release Workflow

## Overview

JVM library releases are CI-driven: bump the version, update the changelog, commit,
tag, and push. The GitHub Actions `release.yml` workflow handles signing and publishing
to Maven Central or GitHub Packages.

The `jvm-release` skill automates the interactive checklist portion of this workflow.

## Determine Target Repository

| Target | When | Portal |
|---|---|---|
| **Maven Central (new portal)** | New projects (registered after mid-2024) | `central.sonatype.com` via `com.gradleup.nmcp` |
| **Maven Central (legacy OSSRH)** | Existing pre-mid-2024 projects only | `s01.oss.sonatype.org` — manual staging close |
| **GitHub Packages** | Internal/preview releases | `GITHUB_TOKEN` |

For new projects, **always use the new portal**. Legacy OSSRH is documented in
`jvm.md` but should not be used for new project registrations.

## Prerequisites

- All tests pass: `./gradlew test integrationTest` (or `mvn verify`)
- Lint clean: `./gradlew ktlintCheck detekt` (or `mvn checkstyle:check`)
- Working tree clean: `git status`
- On `main`, up to date with remote
- Version does NOT end in `-SNAPSHOT`
- Required secrets set in GitHub repository settings (see below)

## Step-by-Step

### 1. Determine release type

| Type | When |
|---|---|
| **patch** | Bug fixes; no API changes |
| **minor** | New backward-compatible features |
| **major** | Breaking changes — requires explicit owner confirmation |

### 2. Update version

**Gradle** — edit `build.gradle.kts`:

```kotlin
version = "1.2.3"
```

**Maven** — edit `pom.xml`:

```xml
<version>1.2.3</version>
```

### 3. Update CHANGELOG.md

Same pattern as Ruby: rename `[Unreleased]` → `[1.2.3] - 2026-04-30`, add new empty
`[Unreleased]` at top. See `instructions/changelog-automation.md` for git-cliff.

### 4. Commit, tag, push

```bash
git add build.gradle.kts CHANGELOG.md   # or pom.xml
git commit -m "Release v1.2.3"
git tag v1.2.3
git push && git push --tags
```

### 5. Monitor publish

**New portal**: the `publishToMavenCentral` task (via `com.gradleup.nmcp`) auto-releases
if `publishingType = "automatic"`. Monitor at `https://central.sonatype.com/publishing/deployments`.

**Legacy OSSRH**: after `publishToOSSRH`:
1. Log in to `https://s01.oss.sonatype.org`
2. Go to Staging Repositories
3. Find your staging repo → Close → Release

Sync to Maven Central search takes 10–30 minutes.

## Required Secrets

| Secret | Portal | How to get |
|---|---|---|
| `MAVEN_CENTRAL_USERNAME` | New portal | Sonatype account → User Token |
| `MAVEN_CENTRAL_PASSWORD` | New portal | Sonatype account → User Token |
| `OSSRH_USERNAME` | Legacy OSSRH | Sonatype JIRA username |
| `OSSRH_PASSWORD` | Legacy OSSRH | Sonatype JIRA password |
| `GPG_PRIVATE_KEY` | Both | `gpg --armor --export-secret-keys KEY_ID` |
| `GPG_PASSPHRASE` | Both | GPG key passphrase |

Signing is always required for Maven Central publications (both portals). See
`instructions/core.md` for GPG key setup.

## CI Release Workflow Shape (Gradle + New Portal)

```yaml
on:
  push:
    tags: ["v*"]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@<SHA>
      - uses: actions/setup-java@<SHA>
        with:
          java-version: "21"
          distribution: "temurin"
      - name: Publish to Maven Central
        run: ./gradlew publishToMavenCentral
        env:
          MAVEN_CENTRAL_USERNAME: ${{ secrets.MAVEN_CENTRAL_USERNAME }}
          MAVEN_CENTRAL_PASSWORD: ${{ secrets.MAVEN_CENTRAL_PASSWORD }}
          GPG_PRIVATE_KEY: ${{ secrets.GPG_PRIVATE_KEY }}
          GPG_PASSPHRASE: ${{ secrets.GPG_PASSPHRASE }}
```

## Agent Rules

- Never publish a `-SNAPSHOT` version to Maven Central.
- Never publish without a full passing test suite.
- Never bump major version without explicit owner confirmation.
- Artifacts published to Maven Central cannot be deleted — verify the version before releasing.
- Use the `jvm-release` skill for interactive guidance.


# Supersession Note

Superseded 2026-05-01. JVM coverage removed from the scaffold in decision:0004
(scope pullback to Ruby + general). `instructions/jvm.md` and the `jvm-release`
skill were deleted. This page is retained for historical reference only.
