---
name: demo
description: Demo-driven review (Cat Wu pattern, replaces standups). Weekly self-demo of what shipped, what didn't, what's used. Output is a ship/iterate/cut decision per item. Run every Friday or when user says "demo time".
allowed-tools: [Read, Bash, Edit, Write, Grep, Glob]
---

# /demo — Demo-Driven Review

> "We replaced standups with demos." — Cat Wu, Head of Product, Claude Code

For solo: weekly self-review of what shipped, what didn't, and crucially — what got used. Forces accountability without a manager.

## When to invoke

**Auto-fire (Tier 2 — once per week, ideally Friday):**
- First user message of the week's last work session (e.g., Friday afternoon)
- After 7 days of no /demo run
- User says "demo time" / "weekly review" / "what shipped this week"

**User-invoked:**
- `/demo` — current week
- `/demo weekly` — same as above
- `/demo monthly` — broader scope, last 4 weeks

## Workflow

### Step 1 — Inventory what changed (3 min)

```bash
# Commits this week
git log --since="last monday" --pretty=format:"%h %s" --no-merges

# Per project (workspace meta-repo + all sub-repos)
for repo in [list]; do
  cd $repo && git log --since="last monday" --oneline
done

# Specs touched
ls specs/ -lt | head

# Postmortems / premortems written
ls postmortems/ -lt | head
ls premortems/ -lt | head

# What got pushed to public
git log --since="last monday" --pretty=format:"%h %s" origin/main
```

### Step 2 — Demo each item (5-10 min)

For each shipped item, write 1-3 lines:

```markdown
### [feature/fix name]
- **What:** [one line]
- **Used by me:** yes / no / partially
- **Internal DAU days:** N/7 (if features tracking 7-day gate)
- **Status:** shipped public / shipped internal / parked / iterating
- **Decision:** keep / iterate / cut
```

### Step 3 — Decision per item (Cat Wu's gate)

For each shipped item, ask: **did I actually use it daily?**

- **YES, daily** → keep, optionally /launch publicly
- **YES, but not daily** → iterate or cut. Why isn't it used? Wrong feature, wrong UX, or wrong problem?
- **NO, not used** → CUT it. Don't keep dead features. Document why in `wiki/decisions/`.

For non-shipped (in progress):
- **Stuck >2 weeks** → STOP. Either /premortem to find blocker, or cut.

### Step 4 — Surface lessons & gaps

```markdown
## Lessons This Week
- [pattern observed — e.g., "tests kept failing on Friday because X"]
- [success — e.g., "/coordinator pattern actually saved 2 hours on the X feature"]

## What I Got Wrong
- [thing I underestimated / overestimated / misjudged]
- [decision I'd make differently]

## Skill Usage
- Skills used 3+ times: [list] → keeping
- Skills used 0 times: [list] → /prune-skills candidates
- New patterns observed 3+ times → candidates for new skill

## Roadmap Update
- Done from "Now" → move to "Recently Shipped"
- "Now" candidates from "Next" → promote
- Things to add to "Not Doing" → list
```

### Step 5 — Update artifacts

- Update `ROADMAP.md` (move shipped items, promote next, add to Not Doing)
- Append to `tasks/lessons.md` if any new patterns
- Append to `wiki/log.md`: `## [YYYY-MM-DD] demo | [N items reviewed] | [N kept / N cut / N iterating]`

## Output Path

`demos/[YYYY-MM-DD]-weekly.md` (workspace level — cross-project view)

```markdown
# Weekly Demo — YYYY-MM-DD
**Period:** [last monday] to [this friday]
**Items reviewed:** N

## Shipped This Week
[per-item demo + decision]

## In Progress (>1 week)
[items still cooking + age]

## Cut This Week
[items deliberately killed + why]

## Lessons & Patterns
[from step 4]

## Skill Usage Audit
[which skills were used / unused / new patterns observed]

## Roadmap Updates
[what moved between Now/Next/Later/Recently/Not Doing]

## Next Week Focus
[1-3 things to focus on]
```

## Rules

- **Be honest about "did I actually use it."** This is the only signal that matters. Lying to yourself wastes weeks.
- **Cut without guilt.** Killed features are not failures. They're learning. Document why and move on.
- **No long demos.** 30 min max per week. If you can't review the week in 30 min, you shipped too much (or not enough).
- **Follow up cuts in code.** If you decide to cut something, also cut the code/UI/docs in the same session.
- **Internal-DAU >>> public DAU at this stage.** You're the test user. If you don't use it, neither will others.

## Anti-Patterns

- **Skipping demos when "nothing happened" this week** — that itself is a finding. Why was the week unproductive?
- **Demo-as-status-report** — it's not for an audience. It's for your own clarity.
- **Cutting things you "might use later"** — if 2 weeks haven't started, future you won't either
- **Avoiding the "did I use it" question** — that's the whole point
- **Long-form prose demos** — keep it punchy. Bullet points beat paragraphs.

## Integration

- Auto-trigger fires Friday OR after 7 days no run
- Reads from: `git log` across all projects, `specs/`, `postmortems/`, `premortems/`, `ROADMAP.md`
- Writes to: `demos/[date].md`, updates `ROADMAP.md`, appends `tasks/lessons.md`, `wiki/log.md`
- Hooks into `/prune-skills` — skills audit feeds into demo's skill-usage section
- Hooks into `/launch` — items passing 7-day Internal-DAU surface as launch candidates
