# Workspace: mlibrary-agentic-scaffold

This is the `mlibrary-agentic-scaffold` workspace — a personal agentic library
system for building Ruby gems and Java/Kotlin library artifacts with OpenCode +
GitHub Copilot. Maintained by the University of Michigan Library IT group (mlibrary).

## Workspace Root

```
<this directory>   # wherever mlibrary-agentic-scaffold/ has been placed
```

The `.loom/` directory is co-located inside this directory, making the scaffold
self-contained and relocatable.

## Scope

Single repository, single owner. No multi-worktree setup.

## Repository Alias

`repo:root` → `<this directory>` (the `mlibrary-agentic-scaffold/` root)

## Loom State

Loom was initialized 2026-04-30. See `constitution:main` for project identity
and principles.

## Key Entry Points

| Record | Path | Purpose |
|--------|------|---------|
| Constitution | `.loom/constitution/constitution.md` | Project identity, principles, constraints |
| Initiative | `.loom/initiatives/initiative-mlibrary-agentic-scaffold-stable.md` | Strategic outcome |
| Plan | `.loom/plans/plan-mlibrary-agentic-scaffold-stabilize.md` | Execution sequencing |
| Tickets | `.loom/tickets/` | Live execution state |
| Wiki | `.loom/wiki/` | Accepted explanation |

## Subject System

```
mlibrary-agentic-scaffold/
├── instructions/     # Agent instruction files (language-agnostic + Ruby + JVM)
├── skills/           # OpenCode skills (adr-create, release, lint, integration-test, docker)
├── templates/        # AGENTS.md templates for new projects
└── mcp/              # MCP server recommendations
```

## How to Orient

A fresh agent should:

1. Read this file
2. Read `constitution:main`
3. Run `rg -n '^status:' .loom/tickets/*.md` to find active work
4. Read the active ticket's `# Next Move / Next Route` section

For a longer orientation guide, see `wiki:loom-workspace-orientation`.
