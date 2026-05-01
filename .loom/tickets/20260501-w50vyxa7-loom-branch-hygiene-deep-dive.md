---
id: ticket:20260501-w50vyxa7-loom-branch-hygiene-deep-dive
kind: ticket
status: proposed
created_at: 2026-05-01T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
title: "Deep dive: Loom hygiene in a branching workflow"
priority: medium
links:
  related:
    - ticket:20260501-5eppe9bj-preserve-findings-skill
    - ticket:20260501-f80guw2n-git-add-public-files-guidance
---

## Why

Loom assumes the filesystem is the graph. Git assumes the repository is the
history. These two assumptions conflict when you use branches: Loom records
live on the branch that created them, not on main, and the standard Git workflow
(branch → work → merge or drop) can cause knowledge loss, merge conflicts in
Loom files, and a lot of friction just switching contexts.

We have partial solutions (`preserve-findings` skill, git hygiene rule) but no
coherent policy. This ticket is a placeholder for a thorough investigation and
documented set of practices.

## Problem Space

### 1. Lost decisions and findings

When a branch is abandoned without merging, any Loom records created on it
(research notes, evidence, critique findings, decisions) are gone from the
working tree. `preserve-findings` helps, but only if the user remembers to
invoke it and only for the records they consciously choose to save. Decisions
that "seemed fine" during the spike but were later reversed may never be
recorded as failures.

### 2. Merge conflicts in Loom files

Loom records are prose Markdown files, not structured data. Two branches that
both add files to `.loom/tickets/` or modify `.loom/wiki/` will produce merge
conflicts in those directories. This is particularly painful for:

- Long-running feature branches where `main` accumulated new wiki/research
  pages in the meantime
- Parallel work by (hypothetical) multiple contributors
- Rebase workflows where Loom file conflicts surface repeatedly

Currently there is no guidance on how to resolve these conflicts beyond "use
your judgment."

### 3. Branch-switching friction

Checking out a different branch changes the `.loom/` directory to reflect that
branch's state. If you have active work recorded in `.loom/tickets/` on one
branch and switch to another to do a hotfix, the Loom state visible to the agent
changes too. This can cause:

- Agent confusion about what work is "active" (ticket statuses on the branch
  you just switched away from are invisible)
- Accidental edits to Loom records while on the wrong branch
- In a mid-session switch, losing the mental model of where you were

### 4. Main as the canonical Loom database

The current implicit model is that `main` is the canonical Loom graph, and
branches are ephemeral. But the `preserve-findings` skill works against this:
it creates a *separate* chore branch to land Loom records, which itself needs
to be merged. That means two PRs per spike (the code PR and the findings PR),
or the findings are bundled into the code PR (which mixes concerns).

There is no clear policy on: should Loom records ever live only on a feature
branch, or should they always be committed to main as they are created?

### 5. "Two-stream" model as an alternative

An alternative model: Loom records always go directly to `main` (via direct
commits or a perpetually open Loom-records branch), while code changes live on
feature branches. The agent would need to know which stream each file belongs
to and would need to split commits accordingly. This eliminates knowledge loss
but introduces complexity and requires discipline that is easy to break.

### 6. Git worktrees as a possible mitigation

`git worktree` allows multiple working trees from the same repository, each on
a different branch. A Loom-records worktree on `main` could stay open
permanently while feature work happens in a separate worktree. This is the
cleanest isolation model but adds setup complexity and is not documented
anywhere in the scaffold.

### 7. When to commit Loom records vs. code

Currently there is no guidance on commit cadence for Loom records. Options:

- Commit Loom records after every meaningful update (like a live log)
- Commit Loom records only at task boundaries (same cadence as code)
- Commit Loom records in a separate pass before closing a ticket
- Never commit Loom records mid-ticket; only commit the final state

Each has tradeoffs. The current default is "whenever the user asks" which means
Loom records often aren't committed at all during a session.

### 8. Ticket state and branch state can diverge

A ticket can be marked `active` on one branch and `proposed` on another. If
you merge a branch that touched a ticket, the merged ticket state may conflict
with changes made to the same ticket on main. This is a structural problem with
using mutable files as an execution ledger — there is no conflict-free merge
strategy for ticket status fields.

### 9. Agent awareness of current branch

When an agent starts a session, it should know:
- What branch it's on
- Whether that branch has Loom records that differ from main
- Whether there are uncommitted Loom changes

Currently there is no instruction telling the agent to orient against the branch
state at session start. The git hygiene rule helps at session *end* but not at
session *start*.

---

## Questions to Answer

1. Should Loom records always live on main, or is it acceptable for them to live
   on branches temporarily?

2. What is the recommended merge strategy for `.loom/` directories when conflicts
   arise?

3. Should the scaffold recommend `git worktree` for Loom-heavy workflows? If so,
   what is the setup procedure and when should it be used?

4. How should the agent orient at session start on a branch — what checks should
   it run and what should it report?

5. Is the two-stream model (Loom on main, code on branches) worth the complexity?
   What is the minimum-friction version of it?

6. How should the `preserve-findings` skill interact with an open PR? If a PR is
   already open, should findings go into that PR's branch or a separate chore
   branch?

7. What should the policy be for commit cadence of Loom records? Should there be
   an automatic checkpoint before branch switching?

8. Is there a way to detect and warn when ticket state diverges across branches
   before it becomes a merge conflict?

---

## Suggested Approach

This deserves a research pass before any new rules are written. The output
should be a `research:loom-branch-hygiene` record that surveys the tradeoffs
and ends with a ranked set of recommendations. From there, policy changes would
flow into `instructions/core.md`, the `preserve-findings` skill, and possibly
a new `loom-git-workflow` wiki page.

Do not write new rules speculatively before the research pass — there are enough
tradeoffs here that guessing the right answer is likely to produce contradictory
guidance.

## Acceptance

- A `research:loom-branch-hygiene` record exists with a concrete recommendation
- At least one of the nine problem areas above results in a new or updated agent
  rule, instruction, or wiki page
- The `preserve-findings` skill is updated if the research changes how it should
  work
- This ticket is closed with a summary of what was resolved and what was
  explicitly deferred

## Execution Notes

- Created 2026-05-01 after implementing `preserve-findings` skill and recognizing
  the partial-solution problem
- Not time-sensitive; defer until the current partial solutions have been used
  enough to identify the real friction points
