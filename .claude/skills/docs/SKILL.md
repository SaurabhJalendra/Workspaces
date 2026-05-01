---
name: docs
description: Unified documentation manager — create, update, maintain ALL project docs via subcommands. 10 modes covering CHANGELOG, PR, release notes, drift detection, IDEA, ADR, roadmap, contributing, security, troubleshooting.
allowed-tools: [Read, Write, Edit, Bash, Grep, Glob]
---

# Docs — Unified Documentation Skill

One skill manages ALL project documentation. `$ARGUMENTS` picks the mode.

**Auto-trigger rule:** After ANY non-trivial code change, suggest the relevant mode (`changelog` after commits, `sync` if docs may have drifted, `adr` for architectural decisions).

## Modes

### `/docs changelog` — Update CHANGELOG.md (most common)
1. `git log --oneline --since="last commit on CHANGELOG"`
2. `git diff main...HEAD --stat`
3. Append to CHANGELOG under `## [Unreleased]`:
   - `### Added` for new features
   - `### Changed` for modifications
   - `### Fixed` for bug fixes
   - `### Removed` for deletions
4. Past tense, specific, include file references

### `/docs pr` — Generate PR description
```
## Summary
[1-3 bullets]

## Changes
- [key change + file ref]

## Testing
- [how to verify]

## Notes
[anything reviewers should know]
```

### `/docs release` — User-facing release notes
- `git describe --tags --abbrev=0` for last tag
- `git log [last-tag]...HEAD --oneline`
- Audience: USERS, not developers
- Group: New / Improved / Fixed / Breaking

