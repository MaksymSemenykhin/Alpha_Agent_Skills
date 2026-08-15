---
id: context-links
modes: code, plan, run, git, analysis, ask
priority: 8
---

# Project context links (Связи)

Project memory: **associations** the engine stores between named things — entities, task patterns (`modify:…`), symbols, repo paths, multi-endpoint pairs, and whatever else the project records. File: `.agent/context/entity-links.json`. UI: **Связи**.

## Modes

| | All modes | Code mode |
|---|-----------|-----------|
| Read links (prompt snapshot, search boost) | yes | yes |
| Agent changes links (`manage_context_link`) | no | yes |
| User changes links (**Связи** page) | yes | yes |

Use memory in every mode. Do not re-search for paths the snapshot already gives you.

## manage_context_link

Call only in **Code mode**, only when the user **explicitly** asks to change memory.

- `confirm` — store or strengthen a link
- `reject` — weaken or remove a link

Skip after normal search/edit turns — the engine reinforces links on its own.

Send `targets[]` per the engine/tool contract (`entityKeys`, `artifactPath`, optional `pairedArtifactPath`). Map the user’s wording (entity↔entity, entity↔file, file↔file, task↔symbols, several keys at once) onto those fields using evidence from the Связи snapshot or recent tool output — never invent endpoints.

## Project-specific rules

<!-- project-specific hints -->
