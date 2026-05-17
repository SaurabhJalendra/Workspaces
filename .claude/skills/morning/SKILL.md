---
name: morning
description: Session-start ritual + daily planning. Briefs Saurabh on yesterday's work across active projects, suggests today's tasks from each project's ROADMAP, plans collaboratively, generates queue/pending/ files. The PM-engineer daily sync.
allowed-tools: [Bash, Read, Edit, Write, Grep, Glob]
---

# Morning Briefing + Daily Planning

The PM-engineer ritual. Saurabh is the strategic decision-maker, I am the execution team. This skill runs in 4 phases.

## ACTIVE PROJECTS (read these every morning)

Tier A (full briefing):
- Personal-Agents
- Email_daily_newsletter_summary
- Analyst_agentic_coder
- gcs_cplus
- Research

Extend list if Saurabh signals others are active for the day.

## PHASE 1: Brief — what shipped

For each Tier A project:
1. `git log --since="24 hours ago" --oneline` — yesterday's commits
2. Read `<project>/inbox.md` — any agent-flagged questions awaiting Saurabh?
3. Read `<project>/tasks/lessons.md` last 3 entries (if exists)
4. Read `<project>/wiki/log.md` last 3 entries (if exists)
5. `git status` in project — anything uncommitted?

Also read across workspace:
- `D:/Git repos/queue/pending/` — leftover from yesterday
- `D:/Git repos/queue/blocked/` — items awaiting Saurabh's decision
- `D:/Git repos/queue/in-progress/` — sessions still running

## PHASE 2: Suggest — forward steps

For each active project, propose 1-3 specific tasks for today. Pull from in order of priority:
1. **Blocked items** (waiting on Saurabh) — highest priority, surface first
2. **Carryover** from queue/pending/
3. **ROADMAP.md "Now" section** — top items
4. **IDEA.md "Open Questions"** — research/decision tasks
5. **Inbox questions** — explicit pending input

Each suggestion must be:
- Concrete (specific file/feature/decision)
- Time-boxed (≤2 hours of agent work)
- Has clear acceptance criteria

## PHASE 3: Present + ask (then WAIT)

Present in this structure:

```markdown
# Morning Briefing — YYYY-MM-DD

## Yesterday across active projects
- Personal-Agents: [N commits — most-impactful summary]
- Email_daily_newsletter_summary: [N commits — most-impactful summary]
- Analyst_agentic_coder: [N commits — most-impactful summary]
- gcs_cplus: [N commits — most-impactful summary]
- Research: [N commits — most-impactful summary]

## Inbox status
- [Project]: [N questions OR "all clear"]

## Carryover from yesterday's queue
- [N pending tasks listed by project]
- [N blocked items listed with the question]

## Today's suggested tasks (from each project's ROADMAP Now)

### Focus candidates (high priority)
- **Personal-Agents**: [Suggestion 1 — what + why + est minutes]
- **Email_daily_newsletter_summary**: [Suggestion 1 — what + why + est minutes]

### Background candidates (medium priority)
- **Analyst_agentic_coder**: [Suggestion 1]
- **gcs_cplus**: [Suggestion 1]
- **Research**: [Suggestion 1]

## Questions for you (decide before I plan)
1. **Focus today**: Which 2-3 projects get active attention?
2. **Background**: Any others to keep ticking via Agent View?
3. **Skip**: Anything to explicitly NOT touch today?
4. **Urgent**: Anything not in the ROADMAPs that needs today's attention?
5. **Already done**: Anything I'm suggesting that's already complete?
```

Then STOP. Wait for Saurabh's response. Do NOT proceed to Phase 4 without his input.

## PHASE 4: Plan — write the day's queue

Based on Saurabh's response, generate task files in `D:/Git repos/queue/pending/`:

Filename: `YYYY-MM-DD-HHMM-<project>-<short-slug>.md`

Task file content:
```yaml
---
project: <project name>
priority: high | medium | low
estimated_minutes: 30-120
created: YYYY-MM-DDTHH:MM:SSZ
status: pending
assigned_agent: morning-plan
tags: [feature, bug, refactor, docs, research]
push_when_blocked: true
---

## Task
[Specific description from morning planning conversation]

## Acceptance Criteria
- [ ] Outcome 1
- [ ] Outcome 2

## Context
[Link to ROADMAP item, inbox question, or yesterday's commit]

## When you (the executing agent) get blocked or need a decision
1. Write a "QUESTION:" entry to <project>/inbox.md with full context
2. EXPLICITLY request a push notification to my phone: include in your
   thinking "I need to ask Saurabh about X — please push notify him."
   Per Anthropic docs, requesting push in your reasoning triggers the
   native iOS push notification mechanism.
3. Stop execution. Move this task file from queue/in-progress/ to queue/blocked/.
4. Saurabh will answer via iOS Claude app. Resume on next turn.
```

