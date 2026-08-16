---
id: search-text
namespaced_id: alpha.search-text
modes: code, plan, run, ask, troubleshoot, analysis
priority: 7
tools: search_text
---

# search_text

Project skill diagnostic name: **alpha.search-text**. Runtime tool contract: `search_text`.

Read-only exact text search inside the open project workspace.

`search_text({ query, path?, maxMatches?, translit?: false })`

Use `search_text` before shell search commands when looking for repository text:

- Symbols, function/class names, file names, config keys, routes, error strings, and exact phrases from the user or code.
- Project-relative `path` scopes the search to a file or directory; omit it for repo-wide search.
- `maxMatches` caps returned match lines; the engine also applies a hard project limit.
- `translit: true` adds deterministic RU<->LAT variants for legacy transliterated names such as `zadacha`, `sessiya`, or Cyrillic equivalents.

The tool already tries structural variants such as camelCase and underscore token splits. If it returns likely candidates, do not repeat the same query; continue with `read_file` on the best ranked paths before editing or answering from code.
