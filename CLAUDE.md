# Saurabh Workspace — Universal Rules

> This is the workspace-level CLAUDE.md. Loaded automatically when Claude Code is opened from `D:/Git Repos/` or any child folder via the multi-root VS Code workspace.
>
> Two workspace files exist (the repo set differs per machine): `saurabh-pc.code-workspace` and `saurabh-laptop.code-workspace`. Open the one matching the current machine.
>
> Project-specific rules live in each repo's own `CLAUDE.md` under `D:/Git Repos/<repo>/`. They override or extend these universal rules.

---

## Workflow Orchestration

### 1. Plan Mode Default
- Enter plan mode (Shift+Tab×2) for ANY non-trivial task (3+ steps or architectural decisions)
- For UI/design tasks: ALWAYS plan mode first — state files affected, components reused, blast radius
- If something goes sideways, STOP and re-plan immediately

### 2. Subagent Strategy
- Offload research, exploration, parallel analysis to subagents
- One task per subagent
- **Fork** (omit subagent_type) when child needs context — reuses KV cache
- **Explore** for quick codebase searches — Haiku, fast and cheap
- **deep-research** for multi-source investigation
- **general-purpose** for independent implementation
- Never delegate understanding — synthesize findings yourself

### 3. Adversarial Review by Default
- After ANY non-trivial implementation: run `/code-review` (4+N parallel from anthropics/claude-code plugin)
- For high-stakes changes: also run `/adversarial-reviewer` (3 personas, mandatory dissent)
- Single-agent review = same blind spots as implementer

### 4. Verification Before Done
- Never claim done without proof — run tests, check logs, screenshot UI
- Use `superpowers:verification-before-completion` reflexively
- For UI: dev server + screenshot + compare to spec/Figma
- "Would a staff engineer approve this?"

