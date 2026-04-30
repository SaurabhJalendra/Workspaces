---
name: go
description: End-to-end ship workflow — verify (tests + type + lint + coverage + dep audit), simplify, adversarial review, commit, push, optionally PR. Boris Cherny's daily driver pattern, hardened with traditional SE gates.
allowed-tools: [Bash, Read, Edit, Grep, Glob, Agent, Skill]
---

# /go — Ship It (with all the gates)

Boris Cherny's daily driver, hardened with traditional SE quality + security gates.
Composed from primitives.

## Steps

### 1. Verify — ALL gates must pass (NEVER skip any)

#### 1a. Tests
- Run project test command (detected from package.json/pyproject.toml/go.mod/Cargo.toml)
- If tests fail: STOP. Fix and rerun.
- For UI: spin up dev server, screenshot, compare expected

#### 1b. Type check (if stack supports)
| Stack | Command |
|---|---|
| TypeScript | `npx tsc --noEmit` |
| Python | `mypy .` (if mypy.ini exists) |
| Rust | `cargo check` |
| Go | `go vet ./...` |
- If type errors: STOP. Fix.

#### 1c. Linter
| Stack | Command |
|---|---|
| JS/TS | `npx eslint . --max-warnings=0` |
| Python | `ruff check .` or `pylint **/*.py` |
| Go | `golangci-lint run` |
| Rust | `cargo clippy -- -D warnings` |
- If lint errors: STOP. Fix.

#### 1d. Coverage threshold (if .coveragerc / jest.config / pyproject configures it)
- Run coverage tool: `pytest --cov` / `npx jest --coverage` / `cargo tarpaulin`
- If coverage drops below project threshold: STOP. Add tests or update threshold deliberately.

#### 1e. Dependency vulnerability audit (quick)
| Stack | Command |
|---|---|
| Node | `npm audit --audit-level=high` |
| Python | `pip-audit` (if installed) |
| Rust | `cargo audit` |
| Go | `govulncheck ./...` |
- If high+ severity: STOP, run `/security-scan` for full analysis.

#### 1f. Run `verification-before-completion` skill
- Final adversarial sanity check.

### 2. Simplify
- Run `simplify` skill on changed files
- Look for: dead code, redundant abstractions, over-engineering
- Apply changes; rerun tests if simplify changed implementation

### 3. Adversarial Review
- Dispatch `/code-review` (4+N parallel from anthropics/claude-code plugin) if available
- Otherwise: `adversarial-reviewer` skill (3 personas, mandatory dissent)
- Address CRITICAL findings before proceeding
- WARNINGs: address or document why deferred

### 4. Commit
- Use `/commit` skill — specific staging, conventional commit message
- Pre-commit hooks fire here automatically (gitleaks, lint, format, commitlint)
- If pre-commit fails: STOP, fix, NEVER use --no-verify

### 5. Push
- `git push` to current branch
- If on main: ASK FIRST. Never auto-push to main.

### 6. PR (only if user said "and PR" or branch != main)
- `gh pr create` with summary from commits + diff
- Title: imperative, under 70 chars
- Body: Summary / Changes / Testing / Notes
- Open in browser: `gh pr view --web`

## Special: Security-Sensitive Changes

If changes touch: auth, secrets, payments, user data, file uploads, deserialization, eval — also run `/security-scan` before push.

Detect via: changed paths matching `auth/`, `payment/`, `crypto/`, `secret/`, `upload/`, `admin/`, `api/users/`, etc.

## Anti-Pattern Guards
- NEVER auto-push to main without confirmation
- NEVER skip ANY of the 1a-1f gates
- NEVER use --no-verify to bypass pre-commit
- NEVER commit .env or credentials
- If verification fails 3 times, STOP and ask user
- If `/security-scan` finds CRITICAL, BLOCK release until fixed
