---
id: constitution:main
kind: constitution
status: active
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-05-01T00:00:00Z
scope:
  kind: workspace
links: {}
---

# Vision

A personal, reusable agentic scaffold that makes AI agents genuinely useful
when building software projects — with deep coverage for Ruby gems, solid
general-purpose conventions for any project, and room to add platform-specific
layers over time — without requiring the owner to explain the same context twice.

# Principles

## Ruby-first, general-purpose

This scaffold has deep coverage for Ruby gem authors. Beyond Ruby, it provides
solid general-purpose conventions (Loom workflow, git, changelog, CI, testing
philosophy, security posture, agentic memory) that benefit any project. JVM
(Kotlin/Java) was previously a stated first-class concern; that commitment has
been withdrawn pending proper coverage being written. See `decision:0004`.

## Language coverage

Ruby is the primary language with dedicated instruction files. Other languages
are not explicitly covered; agents working on non-Ruby projects should rely on
general instructions and their own knowledge of ecosystem conventions.

If JVM coverage is added in the future: Java 21 LTS minimum, Gradle Kotlin DSL
preferred. JVM 8/11 compatibility shims are not supported. This guidance is
relevant for anyone setting up JVM-based services (e.g., for integration testing)
regardless of whether full JVM project coverage exists.

## Minimal infrastructure, transparent state

Prefer solutions that require no persistent daemon processes. `CONTEXT.md`
and Loom records are the default cross-session memory mechanism. Richer
memory infrastructure (e.g., mem0) may be added only after explicit
deliberation with a recorded decision.

## Convention over custom

Lean on community conventions: RSpec for Ruby tests, StandardRB for Ruby
style, GitHub Actions for CI, Keep a Changelog format. Deviating from
convention requires a recorded decision.

## Completeness of coverage

Agent guidance must cover: documentation, project layout, ADRs, testing,
coverage enforcement, Docker-based integration testing, CI, linting, process
automation, and release. Gaps in coverage are tracked as open tickets, not
silently ignored.

## Skills over inline prose

Repeatable, bounded workflows (release, lint-fix, ADR creation, integration
test lifecycle) are encoded as OpenCode skills, not repeated in prose. Skills
are the primary reuse mechanism.

## Honest uncertainty

Open questions and unresolved assumptions are recorded explicitly in
`OPEN_QUESTIONS.md` (project-level) and as Loom open constitutional
questions below. An agent should never silently resolve a policy gap by
guessing; it should note the gap.

# Constraints

- **Ruby-first**: Deep coverage for Ruby gems. General conventions apply to any project. JVM (Kotlin/Java) coverage is not currently available — see `decision:0004`. Frontend, full-stack, and data-pipeline projects are out of scope.
- **No C extensions**: Ruby gem guidance assumes pure-Ruby gems. `rake-compiler`
  and native extension tooling are out of scope unless explicitly added.
- **No legacy JVM targets**: If JVM projects are added, Java 21 LTS minimum applies.
  JVM 8/11 compatibility shims are not supported.
- **GitHub Actions only**: No other CI system is supported. Jenkins, CircleCI,
  GitLab CI guidance belongs elsewhere.
- **GitHub Copilot only**: The owner's AI access is GitHub Copilot via OpenCode.
  Suggestions requiring direct API access to other providers (Anthropic, OpenAI,
  etc.) must be flagged as unsupported.
- **Loom is the workflow layer**: This workspace uses Loom for all durable
  execution tracking. Ad-hoc todo files and informal chat-only plans are not
  acceptable substitutes for Loom records when the work is non-trivial.

# Strategic Direction

1. **Complete and stabilize `mlibrary-agentic-scaffold/`** — the existing instruction
   files, skills, and templates represent a substantial first pass. The next
   priority is audit, gap-fill, and quality hardening.

2. **Integrate Loom as the governing workflow layer** — all future work on
   this workspace runs through Loom records. The `mlibrary-agentic-scaffold/` system
   itself is the subject of Loom-tracked work.

3. **Grow the skills library** — skills are more valuable than prose instructions
   because they are invocable on demand. Expand skills coverage as new
   repeatable workflows are identified.

4. **Resolve open questions** — the items in `OPEN_QUESTIONS.md` and below are
   real policy gaps. Each one should become either a decision record or a
   confirmed constraint before the mlibrary-agentic-scaffold system is considered stable.

5. **Establish a review cadence** — `agentic-memory.md` and MCP recommendations
   should be revisited quarterly; the agentic tooling landscape changes fast.

# Current Focus

Initializing the Loom workspace for `agentstuff` and recording the project's
existing state accurately so future agents can enter cold and orient without
reading the full session transcript.

# Open Constitutional Questions

1. **Ruby type checking** — Resolved. Sorbet adopted for AI-agent-authored
   type annotations. See `decision:0002`.

2. **Agentic memory tier** — Resolved. Three-tier model adopted. Tier 1:
   CONTEXT.md + Loom records (mandatory). Tier 2: `@modelcontextprotocol/server-memory`
   (zero-infra, `npx` install). Tier 3: mem0 (conditional, Python + Docker).
   `memory-mcp-lite` deferred for review late June 2026. See `decision:0001`.

3. **Semantic Release** — Deferred. Manual release workflow sufficient for now.
   No evaluation done. Activate `ticket:semantic-release` if manual releases
   become a burden. No blocking action required.

4. **Multi-module JaCoCo aggregation** — Moot (JVM coverage withdrawn). Closed.

5. **GraalVM native image** — Deferred (moot until JVM coverage is restored).
   Reactivate if a JVM project requires a native binary.

# Change History

- 2026-04-30: Initial constitution created during Loom workspace bootstrap.
- 2026-04-30: Open question #2 (agentic memory tier) resolved. `@modelcontextprotocol/server-memory` adopted as Tier 2, replacing initially-proposed memU (prior characterization of memU was factually incorrect). See decision:0001 amendment.
- 2026-04-30: Softened "Libraries only" to "Library-first, backend-friendly." Backend services now welcome with caveat of reduced coverage. Frontend/data-pipeline remain out of scope. "No Rails" and "No frontend" hard constraints removed; replaced with coverage-scoping language.
- 2026-05-01: Scope pulled back to Ruby + general-purpose. JVM (Kotlin/Java) withdrawn as a first-class concern pending proper coverage being written. JVM instruction file, JVM template, and JVM split tickets removed. ADR-008 superseded. See decision:0004.