After writing all task files, confirm to Saurabh:

```
✅ Plan committed to queue/pending/. N tasks across M projects.

Focus projects:
  - Personal-Agents: [N tasks]
  - [Other]: [N tasks]

Background:
  - [Other]: [N tasks]

Dispatching now via Agent View for focus projects? Or hold until you're ready?
```

## SPECIAL TASK TEMPLATE — Doc bootstrap from past chats + state

When a focus project is missing ROADMAP.md / IDEA.md / inbox.md, generate this
specific task instead of normal task. Read past Claude session chats as a
PRIMARY context source — they contain decisions and plans not yet committed.

```yaml
---
project: <project name>
priority: high
estimated_minutes: 60
status: pending
assigned_agent: doc-bootstrap
tags: [docs, bootstrapping, foundation]
push_when_blocked: true
---

## Task
Bootstrap missing project docs (IDEA.md and/or ROADMAP.md and/or inbox.md)
using ALL available context including past Claude session chats.

## Context sources (read in this order)

1. **Past Claude session chats** (high value — uncommitted decisions live here):
   Path: `~/.claude/projects/D--Git-repos-<project>/*.jsonl`
   Look at the 5-10 most recent .jsonl files. For each:
   - Extract user messages: `grep '"type":"user"' <file>.jsonl`
   - Extract assistant text (skip thinking blocks, skip tool calls):
     filter for `"type":"assistant"` with `"text":` content
   - Note recurring themes: what was the user trying to do?
   - Note decisions made: what was chosen and why?
   - Note open questions: what was never resolved?
   - Note planned-but-not-done: what was discussed but no commit followed?

2. **Git log** (committed history):
   `git log --since="3 months ago" --oneline`
   Group commits by theme to see project momentum.

3. **README.md** — user-facing purpose

4. **CLAUDE.md** (if exists) — project-specific rules / focus

5. **Existing wiki/log.md, tasks/lessons.md** (if exist)

## Generation rules

- Synthesize across ALL sources — past chats often reveal direction that
  README doesn't capture
- For IDEA.md "Open Questions" section: pull from unresolved past-chat threads
- For ROADMAP.md "Now": align with most recent commits + uncommitted plans
- For ROADMAP.md "Next": pull from past-chat ideas that user discussed but didn't pursue
- For ROADMAP.md "Not Doing": pull from past-chat decisions to deprioritize
- For inbox.md: just create the empty template

## Token budget

- Don't read every line of every .jsonl file (some are 100K+ lines)
- Focus on USER messages and final ASSISTANT text — skip tool calls,
  thinking blocks, system reminders
- Cap at most 5 sessions back in time, or last 30 days, whichever is shorter

## Acceptance Criteria

- [ ] IDEA.md exists, accurately reflects project's REAL purpose (not generic)
- [ ] ROADMAP.md exists with specific items in Now/Next/Later
- [ ] inbox.md exists with empty header
- [ ] Generated docs cite past-chat decisions where relevant (in comments
      or "Open Questions" section)
- [ ] Commit message: "docs: bootstrap IDEA/ROADMAP/inbox from past chats + state"

## If past chats reveal a major unresolved decision

Write a "QUESTION:" entry to inbox.md AND push notify Saurabh:
  "Past chats show you discussed [X] but never decided. The bootstrap
   roadmap assumes [Y] direction. Confirm or override?"
```

This task type is generated automatically by /morning when it detects missing
docs in a focus project. Saurabh agrees to the bootstrap, and the dispatched
agent reads past chats to inform the new docs.

## PHASE 5: Optional immediate dispatch

If Saurabh says "dispatch", "start", "go", or similar:
- For each high-priority task in queue/pending/, dispatch a background session via Agent View
- Move file: queue/pending/X.md → queue/claimed/X.md (atomic via mv)
- In each session, work the task per its description
- Each session knows: write to inbox.md + push when blocked

If Saurabh says "hold" or doesn't dispatch:
- Tasks sit in queue/pending/
- Saurabh dispatches manually when ready

## Rules

- Phase 1 + 2 + 3 happen in ONE response — brief + suggest + ask
- WAIT for Saurabh between Phase 3 and Phase 4
- Don't start implementing during Phase 1-3 — planning, not sprinting
- Push notifications are CRITICAL — every dispatched session must know to push when blocked
- Honest reporting: empty days are empty days. Don't pad.
- Surface stale projects: 7+ days no commits = "consider /demo to evaluate cut/iterate"
- Cap planned tasks: 5-7 per day across all projects. Beyond burns quota and review queue.

## Anti-patterns

- Generic tasks like "improve project X" — must be specific with acceptance criteria
- Suggesting tasks not from ROADMAP without flagging "new"
- Mixing focus + background in same priority bucket
- Skipping the questions phase — Saurabh IS the PM, his input shapes the plan
- Auto-pushing tasks to queue without his approval
