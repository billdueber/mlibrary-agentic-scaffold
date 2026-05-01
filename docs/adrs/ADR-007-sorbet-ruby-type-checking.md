# ADR-007: Sorbet for Ruby Type Checking

## Status

Accepted

## Date

2026-04-30

## Context

See `.loom/constitution/decisions/decision-0002-ruby-type-checking.md` for the
full rationale. This ADR summarizes that decision in the ADR format for
completeness of the `docs/adrs/` record.

Ruby type checking for library projects governed by this scaffold requires
choosing between the two credible options: Sorbet (Stripe-backed, inline `sig`
blocks) and RBS + Steep (Matz-backed, external `.rbs` signature files).

The decisive constraint: the primary type annotation author is an AI agent, not
a human developer. This changes the evaluation criteria significantly.

## Decision

Ruby type checking uses **Sorbet** (`sorbet` + `sorbet-runtime` + Tapioca).

- `# typed: strict` on new files; `# typed: true` as baseline on existing files
- `bundle exec tapioca gems` to generate `.rbi` stubs for dependencies
- `bundle exec srb tc` in CI as the type-check gate
- Inline `sig` blocks as the authoring surface (no separate signature files)
- `sorbet-runtime` in `Gemfile` under `group :development, :test` only; **not**
  in gemspec `add_runtime_dependency` — gem consumers receive no runtime
  dependency

## Consequences

### Positive

- Inline `sig` annotations reduce agent context cost: type contract is visible
  beside the method, no parallel `.rbs` file to maintain.
- `srb tc` produces structured, actionable error output — better agent iteration
  loop than Steep's current output quality.
- Tapioca generates stubs for the full dependency tree with minimal agent effort.
- No runtime dependency imposed on gem consumers.

### Negative

- Sorbet's long-term community trajectory is uncertain; Matz has positioned RBS
  as the official direction.
- Adding `sorbet-runtime` as a dev dependency adds gem install time in CI.
- If Sorbet is eventually abandoned, migration to RBS is needed (though it is
  mechanically tractable — see migration plan in `decision:0002`).

### Neutral

- Migration from Sorbet to RBS when warranted is file-by-file and well-defined;
  `sig` blocks map directly to RBS method signatures.
- This decision should be revisited when `steep check` inference and error
  message quality reach visible parity with `srb tc`.

## Alternatives Considered

**RBS + Steep**: The community-standard direction, but external `.rbs` files
require an agent to maintain synchronization across two files per class, doubling
context surface. Steep's error output is noisier. Rejected given AI-agent-primary
authorship.

**No type checking**: Rejected. Type checking catches errors that are otherwise
invisible until runtime and gives agents a machine-readable correctness signal.

**Gradual typing only (no checker)**: Annotations without a CI gate are
documentation, not verification. Rejected.

## Open Questions

Revisit when:
- `steep check` inference and error messages reach visible parity with `srb tc`
- Sorbet's LSP or CLI shows degradation signs
- A community-maintained Sorbet-to-RBS migration tool matures
