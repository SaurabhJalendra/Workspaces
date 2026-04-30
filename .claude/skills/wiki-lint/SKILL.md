---
name: wiki-lint
description: Health-check the wiki/ for contradictions, orphan pages, stale claims, missing cross-references. Run periodically to keep wiki accurate.
allowed-tools: [Read, Grep, Glob, WebSearch]
---

# Wiki Lint

## Steps
1. Read `wiki/index.md`
2. For each page: inbound links? cites sources? referenced pages in index?
3. Cross-check claims: contradictions between pages, claims without source citations
4. Identify gaps: concepts mentioned but lacking page, entities referenced but undocumented
5. Report with page references and suggested fixes

## Output
- Contradictions table
- Orphan pages list
- Stale claim list (e.g., "decision X superseded by Y but page still says X is current")
- Missing cross-reference suggestions
- Fix priority ranking
