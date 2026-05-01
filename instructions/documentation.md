# Documentation Standards

These instructions apply to all library projects.

---

## Philosophy

Documentation is a first-class deliverable, not an afterthought. For a library,
**the documentation is the product interface**. Users cannot inspect your source;
they read your docs.

---

## README.md

Every library must have a `README.md` at the root. Minimum required sections:

1. **What it is** — one-sentence description
2. **Installation** — exact commands, with version pinning examples
3. **Quick Start** — a working code example that demonstrates the primary use case
4. **API Overview** — a brief tour of the public API (link to detailed docs if they exist)
5. **Configuration** — all available options, their types, defaults, and meaning
6. **Requirements** — runtime dependencies and minimum language versions
7. **Contributing** — link to `CONTRIBUTING.md`
8. **License**

Agents must keep README examples accurate. If you change an API, update all README
examples in the same commit.

---

## Inline Documentation

### Ruby

Use [YARD](https://yardoc.org/) for all public methods and classes.

Required tags for every public method:

```ruby
# Brief one-line description.
#
# Longer description if needed. Explain behavior, not implementation.
#
# @param name [Type] Description of the parameter
# @return [Type] Description of what is returned
# @raise [ErrorClass] When and why this is raised
# @example
#   result = my_method(42)
#   result # => "forty-two"
def my_method(value)
```

- Private methods: document only if the behavior is non-obvious.
- Do not document `attr_reader` / `attr_writer` unless the attribute has semantics
  beyond the obvious.

---

## CHANGELOG.md

Follow [Keep a Changelog](https://keepachangelog.com) format strictly:

```markdown
# Changelog

## [Unreleased]
### Added
- New feature X

## [1.2.0] - 2026-01-15
### Added
- ...
### Fixed
- ...

[Unreleased]: https://github.com/owner/repo/compare/v1.2.0...HEAD
[1.2.0]: https://github.com/owner/repo/compare/v1.1.0...v1.2.0
```

**Agent rule**: Add a CHANGELOG entry for every PR / change set before it is merged
or released. Never release without updating `CHANGELOG.md`.

---

## CONTRIBUTING.md

Minimum sections:

1. Development environment setup (exact commands)
2. How to run the test suite
3. How to run integration tests
4. Coding standards (or reference to linter config)
5. Branch and PR process
6. How to create a release (or note that only maintainers do this)

---

## docs/guides/

Use this directory for longer-form narrative documentation: tutorials, how-tos,
conceptual explanations. Format: Markdown. Naming: `slug-form.md`.

Keep guides in sync with the code. If you change behavior, update the relevant guide
in the same PR.

---

## What Agents Should Do

- When adding a new public method or class, always add inline docs.
- When fixing a bug that was user-visible, add a CHANGELOG entry under `[Unreleased] > Fixed`.
- When adding a new feature, update README, add a CHANGELOG entry, and add/update guides.
- Proactively note when documentation is missing or stale.
