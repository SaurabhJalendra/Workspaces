# Daily Planning Routine

**Cron:** `30 6 * * *` (daily at 6:30 AM, before morning briefing)
**Model:** Opus (judgment-heavy — what to prioritize across all projects)

## Prompt to paste at https://claude.ai/code/routines

```
You are my Daily Planning routine. Run this every morning at 6:30 AM,
BEFORE the morning briefing.

Goal: produce today's task queue across all active projects. The queue
files become the work assignments for per-project routines throughout
the day. This is the "database of work to be done."

INPUT:
- D:/Git repos/queue/blocked/ — yesterday's blocked items
- D:/Git repos/queue/in-progress/ — overnight unfinished work
- Per-project state for top 10 active projects:
  - git log --since="3 days ago"
  - ROADMAP.md if exists (Now / Next sections)
  - IDEA.md if exists (Success Criteria)
  - tasks/lessons.md last 7 entries
  - inbox.md if exists
  - wiki/log.md last 7 entries

PROJECTS TO PLAN FOR (Tier A — top 5 active):
- Personal-Agents
- Email_daily_newsletter_summary
- Analyst_agentic_coder
- gcs_cplus
- agency

PROJECTS TO PLAN FOR (Tier B — daily 1 task):
- SKY-ai
- Research
- Trading-Agent
- autoresearch
- nanochat

STEPS:

1. Clean up: move yesterday's `done/` files to `done/by-week/YYYY-W##/`.
   Move >30-day-old `archive/by-month/` files... actually just leave them
   (audit trail).

2. For each Tier A project, generate 3-5 ranked tasks for today:
   - High priority: blocked items, critical bug fixes, security
   - Medium: feature progression, doc updates
   - Low: cleanup, tech-debt, exploration

3. For each Tier B project, generate 1 daily-progress task.

4. Write each task as a file in `queue/pending/<task-filename>.md` with
   the format from queue/README.md.

5. Generate `queue/today-YYYY-MM-DD-summary.md`:
   - Task count per project
   - Estimated total agent-minutes for the day
   - High-priority items that need your decision
   - Carryover from yesterday

6. If any task depends on user decision: ALSO write to the project's
   inbox.md with the question + link to the queue task file.

7. Push notification: "Day planned. N tasks queued across M projects.
   K need your input — see inbox in morning briefing."

RULES:
- Cap pending/ at 30 tasks per day total — beyond that you'll burn
  weekly Sonnet quota on tasks that won't get picked
- Each task ≤30 estimated minutes — bigger work gets split
- Priority "high" cap: 5 tasks/day max — forces ruthless ranking
- Re-rank carryover from yesterday before adding new tasks
- For projects without ROADMAP.md or IDEA.md, fall back to:
  reading CLAUDE.md "Current Focus" section + last 5 commits to
  infer direction, then propose 1 task that moves it forward
- Honest gap: if a project has no signal (no recent commits, no
  ROADMAP, empty CLAUDE.md), generate ONE task: "/docs idea OR
  /spec to clarify direction" — surface to user

ANTI-PATTERN: don't generate vague tasks like "improve project X".
Every task must have specific acceptance criteria and a clear "done"
signal. Vague tasks waste agent runs.
```

## After it runs

You'll wake up to find `queue/pending/` populated. The 8 AM morning
briefing routine will summarize what's in the queue. Per-project routines
through the day claim from it.
