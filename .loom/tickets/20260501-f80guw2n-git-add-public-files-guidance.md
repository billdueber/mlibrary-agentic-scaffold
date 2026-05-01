---
id: ticket:20260501-f80guw2n-git-add-public-files-guidance
kind: ticket
status: closed
created_at: 2026-05-01T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
title: "Figure out best way to instruct agents to git-add files created outside .loom"
priority: medium
links: {}
---

## Why

Agents working on this scaffold create files in "public" spaces — `docs/`, `bin/`,
`instructions/`, `skills/`, `templates/`, `mcp/`, repo root — but there is no
instruction anywhere telling them to stage those files with git. Loom records go
into `.loom/` which is also untracked unless explicitly added.

The result: entire sessions of work accumulate as unstaged changes, and the agent
never mentions git unless the user explicitly asks. A long session of good work
can be lost or hard to review if the user forgets to commit before closing.

## Questions to answer

1. **Where should the instruction live?** Options:
   - `instructions/core.md` — loaded every session for downstream projects too;
     appropriate since git hygiene is cross-project
   - `AGENTS.md` of this scaffold only — scaffold-specific, not pushed downstream
   - A new `instructions/git.md` — separates concerns but adds another file

2. **What should the instruction say?** Options:
   - "At the end of a session, run `git status` and report unstaged files to the
     user; do not commit without explicit request"
   - "After creating or modifying a public file, stage it with `git add`
     immediately" — more proactive, harder to scope correctly
   - "When the user says the session is ending, run `git status` and summarize
     what would be committed"

3. **Should the agent ever commit autonomously?** Almost certainly no — the existing
   must-not-do list already says "Push to main" requires approval. Committing is
   lower risk but still a user decision. The instruction should report, not commit.

4. **Loom files vs public files** — `.loom/` records are also untracked. Should
   the same instruction cover both, or is `.loom/` implicitly understood as
   "always tracked"?

## Suggested starting point

Add a short "Git hygiene" agent rule to `instructions/core.md`:

```
Agent rule: At natural session breakpoints (user says "good stopping point",
"I'm done", "going to bed", etc.), run `git status` in the project root and
report any untracked or modified files outside of build/vendor/cache directories.
Do not commit without explicit user instruction. Summarize what would be staged
as a list, not a wall of output.
```

## Acceptance

- A concrete instruction exists in one canonical location
- It is clear whether the instruction covers `.loom/` files as well as public files
- The instruction does not autonomously commit
- The location is justified (why there and not somewhere else)
- Downstream projects inherit it if appropriate, or not if scaffold-specific

## Execution notes

Raised at end of 2026-05-01 session. Multiple files were created this session
(docs/, bin/, instructions/, .loom/tickets/, .loom/wiki/, .loom/critique/) without
any git staging guidance from the agent.
