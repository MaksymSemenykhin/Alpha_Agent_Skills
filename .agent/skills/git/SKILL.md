
---
id: git-commits
modes: git
priority: 10
commit_types: feat, fix, refactor, test, docs, chore
commit_scope: client
commit_language: en
commit_min_summary_chars: 4
example: feat(client): add git mode commit guards
---

# Git commits

Split work into logical slices — one concern per commit.

## Message format

- Prefix: feat | fix | refactor | test | docs | chore
- Optional scope in parentheses, e.g. `(client)`
- Short one-line summary (English by default)
- No vague folder summaries like "changes in components"

## Examples

- `feat(client): add git mode commit guards`
- `fix: handle provider billing errors`
- `chore(client): bump version`
