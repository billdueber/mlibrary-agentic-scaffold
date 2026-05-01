---
name: lint-fix
description: Run all configured linters for the project (StandardRB + Sorbet for Ruby) and apply auto-fixes where possible.
license: MIT
compatibility: opencode
metadata:
  audience: library-maintainers
  workflow: code-quality
---

## What I Do

Run the appropriate linter(s) for the project, apply auto-fixes, and
report any remaining issues that need manual attention.

## Ruby (StandardRB + Sorbet)

```bash
# Check style
bundle exec standardrb

# Auto-fix style
bundle exec standardrb --fix

# Type check
bundle exec srb tc
```

StandardRB is opinionated and not configurable. Accept its decisions.
If a suppression is genuinely needed on one line:
```ruby
result = thing  # standard:disable Layout/LineLength
```

Sorbet type errors require manual resolution. Explain each error and suggest a fix.
Do not add `# typed: false` to silence errors without explaining why.

## After Running

1. Show a summary of what was auto-fixed (changed files).
2. List remaining warnings/errors that need manual attention.
3. For each remaining issue, explain what it means and how to fix it.
4. Do NOT suppress a linter warning without explaining why the suppression is justified.

## Rules

- Never suppress a warning just to make the build pass.
- A clean lint output is a release prerequisite.
- If StandardRB wants to reformat something you disagree with, accept it anyway —
  consistency is more valuable than personal preference.
