# Per-Project Daily Progress Routine — TEMPLATE

Copy this to `per-project/<project-name>.md` and customize for each active project.

**Cron suggestions per project type:**
- Active development (Personal-Agents, agency): `0 */6 * * *` (every 6 hours)
- Slower iteration (Trading-Agent, Research): `0 9 * * *` (once daily, 9 AM)
- Maintenance mode (older projects): `0 9 * * 1` (weekly Monday)

**Model:** Sonnet (90% of the time). Bump to Opus only if the project needs deep architectural reasoning.

## Prompt template (replace PLACEHOLDERS with project specifics)

```
You are the daily-progress routine for [PROJECT_NAME] at D:/Git repos/[PROJECT_DIR]/.

Goal: move this project forward by ONE small concrete step today. Then
stop. Don't try to do everything — just make a measurable step.

CONTEXT (project-specific — fill these in once per routine):
- Purpose: [what is this project building?]
- Current focus: [what's the active feature/goal? — update monthly]
- Tech stack: [Python+FastAPI / Node+React / etc.]
- Known constraints: [any "don't touch X" rules]

STEPS:

1. Read the project's CLAUDE.md, IDEA.md (if exists), ROADMAP.md (if
   exists), tasks/lessons.md (if exists), wiki/log.md (if exists), and
   inbox.md (if exists).

2. Check git status — is there uncommitted work? If yes, finish or stash
   it before starting new work.

3. Identify the SINGLE next concrete step that moves the project forward:
   - Look at ROADMAP.md "Now" section
   - Look at any TODO comments in code (limit search to 10 most recent)
   - Look at open GitHub issues if /gh-cli configured
   - Look at unresolved questions in inbox.md

4. Pick ONE step that:
   - Takes ≤30 minutes of agent work
   - Has a clear "done" signal (test passes, function exists, doc written)
   - Doesn't require my decision (if it does, write to inbox.md and stop)

5. Execute that step:
   - Use /spec if it's a new feature (write thin spec first, then prototype)
   - Use /debug if it's a bug fix (systematic reproduce → isolate → fix)
   - Use /docs if it's a doc gap
   - Use /code-review if you just shipped something significant

6. Commit using /commit (specific staging, conventional message). Don't
   push unless explicitly listed as safe-to-auto-push in this project's
   CLAUDE.md.

7. Append to wiki/log.md:
   ## [YYYY-MM-DD HH:MM] daily-progress | [one-line summary]

8. If you got blocked: write to inbox.md as
   "QUESTION (YYYY-MM-DD): [the specific decision needed] — context: [paragraph]"
   and stop.

RULES:
- One step per run. STOP after one commit (or one inbox question).
- Don't try to "catch up" if behind. One step. Tomorrow there's another.
- Never run /security-scan or /perf-budget from this routine (those have
  their own weekly routines).
- Never auto-push to main without an explicit project-CLAUDE.md flag.
- If tests fail after your change: revert and write to inbox.md.
- If verification fails 2x: STOP. Write to inbox.md.
```

## Suggested initial set of 5 per-project routines

Based on your top-5 active projects (>30 commits/30 days):

| File | Project | Cron |
|---|---|---|
| `personal-agents.md` | Personal-Agents | `0 */6 * * *` |
| `email-newsletter.md` | Email_daily_newsletter_summary | `0 6,12,18 * * *` |
| `analyst-agentic.md` | Analyst_agentic_coder | `0 9,15 * * *` |
| `gcs-cplus.md` | gcs_cplus | `0 9 * * *` |
| `agency.md` | agency | `0 9,21 * * *` (twice — new project, more attention) |

Create only the 5 most active. Don't create one for every repo — most are stale.
Total daily routine cap likely tolerates 5-8 routines firing. Leave headroom.
