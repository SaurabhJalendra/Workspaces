---
name: spec
description: Cat Wu's "spec-as-prototype" pattern — write a thin spec, then immediately have Claude Code prototype it. The prototype IS the spec. Bridges idea → working code without a 20-page PRD.
allowed-tools: [Read, Write, Edit, Bash, Grep, Glob, Agent, Skill]
---

# /spec — Spec-as-Prototype (Cat Wu Pattern)

> "We don't use Google Docs much on our team. The source of truth is the code base."
> — Cat Wu, Head of Product, Claude Code

The spec is a paragraph, not a document. The prototype is the spec. If Claude can build it, the spec was good enough. If not, the spec needs sharpening.

## When to invoke

**Auto-fire (Tier 2):**
- User says "let's build [feature]" for a non-trivial feature
- User starts describing an idea in 3+ sentences
- New feature request before any code is written

**User-invoked:**
- `/spec [one-line description]` — write thin spec, generate 2-3 prototypes
- `/spec` (no args) — interactive: ask user for the idea, then proceed

## Workflow (the whole thing fits in 30 minutes for a small feature)

### Phase 1 — Write the thin spec (5 min)

Save to `specs/[feature-name]-[YYYY-MM-DD].md`. Single page max:

```markdown
# Spec: [Feature Name]
**Date:** YYYY-MM-DD
**Status:** drafting / prototyping / shipped / parked

## What
[1-2 sentence description. Plain language. Not technical.]

## Why
[1 sentence: what user pain or opportunity does this solve? Skip if obvious.]

## Who
[1 line: which user / use case]

## How (preliminary — will be refined by prototype)
[3-5 bullet points. Not implementation details. User-visible behavior.]

## Success Criteria
[1-3 bullet points. Specific. Measurable if possible.]

## Out of Scope (CRITICAL)
[1-3 bullets of what this explicitly is NOT. Prevents scope creep.]

## Open Questions
[Things the prototype will answer.]
```

### Phase 2 — Prototype 2-3 versions (15 min)

Boris Cherny's pattern: 20 prototypes for the todo-list feature in 2 days. Solo equivalent: 2-3 prototypes per spec.

For each prototype, vary the approach:
- Variant 1: simplest possible implementation
- Variant 2: with the "obvious" extension (auth, persistence, etc.)
- Variant 3: a different angle entirely (different data model, different UX)

Use git worktrees if needed (`muxtree spec-[name] 3`).

### Phase 3 — Compare & decide (5 min)

Side-by-side: which prototype best meets the success criteria?

```markdown
## Prototype Comparison

| Variant | Approach | Pros | Cons | Fits success criteria? |
|---|---|---|---|---|
| A | minimal | fast, simple | missing X | partially |
| B | with auth | secure, prod-ready | overkill for MVP | yes but heavy |
| C | event-based | extensible | complexity | yes |

## Decision
[Pick one. State why. Note what to bring from the others.]
```

### Phase 4 — Ship the chosen variant (5 min)

- Move chosen prototype code into the project (or keep in dedicated branch)
- Update `specs/[feature]-[date].md` Status to "shipped" or "prototyping" if iterating
- If shipped: enter Internal-DAU phase (use it yourself for 7 days before any public mention)
- Update `wiki/log.md`: `## [YYYY-MM-DD] spec | [feature] | [variant chosen]`

## Output Path

`specs/[YYYY-MM-DD]-[feature-name].md` (per project)

## Rules

- **Spec is < 1 page.** If it grows beyond, you're writing a PRD instead of a spec. Cut.
- **Prototypes are throwaway by default.** They prove the idea works, not the architecture.
- **2-3 prototypes is the sweet spot.** 1 = no comparison. 5+ = analysis paralysis.
- **Out-of-Scope section is mandatory.** Without it, scope creeps during prototyping.
- **If the prototype reveals the spec was wrong:** update the spec. Don't pretend the original was right.
- **Don't write prototype code without writing the thin spec first.** Even one paragraph forces clarity.

## Anti-Patterns

- **Skipping the spec** because "this is simple" — even simple features benefit from named success criteria
- **Writing 5-page PRD-style spec** — defeats the purpose; you'd never get to prototyping
- **Prototyping in main** — use a worktree or feature branch, prototypes are exploratory
- **Marrying the first prototype** — always build at least 2; comparison reveals what matters
- **Treating prototype as production code** — it's not; prototype proves the idea, then you refactor for prod

## Integration

- After `/spec` decision → optionally call `/coordinator` for non-trivial implementation
- After `/spec` ship → start Internal-DAU 7-day clock
- After 7-day Internal-DAU → if you used it daily, `/launch` for public release
- If spec is parked: move file to `specs/_parked/`, append to wiki/log.md

## When NOT to use

- Typo / one-line bug fix
- Refactor that preserves behavior (use `simplify` instead)
- Pure backend internal change with no user surface
