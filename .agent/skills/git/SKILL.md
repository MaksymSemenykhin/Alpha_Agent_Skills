---
id: git-commits
modes: git
priority: 10
commit_types: feat, fix, refactor, test, docs, chore
commit_scope: client
commit_min_summary_chars: 4
example: feat(client): add git mode commit guards
---

# Git commits

## Priority hierarchy

When rules conflict, follow this order (higher wins):

1. **User instruction this turn** — e.g. commit separately, do not push, amend, specific scope or wording.
2. **Recent repo history** — last 5–10 commits from `git log` (language, prefix types, scopes, message length, tone).
3. **Other project git skills** — additional files under `.agent/skills/git/` or with `modes: git`.
4. **Defaults in this skill** — `commit_types`, scope hint, and examples in frontmatter below.

Never override (1) with guesses. Never invent style when (2) is available — read `git log` first.

## Execution order

1. `git status`, `git diff`, staged diffs — see what changed.
2. `git log -10 --oneline` (or `git log -10`) — learn how this repo writes commits.
3. **Plan slices** — list logical commit groups before staging (see next section).
4. **Per slice:** `git add` only those paths → `git commit -m "…"` → repeat for next slice.
5. `git push` only when the user explicitly asked.

## Planning commit slices

Each slice = one logical concern. Decide grouping from diffs and file relationships, not from folder names alone.

When the user asked to commit separately / split commits:

- One concern per commit — unrelated changes never share one commit.
- Separate `git add` + `git commit` per slice; do not `git add -A` then one catch-all commit.
- Order slices for readable history (e.g. refactors before features that depend on them).
- Each message describes **that slice only** — what changed and why in those files.

## Message format

Derive from levels 1–2 above; use level 4 only as fallback when history is empty or ambiguous.

- Prefix: feat | fix | refactor | test | docs | chore — prefer types the repo already uses.
- Optional scope in parentheses when the repo uses scopes, e.g. `(client)`.
- Short one-line summary in the same language and tone as recent commits.
- No Cursor attribution trailers.

## Examples (adapt to `git log`, not copied blindly)

- `feat(client): add git mode commit guards`
- `fix: handle provider billing errors`
- `chore(client): bump version`

## More git skills

Add additional files under `.agent/skills/git/` (or anywhere under `.agent/skills/` with `modes: git`).

Examples:

- `.agent/skills/git/branches/SKILL.md` — branch naming / merge policy
- `.agent/skills/release-git/SKILL.md` with `modes: git` — release tagging workflow

## Frontmatter

Shared skill fields:

- `id` — stable skill name in prompts
- `modes` — comma-separated workflow modes (`git`, `code`, `plan`, …)
- `priority` — higher loads first when multiple skills match

Commit validation fields (optional, first matching skill wins):

- `commit_types`, `commit_scope`, `commit_min_summary_chars`
- `commit_message_pattern` — custom regex override for prefix/length only
- `example` — sample commit message

If no skill defines commit fields, Alpha uses the engine default conventional commit style.
