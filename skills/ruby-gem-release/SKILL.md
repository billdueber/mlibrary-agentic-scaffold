---
name: ruby-gem-release
description: Guide through releasing a new version of a Ruby gem to RubyGems.org, including version bump, changelog update, and tag creation.
license: MIT
compatibility: opencode
metadata:
  audience: ruby-library-maintainers
  workflow: release
---

## What I Do

Walk through the complete release process for a Ruby gem, checking each step
before proceeding.

## Release Checklist

### 1. Pre-flight Checks

- [ ] All tests pass: `bundle exec rspec`
- [ ] Lint is clean: `bundle exec standardrb`
- [ ] Working tree is clean (`git status`)
- [ ] You are on `main` and it is up to date with remote

### 2. Determine New Version

Ask: "What kind of release is this?"
- **patch** — bug fixes only; no new features; no breaking changes
- **minor** — new backward-compatible features
- **major** — breaking changes (requires explicit confirmation)

Calculate new version from `lib/*/version.rb`.

### 3. Update Files

1. Update `VERSION` in `lib/*/version.rb`
2. Update `CHANGELOG.md`:
   - Rename `[Unreleased]` section to `[x.y.z] - YYYY-MM-DD`
   - Add new empty `[Unreleased]` section at top
   - Update the comparison links at the bottom

### 4. Commit and Tag

```bash
git add lib/*/version.rb CHANGELOG.md
git commit -m "Release v{version}"
git tag v{version}
```

### 5. Push

```bash
git push && git push --tags
```

This triggers the CI `release.yml` workflow which builds and pushes to RubyGems.

### 6. Verify

After CI completes:
- Check that the new version appears at `https://rubygems.org/gems/{gem_name}`
- Create a GitHub Release if your workflow doesn't do it automatically

## Manual Publish (Emergency Only)

```bash
gem build *.gemspec
gem push *.gem
# Requires: gem signin or GEM_HOST_API_KEY env var
```

## Rules

- Never publish without running the full test suite first.
- Never publish with uncommitted changes.
- Never bump major version without explicit human confirmation.
- The gem's `rubygems_mfa_required` metadata must be `"true"`.
