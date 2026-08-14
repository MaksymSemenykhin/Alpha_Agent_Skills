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

1. **User instruction this turn** — what they asked for now (e.g. "commit all", "English only", "split separately", "do not push").
2. **Recent repo history** — last 5–10 commits from `git log` (language, prefix types, scopes, tone).
3. **Other project git skills** — additional files under `.agent/skills/git/` or with `modes: git`.
4. **Defaults in this skill** — `commit_types`, scope hint, examples in frontmatter.

When the user asks for a **target language** for messages (e.g. translate commits to English), use that language for all new or rewritten messages this turn — even if older commits in `git log` are in another language.

## Inspect vs mutate

Infer from the user's intent — not from keywords or language.

**Inspect** — user wants to understand the working tree (what changed, whether something is needed):

- Read-only git: `git status`, `git diff`, `git log`.
- Reply in action `"answer"` with findings.
- Do **not** run `git add`, `git commit`, or `git push`.

**Mutate** — user explicitly asked for version-control actions (commit, split commits, rewrite messages, push, stash, branch):

- Follow execution order and planning sections below.
- **Do not ask again** with option menus (commit / delete / something else) — the request is already clear.
- **Do not use ask_user** for git confirmations — proceed with slices or report blockers in action `"answer"`.

## Push

Do not run `git push` automatically after commit — only when the user asked to push in this dialog (or another project git skill says otherwise).

## Execution order (mutate only)

1. `git status --short` — list every dirty path (engine may prefetch batched diffs automatically — see **Batched diff inspection**).
2. `git log -10 --oneline` (or `git log -10`) — **read before writing any commit message.** Note the repo's language, prefix pattern, scopes, and tone (unless the user specified a target language this turn).
3. Plan slices — logical commit groups before staging (see **Planning commit slices**).
4. Per slice: `git add` (specific paths) → `git commit -m "…"` → `git status --short` → next slice if needed.
5. **Verify before answer** — see **Finish mutating git**; do not push unless the user asked in this dialog.

## Batched diff inspection

**Do not** run `git diff <one-file>` in a loop — that wastes turns and hits the read-only git budget.

After `git status --short`, the engine may prefetch batched diffs for you (`--stat`, staged/unstaged path batches, untracked). **Use those results** for slice planning.

When you run diff yourself (inspect-only turns, or prefetch missed a path):

1. `git diff --stat` and `git diff --cached --stat` first.
2. One command for many paths: `git diff -- path1 path2 path3 …` (up to ~8 paths per call).
3. Never per-file diff loops — batch by slice or by size.

If 20 files changed, two or three batched `git diff -- …` calls beat twenty single-file calls.

## Finish mutating git (any operation)

After **any** mutating git command this turn (`add`, `commit`, `rebase`, `stash`, `merge`, `checkout`, `reset`, …):

1. Run `git status --short` (or `-s`) — **must be empty** before action `"answer"`. Plain `git status` does not count.
2. If history changed (`commit`, `rebase`, …): run `git log -N --oneline` and report per **Report results**.

**Commit all / everything:** when the user asked to commit all changes, every path from the initial `git status` must reach `git commit` (in one or several slices). Partial completion + answer is **forbidden** — e.g. 8 files dirty, 4 committed, 4 left → continue, do not answer.

This applies to commits, rebases, stashes, and other git work — not only `git commit`.

## Planning commit slices

**«Отдельно» / «separately» = separate logical concerns** (not necessarily one file — per-file commits are fine if that is the slice).

Before the first `git add`, run `git status --short` and list **every** dirty path.

1. Group paths that belong to one concern (optional — for cleaner history).
2. `git add` → `git commit` per slice until **status is empty**.
3. After **each** commit: `git status --short` — if any path remains, continue; **never answer while paths remain** when the user asked to commit.

**Wrong:** user asked to commit all / everything, you committed some files and answered while others are still in `git status --short`.

**OK:** one file per commit, as long as **every** dirty path was committed before answer.

### Example (multi-file git-mode work)

| Slice | Paths (one `git add`) | Message idea |
| --- | --- | --- |
| Skill docs | `docs/templates/.../SKILL.md`, `client/src/shared/git-commit-conventions.ts` | docs + default skill sync |
| Turn guards | `client/src/shared/agent-turn-guards.ts`, `client/src/shared/agent-turn-guards.test.ts` | guards + tests |
| Agent loop wiring | `client/src/main/lib/agent/agent-loop.ts`, `agent-context.ts`, `agent-tool-runner.ts`, `agent-types.ts` | engine git slice gates |
| Workflow prompt | `client/src/shared/workflow-mode.ts` | git session prompt line |