### 5. Self-Improvement
- ANY correction → append pattern to `tasks/lessons.md` (in current project)
- Suggest CLAUDE.md updates at session end (don't auto-apply mid-session)

### 6. Autonomous Execution
- Don't ask permission for: read files, search web, run safe commands, run tests
- Don't say "would you like me to..." — just do it
- ONLY pause for: destructive git ops, sending external messages, spending money, deploying to prod

### 6a. Never Delegate Understanding
- The coordinator MUST synthesize subagent findings — never write "based on your findings, implement X"
- If you delegate, you can't catch contradictions between agents
- Read all subagent output, understand it, write the plan yourself
- This is Anthropic's documented coordinator-prompt rule, verbatim

### 6b. Test-Driven Development by Default
- For features and bug fixes: write the failing test FIRST, then the fix
- Use `superpowers:test-driven-development` skill reflexively, not as exception
- The test that fails BEFORE your fix is the one that proves the fix works
- Skip TDD only for: typo fixes, doc-only changes, refactor with comprehensive existing tests

### 6c. Coordinator Pattern for Non-Trivial Work
- Any work touching 5+ files OR 200+ lines OR new architecture: use `/coordinator` skill
- 4 phases: Research (parallel) → Synthesize → Implement → Verify (adversarial)
- Skip for trivial work — overhead exceeds value
- See `.claude/skills/coordinator/SKILL.md` for full spec

### 6d. Pre-Mortem Before Risky Work
- Use `/premortem` BEFORE: deploys, irreversible operations, security-sensitive changes, 5+ file changes, architectural shifts
- Imagining failure forces enumeration of failure modes you'd otherwise discover the hard way
- Pre-mortem itself is the gate — if you can't articulate the failure, you don't understand the risk

### 6e. Simplify on Every Model Upgrade
- When Anthropic releases a new Claude model: spend an hour with `simplify` skill on hooks, skills, slash commands, CLAUDE.md
- Cherny: every release deletes code that the previous model couldn't handle
- Push complexity into the model, not the harness

### 6f. ADR Mandatory Before Major Architectural Decisions
- Choosing framework, database, deployment infra, message queue, auth pattern → write `/docs adr "decision"` FIRST
- Decision-without-ADR = we forget WHY in 3 months and can't undo
- ADRs are immutable when accepted; supersede with new ADR

### 6g. Internal-DAU Hard Gate Before Public Ship
- Use the feature yourself daily for 7 consecutive days BEFORE any public release
- Skip a day without external reason → not ready, reset the counter
- Anthropic's launch criterion: internal DAU goes vertical. Solo equivalent: yourself.

### 6h. Token Economy & Cache Discipline
- This file: static content first, variable content last (cache optimization)
- Do NOT edit CLAUDE.md mid-session — busts entire prompt cache
- Keep MCP server list stable within a session
- Fork subagents reuse parent's KV cache — prefer fork when context sharing needed
- For long output, say "complete implementation" upfront (8K → 64K cap escalation)
- Cite token cost when proposing skill/agent additions — every skill adds ~200 tokens to listings
- Run `/prune-skills` quarterly to keep listings lean

### 6i. Skill Creation Pattern (Auto Skill Creation)
- Workflow done 3+ times same pattern → automatically create a skill
- Use `superpowers:writing-skills` for skill creation
- Skills need: precise 250-char description, allowed-tools, clear instructions
- Don't ask — build it, save it, tell user what was created
- Save to `D:/Git Repos/.claude/skills/<name>/SKILL.md` (workspace) or `<project>/.claude/skills/` (project-specific)

### 6j. Memory Active Use
- MEMORY.md index loaded automatically — Sonnet picks up to 5 relevant files
- Save: architecture decisions, user preferences, key findings, feedback patterns
- Don't save: code patterns derivable from codebase, git history, ephemeral details
- Update memory on: any correction from user, any non-obvious finding, any preference declaration
- Memory location: `C:/Users/Saurabh/.claude/projects/<workspace-id>/memory/`
- Reference memory before answering questions about prior work — don't re-derive from search

### 6l. Repository Hygiene (Standards That Compound)

Beyond skills — these are background standards every project follows:

- **Lockfiles committed:** `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`, `Cargo.lock`, `poetry.lock`, `go.sum`. NEVER `.gitignore` these. Reproducible builds depend on them.
- **Dependency updates automated:** Renovate or Dependabot configured per project. Auto-PR for non-major bumps; review for major.
- **License declared:** every project has a `LICENSE` file (MIT/Apache/BSD/proprietary — pick one explicitly). Public repos without LICENSE = unusable by others.
- **README mandatory:** every project has a README with: purpose (1 sentence), setup (commands), usage (1 example), license. Even side projects.
- **CHANGELOG maintained:** `CHANGELOG.md` updated on every meaningful commit (handled by `/docs changelog`). Keep-a-Changelog format.
- **CI/CD on every push:** at minimum, run tests + lint on push. Free for small projects (GitHub Actions).
- **SBOM at release:** for projects shipping artifacts, generate Software Bill of Materials. `npx @cyclonedx/cyclonedx-npm`, `pip-licenses`, `cargo cyclonedx`.
- **Performance budget defined:** projects with UI or API have `perf-budget.json` (see `/perf-budget` skill).
- **A11y level declared:** UI projects target `WCAG AA` minimum. Documented in project config.

When starting a new project: surface this list. When working on existing project missing items: surface gaps without nagging (one mention per session max).

### 6m. Cost Economics Awareness

Anthropic measures every design decision in tokens (Gtok/week). Solo equivalent:

- **Token cost:** be aware of session token cost. `/compact` aggressively when context approaches limits. Fork subagents to reuse KV cache. Edit existing files instead of rewriting.
- **Cloud cost:** any cloud service (AWS, GCP, Vercel, etc.) — set billing alerts. Test with smallest tier. Profile cost before scaling.
- **API cost:** for AI/ML features, track API spend per feature. Surface unexpected spikes.
- **Build cost:** CI minutes are finite (free tier). Use caching aggressively. Skip CI on doc-only changes.

When proposing infrastructure choices: state cost trade-off, not just technical trade-off.

### 6k. Auto Mode Behavior (Saurabh runs Claude in Auto Mode)

**The user always runs in auto mode.** Pre-decide Tier 2 vs Tier 3 vs Tier 4 — don't make the user invoke routine commands.

**Tier 1 (truly automatic — settings.json hooks):**
Formatter, dangerous-bash blocker, pre-commit hooks. Fire without involvement.

**Tier 2 (auto-invoke without asking):**
- `/morning` on first user message of session — RUN, don't ask
- `documenter` agent after meaningful code change — RUN, don't ask
- `/docs changelog` after commits — RUN, don't ask
- `/code-review` after non-trivial change before commit — RUN, don't ask
- `/explore` before working in unfamiliar code — RUN, don't ask
- `simplify` after major code changes — RUN, don't ask
- `/security-scan` Phase 2 (dep audit) when dependencies modified — RUN, don't ask
- `/security-scan` full when about to deploy/release — RUN, don't ask
- `/docs threat-model` when implementing auth/payment/secret/upload code — RUN, don't ask
- `verification-before-completion` before claiming done — RUN, don't ask

When auto-running: state in ONE sentence what's being run, then run it. Don't list options.

**Tier 3 (mention then run, unless trivial path):**
- `/go` — when user signals shipping intent ("commit and push", "ship it", "ready"); state "running /go" and run
- `/wrap` — at end-of-session signals ("wrapping up", "EOD"); state "running /wrap" and run
- `/postmortem` — after detected failure or near-miss; state "writing postmortem" and run
- `/premortem` — before risky work (deploys, irreversibles, 5+ files); state "running premortem first" and run

**Tier 4 (PAUSE — never auto in auto mode):**
- Push to main / production deploys
- Force push, hard reset, branch deletion
- Removing dependencies (vs adding)
- Sending external messages (Slack, email, GitHub PR posts)
- Spending money (paid API calls, services, infra)
- Bypassing pre-commit (`--no-verify`)
- Disabling tests / lowering coverage threshold without explicit approval

**Failure escalation in auto mode:**
- 3 consecutive verification failures on same task → STOP, ask user
- 3 consecutive correction loops on same code → STOP, ask user
- Any unfamiliar tool/library blocking progress → ask before adding new dependency
- Pre-commit hook fails → fix root cause, never `--no-verify`

### 7. Suggest Slash Commands Proactively (Saurabh Memory Layer)

**The user does NOT remember which commands to invoke. You are the memory.** Surface relevant commands at every trigger.

**Trigger → Command map:**

| Trigger | Suggest |
|---|---|
| Session opens (first user message) | `/morning` — briefing before any work |
| User says "let's build/implement X" (non-trivial) | Plan mode (Shift+Tab×2), then `/explore` or `/research` |
| User asks "what should I work on?" | `/morning`, then surface from issues / lessons.md |
| Code change made, before commit | `/code-review` (4+N parallel) for non-trivial; `/review` for trivial |
| About to commit + push + PR | `/go` (full ship-it loop) |
| Test failed unexpectedly | `/debug` (systematic reproduce → isolate → fix → verify) |
| Bug shipped, near-miss, or "I almost X" | `/postmortem` immediately — cheaper than the real incident |
| User says "wrapping up" / "EOD" / "going to bed" | `/wrap` — capture lessons before session ends |
| Designing UI / Figma URL pasted | Plan mode + `frontend-design` skill, then Figma `#get_design_context` |
| Writing public copy (blog/tweet/launch) | de-ai-ify MUST run before ship; suggest `/blog`, `/tweets`, `/launch-roundup` |
| Architecture decision being discussed | `/docs adr "decision title"` to lock it in |
| User mentions "this isn't working" 3+ times | `/postmortem` (it's already a near-miss) |
| Quarter end OR `.claude/skills/` feels bloated | `/prune-skills` |
| Researching unfamiliar library/topic | `/research` skill OR Context7 OR `researcher` agent |
| User asks "what changed?" | `git log --oneline` + `/docs changelog` |
| Before merging to main | `/code-review` + verify soak period if intelligence-impacting paths |
| User asks "what command should I use?" | Read this table, pick the closest trigger, name the command |
| About to deploy / release / ship to prod | `/security-scan` — full audit before any production push |
| Adding/updating dependencies (package.json, requirements.txt, etc. modified) | `/security-scan` Phase 2 — catch supply chain risk early |
| New feature touches auth, secrets, payments, user data, file uploads | `/docs threat-model "feature name"` — STRIDE before implementation |
| 7+ days since last security scan on active project | `/security-scan` — newly disclosed CVEs |
| Major release (semver bump) approaching | `/security-scan` + soak verification + `/docs release` |
| First commit in a project (no `.pre-commit-config.yaml`) | Suggest installing pre-commit framework with template at `D:/Git Repos/.claude/templates/pre-commit-config-template.yaml` |
| Test coverage drops in `/go` step 1d | Suggest writing tests OR explicitly updating threshold (don't silently lower) |
| Tests untouched 30+ days but code changed | Suggest mutation testing (Stryker/mutmut) to verify tests still catch bugs |
| Adding external API call / web fetch / file read in user-controlled path | `/docs threat-model` — SSRF / path traversal / injection review |
| About to `npm install`, `pip install`, `cargo add`, `go get`, etc. (NEW dependency) | `/tech-radar [package-name]` first — is this still the best choice in 2026? |
| Choosing a framework / database / message queue / auth lib / deploy infra for new feature | `/tech-radar` BEFORE writing code — capture alternatives, decide deliberately |
| User says "let's use X" for a non-trivial library/framework choice | `/tech-radar X` — verify it's still the right call, not last year's default |
| Writing an ADR via `/docs adr` | Call `/tech-radar` FIRST to populate "Alternatives Considered" with real market data |
| Quarterly stack health check (90+ days since last review) | `/tech-radar` per major dependency — flags dead deps, surfaces newer options |
| User asks "what should I use for X?" | `/tech-radar X` — gives current state of art, not pre-trained defaults |
| New model release from Anthropic | After `simplify` skill: also `/tech-radar` on stack — new model may handle older tools better OR new tools may have emerged |
| UI component changed / theme changed / color updated | `/a11y-check` — WCAG audit, contrast, focus management |
| New project with UI (no perf-budget.json yet) | `/perf-budget set` — initialize bundle/latency/memory budgets |
| Bundle size or build time grew significantly | `/perf-budget check` — surface regression before merge |
| Migration file created in db/migrations/, prisma/migrations/, etc. | `/migrate-safe` — 6-gate safety check before applying |
| ALTER TABLE / DROP / new column in schema | `/migrate-safe` — pre-mortem + backup + dry-run before running |
| Project missing LICENSE / README / CHANGELOG / lockfile | One mention per session — surface gap, suggest fix |

**Auto mode behavior on triggers (refer to Rule 6k for tiers):**
- **Tier 2 triggers** (safe, reversible): RUN the command, state in one sentence what's running. Don't ask.
- **Tier 3 triggers** (state-changing): announce then run. Example: "Running /go" then proceed.
- **Tier 4 triggers** (irreversible/external): mention command, PAUSE for explicit approval.

**Anti-naggy rules:**
- ONE command focus per turn — never dump the whole trigger table
- If user already invoked the right command, don't re-invoke
- If user said "skip the review" / "I'll commit manually" — respect for that turn
- For trivial tasks (typo fix, rename), don't auto-run `/code-review` or `/go` — overhead exceeds value

---

## 8. Documentation Excellence (MANDATORY across every project)

**A project with excellent docs is automatically higher-class. Treat docs as first-class — never an afterthought.**

### Auto-Maintenance Rules (apply on EVERY non-trivial change)

After ANY of these triggers, dispatch `documenter` agent or run `/docs <mode>`:

| Trigger | Action |
|---|---|
| New project, no README | `documenter` agent generates README from codebase scan |
| Code change (feature/fix/refactor) | `/docs changelog` — append CHANGELOG entry |
| API endpoint added/changed/removed | Update `docs/api.md` in same edit |
| New function/class without docstring | Add docstring in same edit (PEP 257 / JSDoc / per-language standard) |
| Architecture decision discussed | `/docs adr "decision title"` — lock it in |
| Setup process changed | Update README setup section in same edit |
| Breaking change | CHANGELOG `### Changed` + bump version + migration notes |
| Recurring user problem (3+ times) | `/docs troubleshooting` — exact error + cause + fix |
| Before opening any PR | `/docs sync` — detect/fix doc drift |
| Before tagging a release | `/docs release` — user-facing notes |
| Major design decision | `/docs adr` — never edit accepted ADRs, supersede |

### The Hard Rule

**Never ship code changes without corresponding doc updates in the same session.**

If a commit changes user-visible behavior and CHANGELOG isn't touched → `documenter` runs automatically before push.

### Doc Quality Bar

- Match existing doc style EXACTLY (emojis if used, none if not)
- Never duplicate info — link to source of truth
- Focus on WHY, not WHAT (code shows WHAT)
- EXACT error messages in TROUBLESHOOTING (users search for them)
- Every command in docs must actually work — test before documenting
- Update, don't rewrite — preserve history and context

### LLM Wiki (Karpathy Pattern) — Per-Project Living Knowledge

Every project should have `wiki/` with:
- `wiki/log.md` — chronological activity record
- `wiki/decisions/` — architecture decisions
- `wiki/learnings/` — debugging patterns, gotchas
- `wiki/patterns/` — recurring code patterns

After ANY meaningful event, append to `wiki/log.md`:
- Decision → also create `wiki/decisions/[name].md`
- Tricky bug fix → also create `wiki/learnings/[topic].md`
- Recurring pattern → also create `wiki/patterns/[name].md`

Run `/wiki-lint` quarterly to catch contradictions, orphans, stale claims.

---

## 9. Security & Quality Gates (Traditional SE Coverage)

**Automated** (no thinking required):
- Pre-commit hooks (one-time per project): gitleaks (secret scan), conventional-commits, formatter, linter — fire on every `git commit`
- PostToolUse hook (settings.json): formatter on every Edit/Write
- PreToolUse hook (settings.json): blocks dangerous bash patterns
- GitHub Actions: tests, soak-period gate, branch protection

**Inside `/go` ship loop** (every commit/PR):
- 1a Tests pass → 1b Type check → 1c Linter clean → 1d Coverage threshold → 1e Dep audit (high+ blocks) → 1f `verification-before-completion`

**Suggested by trigger map** (Claude surfaces, user invokes):
- `/security-scan` — full SAST + dep audit + secret scan + license check (before release, after dep changes, weekly on active projects)
- `/docs threat-model` — STRIDE for security-sensitive features (auth, payments, user data, uploads)
- `/postmortem` — on any incident or near-miss
- Mutation testing — when test coverage feels stale

**Pre-commit framework setup per project** (one-time):
```bash
cp "D:/Git Repos/.claude/templates/pre-commit-config-template.yaml" .pre-commit-config.yaml
# Uncomment relevant language sections
pip install pre-commit
pre-commit install
pre-commit install --hook-type commit-msg
```

After install, every `git commit` automatically runs: trailing-whitespace, end-of-file-fixer, gitleaks, large-file blocker, conventional-commits validator, plus language-specific (ruff/eslint/clippy/etc.).

---

## 10. Inheritance Behavior — How Workspace Rules Reach Projects

**Critical to understand: not everything inherits the same way.**

| Config type | Inheritance | What this means |
|---|---|---|
| `CLAUDE.md` | **Additive** — workspace + project both load | Project CLAUDE.md ADDS to workspace rules. Both are in context. |
| `.claude/skills/` | **Additive + project overrides** | Project skill with same name overrides workspace; new project skills add to pool |
| `.claude/agents/` | Same as skills | Override by name, additive otherwise |
| `.claude/settings.json` | **OVERRIDES — does NOT merge** | If project has settings.json, it REPLACES workspace settings.json entirely |
| MCP servers | Per-scope | Workspace-level MCP servers load when working in workspace |
| Plugins | Global (account-level) | Installed once, available everywhere |

### THE INHERITANCE TRAP (must avoid)

**Problem:** A project's `.claude/settings.json` does NOT merge with workspace settings. If you create a project settings.json with just one extra permission, you LOSE the workspace deny rules (rm -rf, force push, sudo, etc.).

**Solution:** Per-project settings.json must REPRODUCE workspace deny rules + add project-specific permissions.

### Safe Per-Project Settings Pattern

When customizing settings for a specific project, use this template:

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "permissions": {
    "allow": [
      "// === INHERITED from workspace ===",
      "Bash(git status)", "Bash(git status:*)", "Bash(git diff:*)",
      "Bash(git log:*)", "Bash(git show:*)", "Bash(git branch:*)",
      "Bash(ls:*)", "Bash(pwd)",

      "// === PROJECT-SPECIFIC additions ===",
      "Bash([build cmd]:*)", "Bash([test cmd]:*)",
      "Bash([linter]:*)", "Bash([formatter]:*)"
    ],
    "deny": [
      "// === MUST REPRODUCE workspace deny rules ===",
      "Bash(rm -rf /:*)",
      "Bash(rm -rf ~*)",
      "Bash(git push --force:*)",
      "Bash(git push -f:*)",
      "Bash(git reset --hard:*)",
      "Bash(sudo:*)",

      "// === PROJECT-SPECIFIC denies ===",
      "// (e.g., Bash(prod-deploy:*) for safety)"
    ]
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          { "type": "command", "command": "[detected formatter] $CLAUDE_FILE_PATH 2>/dev/null || true" }
        ]
      }
    ]
  }
}
```

### Inheritance Health Check (run during `/morning`)

`/morning` should verify:
- Project CLAUDE.md exists (or note that workspace rules apply)
- If `.claude/settings.json` exists, deny block reproduces workspace deny rules
- If `.claude/skills/` exists, no skill silently shadows a workspace skill (warn user)

If any inheritance gap detected → flag to user before starting work.

### When Per-Project Customization Is Right

- **Stack-specific**: build/test/lint commands → project settings.json
- **Domain-specific rules**: e.g., "this is a security-critical project, no eval()" → project CLAUDE.md
- **Per-project skills**: e.g., a project-specific deployment workflow → project `.claude/skills/`
- **Sensitive deny rules**: e.g., "never touch production database" → project settings.json deny block

### When NOT to Customize Per-Project

- Universal workflow rules (already in workspace)
- Universal skills like `/go`, `/morning`, `/wrap` (already at workspace)
- Generic permissions/denies (workspace handles them)

---

## Workspace Structure

The workspace root IS `D:/Git Repos/` — workspace config and project repos live as siblings here.

```
D:/Git Repos/                       # Workspace root (also contains all repos)
├── .claude/
│   ├── settings.json               # Hardened permissions (specific allow + deny)
│   ├── skills/                     # Universal slash commands
│   │   ├── adversarial-reviewer/   # 3-persona mandatory-dissent review
│   │   ├── commit/                 # Conventional commit creator
│   │   ├── coordinator/            # 4-phase research → synthesize → implement → verify
│   │   ├── debug/                  # Systematic debugging loop
│   │   ├── docs/                   # Unified docs manager (CHANGELOG, ADR, PR notes, ...)
│   │   ├── explore/                # Deep codebase exploration
│   │   ├── go/                     # Boris's daily-driver ship loop
│   │   ├── morning/                # Session-start briefing
│   │   ├── postmortem/             # Blameless incident writeup
│   │   ├── premortem/              # Pre-mortem before risky work
│   │   ├── prune-skills/           # Quarterly skill audit
│   │   ├── research/               # Multi-source research
│   │   ├── review/                 # Single-agent code review
│   │   ├── security-scan/          # SAST + dep audit + secret + license
│   │   ├── wiki-lint/              # Wiki health check
│   │   └── wrap/                   # Session-end ritual
│   ├── agents/
│   │   ├── documenter.md           # Doc maintainer
│   │   ├── researcher.md           # Deep research
│   │   └── staff-reviewer.md       # Senior architect review
│   ├── output-styles/
│   │   └── engineering.md
│   └── templates/
│       └── pre-commit-config-template.yaml
├── CLAUDE.md                       # This file
├── saurabh-pc.code-workspace       # VS Code workspace — PC repo set
├── saurabh-laptop.code-workspace   # VS Code workspace — laptop repo set
├── .gitignore
├── scripts/
│   └── muxtree                     # Worktree creation tool
└── <repo-folders>/                 # Project repos as siblings (set differs per machine)
    ├── CLAUDE.md                   # Project-specific rules (additive to workspace)
    ├── .claude/                    # Project-specific skills if any
    ├── tasks/lessons.md            # Per-project mistake log
    ├── wiki/                       # Per-project Karpathy LLM Wiki
    └── ...
