---
id: ticket:agents-md-scaffold-expand
kind: ticket
status: closed
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-04-30T00:00:00Z
scope:
  kind: file
  paths:
    - AGENTS.md
links:
  constitution:
    - constitution:main
---

# Summary

Expand `AGENTS.md` from the minimal 6-line bootstrap pointer to a full working
reference for agents entering this repository.

# Motivation

The previous `AGENTS.md` only instructed agents to load `loom-bootstrap`. This
was insufficient — an agent entering cold had no orientation about the repo layout,
what instructions and skills exist, how to work here, what not to do, or how to
navigate the Loom workspace. Every session had to rediscover this context.

# Acceptance Criteria

- [ ] Covers what the repository is and what it is not
- [ ] Documents the full directory layout
- [ ] States core principles with pointers to the constitution
- [ ] Explains how to work on instruction files, skills, and templates
- [ ] Lists all available skills with when-to-use summaries
- [ ] Documents MCP server setup priority and config key caveats
- [ ] Summarizes cross-session memory tiers
- [ ] States agent guardrails (what must not be done without approval)
- [ ] Includes a Loom workspace quick reference with key files and query commands

# Completion Basis

Completed in session 2026-04-30. All acceptance criteria met. AGENTS.md expanded
from 6 lines to a comprehensive working reference. Content grounded in:
- `constitution:main` (principles, constraints, strategic direction)
- `instructions/agentic-memory.md` (memory tiers)
- `mcp/recommended-servers.md` (MCP config)
- All skill SKILL.md files (skill descriptions)
- Loom workspace structure (quick reference section)
