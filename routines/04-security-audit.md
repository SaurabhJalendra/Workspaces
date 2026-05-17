# Weekly Security Audit (Monday)

**Cron:** `0 8 * * 1` (Mondays at 8 AM)
**Model:** Sonnet (mostly tool execution + parsing)

## Prompt to paste

```
You are my Weekly Security Audit routine. Run every Monday at 8 AM.

Goal: catch newly-disclosed CVEs, drifting dependencies, accidental secret
commits, license issues — across all active projects, before they ship.

STEPS:

1. For each active project under D:/Git repos/:
   - Personal-Agents
   - Email_daily_newsletter_summary
   - Analyst_agentic_coder
   - SKY-ai
   - agency
   - Trading-Agent

2. For each project, run /security-scan (the workspace skill):
   - Phase 1: SAST (Semgrep, Bandit, ESLint security, gosec — per stack)
   - Phase 2: Dep audit (npm audit, pip-audit, cargo audit — per stack)
   - Phase 3: Secret scan (gitleaks)
   - Phase 4: License compliance check

3. For each project, write `security-reports/YYYY-MM-DD-monday.md` with
   findings.

4. Aggregate critical/high findings across all projects to:
   D:/Git repos/posts/drafts/security-audits/YYYY-MM-DD.md

5. For each CRITICAL or HIGH:
   - Add an entry to that project's inbox.md as:
     "SECURITY: [package@version] CVE-XXXX — [recommendation]"

6. Send push: "Security audit done. N critical, M high. Top: [most urgent]"

RULES:
- BLOCK any project from /go this week if it has unfixed CRITICALs
- Don't fail on missing tools (e.g., no semgrep installed) — skip and
  note in report
- For dep bumps that are SAFE (patch/minor with passing CI history),
  optionally open a PR via /go's auto-fix mode
- Report exactly which CVEs apply to which specific versions
```
