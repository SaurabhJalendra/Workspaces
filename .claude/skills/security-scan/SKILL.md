---
name: security-scan
description: Comprehensive security audit — SAST (static analysis), dependency vulnerabilities, secret scan, license compliance. Run before any release or when shipping security-sensitive changes.
allowed-tools: [Bash, Read, Grep, Glob]
---

# /security-scan — Composite Security Audit

Runs in 4 phases. Each phase is independent — partial results are useful.

## Auto-detect stack and run appropriate scanners

### Phase 1: SAST (Static Application Security Testing)

| Stack | Tool | Command |
|---|---|---|
| Python | bandit | `bandit -r . -ll -f txt` (low+ severity) |
| Python | semgrep | `semgrep --config=auto .` (if installed) |
| JS/TS | eslint security | `npx eslint . --plugin security` |
| JS/TS | semgrep | `semgrep --config=p/javascript .` |
| Go | gosec | `gosec ./...` |
| Rust | cargo-audit + clippy | `cargo clippy -- -W clippy::all && cargo audit` |
| Java | spotbugs | `spotbugs -textui .` |
| Multi-stack | semgrep | `semgrep --config=p/security-audit .` |

**Default if available:** `semgrep --config=auto .` — covers most languages.

If no SAST tool detected, install one:
```bash
pip install semgrep   # universal, recommended
```

### Phase 2: Dependency Vulnerability Audit

| Stack | Command | Severity threshold |
|---|---|---|
| Node | `npm audit --audit-level=high` | high+ fails |
| Node | `npx better-npm-audit audit` | with whitelist support |
| Python (pip) | `pip-audit` | any vuln fails |
| Python (poetry) | `poetry export -f requirements.txt \| pip-audit -r /dev/stdin` | |
| Rust | `cargo audit` | any vuln fails |
| Go | `govulncheck ./...` | any vuln fails |
| Ruby | `bundler-audit check --update` | any vuln fails |

### Phase 3: Secret Scan

```bash
# Preferred: gitleaks
gitleaks detect --source . --no-banner

# Fallback: trufflehog
trufflehog filesystem . --only-verified

# Quick git history check
gitleaks detect --source . --log-opts="--all"
```

If neither installed:
```bash
# install gitleaks (one-time)
# Windows: scoop install gitleaks  OR  winget install gitleaks
# Mac: brew install gitleaks
# Linux: see https://github.com/gitleaks/gitleaks
```

### Phase 4: License Compliance

```bash
# Node
npx license-checker --summary

# Python
pip-licenses --format=markdown

# Rust
cargo-license

# Generic: list dependencies, flag GPL/AGPL/proprietary in commercial projects
```

## Output Format

```markdown
# Security Scan — YYYY-MM-DD HH:MM

## Phase 1: SAST
- Tool used: [bandit / semgrep / etc.]
- Findings: [count by severity]
- CRITICAL: [list with file:line]
- HIGH: [list]

## Phase 2: Dependency Audit
- Total deps scanned: N
- Vulnerabilities: [count by severity]
- CRITICAL/HIGH: [package@version → CVE → suggested fix]

## Phase 3: Secrets
- Scanner: [gitleaks / trufflehog]
- Findings: [list with file:line:type]
- Fix: rotate exposed secret AND remove from git history (`git filter-repo`)

## Phase 4: License
- Total deps: N
- License breakdown: [MIT: N, Apache: N, GPL: N, ...]
- Flagged: [list of GPL/AGPL/proprietary in commercial code]

## Action Items
- [ ] Fix CRITICAL findings before next release
- [ ] Update dependencies with known CVEs
- [ ] Rotate any exposed secrets
- [ ] Review flagged licenses with legal if commercial

## Pass/Fail
PASS if: 0 CRITICAL findings, 0 HIGH unfixed deps, 0 verified secrets, 0 license issues
FAIL otherwise — block release.
```

## When to Run

- **Before any release / production deploy** (mandatory)
- **After adding new dependencies** (catch supply-chain risk early)
- **Weekly on main branch** (catch newly-disclosed CVEs)
- **Before opening sensitive PRs** (auth, payments, PII handling)
- **As part of `/go` for security-tagged repos** (set in CLAUDE.md)

## Auto-Suggestion Triggers

Claude should suggest `/security-scan`:
- Before any merge to main on a deployed project
- After adding/updating dependencies (`package.json`, `requirements.txt`, etc. modified)
- When user mentions "deploy" / "release" / "ship to production"
- When code touches authentication, secrets, payments, or user data
- 7+ days since last scan on active project

## Rules
- NEVER swallow failures — report all findings, even if user says "ship anyway"
- For exposed secrets: STOP and require rotation BEFORE any commit
- Cache results to `.security-scan/last-run.md` so /morning can show last scan date
- If a scanner isn't installed, suggest install — don't skip the phase silently
