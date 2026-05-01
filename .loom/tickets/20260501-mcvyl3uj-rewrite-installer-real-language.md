---
id: ticket:20260501-mcvyl3uj-rewrite-installer-real-language
kind: ticket
status: proposed
created_at: 2026-05-01T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
title: "Rewrite bin/initialize_for_opencode in a real language"
priority: low
not_before: 2027-01-01
links: {}
---

## Why

`bin/initialize_for_opencode` is a ~950-line bash script doing JSON manipulation,
interactive prompts, platform detection, and now argument parsing. It has already
required two critique rounds (critique `20260501-ewtdxi6z`) to catch bugs introduced
by bash's limitations (forward-reference functions, regex in Python inline blocks,
`eval` on composed strings).

The script's complexity has hit the point where bash is the wrong tool. The decision
to stay in bash was explicitly made at the simple-args inflection point (2026-05-01),
with a note to revisit when complexity grows further.

**Trigger conditions** — consider rewriting when any of the following are true:
- A third language/infra layer is added (the flag surface gets unwieldy)
- Mutual-exclusion between layers is needed (e.g. only one language layer allowed)
- `--dry-run` or `--help` is requested
- Layer dependencies emerge (e.g. `k8s` implies `docker`)
- Non-interactive / CI usage becomes a first-class requirement
- Another critique finds a bash-specific bug that a typed language would have prevented

## Scope

Rewrite `bin/initialize_for_opencode` (and likely `bin/update_mlibrary_scaffold`)
in Ruby, Python, or Go with:
- Proper OptionParser / argparse / cobra option parsing
- `--layer` flags (multiple), `--help`, `--dry-run`
- Layer registry as data, not code
- Layer dependency/exclusion rules
- Non-interactive mode for CI (`--non-interactive`, accept defaults)
- Same end-state behavior as the bash script (MCP merge, symlinks, Loom init, AGENTS.md)

## Language choice

At rewrite time: prefer the language already present in the project's toolchain.
This scaffold is Ruby-first — Ruby + Thor or OptionParser is the natural default.
Python is acceptable if uv is already a dependency (it is, for some MCP installs).
Go produces a single binary but adds a build step.

Decide at rewrite time; record as an ADR.

## Out of scope

This ticket does not require changing the installer's behavior, only its
implementation language and option-parsing approach.

## Acceptance

- `initialize_for_opencode --help` prints usage with all layers listed
- `initialize_for_opencode ruby docker` works non-interactively (MCP prompts still interactive unless `--non-interactive`)
- `initialize_for_opencode --dry-run ruby` prints what would be done without mutating files
- All existing bash behavior preserved (MCP merge, symlinks, Loom init, AGENTS.md writing)
- `bash -n` is no longer relevant; language-native tests pass instead

## Execution notes

Recorded 2026-05-01 during simple-args addition to bash script.
The comment in `bin/initialize_for_opencode` near the layer parsing block references
this ticket ID (`ticket:rewrite-installer-real-language`).
