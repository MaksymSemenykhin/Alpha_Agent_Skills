---
id: context-links
namespaced_id: alpha.context-links
modes: code, plan, run, git, analysis, ask
priority: 8
---

# Project context links (Связи)

Project skill diagnostic name: **alpha.context-links**. Runtime tool contract when memory changes are explicitly requested: `manage_context_link`.

Project memory: **associations** the engine stores between named things — entities, task patterns (`modify:…`), symbols, repo paths, multi-endpoint pairs, and whatever else the project records. File: `.agent/context/entity-links.json`. UI: **Связи**.

## Modes

| | All modes | Code mode |
|---|-----------|-----------|
| Read links (prompt snapshot, search boost) | yes | yes |
| Agent changes links (`manage_context_link`) | no | yes |
| User changes links (**Связи** page) | yes | yes |

Use memory in every mode. Do not re-search for paths the snapshot already gives you.

## Link shapes

- `entity/task -> file`: direct memory that this entity or task pattern usually uses a specific file.
- `entity/task -> folder`: stored as `relation: "container"` with a trailing slash path such as `moduls/`. Search may lightly boost files inside that folder. Treat this as weaker than a direct file link: useful for nearby candidates, not proof that every file in the folder is relevant.
- `file <-> file`: stored as `relation: "paired"` with `pairedArtifactPath`; use when two files are commonly read or edited together.

When a snapshot lists `folder`, expect search results from files under that folder to be memory-assisted. Prefer structural path reasoning (`dirname`, prefix containment, scoped `list_files` / `search_text`) over natural-language guesses.

## manage_context_link

Call only in **Code mode**, only when the user **explicitly** asks to change memory.

- `confirm` — store or strengthen a link
- `reject` — weaken or remove a link

Skip after normal search/edit turns — the engine reinforces links on its own.

Send `targets[]` per the engine/tool contract (`entityKeys`, `artifactPath`, optional `pairedArtifactPath`). Map the user’s wording (entity↔entity, entity↔file, file↔file, task↔symbols, several keys at once) onto those fields using evidence from the Связи snapshot or recent tool output — never invent endpoints.

For folder links, use `artifactPath` with a trailing slash and no `pairedArtifactPath`, for example `{ "entityKeys": ["modify:stage_x"], "artifactPath": "moduls/" }`.

## Project-specific rules

<!-- project-specific hints -->
