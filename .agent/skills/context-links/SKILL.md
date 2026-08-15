---
id: context-links
modes: code, plan, run, git, analysis, ask
priority: 8
---

# Project context links (Связи)

Persistent memory in `.agent/context/entity-links.json`: **keys** (entities, task patterns like `modify:…`, etc.) and **files** wired together. Read-only snapshot is already in the system prompt; UI: **Связи**.

## When to call manage_context_link

User **explicitly** asks to **change** stored links.

- `confirm` — add or raise trust
- `reject` — lower trust or drop

No routine calls after search/edit — engine reinforces automatically.

**Code mode only**; otherwise point to **Code** or **Связи**.

## Targets (mix in one call)

Each target: `entityKeys[]`, `artifactPath`, optional `pairedArtifactPath`. Paths from recent tools or the Связи snapshot — never invent.

| Shape | Fields |
|-------|--------|
| Key(s) → file | `entityKeys` + `artifactPath` |
| File ↔ file | `artifactPath` + `pairedArtifactPath` (pair can also carry `entityKeys`) |

One `entityKeys` entry can be a bare entity, a task pattern, or several keys for the same file.

## Examples

| User intent | Action |
|-------------|--------|
| Save / keep this mapping | confirm |
| Drop / stop using this mapping | reject |
| Two files belong together | confirm + `pairedArtifactPath` |

## Project-specific rules

<!-- project-specific hints -->
