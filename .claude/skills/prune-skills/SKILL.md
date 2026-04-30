---
name: prune-skills
description: Quarterly audit of skills, slash commands, and agents. Identifies bloat, suggests deletions for items used less than 3 times per week.
allowed-tools: [Read, Grep, Glob, Bash]
---

# Prune Skills

Run quarterly. Skill explosion is the #1 anti-pattern in solo dev workflows.

## Steps
1. List all `.claude/skills/`, `.claude/commands/`, `.claude/agents/` (workspace + global)
2. Grep `tasks/lessons.md` and shell history for invocation count over last 90 days
3. For each, classify:
   - **Keep**: used 3+ times/week, clear purpose, no overlap
   - **Refactor**: bloated (see detectors below)
   - **Delete**: used <3x/week OR overlapping OR unused 90+ days
4. Generate `prune-report.md` with classification table
5. Ask user to confirm deletions before applying
6. Archive deleted items to `.claude/_archive/YYYY-MM-DD/` — never hard-delete

## Anti-Pattern Detectors
- **OVER_CONSTRAINED**: skill has >5 mandatory steps with no escape hatch
- **BLOATED_SKILL**: SKILL.md >150 lines (description bloat)
- **OVERLAPPING_SCOPE**: two skills do same thing with different names
- **DEAD_DEPENDENCY**: skill calls a tool/agent that no longer exists
- **STALE_BRANCH**: last modified >180 days ago, zero recent invocations
- **CIRCULAR_DEPENDENCY**: skill A calls skill B which calls skill A

## Output Format
```markdown
# Skill Prune Report — YYYY-MM-DD

| Skill/Command/Agent | Invocations (90d) | Classification | Reason |
|---|---|---|---|
| /go | 87 | Keep | Daily driver |
| /old-thing | 0 | Delete | Unused 120+ days |
| /bloated | 12 | Refactor | BLOATED_SKILL — 230 lines |

## Suggested Actions
- DELETE: /old-thing, /unused-2 → archive to .claude/_archive/2026-04/
- REFACTOR: /bloated → split into /thing-a and /thing-b
- KEEP: 9 skills passing audit
```

## Rule
- Always archive, never hard-delete. Recovery costs nothing.
- Confirm with user before applying any deletion.
