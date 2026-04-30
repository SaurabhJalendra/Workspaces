---
name: wrap
description: Session-end ritual — WIP commit if needed, update lessons.md, suggest CLAUDE.md updates, append to wiki/log.md
allowed-tools: [Bash, Read, Edit, Grep]
---

# Wrap Ritual

End-of-session capture. The act of writing forces consolidation.

## Steps
1. `git status` — uncommitted work?
2. If uncommitted: ASK — WIP commit, stash, or leave?
3. Review session: was there a correction, surprise, or new pattern?
   - If yes: append to `tasks/lessons.md`
4. Suggest CLAUDE.md updates: any rule that would have prevented today's mistakes?
   - Suggest only — do NOT auto-edit CLAUDE.md (cache concern)
5. Append to `wiki/log.md`: `## [YYYY-MM-DD] session-wrap | [1-line summary]`
6. Brief end-of-session summary:
   - What shipped
   - What's pending
   - Suggested next session focus

## Rule
- NEVER auto-update CLAUDE.md. Suggest, don't apply. User edits between sessions.
- If `tasks/lessons.md` doesn't exist, create it with a header before appending.
