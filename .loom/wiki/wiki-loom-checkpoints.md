---
id: wiki:loom-checkpoints
kind: wiki
page_type: concept
status: accepted
created_at: 2026-05-01T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
scope:
  kind: workspace
links:
  constitution:
    - constitution:main
  wiki:
    - wiki:loom-workspace-orientation
    - wiki:mlibrary-agentic-scaffold-overview
---

# Loom Checkpoints

## Summary

At natural breakpoints in a work session, agents should run lightweight Loom
maintenance passes to keep the graph truthful and compact. The checkpoint policy
defines which passes are automatic in `ss` mode and which are always
owner-preference decisions.

## Why Checkpoints Exist

Loom records accumulate debt in flight: tickets stay `active` after work lands,
wiki pages fall behind accepted truth, retrospective promotions get skipped,
and critique suggestions vanish into chat. Checkpoints are the named intervention
that prevents this drift.

Without deliberate checkpoints, Loom's value degrades session-over-session —
the same questions get re-derived, tickets close on vibes, and wiki pages rot.

## Trigger Table

| Trigger | Skill | Weight in ss mode |
|---------|-------|-------------------|
| Non-trivial ticket or large task just completed | `loom-retrospective` | lightweight — auto |
| Proposed tickets are stale or need triage | `loom-tickets` | lightweight — auto |
| Accepted understanding should persist as a page | `loom-wiki` | lightweight — auto |
| Risky or architectural change landed | `loom-critique` | heavy — suggest only, never auto |

## What "Lightweight" Means

A lightweight checkpoint is a focused, bounded pass that:

- reads only the records directly in scope
- makes targeted updates (status transitions, new wiki page, wiki update, ticket
  closure, retrospective promotion)
- does not open new scope or start new research
- takes minutes, not a full session

Heavy passes (critique) involve adversarial review and require owner judgment
on findings. They should be suggested but never run autonomously.

## Behavior in `ss` Mode

In `ss` (self-sufficient) mode the agent:

1. Runs `loom-retrospective` when a non-trivial ticket or task closes.
2. Triages proposed tickets (`loom-tickets`) when the work slice is complete.
3. Promotes accepted understanding to wiki (`loom-wiki`) when the session
   produced concepts or workflows worth preserving.
4. Includes a one-line critique suggestion in the session summary when a risky
   or architectural change landed — but does **not** run critique autonomously.

The agent does not need to ask the owner before running a lightweight checkpoint
in `ss` mode. Critique always requires explicit owner instruction.

## Behavior in Interactive Mode

In normal interactive mode the agent suggests the applicable checkpoint skill
at natural breakpoints. The owner decides whether to run it.

Example at end of a multi-step task:

> "The task is complete. Consider running `loom-retrospective` to promote
> accepted understanding into wiki and close the ticket cleanly."

## What Each Checkpoint Does

### loom-retrospective

Routes durable lessons from a closing ticket or initiative into the owner layers
that should maintain them:

- accepted explanations → wiki
- investigation results, rejected options → research
- clarified behavior → spec
- changed sequencing → plan
- changed principles or decisions → constitution
- observed artifacts → evidence

### loom-tickets

Triage and update live execution truth:

- close tickets whose work is complete and accepted
- cancel tickets whose rationale is gone
- update blockers or next-move notes
- transition stale `proposed` tickets to `cancelled` or `ready`

### loom-wiki

Create or update pages when accepted understanding should persist:

- new concept, workflow, or reference pages
- updates to existing pages when accepted owner truth changed
- staleness markers when a page no longer matches reality

### loom-critique (suggest only)

Adversarial review of code changes, behavior changes, or Loom records:

- surfaces mismatches, hidden assumptions, weak evidence
- assigns severity and follow-up requirements
- blocks ticket closure when mandatory critique is outstanding

Never run autonomously. Always an owner-preference decision.

## Sources

- `AGENTS.md` — Loom Checkpoints section
- `skills/ss/SKILL.md` — ss mode checkpoint behavior
- Loom bootstrap doctrine — critique and wiki as first-class layers

## Related Pages

- `wiki:loom-workspace-orientation` — how to orient in the workspace
- `wiki:mlibrary-agentic-scaffold-overview` — system overview

## Change Notes

- 2026-05-01: Initial page created. Captures checkpoint policy added to AGENTS.md
  and skills/ss/SKILL.md during 2026-05-01 session retrospective.