When the user asked to split into separate commits:

- One concern per commit — never one commit for unrelated areas (e.g. agent loop, shared guards, skill docs = separate commits).
- Stage **specific paths only** — never `git add -A`, `git add .`, or `git add --all`.
- Separate `git add` + `git commit` per slice.
- After each commit run `git status --short`; while changes remain, start the next slice.
- Each message describes that slice only; one language/style — never mix languages in one message.

## Rewrite commit messages (history)

When the user asks to rename, reword, or translate **existing** commits:

- This is **mutate** — run real git commands; **never** claim success from memory or prior turns.
- Run `git log -N --oneline` **before** and **after**; report only hashes and messages from the **after** log.
- **HEAD only:** `git commit --amend -m "new message"`.
- **Last N commits:** use non-interactive history rewrite (`git rebase -i HEAD~N` with `GIT_SEQUENCE_EDITOR` / `GIT_EDITOR`, or another non-interactive flow). If rebase fails, report the error in action `"answer"` — do not invent hashes.
- After rewrite, run `git log -N --oneline` and confirm each target commit shows the new message before saying "updated".

## Report results (final answer after mutate)

**Always:** confirm step 1 in **Finish mutating git** (clean `git status --short`).

**When commits were created or rewritten this turn:**

1. Run `git log -N --oneline` (N = number of commits this turn).
2. Reply with **only** a bullet list — one line per commit, **message then short hash**, copied from that log output.

**Required format (every commit):**

- `feat(client): add git mode commit guards` — `ab12cd3`
- `fix(client): block ask_user in git mode` — `c4e5f67`

Rules:

- **Full commit message** (not a paraphrase) + **short hash** from `git log` — same order on every line.
- **One commit** → still one bullet line (not prose like «создан коммит…»).
- **Several commits** → one bullet per commit; no extra summary before the list unless the user asked for context.
- **Forbidden:** summary without the list — e.g. `успешно закоммичены`, `закоммитил отдельно`, `все изменения закоммичены` **without** message + hash on each line.
- Never invent hashes — only lines present in `git log` output.

Non-commit git work (stash, branch, etc.): brief factual line — what ran and `git status` result; no fake commit list.

Keep the answer concise; no push unless the user asked.

## Message format — match `git log`

**Always read `git log` before the first commit.** Your messages should look like they belong in that history — not like a generic template.

### One language, one style

- Pick **one language** (user instruction overrides when they asked for a target language).
- Pick **one prefix pattern** from the log (e.g. `feat:` everywhere, or no prefix at all — follow the repo).
- Pick scope usage from the log (always `(client)`, sometimes, or never).
- Write in the same tone (imperative, past tense, sentence case — mirror what you see).

### Forbidden

- **Mixed languages in one message** — e.g. `feat: actualizar обработку коммits`.
- **Filename-only summaries** — the message must describe the change, not repeat the path.
- **Copying examples from this skill file** when `git log` shows a different style — frontmatter `example` is fallback only.
- **Inventing a new style** when history is available — parrot the repo, do not improvise.

### Good workflow

1. Scan `git log -10` — note dominant language and format (unless user specified target language).
2. Draft each slice message in **that** language and **that** format.
3. Re-read before `git commit` — would this message fit between the last 10 commits? If not, rewrite.

### Other rules

- Short one-line summary for **this slice only**.
- No Cursor attribution trailers.
- **Never ask the user to write or fix commit messages** — adjust from `git log` and retry yourself.

### Illustrative (do not copy — adapt to your repo's `git log`)

If log shows `feat(client): add git mode commit guards` → write similar English `feat(client): …` messages.

If log shows `fix: исправлен роутинг моделей` → write similar Russian `fix: …` messages.

If log shows no prefix, only `Обновлён skill git` → do not add `feat:` just because this skill mentions it.

## More git skills

Add files under `.agent/skills/git/` (or `.agent/skills/` with `modes: git`).

## Frontmatter

- `commit_types`, `commit_scope`, `commit_min_summary_chars`, `commit_message_pattern`, `example`
