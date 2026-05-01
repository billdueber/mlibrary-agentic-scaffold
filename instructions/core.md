# Core Library Development Instructions

These instructions apply to all library projects regardless of language.

---

## Project Identity

At the top of every `AGENTS.md`, always read and state:
- What this library does (one sentence)
- Who its intended consumers are
- The current version and semver policy

---

## Directory Layout (Non-Source Files)

Standard top-level layout for all library projects:

```
project-root/
├── AGENTS.md                  # Agent instructions (this file or references to it)
├── CONTEXT.md                 # Accumulated project knowledge (read at session start)
├── CHANGELOG.md               # Kept up to date; follows Keep a Changelog format
├── README.md                  # User-facing; installation, quickstart, API overview
├── CONTRIBUTING.md            # Contributor guide
├── LICENSE
│
├── docs/
│   ├── adrs/                  # Architecture Decision Records
│   │   ├── ADR-000-template.md
│   │   └── ADR-001-*.md
│   └── guides/                # Optional: longer narrative docs
│
├── docker/                    # Docker Compose files for integration tests
│   ├── docker-compose.yml     # All services used in integration tests
│   └── README.md              # How to start/stop services
│
└── .github/
    └── workflows/             # CI pipelines
```

Language-specific source directories (`lib/`, `src/`, `spec/`, `test/`) follow language
conventions and are described in the language-specific instruction files.

---

## CONTEXT.md Convention

Every project should maintain a `CONTEXT.md` at the root. It is the agent's memory about
the project between sessions.

**At the start of every agent session**, the agent should:
1. Read `CONTEXT.md` if it exists.
2. Treat its content as authoritative project context.

**At the end of every significant session**, the agent should **proactively** update
`CONTEXT.md` without waiting to be asked. Specifically, update it when:
- A design decision was made or confirmed
- A new gotcha or sharp edge was discovered
- An API was added, changed, or removed
- Work-in-progress status changed
- The agent learned something that would save time in the next session

Add a brief entry; do not rewrite existing accurate content. Commit the update alongside
the last code change of the session, or as a standalone `chore(context): update project notes` commit.

**If the project also uses `@modelcontextprotocol/server-memory` (Tier 2 MCP memory):**
Populate both `CONTEXT.md` and the MCP memory store after significant sessions. The MCP
memory server enables cross-session retrieval by entity name; `CONTEXT.md` remains the
authoritative human-readable baseline. See `agentic-memory.md` for setup and workflow.

`CONTEXT.md` template:

```markdown
# Project Context

## What This Library Does
[One paragraph]

## Key Design Decisions
- [Decision + why]

## Architecture Notes
- [Things a new contributor / agent should know]

## Known Gotchas
- [Sharp edges, non-obvious behaviors]

## Current Work in Progress
- [Ongoing work, incomplete features]

## External Dependencies Worth Knowing
- [Non-obvious runtime deps, version constraints, etc.]
```

---

## Versioning

All projects use [Semantic Versioning](https://semver.org) (semver):

- `MAJOR`: breaking API change
- `MINOR`: backward-compatible new capability
- `PATCH`: backward-compatible bug fix

Pre-release: `1.0.0.alpha.1`, `1.0.0.beta.2`, `1.0.0.rc.1` (Ruby convention) or
`1.0.0-alpha.1`, `1.0.0-beta.2`.

**Agent rule**: Never bump MAJOR without explicit human confirmation. When in doubt,
ask before changing the major version.

---

## Changelog

Projects use `git-cliff` with Conventional Commits to generate `CHANGELOG.md`.
See `instructions/changelog-automation.md` for the full tradeoff analysis and setup.

**Summary**:
- Commit format: `type[(scope)]: description` (e.g., `feat(search): add phrase queries`)
- `fix:` → PATCH, `feat:` → MINOR, `feat!:` or `BREAKING CHANGE:` → MAJOR
- `git-cliff --latest -o CHANGELOG.md` regenerates the changelog before each release
- `test:`, `ci:`, `style:` commits are hidden from the changelog

**Agent rule**: Always write commits in Conventional Commits format. Prefer clarity in
the description: `feat(parser): support unicode identifiers` beats `feat: update parser`.

---

## Git Conventions

- Main branch: `main`
- Branch naming: `feature/short-description`, `fix/short-description`, `chore/short-description`
- Commit messages: Conventional Commits format — `type[(scope)]: description`
- Do not commit secrets, credentials, or `.env` files
- Version bump commit: `chore(release): bump version to x.y.z`
- Commits and release tags are GPG-signed

**Agent rule**: At natural transition points — when the user asks "what's next",
"what's on the agenda", or signals they are wrapping up — run `git status` and
report any uncommitted changes as a short list. Do not stage or commit
autonomously. The reminder is enough.

---

## GPG Commit Signing

Sign commits and release tags with a GPG key. Setup is a one-time human task — see
[GitHub's GPG signing guide](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key).
Store the private key and passphrase as `GPG_PRIVATE_KEY` and `GPG_PASSPHRASE` in
GitHub repository secrets for use in release workflows.

**Agent rule**: Do not manage GPG keys. If signing fails in a new environment, report
the error rather than silently creating unsigned commits.

---

## File Organization: What Goes Where

| File type | Location |
|-----------|----------|
| Architecture decisions | `docs/adrs/` |
| Docker/service configs for tests | `docker/` |
| CI pipeline definitions | `.github/workflows/` |
| Project-level notes (not user docs) | `CONTEXT.md` |
| User-facing guides beyond README | `docs/guides/` |
| Scripts for development tasks | `bin/` or `script/` |

---

## When to Create an ADR

Create an ADR (Architecture Decision Record) when:
- Choosing between two or more significant technical approaches
- Making a decision that will be hard to reverse
- Making a choice that future maintainers will likely question
- Deciding on a dependency that affects the public API or runtime behavior

See `instructions/adrs.md` and use the `adr-create` skill.

---

## Security

- Never log sensitive data (passwords, tokens, PII)
- Never hardcode credentials
- Validate all inputs at library entry points
- Document security considerations in the public API
- Pin CI action versions to full SHA hashes (not floating tags like `@v3`)

---

## What Agents Should NOT Do Without Explicit Approval

- Bump major version
- Publish to RubyGems.org or GitHub Packages
- Push to `main`
- Delete branches
- Modify `.github/workflows/` without explaining the change
- Add new runtime dependencies without proposing alternatives
