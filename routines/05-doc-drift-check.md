# Doc Drift Check (Friday)

**Cron:** `0 12 * * 5` (Fridays at noon)
**Model:** Sonnet (drift detection is pattern-matching)

## Prompt to paste

```
You are my Doc Drift Check routine. Run every Friday at noon.

Goal: catch docs that have drifted from code — stale README claims,
outdated API docs, missing CHANGELOG entries for shipped features.

STEPS:

1. For each active project under D:/Git repos/, run /docs sync:
   - Read all docs: README, CHANGELOG, docs/*.md, inline docstrings
   - Compare claims to current codebase
   - Flag discrepancies

2. For each drift detected, classify:
   - 🔴 Doc-claims-feature-that-doesn't-exist (lying to users)
   - 🟡 Doc-missing-recent-feature (incomplete)
   - 🔵 Doc-has-broken-link (housekeeping)
   - 🟢 Code-changed-but-CHANGELOG-empty (procedural miss)

3. For each 🔴 finding: auto-fix where possible (delete the stale claim,
   add a note that the feature was removed). Commit as
   "docs: remove stale claim about X (no longer in codebase)".

4. For each 🟡/🟢 finding: write to project inbox.md as
   "DOC: [file:line] needs update — [what's missing]".

5. Aggregate to D:/Git repos/posts/drafts/doc-drift/YYYY-MM-DD.md

6. Push: "Doc drift check: N projects clean, M projects have drift, X
   auto-fixed, Y need your input"

RULES:
- Never auto-fix in a way that loses information — always ASK before
  removing a feature claim, only auto-fix when the feature definitely
  doesn't exist anymore (no matching file/function/endpoint)
- For projects with no docs at all, flag once but don't nag weekly
- CHANGELOG.md missing entries for shipped features is the highest-value
  catch — most users find features via CHANGELOG
```
