# ADR-004: Skills as the Primary Reuse Mechanism for Repeatable Workflows

## Status

Accepted

## Date

2026-04-30

## Context

Certain workflows recur across every library project this scaffold supports:
releasing a gem, running linters, creating ADRs, running integration tests,
managing Docker services. The question is how to encode these repeatable
workflows so they are reusable, invokable on demand, and maintainable.

Options considered:

1. **Inline prose in instruction files** — describe the workflow in the relevant
   `instructions/*.md` file. Simple to write; cannot be invoked as a unit;
   agents must extract and apply steps manually each time.

2. **Shell scripts** — encode workflows as scripts in a `scripts/` or `bin/`
   directory. Imperative and deterministic, but require execution infrastructure
   and are not easily parameterizable by an AI agent's reasoning.

3. **OpenCode skills** — self-contained `SKILL.md` files in `skills/<name>/`
   that an agent loads on demand. Loom itself ships skills for its workflow
   subsystems. This scaffold can ship parallel skills for library-building
   workflows.

4. **Makefile or task runner targets** — machine-executable but not
   agent-native; agents still need to know which target to run and why.

The primary executor of these workflows is an AI agent, not a human developer.
An agent benefits from a skills surface that it can reason about (when to use
this skill, what it does) rather than a surface that merely provides machine
execution.

## Decision

Repeatable, bounded workflows are encoded as **OpenCode skills** (`SKILL.md`
files in `skills/<name>/`). Prose in instruction files describes conventions and
context; skills encode the step-by-step workflow that an agent invokes.

New workflows that become genuinely repeatable should be promoted to skills
rather than left as prose paragraphs in instruction files.

## Consequences

### Positive

- Skills are invokable on demand with a single load; agents do not need to
  extract steps from prose.
- The `skills/` directory provides a discoverable catalog of available
  workflows.
- Skills can be kept small and focused (one workflow per skill).
- Loom's own skill architecture is reused rather than invented in parallel.
- Skills reduce instruction file length by offloading procedural detail.

### Negative

- Agents must explicitly load a skill before using it (context budget cost).
- Skills written for OpenCode may not transfer directly to other AI harnesses.
- A workflow encoded only as a skill and not at all in prose may be harder for
  a human to understand without loading the skill.

### Neutral

- The `AGENTS.md` skills table is the primary discovery surface — it must be
  kept current as skills are added or changed.
- Skills that are purely procedural (no policy content) do not replace the
  instruction files that provide the context for why the workflow exists.

## Alternatives Considered

**Inline prose only**: Cannot be invoked as a unit; agents repeat the same
extraction work each time. Does not scale as the workflow catalog grows.
Rejected as the sole mechanism.

**Shell scripts**: Not agent-native. Agents must still decide when to run which
script and in what order. Fine as an implementation detail inside a skill, but
not as the primary interface. Rejected as the top-level mechanism.

**Makefile targets**: Same issues as shell scripts for agent ergonomics.
Rejected.

## Open Questions

None at adoption time.
