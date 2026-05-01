---
name: perf-budget
description: Define and enforce performance budgets — bundle size, API latency, memory, render time. Catches regressions at PR time, not in production. Auto-fires on UI changes, API changes, build configuration changes.
allowed-tools: [Bash, Read, Edit, Write, Grep, Glob]
---

# /perf-budget — Set and Enforce Performance Budgets

Performance regressions are easy to ship and hard to recover from. Budget = pass/fail line at PR time.

## When to invoke

**Auto-fire (Tier 2):**
- Project has no `perf-budget.json` yet (first-time setup)
- UI/build configuration changes (`webpack.config`, `vite.config`, `next.config`, etc.)
- API endpoint added or significantly modified
- Before any release or major branch merge

**User-invoked:**
- `/perf-budget set` — initialize budgets for this project
- `/perf-budget check` — run audit against current budgets
- `/perf-budget update` — modify thresholds with justification

## Setup (first time per project)

Create `perf-budget.json` at project root:

```json
{
  "version": 1,
  "_comment": "Targets are pass/fail. Update deliberately with justification, never silently lower.",
  "frontend": {
    "bundle_size_kb_max": 250,
    "lighthouse_performance_min": 90,
    "lcp_ms_max": 2500,
    "fid_ms_max": 100,
    "cls_max": 0.1,
    "ttfb_ms_max": 600
  },
  "api": {
    "p50_ms_max": 100,
    "p95_ms_max": 500,
    "p99_ms_max": 1000,
    "memory_mb_max": 512
  },
  "build": {
    "build_time_seconds_max": 60,
    "dependency_count_max": 200
  },
  "_history": [
    { "date": "YYYY-MM-DD", "change": "initial baseline", "actor": "Saurabh" }
  ]
}
```

Tune values based on the project's actual baseline. For a side project, budgets can be loose. For production, tighter.

## Check (run on every PR / before merge)

### Frontend bundle size
```bash
# Webpack/Vite
npx webpack-bundle-analyzer --no-open --report stats.json
# Or use vite-plugin-visualizer
# Or simply: du -sk dist/ | awk '{print $1}'
```
Compare against `bundle_size_kb_max`. Fail if over.

### Lighthouse (if frontend project with deployable URL)
```bash
npx lighthouse http://localhost:3000 --only-categories=performance --output=json --quiet
# Parse score, compare to lighthouse_performance_min
```

### API latency (if API project)
```bash
# Use k6, autocannon, or wrk
npx autocannon -c 10 -d 30 http://localhost:3000/api/endpoint
# Parse p50/p95/p99, compare to budgets
```

### Build time
```bash
time npm run build
# Compare to build_time_seconds_max
```

### Dependency count drift
```bash
# Node
npm ls --all --json | jq '.dependencies | length'
# Python
pip list | wc -l
```
Compare to `dependency_count_max`.

## Output

```markdown
# Perf Budget — YYYY-MM-DD HH:MM
**Project:** [name]
**Budget version:** N

## Results
| Metric | Budget | Actual | Status |
|---|---|---|---|
| Bundle size (kb) | 250 | 234 | ✅ |
| Lighthouse perf | 90 | 87 | ❌ FAIL |
| LCP (ms) | 2500 | 2100 | ✅ |
| API p95 (ms) | 500 | 612 | ❌ FAIL |
| ... | ... | ... | ... |

## Failures (block merge)
1. **Lighthouse perf 87 < 90 budget** — investigate which metric dropped.
   Hint: check render-blocking resources, image dimensions.
2. **API p95 612ms > 500ms budget** — find slow endpoint.
   Run: `autocannon -c 10 -d 30 http://localhost:3000/api/[endpoint]` per endpoint.

## Recommendation
Fix failures or update budget WITH JUSTIFICATION (append to perf-budget.json _history).
Never silently lower a budget.
```

## Updating Budgets (deliberate, not silent)

If budget needs to change:
1. State why in commit message
2. Append entry to `perf-budget.json._history`:
   ```json
   { "date": "YYYY-MM-DD", "change": "raised LCP from 2500 to 3000ms — image-heavy hero now intentional", "actor": "Saurabh" }
   ```
3. Commit budget change as separate commit from feature change

## Anti-Patterns

- **Silently lowering budgets to make tests pass** — the budget is the contract. Lowering = breaking the contract.
- **Setting budgets too loose at start** — meaningless. Calibrate from a known-good baseline.
- **Ignoring failures** — if budget fails, either fix or formally update. Don't ship through.
- **Skipping check on "small" changes** — small UI tweaks compound into big regressions over months.

## Integration

- `/go` step 1d should call `/perf-budget check` if `perf-budget.json` exists
- New project setup: ask user to set initial budgets after first deployable build
- Append to `wiki/log.md`: `## [YYYY-MM-DD] perf-budget | [pass/fail summary]`
