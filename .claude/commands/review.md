---
description: Review my current changes like a senior engineer
---

Review my uncommitted changes and any commits on this branch that are not on
`main` (use `git diff` and `git diff main...HEAD`).

Review against `CLAUDE.md` conventions and general good practice. Group
findings as:

- **Must fix:** bugs, security problems, broken conventions, missing tests for
  important behaviour.
- **Should consider:** naming, structure, clarity, edge cases.
- **Nice:** what is done well, so I know to keep doing it.

For each finding, point to the file and line and explain why it matters.
Do not change any files. If everything looks good, suggest a Conventional
Commits message for the change.
