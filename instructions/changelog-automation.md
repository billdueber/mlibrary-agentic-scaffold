# Changelog Automation

Commit format and changelog conventions. For the decision rationale, see
`docs/adrs/ADR-009-keep-a-changelog-conventional-commits.md`.

---

## Commit Message Format

```
type[(scope)][!]: short description

[optional body]

[optional footer: BREAKING CHANGE: ...]
```

Key types:
- `fix:` → PATCH bump
- `feat:` → MINOR bump
- `feat!:` or `BREAKING CHANGE:` footer → MAJOR bump
- `chore:`, `docs:`, `refactor:`, `test:`, `ci:`, `style:` → no version bump; hidden from changelog

Examples:
```
feat(search): add phrase query support
fix: handle nil input in Parser#parse
feat!: remove deprecated v1 API
docs: update README quickstart example
chore(deps): bump rspec to 3.14
refactor(parser): extract token normalization
```

**Agent rule**: Always write commits in Conventional Commits format. Use `feat` and `fix`
for user-visible changes. Use `chore` for maintenance. Use `!` suffix for breaking changes —
never silently introduce a breaking change in a `fix:` commit.

---

## Changelog Format

Follow [Keep a Changelog](https://keepachangelog.com/) conventions:

- Sections: `Added`, `Changed`, `Deprecated`, `Removed`, `Fixed`, `Security`
- Keep an `[Unreleased]` section at the top for changes not yet tagged
- On release: rename `[Unreleased]` to the version + date, open a new `[Unreleased]` section

**Agent rule**: When adding a changelog entry, place it under `[Unreleased]` in the correct
section. Do not reformat existing entries. Do not delete the `[Unreleased]` header.

---

## Automation

Projects may use git-cliff or a similar tool to generate CHANGELOG.md from commit history.
Configuration (`cliff.toml`) and CI integration are the project's choice. The commit format
above is compatible with git-cliff's conventional-commits mode out of the box.
