---
id: decision:0002
kind: decision
status: active
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-04-30T00:00:00Z
scope:
  kind: workspace
links:
  constitution:
    - constitution:main
  ticket:
    - ticket:ruby-type-checking
---

# Decision

Ruby type checking in this workspace uses **Sorbet** (`sorbet` + `sorbet-runtime`
+ Tapioca), with `sorbet-runtime` kept in the development/test group only so
gem consumers receive no runtime dependency.

## Adopted approach

- `# typed: strict` on new files; `# typed: true` as baseline on existing files
- `bundle exec tapioca gems` to generate `.rbi` stubs for dependencies
- `bundle exec srb tc` in CI as the type-check gate
- Inline `sig` blocks as the authoring surface (no separate signature files)
- `sorbet-runtime` in `Gemfile` under `group :development, :test` only; not in
  gemspec `add_runtime_dependency`

## Rationale

The primary authoring agent is an AI agent, not a human developer. This constraint
is the decisive factor. Two properties of Sorbet win under AI authorship:

**Inline annotations reduce agent context cost.** `sig { params(x: Integer).returns(String) }`
lives beside the method it annotates. An agent reading or modifying a file sees
the full type contract without loading a parallel `.rbs` file. RBS's external
signature file approach requires an agent to maintain synchronization across two
files per class, which doubles context surface and creates a class of error
(signatures that drift from implementation) that is easy to miss.

**Sorbet's CLI feedback loop is better for agents.** `srb tc` produces
structured, actionable error output. Steep's checker is maturing but produces
noisier output with weaker inference. When an agent is iterating on a type error,
message quality matters.

**Tapioca is the best stub generation tool available.** Running
`bundle exec tapioca gems` gives typed stubs for the full dependency tree with
minimal agent effort. RBS coverage for third-party gems is patchier and requires
more manual intervention.

## Migration plan

Sorbet's long-term community trajectory is uncertain. It was created at Stripe
and has not achieved broad Ruby community adoption. Matz has consistently
positioned RBS as the official direction; Ruby ships RBS stdlib coverage by
default. Sorbet may gradually become a Stripe-internal tool over a 5–10 year
horizon.

The migration from Sorbet to RBS when that time comes is mechanically tractable:

- `sig` blocks map directly to RBS method signatures (same information, different
  syntax)
- The conversion is file-by-file and well-defined; AI agents can perform it
  reliably
- Tooling exists in partial form today (`sord`, `rbs-inline`) and will mature
- The migration should be triggered when `steep check` output quality reaches
  parity with `srb tc` for the error-iteration use case

Revisit this decision when:

- `steep check` inference and error messages reach visible parity with `srb tc`
- Sorbet's LSP or CLI shows degradation signs (unresolved bugs accumulating,
  release cadence dropping)
- A community-maintained Sorbet-to-RBS migration tool matures

## Rejected alternatives

### RBS + Steep

RBS is the Matz-backed, stdlib-integrated direction for Ruby typing. For a human
developer writing types by hand, it wins: no runtime dependency, no gem weight,
lower ceremony. The rejection is context-specific: this workspace uses AI agents
as the type annotation author. Under that constraint, inline Sorbet annotations
are strictly easier to generate and verify than external `.rbs` files. If the
workspace ever shifts to heavy human authorship, this decision should be
revisited.

### Gradual typing only (no dedicated type checker)

Rejected because it provides no CI gate and no structured feedback for agents.
Type annotations without a checker are documentation, not verification.

### No type checking

Rejected. Type checking catches a class of errors that are otherwise invisible
until runtime, and gives agents a machine-readable correctness signal during
code generation. The cost is low; the benefit is real.
