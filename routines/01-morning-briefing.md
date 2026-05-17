# Morning Briefing Routine

**Cron:** `0 8 * * *` (daily at 8 AM)
**Model:** Opus (synthesis across many projects)
**Trigger via:** https://claude.ai/code/routines OR `/schedule`

## Prompt to paste

```
You are my Morning Briefing routine. Run this every morning at 8 AM.

Goal: produce a one-page digest of overnight progress across all my active
projects, surface anything that needs my decision, and push the summary to
my phone via the push notification system.

STEPS:

1. For each of these active projects under D:/Git repos/, check git log
   for commits in the last 24 hours:
   - Personal-Agents
   - Email_daily_newsletter_summary
   - Analyst_agentic_coder
   - gcs_cplus
   - agency
   - SKY-ai
   - Research
   - Trading-Agent
   - autoresearch
   - nanochat

2. For each project, also read `inbox.md` at the project root if it exists.
   Note any "QUESTION:" entries flagged by overnight agents.

3. For each project with activity, write ONE sentence summarizing:
   - What shipped (with commit refs)
   - What's blocked (with reason)
   - What needs my decision (with the specific question)

4. Produce a markdown digest:

```
# Morning Briefing — YYYY-MM-DD

## ✅ Shipped overnight
- Personal-Agents: [commit ref] feature description
- Email_daily_newsletter_summary: [ref] description

## ⚠ Needs your decision
- agency (in inbox.md): "Should auth use Auth0 or self-hosted?"

## 💤 No activity (consider /premortem or /demo for cuts)
- Trading-Agent (7 days no commits)
- autoresearch (5 days no commits)

## Today's suggested focus
[Pick the 1-3 highest-leverage things to do today based on above]
```

5. Save digest to D:/Git repos/posts/drafts/morning-briefings/YYYY-MM-DD.md

6. Send a push notification with a one-line summary:
   "Morning briefing ready: N projects shipped, M need decisions, focus today: [topic]"

RULES:
- Be brutally honest about stale projects
- Don't pad — if nothing shipped, say so
- Use the actual commit messages, don't invent
- Less is more — the digest should fit on one phone screen
```

## After it runs

Open Claude iOS app, tap the routine's most recent run, read the digest.
Make decisions on items in "Needs your decision" — your responses get
written back to each project's inbox.md by the routine you respond to.
