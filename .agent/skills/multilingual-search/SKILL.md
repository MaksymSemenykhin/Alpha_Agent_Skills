---
id: multilingual-search
modes: code, plan, run, ask, troubleshoot, analysis
priority: 7
tools: multilingual_search
---

# multilingual_search

Read-only expanded project search for natural-language or mixed Russian/English terms.

`multilingual_search({ query, path?, maxMatches?, translit?: true })`

Use when the user's wording may not match repository text literally:

- Natural-language Russian or English queries for code/UI concepts.
- Mixed RU/EN tasks where source code may contain English identifiers but the user names the feature in Russian, or the reverse.
- After `search_text` returns few or zero useful matches for a concept, especially labels, settings, UI copy, or domain terms.

The tool starts with exact, structural, and transliteration variants, then may run an auxiliary model to add up to 8 concrete variants:

- `translation` - translated terms between Russian and English.
- `identifier` - likely code identifiers.
- `localization` - likely i18n keys or UI text variants.
- `translit` - transliterated spellings.

Prefer concrete repository terms over broad generic words. Disable transliteration with `translit: false` only when translit noise is likely to swamp the search. After matches, continue with `read_file` on the best ranked paths before editing or answering from code.
