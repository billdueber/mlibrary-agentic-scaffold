# ADR-003: Markdown-Native Scaffold via Referenced Instruction Files

## Status

Accepted

## Date

2026-04-30

## Context

This scaffold needs to deliver reusable agent guidance to multiple downstream
library projects. The question is: what form should that reusable guidance take?

Options considered:

1. **Markdown instruction files included by reference** — plain `.md` files in
   `instructions/`. Downstream projects reference them by URL or path in their
   own `AGENTS.md`. No tooling required to read or edit them.

2. **A DSL or configuration schema** — define a structured format (YAML, TOML,
   JSON) from which instruction content is generated. Adds a build step and
   tooling dependency.

3. **A generator CLI** — a tool that scaffolds a new project's instruction files
   from templates. Higher ceremony; requires installing and maintaining the
   generator.

4. **A shared gem or package** — publish the scaffold as a Ruby gem or npm
   package that projects install. Requires versioning, publishing infrastructure,
   and installation steps in every consumer project.

5. **Copy-and-own templates** — `templates/AGENTS-ruby.md` is a drop-in starting
   point that projects copy and own locally, with upstream changes pulled manually.

The primary consumer of this guidance is an AI agent, not a human developer
running a build. The agent reads Markdown directly. A DSL or generator would
add indirection without improving the agent's ability to read or apply the
guidance.

## Decision

The scaffold uses **plain Markdown instruction files** in `instructions/` as the
primary delivery mechanism, supplemented by **copy-and-own templates** in
`templates/` for new project bootstrapping.

No DSL, generator CLI, or published package is used. No build step is required
to consume the scaffold's guidance.

Downstream projects reference instruction files directly (by path or URL) from
their `AGENTS.md`, or copy the relevant template and own it locally.

## Consequences

### Positive

- Zero tooling required to read, edit, or consume instruction files.
- AI agents can read and reason about the guidance directly — no parsing layer.
- Changes to instruction files are immediately usable without a publish step.
- `git diff` shows the full content of any change; nothing is generated.
- No version management required for consumers who reference files by path.

### Negative

- Downstream projects that copy templates diverge from the scaffold over time;
  pulling upstream changes is a manual process.
- No structured validation that a consumer project's instruction set is
  complete or consistent with the current scaffold.
- Reference-by-URL consumers depend on the scaffold's file paths not changing.

### Neutral

- The template file (`AGENTS-ruby.md`) must be kept in sync
  with instruction file changes manually — this is a stated maintenance
  obligation, not an automated check.
- The `adr-create` and other skills are still invokable workflow modules; they
  are not instruction content, so they sit in `skills/` not `instructions/`.

## Alternatives Considered

**DSL or configuration schema**: Adds a build step and tooling dependency for
no gain — the output is still Markdown that an agent reads. Rejected.

**Generator CLI**: Higher ceremony, requires installation, adds a maintenance
surface. Rejected for this scale.

**Published gem or package**: Requires versioning, publishing infrastructure,
and per-project installation. Overkill for a solo-dev scaffold. Rejected.

## Open Questions

None at adoption time.
