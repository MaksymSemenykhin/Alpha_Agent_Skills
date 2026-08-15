---
id: git-commits
modes: git
priority: 10
commit_types: feat, fix, refactor, test, docs, chore
commit_scope: client
commit_min_summary_chars: 4
commit_language: en
example: feat(client): add git mode commit guards
author_name: Alpha Agent
author_email: AlphaAgent@example.com
---

# Git commits

## Priority hierarchy
1. User instruction this turn.
2. Recent repo history (last 5–10 commits).
3. Other project git skills.
4. Defaults in this skill.

When a target language is specified, use it for all new or rewritten messages.

## Inspect vs mutate
- **Inspect**: Read-only actions (e.g., `git status`, `git diff`, `git log`).
- **Mutate**: User explicitly requests version-control actions (e.g., commit, push).

## Push
Only run `git push` if explicitly requested.

## Execution order (mutate only)
1. `git status --short`.
2. `git log -10 --oneline`.
3. Plan slices.
4. For each slice: `git add` → `git commit -m "..."`.
5. Verify before answer.

## Engine-owned steps
| Step | When | Your job |
| --- | --- | --- |
| **Diff prefetch** | After `git status --short` | Plan slices from batched output. |
| **Status verify** | After each commit | If paths remain, list them. |
| **Auto-finish** | After empty tree | Emit final commit list. |

## Commit all
All paths from initial `git status --short` must reach `git commit`.

## Batched diff inspection
- Avoid per-file diff loops.
- Use `git diff --stat` and batch paths.

## Finish mutating git
1. Run `git status --short` — must be empty.
2. If history changed, run `git log -N --oneline`.

## Planning commit slices
1. Group paths by concern.
2. `git add` → `git commit` per slice until empty.
3. After each commit, check status.

## Rewrite commit messages
- Run `git log -N --oneline` before and after.
- Use `git commit --amend` for HEAD only.
- Use non-interactive history rewrite for last N commits.

## Report results
1. Confirm clean `git status --short`.
2. List commits in required format.

## Message format
- Summary language follows `commit_language` in frontmatter.
- Match `git log` prefix/scope style.
- One language, one style.
- Avoid mixed languages and filename-only summaries.

## More git skills
Add files under `.agent/skills/git/` or `.agent/skills/` with `modes: git`.
