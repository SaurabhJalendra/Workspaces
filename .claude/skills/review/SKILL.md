---
name: review
description: Reviews code changes for bugs, security, quality, project convention adherence. Use for trivial reviews; for non-trivial, use /code-review (4+N parallel) or /adversarial-reviewer (3 personas).
allowed-tools: [Read, Grep, Glob, Bash]
---

# Code Review (Light)

For trivial changes only. Non-trivial → `/code-review` or `/adversarial-reviewer`.

## Steps
1. Get diff: `git diff` (unstaged) or `git diff --cached` (staged)
2. Review for:
   - **Bugs**: logic errors, off-by-one, null/undefined access
   - **Security**: injection, auth bypass, data exposure
   - **Quality**: naming, complexity, duplication
   - **Conventions**: matches project patterns?
   - **Tests**: changes tested?
3. Run typecheck if available
4. Run linter if available

## Output Format
For each issue:
- CRITICAL / WARNING / SUGGESTION
- File:line — what's wrong — how to fix

If clean: "Code looks good. No issues found."
