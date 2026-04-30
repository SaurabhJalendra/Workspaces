---
name: morning
description: Session-start ritual — git status, recent commits, lessons review, project status briefing. Briefing only — does NOT start implementing.
allowed-tools: [Bash, Read, Grep, Glob]
---

# Morning Ritual

Session-start briefing. NOT a sprint.

## Steps
1. `git status` and `git log --oneline -10` — what changed since last session
2. Read `tasks/lessons.md` if it exists — surface relevant lessons
3. Read `wiki/log.md` last 7 entries — recent decisions/learnings
4. Check `MEMORY.md` for project memories
5. If GitHub: `gh issue list --state open --limit 5` and `gh pr list --state open --limit 5`
6. Brief 5-bullet status:
   - In progress
   - Blocked
   - Recently shipped
   - Next likely tasks
   - Lessons relevant to today

## Anti-Pattern
- Do NOT start implementing during /morning. This is a briefing, not a sprint.
- Wait for explicit instruction before any code changes.
- Do NOT auto-pull. Just report status.
