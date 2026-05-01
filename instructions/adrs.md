# ADR Workflow

Architecture Decision Records (ADRs) are short documents that capture important
architectural decisions, the context in which they were made, and their consequences.

---

## When to Write an ADR

Write an ADR when you are:
- Choosing a serialization format or protocol
- Deciding on a dependency that affects the public API
- Choosing a concurrency model
- Making a decision that trades one quality attribute for another (e.g., performance vs. simplicity)
- Making a decision that future maintainers will likely question

When in doubt: if you'd explain it in a PR review comment, it probably deserves an ADR.

---

## File Location and Naming

```
docs/adrs/
├── ADR-000-template.md      # Template; never delete
├── ADR-001-some-decision.md
├── ADR-002-another-topic.md
└── ...
```

Naming convention: `ADR-NNN-short-hyphenated-title.md` where `NNN` is zero-padded
to three digits and increments from `001`.

---

## Lifecycle

ADRs have one of these statuses:

| Status | Meaning |
|--------|---------|
| `Proposed` | Under discussion; not yet accepted |
| `Accepted` | The decision is in effect |
| `Deprecated` | No longer applies (replaced or situation changed) |
| `Superseded by ADR-NNN` | A newer ADR replaces this one |

Never delete an ADR. Deprecate or supersede it instead.

---

## Using the Template

The template lives at `docs/adrs/ADR-000-template.md`. Use the `adr-create` skill
to scaffold a new ADR automatically. Or manually:

1. Copy `docs/adrs/ADR-000-template.md` to `docs/adrs/ADR-NNN-title.md`
2. Fill in all sections
3. Set status to `Proposed`
4. After team/owner review, change to `Accepted` and commit

---

## Agent Rules

- When making a significant architectural choice, proactively ask whether it deserves an ADR.
- When asked to create an ADR, use the `adr-create` skill.
- When reading existing ADRs, respect `Accepted` decisions. If a decision is wrong,
  propose superseding it rather than silently working around it.
- Reference ADR numbers in code comments when the code embodies a non-obvious decision:
  `# See docs/adrs/ADR-003-choosing-msgpack.md`
