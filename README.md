# Library Agent System

A personal agentic scaffold for Ruby gem authors and general software projects,
using OpenCode + GitHub Copilot. It provides instruction files, skills, and
templates that give AI agents the context they need to work effectively —
configured via your project's `opencode.json`.

See [`docs/directory-structure.md`](docs/directory-structure.md) for the full annotated tree.

---

## Quick Start

### Run the installer

The easiest way to set up a project is the interactive installer. It handles
`opencode.json`, AGENTS.md, Loom workspace initialization, MCP server
installation, and skill symlinking in one pass.

```bash
# From your project directory:
/path/to/mlibrary-agentic-scaffold/bin/initialize_for_opencode

# Or pass the project path explicitly:
/path/to/mlibrary-agentic-scaffold/bin/initialize_for_opencode /path/to/your-project
```

The script will walk you through each step interactively, offering choices where
tradeoffs exist (e.g., which MCP servers to install). It never modifies your
project without asking first.

**What the installer does:**
- Configures `opencode.json` with MCP servers and the scaffold's instruction files
- Symlinks scaffold skills into `~/.config/opencode/skills/`
- Initializes a `.loom/` workspace with a stub constitution
- Writes a scaffold-ready `AGENTS.md` (backing up any existing one)

### Staying up to date

When you pull new changes to the scaffold, run the update script to sync missing
skills, check for new recommended MCP servers, and identify any new instruction
files not yet in your `opencode.json`:

```bash
/path/to/mlibrary-agentic-scaffold/bin/update_mlibrary_scaffold
```

The update script fails fast if the installer was never run on the current project.
It makes no changes without telling you first.

---

## Manual Setup

If you prefer to configure things by hand rather than using the installer, this
section covers each step. Each subsection notes what the installer would have
done automatically.

### opencode.json — instruction files

Add instruction files from this scaffold to your project's `opencode.json`
`instructions` array. OpenCode loads them automatically at the start of every
session:

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

Remove language-specific files you don't need (e.g., `ruby.md` for a non-Ruby project).

> **Installer**: handles this automatically, prompting you to confirm the instruction
> file list.

**Fallback**: OpenCode also loads instruction files from a `.opencode/` directory
in your project root. You can symlink or copy individual files there instead.

### Skills installation

Skills must be symlinked (or copied) into `~/.config/opencode/skills/` so OpenCode
can find them. Symlinks are preferred — they pick up content changes automatically
without reinstalling.

```bash
SCAFFOLD_DIR="/path/to/mlibrary-agentic-scaffold"

for skill_dir in "$SCAFFOLD_DIR"/skills/*/; do
  skill_name=$(basename "$skill_dir")
  mkdir -p ~/.config/opencode/skills
  ln -sf "$skill_dir" ~/.config/opencode/skills/"$skill_name"
done
```

Once installed, OpenCode lists them in the `skill` tool and loads them on demand.
Invoke a skill explicitly with: `use the adr-create skill`.

> **Installer**: symlinks all scaffold skills automatically. **Update script**:
> symlinks any new skills added since the last install.

### MCP servers

See [`mcp/recommended-servers.md`](mcp/recommended-servers.md) for full details,
tradeoffs, and installation commands. Quick list:

| Server | Purpose | Notes |
|--------|---------|-------|
| `context-mode` | Context overflow protection + in-session FTS knowledge base | Install first |
| `github` | GitHub API (PRs, issues, releases) | Requires `GITHUB_TOKEN` |
| `fetch` | Read URLs / API docs | Fallback only; skip if using context-mode |
| `sequential-thinking` | Structured multi-step reasoning | |
| `memory` | Cross-session entity knowledge graph | Zero infra, Tier 2 memory |
| `mem0` | Semantic cross-session memory | Optional; requires Python + Docker |

> **Installer**: offers each MCP server interactively and writes approved entries
> to `opencode.json`. **Update script**: reports any recommended servers not yet
> in your `opencode.json`.

### AGENTS.md

Copy the template for your project type and edit it:

```bash
cp /path/to/mlibrary-agentic-scaffold/templates/AGENTS-ruby.md ./AGENTS.md
# Edit: fill in project name, description, and any project-specific overrides
```

> **Installer**: writes a scaffold-ready `AGENTS.md` that includes the Loom
> bootstrap directive and references the scaffold's instruction files. If an
> `AGENTS.md` already exists, it is backed up to `AGENTS.md.bak` and its
> content is preserved in a clearly marked section.

---

## Agentic Memory

Cross-session memory is a real gap for stateless agents. Multiple systems were
researched including mem0, Letta, Graphiti, memU, Beads, and sqlite-memory.

For setup instructions, see [`instructions/agentic-memory.md`](instructions/agentic-memory.md).
For the full research with system profiles and tradeoff analysis, see
[`docs/research/agentic-memory-research.md`](docs/research/agentic-memory-research.md).

| Tier | Approach | Infrastructure | When to use |
|------|----------|---------------|-------------|
| 1 | `CONTEXT.md` + Loom records | None | Always — start here |
| 2 | `@modelcontextprotocol/server-memory` | None (npx) | When you re-explain context repeatedly |
| 3 | `mem0` (vector+graph, MCP) | Python + Docker | When semantic retrieval becomes valuable |

Richer memory systems give better recall at the cost of infrastructure,
maintenance burden, and potential context noise. The `CONTEXT.md` + Loom
approach is transparent and git-versionable — always start there.

---

## Evolving the Scaffold

### Updating a project when the scaffold changes

When you pull new changes to the scaffold (new skills, new recommended servers,
new instruction files), run:

```bash
/path/to/mlibrary-agentic-scaffold/bin/update_mlibrary_scaffold
```

This checks for drift between the scaffold and your project configuration and
reports what needs attention without making changes automatically.

### Contributing to the scaffold itself

This is a living system. Suggested workflow:

1. Notice a recurring pattern or agent failure mode.
2. Open a Loom ticket in `.loom/tickets/` and note it.
3. Write an instruction or skill to address it.
4. Update relevant `AGENTS.md` templates and `docs/adrs/` if the decision is architectural.
