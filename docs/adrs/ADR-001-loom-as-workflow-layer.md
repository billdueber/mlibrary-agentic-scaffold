# ADR-001: Loom as the Workflow Governance Layer

## Status

Accepted

## Date

2026-04-30

## Context

This repository is a scaffold used by an AI agent to build and maintain Ruby and
JVM libraries. Work spans multiple sessions and involves multiple classes of
decisions: scoping, implementation, review, explanation, and release. Without a
durable execution tracking system, session boundaries cause state loss and the
agent re-derives context from scratch each time.

Several alternatives were on the table for tracking work:

- **GitHub Issues** — visible, familiar, but not agent-native; requires network
  access and authentication; no protocol for packet-style bounded handoffs.
- **Ad-hoc Markdown files** — flexible but no enforced structure, no lifecycle,
  no layered truth model. Devolves into inconsistent state.
- **Chat-transcript continuity** — no persistence across sessions by definition.
- **Loom** — a Markdown-native, filesystem-resident protocol that provides
  layered truth ownership (constitution, tickets, wiki, evidence, critique),
  a bounded handoff model (Ralph packets), and is queryable with plain tools
  (`rg`, `find`, `git`). No external services required.

The owner is a solo developer. External issue trackers add overhead without
adding value for a single-person, AI-assisted workflow.

## Decision

We use [Loom](https://github.com/anomalyco/open-loom) as the durable workflow
governance layer for this workspace.

All non-trivial work is tracked as Loom tickets. Durable decisions go in
`.loom/constitution/decisions/`. Accepted understanding goes in `.loom/wiki/`.
Observed artifacts go in `.loom/evidence/`. Implementation iteration uses Ralph
packets. Cross-session context is maintained in `.loom/` and `CONTEXT.md`.

Ad-hoc todo files and chat-only plans are not acceptable substitutes for Loom
records when work is non-trivial.

## Consequences

### Positive

- Work state survives session boundaries and context window resets.
- A cold agent can orient without reading the full session transcript.
- Truth is layered: tickets own execution state, wiki owns explanation, critique
  owns review verdicts, constitution owns policy — no conflation.
- The workspace is queryable with `rg`, `find`, and `git` without special tooling.

### Negative

- Adds overhead for genuinely tiny tasks (though local-work exemption applies).
- Loom is a new protocol; agents unfamiliar with it must load the bootstrap skill
  before work, which consumes context budget.
- If Loom itself changes its protocol significantly, the workspace may need
  migration work.

### Neutral

- Every session begins with `loom-bootstrap` skill load unless the bootstrap
  doctrine is already present in context.
- The `.loom/` directory is the authoritative workspace; external mirrors (GitHub
  Issues, PR descriptions) are non-canonical.

## Alternatives Considered

**GitHub Issues**: Requires authentication and network. Not queryable locally.
No protocol for bounded handoffs or layered truth. Rejected.

**Ad-hoc Markdown files**: No enforced structure or lifecycle. Collapses into
inconsistent state across sessions. Rejected.

**No durable tracking**: Unacceptable — the agent loses too much state across
session boundaries to work reliably on multi-step work.

## Open Questions

None at adoption time.
