---
id: context-links
modes: code, plan, run, git, analysis, ask
priority: 8
---

# Связи проекта (project context links)

**Execution contract** (read-only snapshot in every workflow mode, `manage_context_link` allowed **only in Code mode**, UI on the **Связи** page) lives in the engine — this skill defines **when and how** to strengthen or weaken links.

## What is stored

Project memory: which **task / entity** leads to which **files**. Persisted in `.agent/context/entity-links.json`.

The system prompt already lists current links (read-only). In **Code mode** you may change them with `manage_context_link` when the user explicitly asks.

## Tool shape (engine — do not redefine)

`manage_context_link` accepts:

- **action** — `confirm` (strengthen / remember) or `reject` (weaken / remove)
- **targets** — one or more entries, each with:
  - **entityKeys** — usually one key, e.g. `modify:stage_x` from the user prompt
  - **artifactPath** — file path from recent `search_text` / `edit_file` / read-only snapshot — never invent
  - **pairedArtifactPath** (optional) — second file for js↔sql (or similar) pairs

## When to call

Only when the user **explicitly** asks about memory / links:

- remember, strengthen, link task to file («запомни», «усиль», «свяжи с файлом»)
- forget, weaken, remove («убери связь», «забудь», «ослабь»)
- «this is always in `moduls/stageIds.js`» after a successful search or edit

Do **not** call after every edit — the engine reinforces links automatically after successful runs.

Do **not** call in ask / git / run / plan — engine blocks it; user must switch to **Code** or use the **Связи** page.

## Three link shapes

1. **Task → file** — entity key from prompt identifier (`modify:…`) + path from context
2. **Entity → file** — bare entity name + one path
3. **File pair** — `artifactPath` + `pairedArtifactPath` for files that belong together

## Examples (user phrasing)

| User says | Action |
|-----------|--------|
| «Запомни, что stage_x — это filters/a.js» | confirm — key from identifier, path from context |
| «Убери связь filters/old.js» | reject — path from Связи snapshot or recent search |
| «Эти два файла всегда вместе» | confirm with pair paths |

## Customize for this project

Add rules below — the engine does not overwrite this section:

- Where stage constants live (e.g. `moduls/stageIds.js`)
- Typical js/sql pairs for your filters
- Entity names that repeat across tasks

<!-- project-specific hints -->
