---
id: multilingual-search
namespaced_id: alpha.multilingual-search
modes: code, plan, run, ask, troubleshoot, analysis
priority: 7
tools: multilingual_search
---

# multilingual_search

Project skill diagnostic name: **alpha.multilingual-search**. Runtime tool contract: `multilingual_search`.

Read-only expanded project search for natural-language terms across supported languages.

`multilingual_search({ query, path?, maxMatches?, translit?: true })`

Use when the user's wording may not match repository text literally:

- Natural-language queries for code/UI concepts in any language supported by the expansion backend.
- Multilingual tasks where source code, UI copy, localization, or user wording may use different languages.
- After `search_text` returns few or zero useful matches for a concept, especially labels, settings, UI copy, or domain terms.

The tool starts with exact, structural, and transliteration variants, then may run an auxiliary model to add up to 8 concrete variants:

- `translation` - translated terms across supported languages.
- `identifier` - likely code identifiers.
- `localization` - likely i18n keys or UI text variants.
- `translit` - transliterated spellings.

Prefer concrete repository terms over broad generic words. Disable transliteration with `translit: false` only when translit noise is likely to swamp the search. After matches, continue with `read_file` on the best ranked paths before editing or answering from code.
