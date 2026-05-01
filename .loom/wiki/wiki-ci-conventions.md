---
id: wiki:ci-conventions
kind: wiki
page_type: reference
status: accepted
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
scope:
  kind: workspace
sources:
  - instructions/ci-github-actions.md
  - instructions/changelog-automation.md
links:
  wiki:
    - wiki:mlibrary-agentic-scaffold-overview
    - wiki:testing-and-coverage
    - wiki:ruby-release-workflow
    # - wiki:jvm-release-workflow  # stale, JVM coverage withdrawn (decision:0004)
---

# CI Conventions

## Platform

GitHub Actions only. No other CI systems in use.

## Security: SHA Pinning

All third-party actions must be pinned to a full commit SHA, not a floating tag:

```yaml
# Correct
- uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2

# Wrong — do not use
- uses: actions/checkout@v4
```

Floating tags are a supply-chain attack vector. Dependabot (`dependabot.yml` with
`package-ecosystem: github-actions`) keeps SHAs current automatically.

## Standard Workflow Files

Every project has two workflow files:

| File | Trigger | Purpose |
|---|---|---|
| `ci.yml` | Push to any branch, PRs | Run tests, lint, coverage |
| `release.yml` | Push of `v*` tag | Build and publish gem/artifact |

## `ci.yml` Skeleton

```yaml
name: CI

on:
  push:
    branches: ["**"]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@<SHA>
      # language-specific setup...
      - name: Run tests
        run: # bundle exec rspec OR ./gradlew test
      - name: Lint
        run: # bundle exec standardrb OR ./gradlew ktlintCheck detekt
```

## Changelog Automation with git-cliff

`git-cliff` generates `CHANGELOG.md` entries from conventional commits.

Pinned action:
```yaml
- uses: orhun/git-cliff-action@f50e11560dce63f7c33227798f90b924471a88b5 # v4.8.0
  with:
    config: cliff.toml
    args: --verbose
```

Commit convention: `type(scope): description` where type is one of:
`feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `perf`, `ci`.

## Dependabot Configuration

Every project must have `.github/dependabot.yml`:

```yaml
version: 2
updates:
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"

  - package-ecosystem: "bundler"   # Ruby; adjust for other languages
    directory: "/"
    schedule:
      interval: "weekly"
```

## Secrets Needed for Release Workflows

**Ruby**: `RUBYGEMS_API_KEY`, `GPG_PRIVATE_KEY`, `GPG_PASSPHRASE`

## Agent Rules

- Never use floating action tags (`@v4`). Always pin to SHA.
- Never modify `.github/workflows/` without explaining the change to the owner.
- Dependabot will open PRs to update action SHAs — review and merge them promptly.
- The `release.yml` workflow is the authoritative publish path; the manual publish
  steps in the release skills are emergency-only fallbacks.

# Change Notes

- 2026-04-30: Initial page created during Loom workspace bootstrap.
- 2026-05-01: Removed JVM secrets (Maven Central, OSSRH) and Gradle Dependabot
  entry (decision:0004, JVM coverage withdrawn). Dependabot ecosystem note
  updated to Ruby-primary.