```

---

## Per-Project Setup (when you start work in a new repo)

Each repo under `D:/Git Repos/` gets its own minimal config — workspace skills are inherited, only project-specific stuff goes in the repo:

1. Project `CLAUDE.md` at repo root: stack, build/test commands, conventions
2. `tasks/lessons.md`: empty file, log corrections here
3. `wiki/` (Karpathy pattern): create if project will accumulate knowledge
4. `.claude/settings.json` only if project needs different permissions/hooks than workspace defaults

For full per-project bootstrap details: `D:/Git Repos/Research/workspaces/projects/claude-code-strategy/SETUP.md`

For migrating an existing project: `D:/Git Repos/Research/workspaces/projects/claude-code-strategy/MIGRATE-EXISTING.md`

---

## Worktrees (Parallel Claude Sessions)

Use `D:/Git Repos/scripts/muxtree <branch-name> [count]` from inside any repo to create numbered parallel worktrees:

```bash
cd "D:/Git Repos/SKY-ai"
"D:/Git Repos/scripts/muxtree" feature-x 3
# Creates: D:/Git Repos/SKY-ai/worktrees/wt1, wt2, wt3
# Open 3 Claude Code sessions, one per worktree
```

Worktrees live INSIDE the repo (git requires this) but the script lives at workspace level so it's accessible from any repo.

---

## Cache Optimization
- This file: static content first, variable content last
- Do NOT edit this file mid-session — busts entire prompt cache
- Keep MCP server list stable within a session
- Fork subagents reuse parent's KV cache

---

## Memory System
- MEMORY.md index loaded automatically (Sonnet selects up to 5 relevant files)
- Save to memory: architecture decisions, user preferences, key findings, feedback
- Don't save: code patterns from codebase, git history, ephemeral details
- Memory location: `C:\Users\Saurabh\.claude\projects\<workspace-id>\memory\`
