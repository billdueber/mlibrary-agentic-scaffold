# CI and GitHub Actions

These instructions apply to all library projects hosted on GitHub.

---

## Workflow File Location

All CI workflows live in `.github/workflows/`. Standard files:

| File | Purpose |
|------|---------|
| `ci.yml` | Main CI: lint + unit tests on every push/PR |
| `integration.yml` | Integration tests (may run on PR + merge to main) |
| `release.yml` | Publishes to RubyGems / GitHub Packages |

---

## Security: Pin Action Versions

**Always** pin GitHub Actions to a full commit SHA, not a floating tag:

```yaml
# Bad - floating tag can be hijacked
- uses: actions/checkout@v4

# Good - pinned to exact SHA
- uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
```

Use [Dependabot](https://docs.github.com/en/code-security/dependabot) or
[Renovate](https://github.com/renovatebot/renovate) to keep pinned SHAs up to date.

---

## Standard CI Workflow (Ruby)

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        ruby-version: ["3.2", "3.3", "3.4"]

    # Add a 'services:' block here for any Docker services your integration
    # tests require (postgres, redis, opensearch, etc.). See the example in
    # instructions/docker-integration.md. Omit this section if your project
    # has no external service dependencies.

    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2

      - uses: ruby/setup-ruby@32110d4e311bd8996b2e82c14159b0b4a7f5b37c # v1.230.0
        with:
          ruby-version: ${{ matrix.ruby-version }}
          bundler-cache: true

      - name: Lint
        run: bundle exec standardrb --no-fix

      - name: Unit tests
        run: bundle exec rspec --exclude-pattern "spec/integration/**/*_spec.rb"

      - name: Integration tests
        run: bundle exec rspec spec/integration
        # Add env vars for service connection strings if services are configured above:
        # env:
        #   TEST_POSTGRES_URL: postgresql://test_user:test_password@localhost:5432/test_db
```

---

## Release Workflow

Release workflows should:
1. Be triggered manually (`workflow_dispatch`) or by pushing a version tag (`v*`)
2. Re-run the full test suite before publishing
3. Require the previous step to pass before publishing
4. Never auto-release on push to main without explicit version tagging

```yaml
# .github/workflows/release.yml
name: Release

on:
  push:
    tags: ["v*"]

jobs:
  release:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      packages: write

    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2

      # ... setup steps ...

      - name: Run full test suite
        run: # (same as ci.yml test steps)

      - name: Publish
        run: gem push *.gem
        env:
          GEM_HOST_API_KEY: ${{ secrets.RUBYGEMS_API_KEY }}
```

---

## Secrets Management

Never commit secrets. Store them in GitHub repository secrets (`Settings > Secrets`).
Standard secret names used in these workflows:

| Secret | Used by |
|--------|---------|
| `RUBYGEMS_API_KEY` | Ruby gem publishing |
| `GPG_PRIVATE_KEY` | GPG commit/tag signing |
| `GPG_PASSPHRASE` | GPG commit/tag signing |
| `GITHUB_TOKEN` | GitHub Packages (auto-provided by Actions) |

---

## Branch Protection

Configure these on `main`:
- Require PR before merging
- Require CI to pass
- Require at least 1 review (even if you're solo — it forces discipline)
- Do not allow force pushes

---

## Dependabot

Add `.github/dependabot.yml` to keep dependencies and action SHAs current:

```yaml
version: 2
updates:
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"

  - package-ecosystem: "bundler"   # Ruby
    directory: "/"
    schedule:
      interval: "weekly"
```
