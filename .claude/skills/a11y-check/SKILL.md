---
name: a11y-check
description: Accessibility audit for UI changes — WCAG compliance via axe-core/pa11y, keyboard navigation test, color contrast, ARIA correctness. Auto-fires on UI changes. Required for any user-facing app.
allowed-tools: [Bash, Read, Edit, Write, Grep, Glob]
---

# /a11y-check — Accessibility Audit

A11y is increasingly legal in many jurisdictions (ADA, EAA, EN 301 549). Beyond legal: it's a quality bar. Skipping it = shipping broken software for ~15% of users.

## When to invoke

**Auto-fire (Tier 2):**
- UI components added or modified
- Color/theme changes
- Form changes
- Navigation changes
- Before any release of a user-facing app

**User-invoked:**
- `/a11y-check` — run full audit on dev server
- `/a11y-check [url]` — audit specific URL/page

## Setup (first time)

```bash
# Node project
npm install --save-dev @axe-core/cli pa11y
# Or use Playwright with @axe-core/playwright

# Python (Django/Flask) — use axe-core via Selenium or Playwright
pip install playwright
playwright install
pip install axe-playwright-python
```

Add a `wcag-level` to project config (typically `AA`):
```json
// In package.json or project config
{
  "a11y": {
    "wcag_level": "AA",
    "rules_disabled": [],
    "_comment": "Disable rules only with documented justification"
  }
}
```

## Audit Steps

### Step 1: Static check (axe-core CLI)
```bash
# Spin up dev server first
npm run dev &
DEV_PID=$!

# Wait for ready, then audit
sleep 5
npx axe http://localhost:3000 --tags wcag2a,wcag2aa --exit

# Kill dev server
kill $DEV_PID
```

### Step 2: Per-route audit (if multi-page)
```bash
# Loop through key routes
for route in / /about /signup /dashboard; do
  npx pa11y http://localhost:3000$route --standard WCAG2AA
done
```

### Step 3: Keyboard navigation test
Manual or via Playwright script:
```javascript
// Tab through page, verify focus visible, no traps
await page.keyboard.press('Tab');
// Check focused element is visible + has clear focus ring
```

### Step 4: Color contrast (if theme change)
```bash
# Use axe-core which includes contrast checks
# Or specifically:
npx pa11y http://localhost:3000 --include-warnings --runner axe
```

### Step 5: ARIA correctness
- Roles match actual element behavior
- aria-label / aria-labelledby on interactive elements
- aria-live regions for dynamic content
- No conflicting/duplicate landmarks

## Output

```markdown
# A11y Audit — YYYY-MM-DD HH:MM
**Project:** [name]
**WCAG level:** AA
**URL/route:** [route audited]

## Summary
- Violations: N
- Warnings: M
- Pass: P checks

## Critical Violations (block merge)
1. **[rule-id]** — [description]
   - Element: `selector`
   - Impact: critical / serious / moderate / minor
   - Fix: [specific guidance]
   - Ref: https://dequeuniversity.com/rules/axe/[rule]

## Warnings (review and triage)
[similar format]

## Manual Verification Needed
- [ ] Tab order logical
- [ ] Focus indicators visible on all interactive elements
- [ ] Screen reader announces dynamic content
- [ ] Forms have proper label associations
- [ ] Modal dialogs trap focus correctly

## Recommendation
[Fix critical violations before merge. Document any disabled rules with reason in project config.]
```

## Anti-Patterns

- **Disabling rules to silence the linter** — disable only with documented justification (real false positive)
- **WCAG A only** — that's the floor. Aim for AA at minimum (most jurisdictions require it)
- **Skipping keyboard test** — automated tools miss focus management bugs
- **Dark UI without contrast check** — most "stylish" dark themes fail contrast for body text
- **Putting a11y at end of project** — bake in from start; retrofitting is 3-5x more expensive

## Common Quick Wins

- Add `<html lang="en">` (catches ~5% of users with screen readers)
- Visible focus rings (`*:focus-visible { outline: 2px solid ... }`)
- Alt text on every `<img>` (or `alt=""` for decorative)
- Form labels: `<label for="email">Email</label><input id="email">`
- Skip-to-main-content link at top of page
- `aria-current="page"` on active nav item
- Min touch target 44×44px on mobile

## Integration

- `/go` step adds a11y check before commit if UI files changed (detect via git diff)
- New UI project: install axe-core in initial setup
- Append to `wiki/log.md`: `## [YYYY-MM-DD] a11y | [violations count] | [route]`
- Failures should block merge unless explicitly waived with justification

## When NOT to run

- Pure backend / API project with no UI
- CLI tool
- Library code with no examples
