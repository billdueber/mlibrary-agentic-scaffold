---
id: wiki:new-project-setup
kind: wiki
page_type: workflow
status: accepted
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
scope:
  kind: workspace
links:
  constitution:
    - constitution:main
  initiative:
    - initiative:mlibrary-agentic-scaffold-stable
---

# Summary

How to start a new Ruby gem or general library project using `mlibrary-agentic-scaffold/`
as the agent instruction foundation. The interactive installer handles most setup
automatically; manual steps are available as a fallback.

# When To Use It

When creating a new project that should benefit from this agentic system.

# Primary Path: Use the Installer

```bash
# From your project directory:
/path/to/mlibrary-agentic-scaffold/bin/initialize_for_opencode

# Or pass the project path explicitly:
/path/to/mlibrary-agentic-scaffold/bin/initialize_for_opencode /path/to/your-project
```

The installer handles all of the following interactively:

1. **`opencode.json`** — MCP server entries + `instructions[]` array pointing to scaffold files
2. **Skills** — symlinks all scaffold skills into `~/.config/opencode/skills/`
3. **`.loom/`** — initializes a Loom workspace with a stub constitution
4. **`AGENTS.md`** — writes a scaffold-ready file; backs up any existing one to `AGENTS.md.bak`

After running, edit `.loom/constitution/constitution.md` to describe what the
project is, its principles, and any hard constraints for agents.

# Staying Up to Date

When you pull new scaffold changes:

```bash
/path/to/mlibrary-agentic-scaffold/bin/update_mlibrary_scaffold
```

This fails fast if the installer was never run on the project. It reports:
- New skills not yet symlinked
- MCP servers in `recommended-servers.md` not in `opencode.json`
- Scaffold instruction files not in `opencode.json instructions[]`

It makes no changes automatically — it reports only.

# Manual Path (fallback)

If you prefer not to use the installer, do each step by hand:

## Step 1: opencode.json — instruction files

Create or edit `opencode.json` in your project root:

```json
{
  "instructions": [
    "AGENTS.md",
    "/path/to/mlibrary-agentic-scaffold/instructions/core.md",
    "/path/to/mlibrary-agentic-scaffold/instructions/ruby.md",
    "/path/to/mlibrary-agentic-scaffold/instructions/testing.md",
    "/path/to/mlibrary-agentic-scaffold/instructions/documentation.md",
    "/path/to/mlibrary-agentic-scaffold/instructions/changelog-automation.md"
  ]
}
```

Remove language-specific files that don't apply.

## Step 2: Install skills

```bash
SCAFFOLD_DIR="/path/to/mlibrary-agentic-scaffold"
for skill_dir in "$SCAFFOLD_DIR"/skills/*/; do
  skill_name=$(basename "$skill_dir")
  mkdir -p ~/.config/opencode/skills
  ln -sf "$skill_dir" ~/.config/opencode/skills/"$skill_name"
done
```

## Step 3: Copy and edit AGENTS.md template

```bash
cp /path/to/mlibrary-agentic-scaffold/templates/AGENTS-ruby.md ./AGENTS.md
```

Edit: fill in project name, description, and any project-specific overrides.

## Step 4: Initialize Loom

Create `.loom/constitution/constitution.md` with a stub describing the project.
See `wiki:loom-workspace-orientation` for structure.

## Step 5: Create CONTEXT.md

```markdown
# Project Context

## What this is
<one sentence>

## Current state
<in-progress work, if any>

## Key decisions
<any decisions already made>

## Known sharp edges
<any gotchas>
```

The agent maintains this file across sessions as working memory.

# Outputs

- `opencode.json` with MCP config and instructions array
- Skills symlinked into `~/.config/opencode/skills/`
- `AGENTS.md` customized for the project
- `.loom/` workspace initialized
- (Optional) `CONTEXT.md` initialized

# Failure Modes

- **Skills not found**: check that symlinks in `~/.config/opencode/skills/` point
  to valid SKILL.md files. Re-run the installer or the manual symlink loop.
- **Update script fails fast**: means the installer was never run. Run
  `bin/initialize_for_opencode` first.
- **AGENTS.md too generic**: add project-specific overrides near the top.
- **CONTEXT.md drifts**: prompt explicitly at session end: "please update CONTEXT.md."

# Sources

- `mlibrary-agentic-scaffold/README.md` — Quick Start and Manual Setup sections
- `mlibrary-agentic-scaffold/bin/initialize_for_opencode` — installer implementation
- `mlibrary-agentic-scaffold/bin/update_mlibrary_scaffold` — update script implementation
- `constitution:main` — project scope and constraints

# Related Pages

- `wiki:mlibrary-agentic-scaffold-overview` — what the system is
- `wiki:cross-session-memory` — CONTEXT.md and memory tiers
- `wiki:loom-workspace-orientation` — orienting in the Loom workspace

# Change Notes

- 2026-04-30: Initial page created during Loom workspace bootstrap.
- 2026-05-01: Major rewrite. Installer (`bin/initialize_for_opencode`) and update
  script (`bin/update_mlibrary_scaffold`) are now the primary path. Manual steps
  demoted to fallback. JVM references removed (decision:0004). `opencode.json`
  instructions array documented as the primary instruction-file mechanism.
