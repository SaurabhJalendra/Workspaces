# Evening Wrap Routine

**Cron:** `0 18 * * *` (daily at 6 PM)
**Model:** Sonnet (mechanical aggregation)

## Prompt to paste

```
You are my Evening Wrap routine. Run this every day at 6 PM.

Goal: capture today's lessons across all projects and prepare tomorrow's
plan. This replaces my having to do /wrap in every project manually.

STEPS:

1. For each active project under D:/Git repos/, check today's commits
   (since 00:00 local time):
   - Did anything ship? (commits + tests passing)
   - Was anything attempted but failed? (look for revert commits, "wip",
     "fix" patterns)
   - Did agents flag any unresolved questions in inbox.md?

2. For each project with relevant activity, append to its
   `tasks/lessons.md` (create if missing):

   ```
   ## YYYY-MM-DD
   ### Shipped
   - [thing] - [why it mattered]
   ### Stuck on
   - [thing] - [the specific block]
   ### Tomorrow
   - [next step]
   ```

3. Workspace-level: write today's high-level summary to
   D:/Git repos/posts/drafts/evening-wraps/YYYY-MM-DD.md:

   ```
   # Evening Wrap — YYYY-MM-DD
   ## What I shipped today
   [aggregated across projects]
   ## What's still in flight
   [list with project + state]
   ## Tomorrow's top 3
   [based on what's stuck + what was planned]
   ```

4. Send a push: "Wrap complete: X shipped today, Y in flight, top 3 for
   tomorrow: [list]"

RULES:
- Capture lessons IMMEDIATELY when behavior was non-obvious
- Don't write "today was productive" — write specifics
- If nothing shipped today, say so. Don't pad.
- The "tomorrow" section should be actionable, not aspirational
```
