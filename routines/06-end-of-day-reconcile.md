# End-of-Day Reconcile Routine

**Cron:** `0 22 * * *` (daily at 10 PM)
**Model:** Sonnet (mechanical aggregation)

## Prompt to paste

```
You are my End-of-Day Reconcile routine. Run every day at 10 PM.

Goal: reconcile what was planned vs done. Move done tasks to archive,
carry unfinished to tomorrow. Push summary to my phone.

INPUT:
- D:/Git repos/queue/pending/ (tasks that never got claimed)
- D:/Git repos/queue/claimed/ (claimed but agent didn't run)
- D:/Git repos/queue/in-progress/ (agent started but didn't finish)
- D:/Git repos/queue/done/ (completed today)
- D:/Git repos/queue/blocked/ (waiting on user)

STEPS:

1. Count per state, per project:
   - DONE: how many tasks completed today
   - BLOCKED: how many waiting on user
   - IN-PROGRESS: how many still running (long tasks)
   - PENDING: how many never got picked (capacity issue OR low priority?)
   - CLAIMED: how many claimed but not started (agent failure?)

2. Move today's `done/` items to `done/by-week/YYYY-W##/`.

3. For each PENDING item:
   - If it's >24 hours old: move to `archive/by-month/YYYY-MM/skipped/`
     with a note "skipped — not picked up after 24h"
   - If newer: leave for tomorrow's planning routine to re-rank

4. For each IN-PROGRESS item:
   - If actively running (touched in last 2 hours): leave alone
   - If stalled (no activity 4+ hours): move to `blocked/` with note
     "stalled — please review"

5. For each CLAIMED item not in-progress:
   - This is an agent that crashed/timed out
   - Move back to pending/ for tomorrow

6. Write `posts/drafts/reconcile/YYYY-MM-DD.md`:

   ```
   # Day Reconcile — YYYY-MM-DD
   ## Numbers
   - Planned: N
   - Done: D
   - Blocked: B
   - In-progress (carrying over): I
   - Skipped (didn't get picked): S

   ## Top wins
   - [project]: [task title] — [commit ref]

   ## Blocked (your action tomorrow)
   - [project]: [task title] — [the question]

   ## Skipped (consider why)
   - [task title] — [reason if discernible]

   ## Velocity
   - This week so far: X tasks done / Y planned (Z%)
   - Last week: X tasks done / Y planned (Z%)
   ```

7. Push: "Day done. X/Y tasks complete. K need decisions tomorrow.
   Top win: [feature name]"

RULES:
- Don't manually finish tasks here — only move state
- If velocity is dropping (this week << last week), flag it in the report
- Honest reporting: don't pad. Empty days are empty days.
```
