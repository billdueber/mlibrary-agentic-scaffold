---
name: ss
description: Self-sufficient execution mode. Complete the requested task autonomously without asking for input, unless a genuine owner-preference decision point is reached.
compatibility: opencode
---

## Behavior

- Execute the task fully and autonomously.
- Do not ask for input unless you reach a genuine decision point that requires owner preference.
- Record your findings and close the work.
- When in doubt about a non-critical choice, pick the most reasonable option, state what you chose and why, and continue.

## Loom Checkpoints (automatic in ss mode)

After completing the requested task, check whether any lightweight Loom checkpoints
are triggered (per the Loom Checkpoints table in AGENTS.md) and run them automatically
without asking. Report what was run in your closing summary.

- **`loom-retrospective`** — run if a non-trivial ticket or large task just completed
- **`loom-tickets`** — run if proposed tickets look stale or need triage
- **`loom-wiki`** — run if accepted understanding should persist as a page

Do **not** auto-run `loom-critique` in ss mode. If a risky or architectural change
landed, include a one-line note in your summary: "critique warranted — suggest running
loom-critique before accepting this work."

