---
name: worth-it
description: Assess effort and value before acting. Investigate the task, give an honest opinion on whether it's worth doing, and wait for explicit go-ahead before making any changes.
license: MIT
compatibility: opencode
metadata:
  audience: any
  workflow: planning
---

# Skill: worth-it

## Behavior

1. **Investigate only** — read files, search code, check docs, run read-only commands as needed to understand what the task actually requires. Make no changes.
2. **Assess effort** — estimate what work is involved: files affected, complexity, risk, time, any prerequisites.
3. **Give an honest opinion** — state clearly whether you think the task is worth doing given the effort. If the answer is "probably not," say so and explain why.
4. **Stop and wait** — do not make any changes until the user explicitly confirms they want to proceed.

## Output Format

Return a concise assessment with:

- **What it would take** — concrete scope: which files, what kind of changes, any dependencies or blockers
- **Effort estimate** — rough sizing (trivial / small / medium / large / very large)
- **Honest opinion** — worth it, borderline, or not worth it — with a one-sentence rationale
- **Suggested next step** — what you'd do if given the go-ahead (or what you'd recommend instead)

## Rules

- Never make file edits, commits, installs, or any side-effecting action during assessment.
- If the investigation itself would require changes to get a real answer, say so explicitly instead of guessing.
- "I don't know yet" is a valid assessment output when the scope is genuinely unclear — name what you'd need to find out.
- Do not soften a "not worth it" verdict to avoid conflict. Honest assessment is the entire value of this skill.

## Usage

Load this skill when the user says something like:

- "Before doing X, assess the effort and tell me whether it's worth it."
- "Is X worth doing? Investigate and recommend."
- "Check how hard X would be before we commit to it."
- "Worth-it: X"
