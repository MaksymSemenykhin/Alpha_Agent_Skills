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

Git-mode **execution contract** (status verify, diff prefetch, final bullet list) lives in the engine and the git system prompt — this skill defines **commit conventions** and overrides only.

## Priority (when rules conflict)

1. **User instruction this turn** — e.g. "commit all", "English only", "split separately", "do not push".
2. **Recent `git log`** — last 5–10 commits (language, prefixes, scopes, tone).
3. **Other project git skills** — `.agent/skills/git/` or `modes: git`.
4. **Frontmatter below** — `commit_types`, scope, example.

User-requested target language overrides log language for new messages this turn.

## Inspect vs mutate

Infer from intent — not keywords.

- **Inspect:** `git status`, `git diff`, `git log` → action `"answer"` with findings. No `add`/`commit`/`push`.
- **Mutate:** user asked for commits, split, rewrite, push, stash, branch → run git; no ask_user or option menus.

## Push

No `git push` unless the user asked in this dialog (unless another git skill says otherwise).

## Mutate workflow (summary)

1. `git status --short` — every dirty path.
2. `git log -10` — read before first commit message.
3. Plan slices; per slice: `git add` (specific paths) → `git commit -m "…"`.
4. Engine verifies tree after commits — continue while paths remain; answer only when status is clean.
5. Batch diffs (`git diff --stat`, `git diff -- path1 path2 …`) — never per-file diff loops.

**Commit all:** every path from step 1 must reach `git commit` before answer.

**Separate commits:** one concern per slice; never `git add -A` / `git add .`; specific paths only.

## Rewrite existing messages

- Run `git log` before and after; report only **after** hashes/messages.
- HEAD: `git commit --amend -m "…"`.
- Last N: non-interactive rebase (`GIT_SEQUENCE_EDITOR` / `GIT_EDITOR`). Report errors — do not invent hashes.

## Final answer (commits)

Bullet list only — one line per commit from `git log`:

- `feat(client): add git mode commit guards` — `ab12cd3`

Full message + short hash; no prose «готово» without the list. Never invent hashes.

## Message format

- Read `git log` before the first commit; match repo style (language, prefix, scope, tone).
- One language per message; no mixed languages.
- Describe the change — not the filename alone.
- No Cursor attribution trailers.
- Never ask the user to write messages — adjust from log and retry.

Frontmatter `example` is fallback only when log is empty or ambiguous.

## More git skills

Add under `.agent/skills/git/` or `.agent/skills/` with `modes: git`.
