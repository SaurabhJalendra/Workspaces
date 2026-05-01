---
name: tech-radar
description: Audit a technology choice — is it still the best option in 2026, or has the market moved on? Compares current choice (or proposed choice) against modern alternatives, returns stay/switch decision with trade-offs and migration cost. Fires before adding deps, choosing frameworks, writing ADRs, or quarterly stack reviews.
allowed-tools: [WebSearch, WebFetch, Read, Grep, Glob, Agent]
---

# /tech-radar — Is This Still the Best Choice?

Prevent stack drift. Anthropic's "delete code on every model release" pattern applied to dependencies and architectural choices.

## When to invoke

**Auto-fire (Tier 2, no asking):**
- About to add a new dependency (npm install, pip install, cargo add, etc.)
- About to commit `package.json`, `requirements.txt`, `go.mod`, `Cargo.toml` changes
- User says "let's use [library/framework]" for a non-trivial choice
- Writing an ADR (call `/tech-radar` first to populate Alternatives Considered)
- Quarterly stack health check (per major dependency)

**User-invoked:**
- `/tech-radar [topic]` — manual audit
- `/tech-radar` (no arg) — audit current project's stack against modern alternatives

## Workflow

### Step 1 — Establish baseline
- If user provided a topic: that's the focus
- Otherwise scan the project: detect framework, ORM, auth lib, build tool, deploy infra, key libraries
- For each item: record name, version, last release date

### Step 2 — Research (parallel, time-boxed)
Dispatch 2-3 parallel `researcher` subagents with focused queries:
- "Best [category] for [use case] in 2026" — current state of the art
- "Alternatives to [current choice]" — direct competitors with recent traction
- "Migration from [current] to [popular alt] solo dev experience" — switching cost reality

Use Context7 MCP for library-specific docs. Use WebSearch for state-of-the-art.

### Step 3 — Compare
For each alternative, fill:
- Last release date (active maintenance signal)
- GitHub stars + recent commit activity (community signal)
- Major shift since current choice was made (e.g., "v3 rewrite", "deprecation of v1")
- Specific advantage for THIS project's use case
- Specific cost (lock-in, migration effort, team learning)

### Step 4 — Decision

Write decision into output. Three categories:

- **STAY** — current choice is still best for this project. Suppress switching impulse. Cite reason.
- **WATCH** — alternative is gaining ground but not yet decisive. Note trigger to revisit (e.g., "if v2.0 ships with feature X").
- **SWITCH** — alternative is meaningfully better. Quantify expected gain. Estimate migration effort.

**Bias toward STAY for solo dev.** Switching cost is rarely worth it unless the new option is dramatically better OR the current option is approaching unmaintained status. Only SWITCH on strong evidence.

## Output Format

Save to `wiki/tech-radar/[YYYY-MM-DD]-[topic].md` (or `tech-radar/` at workspace level for cross-project audits):

```markdown
# Tech Radar: [Topic]
**Date:** YYYY-MM-DD
**Project:** [project name]
**Current choice:** [name@version, last release: YYYY-MM]
**Proposed choice (if any):** [name@version]

## Current State of the Art (2026)
[2-3 sentences on where the category has moved. Cite primary sources.]

## Top Alternatives

| Option | Last release | Active? | Pros for this project | Cons / cost | Verdict |
|---|---|---|---|---|---|
| **[CURRENT]** | YYYY-MM | yes/no | ... | ... | baseline |
| Option A | YYYY-MM | yes/no | ... | ... | switch / watch / skip |
| Option B | YYYY-MM | yes/no | ... | ... | ... |

## Recommendation
**STAY / WATCH / SWITCH** — [one sentence verdict]

**Why:**
[2-3 sentence reasoning grounded in project's actual constraints]

**If SWITCH — migration plan (high level):**
1. [step]
2. [step]
3. [estimated effort: hours/days]

**If WATCH — revisit trigger:**
[Specific signal that would change the verdict, e.g., "when Astro 6.0 ships with view transitions stable"]

## Sources
[Numbered list of cited URLs — official docs, recent comparisons, GitHub issues, benchmark posts]
```

## Rules

- **Time-box research to 5 minutes.** Tech radar is a quick check, not a deep dive. For deep dives, use `/research` skill.
- **Bias toward STAY** for solo dev — migration cost usually exceeds marginal gain.
- **Cite real, clickable URLs.** No "I think" or "probably" — back claims with sources.
- **Flag dead/unmaintained current choices loudly.** If last release >12 months OR GitHub issues stale, recommend SWITCH or WATCH (not STAY).
- **For new projects:** check before writing ANY code — don't bake in last year's defaults.
- **For new dependencies:** check the SPECIFIC dependency, not the whole category. "Should I add lodash?" not "What's the best utility lib?"
- **Append to `wiki/log.md`:** `## [YYYY-MM-DD] tech-radar | [topic] | [stay/watch/switch]`
- **Update CLAUDE.md if SWITCH is approved:** the new tool's name goes in the project conventions section.

## Anti-Patterns

- **"Hype-driven" switching** — picking the trendy choice without grounding in this project's constraints
- **Skipping baseline scan** — recommending alternatives without knowing what's currently used
- **Ignoring migration cost** — "X is technically better" doesn't matter if migration costs 80 hours for a 5% gain
- **Recommending SWITCH on every check** — that's hype-radar, not tech-radar. Most checks should be STAY.
- **Skipping for "obvious" choices** — even React, Postgres, and other dominant choices deserve periodic checks. Markets shift.

## Integration with `/docs adr`

When user runs `/docs adr "decision title"`, the ADR mode SHOULD call `/tech-radar` first on the decision topic. The radar's output populates the ADR's "Alternatives Considered" section with real research, not generic placeholders. ADR becomes a living document grounded in market reality at the time of decision.
