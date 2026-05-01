---
id: ticket:ci-floating-action-tags
kind: ticket
status: closed
change_class: documentation-explanation
risk_class: low
created_at: 2026-04-30T00:00:00Z
updated_at: 2026-04-30T00:00:00Z
scope:
  kind: workspace
links:
  plan:
    - plan:mlibrary-agentic-scaffold-stabilize
depends_on:
  - ticket:audit-instructions
---

# Summary

Two instruction files contain floating (non-SHA-pinned) GitHub Action tags in
code examples, contradicting the SHA-pinning rule established in
`ci-github-actions.md` and `core.md`.

Affected examples:
- `changelog-automation.md`: `orhun/git-cliff-action@v4`
- `ci-github-actions.md`: same example in the changelog integration section

# Acceptance Criteria

1. Every GitHub Action `uses:` example in `ci-github-actions.md` uses a full
   SHA pin with a comment showing the tag (e.g., `@abc123def # v4.1.0`).
2. Every GitHub Action `uses:` example in `changelog-automation.md` uses a
   full SHA pin with a tag comment.
3. No `@v*` floating tags remain in any instruction file Action examples.

# Notes

Use the actual current SHA for `orhun/git-cliff-action` at the time of the fix.
The SHA in the fix must match a real published release.

# Journal

- 2026-04-30: Created during ticket:audit-instructions audit. Floating tag in
  example violates the SHA-pinning rule the same file teaches.
