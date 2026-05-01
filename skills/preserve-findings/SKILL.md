---
name: preserve-findings
description: Triage Loom records on the current branch and land the keepers to main before the branch is dropped or a PR is opened. Use proactively whenever valuable research, decisions, or evidence should survive regardless of what happens to the branch.
license: MIT
compatibility: opencode
metadata:
  audience: any
  workflow: knowledge-preservation
---

# Skill: preserve-findings

## When to Use

Invoke this skill at any point while on a feature or spike branch when you want
to ensure valuable knowledge lands on main independent of the branch's fate:

- You reached a significant conclusion and want it durable before continuing
- The spike is being abandoned and you want to salvage findings
- Before opening a PR, to ensure the Loom record is clean and complete
- The user says something like "record that decision before I forget" or
  "let's preserve what we know so far"

## Behavior

### Step 1: Orient

1. Confirm the current branch. If already on `main` (or base branch), report
   "already on main — Loom records are already in place" and exit gracefully.
2. Identify the base branch (default: `main`). Confirm with user if unclear.
3. Run `git diff --name-only <base>..<current> -- .loom/` to list all Loom
   records that are new or modified on this branch vs. base.
4. Group records by kind: `research`, `decisions` (constitution), `wiki`,
   `evidence`, `critique`, `tickets`, `other`.
5. Report the list to the user with counts by kind before proceeding.

### Step 2: Triage

For each Loom record in the diff, present a 1–2 sentence summary of its content
and propose a disposition. Wait for user confirmation or override before moving on.

Available dispositions:

| Disposition | When to suggest | What happens |
|-------------|-----------------|--------------|
| `preserve` | Record is complete and directly useful as-is | Cherry-pick to chore branch unchanged |
| `promote` | Record is valuable but rough — needs light rewriting into a cleaner/more durable form | Agent rewrites collaboratively with user, user reviews, then land |
| `drop` | Implementation detail with no lasting value beyond this branch | Skip — stays on feature branch only |
| `reclassify-now` | User explicitly says this decision or approach turned out wrong, but the failure story is worth keeping | Agent helps user articulate the failure inline; lands as a research null-result or deprecated decision record with failure rationale |
| `reclassify-later` | Same as above but user doesn't have bandwidth to articulate it now | Agent creates a Loom ticket (see below) and lands it on the chore branch |

**Default suggestions by record kind:**

- `research:` → `preserve` (findings are usually worth keeping)
- `decisions` / constitution records → `preserve` if accepted, `reclassify-now/later` if the spike revealed the decision was wrong
- `wiki:` → `preserve` if the page reflects accepted understanding; `drop` if it was a working scratch page
- `evidence:` → `preserve` if it supports a finding; `drop` if it's raw spike output with no conclusion attached
- `critique:` → `preserve` findings; `drop` in-progress critique with no verdict
- `tickets:` → `drop` unless a ticket documents a durable open question; spike execution tickets should not move to main

Do not rush. Let the user override any suggestion. Ask clarifying questions
when a record's value is genuinely ambiguous.

### Reclassify-Later Ticket Format

When the user chooses `reclassify-later`, create a Loom ticket in `.loom/tickets/`
with this shape:

```markdown
---
id: ticket:<YYYYMMDD>-<token>-reclassify-<slug>
kind: ticket
status: proposed
created_at: <UTC timestamp>
updated_at: <UTC timestamp>
title: "Reclassify finding from branch <branch-name>: <short description>"
priority: medium
links: {}
---

## Context

While working on branch `<branch-name>` on <date>, a finding was flagged for
reclassification but not resolved during the preserve-findings triage.

**Original record**: `<path to record on branch>`
**What was known at the time**: <1–3 sentence summary of the record's content>
**Why it needs reclassification**: <what the user said — quote directly if possible>

## Work

Clarify the failure, reversal, or open question and update or create the
appropriate Loom record on main:

- If the decision was wrong: create a deprecated decision record with failure rationale
- If the approach failed: create a research null-result record
- If it's genuinely unknown: create or update a research record with an open question

## Acceptance

- A Loom record on main reflects the reclassified state
- The original finding is not silently lost
```

### Step 3: Land

1. Create branch `chore/preserve-findings-YYYYMMDD` off `main` (not off the
   feature branch).
2. For each `preserve` or `promote` record: copy the file from the feature
   branch and commit it.
3. For each `reclassify-now` record: write the new record collaboratively,
   commit it.
4. For each `reclassify-later` ticket: commit it.
5. Single conventional commit message:
   `chore(loom): preserve findings from <branch-name>`
   Body: bullet list of what was preserved, promoted, reclassified, and any
   tickets created.

### Step 4: Advise

Report to the user:

- The chore branch name and what it contains
- Any `reclassify-later` tickets that need follow-up (list with titles)
- Whether the feature branch is now safe to drop (if this was an abandonment)
- Whether the chore branch needs a PR or can be merged directly (user decides)

## Rules

- Never commit to `main` directly. Always use the chore branch.
- Never move in-progress tickets or spike execution tickets to main.
- Never `drop` a record the user flagged as important, even if the agent thinks
  it has no lasting value.
- The user's triage decision overrides the agent's suggestion in all cases.
- Do not rewrite a `preserve` record without asking — only `promote` allows
  rewriting.
- If the branch has no Loom records different from main, say so and exit.

## Usage

Load this skill when the user says something like:

- "preserve-findings"
- "Let's record what we learned before dropping this branch"
- "Save that decision to main before I forget"
- "Salvage the Loom records from this spike"
- "I want to abandon this branch but keep the research"
