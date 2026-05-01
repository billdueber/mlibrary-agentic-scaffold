# Agent Instructions

## Loom

This project uses [Loom](https://github.com/anomalyco/open-loom).
Load the `loom-bootstrap` skill before any work.

---

## What This Repository Is

`mlibrary-agentic-scaffold` is a personal, reusable agentic scaffold **with deep
coverage for Ruby gem authors** and solid general-purpose conventions (Loom, git,
changelog, CI, testing, security) usable with OpenCode + GitHub Copilot.
It is a collection of instruction files, skills, and templates that you copy or
reference from individual project `AGENTS.md` files. JVM (Kotlin/Java) coverage
is not currently available. Frontend and data-pipeline projects are out of scope.

**Owner**: dueberb  
**AI access**: GitHub Copilot via OpenCode only. Do not suggest workflows requiring
direct Anthropic or OpenAI API access.

---

## Repository Layout

`instructions/` — instruction files | `skills/` — skill workflows | `templates/` — drop-in AGENTS.md starters | `mcp/` — MCP config snippets | `.loom/` — Loom workspace. See `README.md` for full tree.

---

## Core Principles (from `constitution:main`)

Read the full constitution at `.loom/constitution/constitution.md` before making
architectural or policy choices. Key points:

- **Ruby-first**: Deep coverage for Ruby gems. General conventions apply to any project. JVM (Kotlin/Java) coverage is not currently available — see `decision:0004`. Frontend and data-pipeline projects are out of scope.
- **GitHub Actions only**: No other CI system is supported.
- **Convention over custom**: StandardRB, RSpec, Keep a Changelog.
  Deviating from convention requires a recorded decision (ADR or Loom decision record).
- **Skills over prose**: Repeatable workflows are encoded as skills, not prose paragraphs.
- **Honest uncertainty**: Open gaps go in Loom tickets (`.loom/tickets/`).
  Never silently resolve a policy gap by guessing.

---

## Working Here

Before non-trivial edits: check `.loom/tickets/` for existing work, create a ticket if none exists. Instruction changes flow downstream to all projects. Templates must stay in sync with instruction files. Style: write for an AI agent — explicit and imperative; use "Agent rule:" callouts; keep language-specific concerns in `ruby.md`, cross-language in `core.md`.

Small question or gap → Loom ticket in `.loom/tickets/` with date. Resolved policy question → Loom decision record in `.loom/constitution/decisions/`. Non-trivial new work → Loom ticket. ADRs for this scaffold live in `docs/adrs/` — use the `adr-create` skill.

---

## Available Skills

Load a skill when the work matches its description:

| Skill | When to use |
|-------|-------------|
| `adr-create` | Creating a new Architecture Decision Record |
| `worth-it` | Assess effort and value before acting; wait for go-ahead before making changes |
| `ss` | Self-sufficient execution mode: complete a task autonomously |
| `loom-*` | Any Loom workflow (bootstrap, tickets, wiki, critique, Ralph, etc.) |

Skills `ruby-gem-release`, `integration-test`, `lint-fix`, and `docker-services` exist in this repo for downstream projects to use; they are not applicable when working on the scaffold itself.

---

## MCP

See `mcp/recommended-servers.md`. Project-level config is in `opencode.json` (checked in). Priority: `context-mode` > `github` > `sequential-thinking` > `memory` > `mem0`. Use `"environment"` (not `"env"`) for env vars; `"command"` must be an array.

---

## Cross-Session Memory

Tier 1: `CONTEXT.md` + Loom records (no infra). Tier 2: `@modelcontextprotocol/server-memory` (local `.jsonl`). Tier 3: mem0 (cloud, semantic). See `instructions/agentic-memory.md`. Loom's `.loom/memory/` is in-session scratch only.

---

## What Agents Must NOT Do Without Explicit Approval

- Bump the major version of any library or this scaffold.
- Publish to RubyGems.org or GitHub Packages.
- Push to `main`.
- Delete branches.
- Modify `.github/workflows/` without explaining the change.
- Add new runtime dependencies without proposing alternatives.
- Resolve a constitutional open question unilaterally — escalate with a proposal.
- Widen Loom ticket scope without updating the ticket.

---

## Critique Discipline

Two rules that must be followed whenever a `loom-critique` pass runs on this scaffold:

**Rule B — Validate emitted artifacts.**
When a critique finding is resolved by *replacing* a config snippet, JSON block, URL,
or other emitted artifact (rather than deleting it), the resolution is not complete
until the replacement is validated against its schema or tested against the real tool.
"I wrote a new value" is not evidence of correctness. Schema URL for OpenCode MCP configs:
`https://opencode.ai/config.json`. For instruction file snippets, verify the referenced
command or package still exists and is not archived/deprecated.

**Rule D — Residuals become tickets immediately.**
If a critique session ends with a "not yet done" note — a follow-up that couldn't be
resolved in the same session — it must land in a Loom ticket before the session closes,
not in prose notes. Prose notes disappear across context windows. Tickets persist.
Use `rg -l 'status: proposed' .loom/tickets/` to verify the ticket was created.

---


At natural breakpoints (end of a multi-step task, finishing a session, closing a ticket),
suggest the applicable skill to the user. In `ss` mode, run lightweight ones automatically.

| Trigger | Skill | Weight |
|---------|-------|--------|
| Non-trivial ticket or large task just completed | `loom-retrospective` | lightweight — auto in ss |
| Proposed tickets are stale or need triage | `loom-tickets` | lightweight — auto in ss |
| Accepted understanding should persist as a page | `loom-wiki` | lightweight — auto in ss |
| Risky or architectural change landed | `loom-critique` | heavy — suggest only, never auto |

---

## Loom Quick Reference

Active tickets: `rg -l 'status: active' .loom/tickets/` | Proposed: `rg -l 'status: proposed' .loom/tickets/`  
Constitution: `.loom/constitution/constitution.md` | Overview: `.loom/wiki/wiki-library-agent-overview.md` | New project setup: `.loom/wiki/wiki-new-project-setup.md`
