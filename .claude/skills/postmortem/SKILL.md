---
name: postmortem
description: Generate a blameless postmortem from an incident, near-miss, or shipped bug. Composes Anthropic April 2026 + Google SRE + Etsy blameless templates.
allowed-tools: [Read, Write, Bash, Grep]
---

# Postmortem

Trigger on: outage, near-miss, shipped bug, "I almost shipped X" moment.

## Output Path
`postmortems/by-year/YYYY/YYYY-MM-DD-short-name.md` (workspace level)
OR
`<project>/postmortems/YYYY-MM-DD-short-name.md` (project level)

## Template

```markdown
# Postmortem: [Short Description]
**Date:** YYYY-MM-DD
**Severity:** [Outage / Bug-shipped / Near-miss]
**Author:** Saurabh

## Summary
[2-3 sentences: what happened, blast radius, who/what affected.]

## Timeline
| Time | Event |
|---|---|
| HH:MM | [trigger event] |
| HH:MM | [detection] |
| HH:MM | [mitigation] |
| HH:MM | [resolution] |

## Root Causes (always plural)
1. [First cause — name precisely, link to commit/PR]
2. [Second cause — often the interaction with #1]
3. [Meta-cause — e.g., "no soak period for this kind of change"]

## Detection Failure (if bug shipped)
[Why didn't review/tests catch it? What evals saturated on the easy half?]

## What We Changed
- [Code/config change with link]
- [Process change — e.g., "added soak period gate for prompts/"]
- [Test added — link]

## What We're Investing In
- [Longer-term mitigation that prevents this CLASS of issue]
- [Tooling making this category visible earlier]

## Going Forward
[1 sentence on the principle this incident teaches.]
```

## Rules
- **Blameless framing** — "X should have known better" is banned. Reasons, not blame.
- **ALWAYS plural root causes.** Single-cause postmortems miss the interaction.
- **Detection failure section is mandatory** if bug shipped — explain why review/tests missed it.
- **Adversarial review at finalization**: dispatch a subagent to challenge the postmortem ("what cause are you missing?")
- Update `wiki/log.md` after writing: `## [YYYY-MM-DD] postmortem | [name]`
