---
id: ticket:20260501-5eppe9bj-preserve-findings-skill
kind: ticket
status: active
created_at: 2026-05-01T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
title: "Add preserve-findings skill and supporting instruction rule"
priority: medium
links: {}
---

## Why

When working on a spike or feature branch, valuable Loom records (research,
decisions, evidence) may be created that should survive even if the branch is
abandoned. There was no mechanism or guidance for getting that knowledge back
to `main` before dropping the branch.

This ticket tracks the implementation of the `preserve-findings` skill and the
agent rule in `instructions/core.md` that points to it.

## Scope

- `skills/preserve-findings/SKILL.md` — interactive triage skill
- `instructions/core.md` — agent rule under Git Conventions
- This ticket

## Out of Scope

- Automating triage decisions (human judgment required)
- Handling the case where main is unprotected (skill always uses a chore branch)

## Acceptance

- `skills/preserve-findings/SKILL.md` exists and covers all five dispositions
  (preserve, promote, drop, reclassify-now, reclassify-later)
- `instructions/core.md` has an agent rule pointing to the skill
- The skill is listed in `AGENTS.md` skills table
- A PR or chore commit lands both files on `main`

## Execution Notes

- Branch: `preserve-findings`
- Skill and instruction rule written 2026-05-01
- Remaining: add skill to AGENTS.md skills table, then PR or preserve-findings
  pass to land on main
