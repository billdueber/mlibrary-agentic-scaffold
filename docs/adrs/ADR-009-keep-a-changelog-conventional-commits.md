# ADR-009: Keep a Changelog Format and Conventional Commits

## Status

Accepted

## Date

2026-04-30

## Context

Library projects need a changelog for consumers and a commit message convention
for automation. The choices here interact: Conventional Commits enables
machine-readable commit history, which git-cliff (and similar tools) can use
to generate Keep a Changelog-formatted output.

Options considered:

**Changelog format:**
- **Keep a Changelog** (`keepachangelog.com`) — human-readable; sections
  (Added, Changed, Deprecated, Removed, Fixed, Security); widely adopted;
  easy for agents to read and update.
- **GitHub releases auto-generated notes** — convenient but shallow; no control
  over grouping or prose quality.
- **Custom format** — maximum flexibility; zero tooling support; inconsistent
  across projects.

**Commit convention:**
- **Conventional Commits** — machine-readable (`feat:`, `fix:`, `chore:`, etc.);
  widely supported by release automation tools; compatible with Semantic Versioning
  bump rules.
- **No convention** — maximum flexibility; no automation possible; inconsistent
  history.
- **Gitmoji** — emoji-based; visually distinctive but noisier for tooling.

**Automation:**
- **git-cliff** — Rust-based, fast, configurable; generates Keep a Changelog
  output from Conventional Commits history; supports custom templates.
- **standard-version / release-please** — more opinionated; heavier; release-please
  requires GitHub Actions integration.
- **No automation** — manual changelog; error-prone and tedious.

## Decision

Library projects governed by this scaffold use:
- **Keep a Changelog format** for `CHANGELOG.md`
- **Conventional Commits** for commit messages
- **git-cliff** for changelog generation from commit history

The `changelog-automation.md` instruction file contains commit format rules and
agent rules. Projects configure git-cliff themselves (`cliff.toml` in project
root); the scaffold does not prescribe a specific config.

## Consequences

### Positive

- Keep a Changelog is easy for agents and humans to read and update.
- Conventional Commits enable automated version bump decisions (major/minor/patch)
  and changelog generation.
- git-cliff is fast, Rust-based (no runtime to install), and configurable via
  `cliff.toml`.
- The combination is widely adopted enough that agents have strong training
  signal on it.

### Negative

- Commit messages require discipline — a stray commit that does not follow
  Conventional Commits format degrades automation quality.
- git-cliff must be installed in CI; adds a dependency.
- The Conventional Commits `BREAKING CHANGE` footer convention requires agents
  to reason about public API impact before committing.

### Neutral

- Semantic Release is **not** adopted (see constitution: deferred, manual release
  workflow sufficient for now). git-cliff generates the changelog; releasing is
  still a manual step using the `ruby-gem-release` skill.
- The `CHANGELOG.md` `[Unreleased]` section is the working buffer for changes
  not yet released.

## Alternatives Considered

**GitHub auto-generated release notes**: No control over grouping or prose.
Insufficient for a public library changelog. Rejected as sole mechanism.

**No commit convention**: Prevents any meaningful automation. Rejected.

**standard-version / release-please**: More opinionated and heavier. release-please
requires GitHub Actions integration and opens PRs automatically, which conflicts
with the owner's preference for manual release control. Rejected.

**Gitmoji**: Visually distinctive but noisier for tooling parsing. Conventional
Commits has better tool support. Rejected.

## Open Questions

- Semantic Release (automated version bump + publish): deferred. Activate
  `ticket:semantic-release` if manual releases become a burden. See constitution
  open question #3.
