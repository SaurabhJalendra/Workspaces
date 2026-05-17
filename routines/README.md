# Cloud Routines — Setup Guide

These are paste-ready prompts for Anthropic's Cloud Routines (run while machine is off, truly unattended).

## How to create a Routine

Two paths:

**Path A (web UI, recommended for first time):**
1. Open https://claude.ai/code/routines
2. Click "New Routine"
3. Paste the prompt from one of the files below
4. Set cron schedule (use the suggestion in each file's header)
5. Pick model: **Sonnet** for routine work, **Opus** for synthesis (daily digest, weekly review)
6. Save

**Path B (from inside any Claude Code session):**
```
/schedule [paste a single-line description]
```
Example:
```
/schedule daily at 8am — read git log of every project under D:/Git repos/, write a digest of overnight activity, push to my phone
```

## The 5 starter routines (set up in this order)

1. `01-morning-briefing.md` — daily 8 AM cross-project digest → push
2. `02-evening-wrap.md` — daily 6 PM lessons + tomorrow plan
3. `03-weekly-demo.md` — Friday 5 PM ship/iterate/cut review
4. `04-security-audit.md` — Monday 8 AM dep audit per active repo
5. `05-doc-drift-check.md` — Friday noon docs sync across active projects

Plus per-project loops (in `per-project/` folder) — one daily-progress prompt per active project.

## When NOT to use a Routine

- Tasks that need your local filesystem (use Agent View locally instead)
- Tasks that need MCP servers configured locally (Routines have separate MCP config)
- One-off tasks (use `/loop` in-session or just dispatch ad-hoc)

## Monitoring

Watch your daily-routine cap at https://claude.ai/code/routines
If you hit the cap, switch to one-off runs (don't count against daily quota).

## Per-project routines

`per-project/` folder has one prompt per active project. Pick the 5 most active projects (Personal-Agents, Email_daily_newsletter_summary, Analyst_agentic_coder, gcs_cplus, agency) and create one routine each, firing every 6-12 hours.

The pattern: each routine moves the project forward one small step, commits, and flags any decisions needed via `inbox.md` in the repo. The morning briefing aggregates all inboxes.