### `/docs sync` — Detect and fix doc drift (CRITICAL)
1. Read all docs: README, CHANGELOG, docs/*.md, inline docstrings
2. Check each claim against current codebase:
   - Are setup steps current?
   - Are API endpoints accurate?
   - Are listed features still present?
   - Do code examples actually run?
3. Flag discrepancies with file:line refs
4. Dispatch `documenter` agent to fix, or report for user decision

**Run before every PR.** Stale docs are technical debt that compounds.

### `/docs idea` — Create or update IDEA.md (north star)
```markdown
# [Project] — Idea Document

## The Problem
## The Solution
## Who It's For
## Why This, Why Now
## Success Looks Like
## Non-Goals   ← Critical: prevents scope creep
## Open Questions
```

### `/docs adr "decision title"` — Architecture Decision Record
Find next number in `docs/adr/`, create `docs/adr/NNNN-decision-title.md`:
```markdown
# ADR-NNNN: [Decision Statement]
**Date:** YYYY-MM-DD
**Status:** proposed | accepted | deprecated | superseded by ADR-XXXX

## Context
## Decision
## Consequences
### Positive / ### Negative / ### Risks
## Alternatives Considered
```
**Rule:** NEVER edit accepted ADR — supersede with new ADR.

### `/docs threat-model "feature/system name"` — Threat Model (STRIDE)

For new features touching auth, secrets, payments, user data, or external surfaces.
Create `docs/threat-models/[feature-name].md`:

```markdown
# Threat Model: [Feature/System Name]
**Date:** YYYY-MM-DD
**Reviewer:** [user]
**Status:** draft | reviewed | implemented | deprecated

## Scope
[What's in scope. What's explicitly NOT in scope.]

## Data Flow Diagram
[Text or mermaid diagram showing trust boundaries, data flows, processes, stores.]

## Assets
- [What we're protecting — user data, API keys, money, reputation, etc.]

## Trust Boundaries
- [Where untrusted input enters trusted zones — network → backend, frontend → API, etc.]

## STRIDE Threats
### S — Spoofing (impersonation)
- Threat: [description]
- Mitigation: [auth pattern]
- Residual risk: [low/med/high]

### T — Tampering (data integrity)
- Threat: [description]
- Mitigation: [signing, HMAC, transactional updates]
- Residual risk: [...]

### R — Repudiation (deniability)
- Threat: [description]
- Mitigation: [audit logs, signed receipts]
- Residual risk: [...]

### I — Information Disclosure (confidentiality)
- Threat: [description]
- Mitigation: [encryption at rest/transit, access controls]
- Residual risk: [...]

### D — Denial of Service (availability)
- Threat: [description]
- Mitigation: [rate limiting, queuing, autoscaling]
- Residual risk: [...]

### E — Elevation of Privilege (authorization)
- Threat: [description]
- Mitigation: [least-privilege RBAC, IDOR checks]
- Residual risk: [...]

## OWASP Top 10 Checklist (web apps)
- [ ] A01 Broken Access Control
- [ ] A02 Cryptographic Failures
- [ ] A03 Injection (SQL, command, prompt)
- [ ] A04 Insecure Design
- [ ] A05 Security Misconfiguration
- [ ] A06 Vulnerable & Outdated Components
- [ ] A07 Identification & Authentication Failures
- [ ] A08 Software & Data Integrity Failures
- [ ] A09 Security Logging & Monitoring Failures
- [ ] A10 Server-Side Request Forgery (SSRF)

## Action Items
- [ ] [specific code/config change with owner + due date]

## Open Questions
[Things still unresolved — get answers before "implemented" status.]
```

**Rule:** Threat-model BEFORE writing security-sensitive code, not after. Update on architectural changes.

### `/docs roadmap` — ROADMAP.md
```markdown
# Roadmap — Last updated: YYYY-MM-DD
## Now (2-4 weeks)
## Next (1-3 months)
## Later (exploratory)
## Recently Shipped (last 10-15)
## Not Doing   ← Prevents same rejected requests coming back
```

### `/docs vision` — VISION.md (annual, cross-project)

The annual personal vision. What kind of work do I want to be doing in 12 months?
Save to workspace level: `D:/Git repos/VISION.md` (one file across all projects, not per-project).

```markdown
# Vision — YYYY
**Set:** YYYY-MM-DD
**Reviewed:** YYYY-MM-DD (last)

## North Star
[1-2 sentences. The work I want to be doing in 12 months.]

## Identity I'm Building Toward
[Who am I when I'm doing my best work? Concrete, not aspirational.]

## What I'm Saying YES To
- [Type of work / problem / domain]
- [Type of work / problem / domain]

## What I'm Saying NO To
- [Type of work I will not chase, even if interesting]
- [Type of work I will not chase, even if profitable]

## Bets I'm Making
- [Belief about market / tech / personal — wager that may not pay off]
- [...]

## Non-Goals
- [Explicit things I am NOT optimizing for this year]

## How I'll Know This Worked
[1-3 measurable outcomes that would mean "yes, the year was on-vision"]
```

**Rule:** Reviewed annually (Jan 1) AND quarterly (alignment check vs goals). Updated annually only. If you change vision mid-year, document why.

### `/docs goals` — GOALS-YYYY-Q[N].md (quarterly OKRs, cross-project)

3-month outcomes. Solo OKR pattern. Save to workspace level: `D:/Git repos/goals/YYYY-Q[N].md`.

```markdown
# Goals — YYYY Q[N]
**Set:** YYYY-MM-DD (start of quarter)
**Reviewed:** YYYY-MM-DD (mid-quarter checkpoint)
**Closed:** YYYY-MM-DD (end of quarter, after /docs review)

## Vision Alignment
[1 sentence connecting these goals back to VISION.md]

## Objective 1: [Outcome statement, not activity]
**Why this matters now:** [1 line]
- KR1: [measurable, specific, time-bound] — Target: [number / state] — Actual: [filled at review]
- KR2: ... — Target: ... — Actual: ...
- KR3 (max): ... — Target: ... — Actual: ...

## Objective 2: [...]
[same structure]

## Objective 3 (max): [...]
[same structure]

## Side Quest Budget
~20% of week reserved for unscheduled prototypes (the MCP origin pattern).
Tracked: [running list of side quests started this quarter]

## What I Decided NOT to Pursue This Quarter
- [Tempting but not aligned with vision]
- [Came up but doesn't move objectives]

## Mid-Quarter Checkpoint (week 6 of 13)
[Filled at review: on track / off track / pivoting? Adjust if needed.]
```

**Rules:**
- **3 objectives MAX.** More = unfocused.
- **3 KRs per objective MAX.** More = noise.
- **Outcomes, not activities.** "Ship X" is an activity. "X has 100 users daily" is an outcome.
- **Measurable.** "Better at React" is not. "Built 3 production React apps using server components" is.
- **Side quest budget is intentional.** Otherwise you'll do them anyway and feel guilty.

### `/docs review` — quarterly review (RETRO-YYYY-Q[N].md)

Honest goal-vs-actual at end of quarter. Save to `D:/Git repos/goals/RETRO-YYYY-Q[N].md`.

```markdown
# Quarterly Review — YYYY Q[N]
**Period:** [start] to [end]
**Goals doc:** GOALS-YYYY-Q[N].md

## Goal Achievement
| Objective | KR | Target | Actual | Status |
|---|---|---|---|---|
| O1 | KR1 | ... | ... | ✅ / ⚠️ / ❌ |
| O1 | KR2 | ... | ... | ... |

Score per objective: 0.0 (no progress) → 1.0 (achieved). Aim for 0.6–0.7 average — if you're hitting 1.0, goals were too easy.

## What Got Shipped This Quarter
[List from `git log` across projects + `posts/published/` + `demos/*` summaries]

## Goal Misalignment Audit
**Did the work match the goals I set?**
- Time on aligned work: ~%
- Time on side quests (out of 20% budget): ~%
- Time on emergencies / unplanned: ~%
- Time on goals NOT pursued (admit it): ~%

**Goals I set but didn't pursue:** [list, brutally honest]
**Work I did that wasn't in goals:** [list — was it valuable? Should it be next quarter's goal?]

## Lessons This Quarter
- [Pattern observed across multiple weeks]
- [Estimation error — what did I consistently misjudge?]
- [Process change to try next quarter]

## Skill Audit Cross-Quarter
[Run `/prune-skills` here — which skills used 3x/week+, which idle]

## Next Quarter
- [3 things I'm carrying forward]
- [3 things I'm dropping]
- [1 brave bet for next quarter]

## Vision Recheck
- Does VISION.md still feel right? [yes/no/adjust]
- If adjust: [1-line update]
```

**Trigger:** Run on last Friday of each quarter (Mar 31 / Jun 30 / Sep 30 / Dec 31 — adjust to last working day).
Auto-suggest at session-start if it's been 90+ days since last review.

**Rule:** Be brutally honest. Goals you didn't pursue = admit it. Self-deception kills the loop.

### `/docs contributing` — CONTRIBUTING.md
Detect setup, generate Development Setup, Workflow, Standards, PR Process, CoC link.

### `/docs security` — SECURITY.md
```markdown
# Security Policy
## Reporting a Vulnerability   ← Private channel, NOT GitHub issue
## Supported Versions
## Security Considerations
## Known Limitations
## Security History   ← CVEs fixed
```

### `/docs troubleshooting` — TROUBLESHOOTING.md
1. Grep codebase for common error messages
2. Read `tasks/lessons.md`
3. Generate with EXACT error messages (users search for them):
```markdown
### Error: [exact text]
**Cause:** [Why this happens]
**Fix:** [Specific steps]
**Prevention:** [How to avoid]
```

## Universal Rules
- Match existing doc style — emojis if used, none if not
- Never duplicate info — link to source of truth
- Focus on WHY, not WHAT (code shows WHAT)
- EXACT error messages in troubleshooting (users search them)
- NEVER silently change docs — report what's changing and why
- Update, don't rewrite — preserve history
- Every command in docs MUST actually work (test before documenting)
- Every PR involving feature/API change → `changelog` + `sync`
- Every architectural decision → `adr`
- Every recurring user problem → `troubleshooting`

## When to Use Which Mode
| Situation | Mode |
|---|---|
| After code changes | `changelog` |
| Opening PR | `pr` |
| Tagging release | `release` |
| Before PR/release | `sync` |
| Project kickoff | `idea`, `roadmap`, `contributing`, `security` |
| Major technical decision | `adr` |
| Recurring user problem | `troubleshooting` |
| Roadmap shifts | `roadmap` |
