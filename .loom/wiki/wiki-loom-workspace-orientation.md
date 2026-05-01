---
id: wiki:loom-workspace-orientation
kind: wiki
page_type: workflow
status: accepted
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-04-30T00:00:00Z
scope:
  kind: workspace
links:
  constitution:
    - constitution:main
---

# Summary

How a fresh agent should orient in the `agentstuff` workspace when entering
cold — before starting any work.

# When To Use It

Every time an agent enters this workspace without prior session context.

# Inputs

- Access to the `agentstuff` workspace filesystem
- Loom bootstrap doctrine loaded (via `loom-bootstrap` skill or preloaded)

# Procedure

## Step 1: Confirm workspace root

```
/Users/dueberb/devel/ai/agentstuff
```

## Step 2: Read the constitution

```bash
cat .loom/constitution/constitution.md
```

This gives: project identity, principles, constraints, strategic direction,
open questions. Read it before making any non-trivial decision.

## Step 3: Check current initiative and plan state

```bash
cat .loom/initiatives/initiative-mlibrary-agentic-scaffold-stable.md
cat .loom/plans/plan-mlibrary-agentic-scaffold-stabilize.md
```

These tell you what the project is trying to accomplish and which wave of work
is current.

## Step 4: Find active tickets

```bash
rg -l 'status: active\|status: ready\|status: blocked' .loom/tickets/
```

Or for all tickets with their statuses:

```bash
rg -n '^status:' .loom/tickets/*.md
```

## Step 5: Identify the next move

Look at the first `ready` or `active` ticket not yet closed. That is the next
governed work item. The ticket's `# Next Move / Next Route` section names the
immediate next step.

## Step 6: Check for open questions

```bash
cat OPEN_QUESTIONS.md
```

This lists unresolved assumptions. Check whether any are relevant to the work
you are about to do.

# Outputs

- Clear understanding of: project scope, current phase, active ticket, next move.
- No guessing about what the project is or what should happen next.

# Failure Modes

- **No .loom/ directory**: the workspace has not been initialized. Run the
  loom-bootstrap + loom-workspace initialization sequence.
- **Constitution missing**: critical. Do not proceed without it; create it using
  the loom-constitution skill.
- **All tickets are proposed**: Wave 1 is not complete yet. Check `ticket:loom-init`
  status first.

# Sources

- `constitution:main`
- `mlibrary-agentic-scaffold/README.md`
- Loom bootstrap doctrine (loaded via `loom-bootstrap` skill)

# Related Pages

- `wiki:mlibrary-agentic-scaffold-overview` — what this workspace is for
- `wiki:new-project-setup` — starting a new library project

# Change Notes

- 2026-04-30: Initial page created during Loom workspace bootstrap.
