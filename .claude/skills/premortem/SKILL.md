---
name: premortem
description: Pre-mortem before risky work — imagine the failure, work backward to prevent. Counterpart to /postmortem. Run before any change touching prod, security, irreversibility, or 5+ files.
allowed-tools: [Read, Write, Bash, Grep]
---

# /premortem — Imagine Failure Before Building

Anthropic does pre-mortems before risky work. The act of writing one forces you to enumerate failure modes you'd otherwise discover the hard way.

## When to invoke

- Before any deploy to production
- Before any irreversible operation (data migration, schema change, force-push, rebase of shared branch)
- Before changes touching auth, payments, secrets, user data
- Before any feature spanning 5+ files or 200+ lines
- Before any architectural shift (new framework, database, infra)
- Before merging a long-running feature branch
- When user says "this might be risky" / "I'm not sure about this"

## Output Path

`premortems/YYYY-MM-DD-short-name.md` (workspace level)
OR
`<project>/premortems/YYYY-MM-DD-short-name.md` (project level if scope is purely local)

## Template

```markdown
# Pre-mortem: [Change Description]
**Date:** YYYY-MM-DD
**Author:** Saurabh
**Status:** [draft / reviewed / approved-to-proceed / aborted / superseded]

## What We're About To Do
[2-3 sentences: the change, in plain English. Files/systems affected. Blast radius.]

## Why This Is Risky
[The specific risk profile — irreversibility, blast radius, dependency on external systems, etc.]

## Imagined Failure: "It's 2 weeks from now and this went wrong"
[Write the postmortem you'd write if this fails. Concrete failure mode, root cause, who/what is impacted, recovery path.]

## Top 5 Things That Could Go Wrong

1. **[Failure mode]**
   - Likelihood: [low/med/high]
   - Impact: [low/med/high]
   - Detection: [how would we notice?]
   - Mitigation: [pre-flight check / rollback plan / monitoring]

2. ...

## Pre-flight Checklist (must all be ✓ before proceeding)
- [ ] Backup taken (if data involved)
- [ ] Rollback plan written and tested
- [ ] Test suite passes including edge cases listed above
- [ ] Monitoring/logging in place for failure detection
- [ ] Soak period observed (if intelligence-impacting)
- [ ] Adversarial review run (`/code-review` or `/adversarial-reviewer`)
- [ ] `/security-scan` if security-sensitive
- [ ] Change is reversible OR irreversibility is documented and accepted

## Decision
- [ ] Proceed (all checks passed, mitigations in place)
- [ ] Defer (mitigation gaps identified — list below)
- [ ] Abort (risk exceeds benefit)

## If Something Goes Wrong During Execution
[Specific steps for the next 30 minutes if you see signs of the failure modes above]
```

## Rules

- **NEVER skip a pre-mortem on operations marked irreversible.** "Force push" / "drop table" / "migrate data" without pre-mortem = abort.
- **The pre-mortem itself is the gate.** If you can't articulate the failure, you don't understand the risk.
- **Adversarial finalization**: dispatch a subagent with prompt *"What failure mode is this pre-mortem missing? Be specific."* — append findings.
- **Update `wiki/log.md`** after writing: `## [YYYY-MM-DD] premortem | [name]`
- **After execution**: if it worked, archive to `premortems/_executed/` with outcome note. If it failed, the pre-mortem becomes the start of the postmortem.
