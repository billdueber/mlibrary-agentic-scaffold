---
name: adr-create
description: Scaffold a new Architecture Decision Record (ADR) for a library project, assign the next sequential number, and open it for editing.
license: MIT
compatibility: opencode
metadata:
  audience: library-maintainers
  workflow: documentation
---

## What I Do

1. Find the `docs/adrs/` directory (create it if it doesn't exist).
2. Determine the next ADR number by scanning existing files.
3. Ask for a short title if not provided.
4. Copy the ADR template to `docs/adrs/ADR-NNN-short-title.md`.
5. Pre-fill the Date field with today's date.
6. Report the new file path so you can open it.

## Template Location

I use the template at `docs/adrs/ADR-000-template.md`. If it doesn't exist in your
project, I'll use the master template from your mlibrary-agentic-scaffold system. Copy the template
to your project with:

```bash
cp [SCAFFOLD_DIR]/templates/ADR-000-template.md docs/adrs/
```

## Usage

Say: `create an ADR about choosing msgpack over JSON`

I will:
- Find the next number (e.g., ADR-004)
- Create `docs/adrs/ADR-004-choosing-msgpack-over-json.md`
- Fill in today's date
- Leave Status as `Proposed`
- Leave you to fill in Context, Decision, and Consequences

## After Creation

1. Fill in the ADR sections.
2. When the decision is settled, change Status to `Accepted`.
3. Commit the file alongside the code that implements the decision.
4. Reference the ADR in code comments where the decision is embodied.

## Rules

- Never skip the Context section — it's the most important part.
- Never delete an ADR; deprecate or supersede instead.
- One decision per ADR.
