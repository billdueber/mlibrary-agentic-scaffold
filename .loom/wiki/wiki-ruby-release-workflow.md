---
id: wiki:ruby-release-workflow
kind: wiki
page_type: workflow
status: accepted
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-04-30T00:00:00Z
scope:
  kind: workspace
sources:
  - instructions/ruby.md
  - instructions/changelog-automation.md
  - instructions/ci-github-actions.md
  - skills/ruby-gem-release/SKILL.md
links:
  wiki:
    - wiki:mlibrary-agentic-scaffold-overview
    - wiki:new-project-setup
---

# Ruby Gem Release Workflow

## Overview

Ruby gem releases in this workspace are CI-driven: you bump the version, update
the changelog, commit, tag, and push. The GitHub Actions `release.yml` workflow
handles building the `.gem` file and pushing to RubyGems.org.

The `ruby-gem-release` skill automates the interactive checklist portion of this
workflow.

## Prerequisites

- All tests passing: `bundle exec rspec`
- Lint clean: `bundle exec standardrb`
- Working tree clean: `git status`
- On `main`, up to date with remote
- RubyGems MFA enabled on your account; gem's `.gemspec` has
  `spec.metadata["rubygems_mfa_required"] = "true"`

## Step-by-Step

### 1. Determine the release type

| Release type | When to use |
|---|---|
| **patch** (x.y.Z) | Bug fixes only; no new API surface |
| **minor** (x.Y.0) | New backward-compatible features |
| **major** (X.0.0) | Breaking changes — requires explicit owner confirmation |

### 2. Update version

Edit `lib/<gem_name>/version.rb`:

```ruby
module GemName
  VERSION = "1.2.3"
end
```

### 3. Update CHANGELOG.md

Rename the `[Unreleased]` section:

```markdown
## [1.2.3] - 2026-04-30

### Added
- ...
```

Add a new empty `[Unreleased]` section at the top. Update the comparison links
at the bottom.

**Automated option**: If `cliff.toml` is present, generate the entry with git-cliff:

```bash
git cliff --tag v1.2.3 --unreleased --prepend CHANGELOG.md
```

See `instructions/changelog-automation.md` for full git-cliff setup.

### 4. Commit, tag, push

```bash
git add lib/*/version.rb CHANGELOG.md
git commit -m "Release v1.2.3"
git tag v1.2.3
git push && git push --tags
```

Pushing the tag triggers the CI `release.yml` workflow.

### 5. Verify

After CI completes (~2 min):
- Check `https://rubygems.org/gems/<gem-name>` for the new version
- Create a GitHub Release from the tag if your `release.yml` doesn't do it automatically

## CI Release Workflow Shape

A typical `release.yml` for a Ruby gem:

```yaml
on:
  push:
    tags: ["v*"]

jobs:
  release:
    runs-on: ubuntu-latest
    permissions:
      contents: write    # for creating GitHub Release
    steps:
      - uses: actions/checkout@<SHA>
      - uses: ruby/setup-ruby@<SHA>
        with:
          ruby-version: "3.3"
          bundler-cache: true
      - name: Build gem
        run: gem build *.gemspec
      - name: Push to RubyGems
        run: gem push *.gem
        env:
          GEM_HOST_API_KEY: ${{ secrets.RUBYGEMS_API_KEY }}
```

Store the `RUBYGEMS_API_KEY` as a repository secret.

## Emergency Manual Publish

Use only if CI is broken:

```bash
gem build *.gemspec
gem push *.gem   # requires `gem signin` or GEM_HOST_API_KEY env var
```

## Agent Rules

- Never publish without a clean test suite.
- Never publish with uncommitted changes.
- Never bump major version without explicit owner confirmation.
- Use the `ruby-gem-release` skill for interactive guidance.
