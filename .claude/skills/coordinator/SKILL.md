---
name: coordinator
description: Anthropic's 4-phase coordinator pattern — research, synthesize, implement, verify. Run for any non-trivial implementation work that benefits from parallel exploration before commitment.
allowed-tools: [Read, Write, Edit, Bash, Grep, Glob, Agent, Skill]
---

# /coordinator — The 4-Phase Pattern (Research → Synthesize → Implement → Verify)

Anthropic's documented coordinator orchestration. The pattern that produces production-grade output by separating exploration from commitment from execution from verification.

**Core principle: NEVER delegate understanding.** The main agent must synthesize subagent findings — never write "based on your findings, implement X" without first understanding the findings yourself.

## Phase 1: RESEARCH (Parallel Exploration)

Dispatch 2-4 parallel subagents, each investigating one angle. Each returns concrete findings, not synthesis.

Examples:
- Codebase exploration: `Explore` agent looks at existing patterns, conventions, similar code
- External research: `researcher` agent looks at libraries, papers, best practices
- Architecture analysis: `Plan` agent traces data flow, dependencies, failure modes
- Risk analysis: a fork of self imagining failure modes

**Rules:**
- Subagents return findings, NOT recommendations. The coordinator synthesizes.
- Run in parallel — single message with multiple Agent tool calls.
- Each agent has a focused, narrow prompt. Avoid "do everything" agents.
- Set token budget — research phase shouldn't consume more than 30% of total budget.

## Phase 2: SYNTHESIZE (Coordinator Reads, Understands, Decides)

The coordinator reads ALL subagent outputs, then writes:

1. **What we know** (from research)
2. **What we don't know** (gaps that matter)
3. **The decision** (specific approach with rationale)
4. **The plan** (numbered steps, exact files, exact line numbers if possible)
5. **Open risks** (things that could still go wrong)

**Critical:** if any agent's findings conflict with another's, the coordinator MUST resolve the conflict explicitly — not punt. Conflicts hidden = bugs shipped.

If risk profile is high (irreversible, prod-touching, security-sensitive), invoke `/premortem` here.

## Phase 3: IMPLEMENT (Specific, Measurable Tasks)

Dispatch implementation subagents (or do it yourself in main context) with:
- Exact file path
- Exact change description
- Exact verification command (the test that proves it works)

**Rules:**
- Implementation prompts are short and specific. NEVER "implement based on the research" — that delegates understanding.
- Each implementation task ends with a verification step.
- If 3+ tasks fail in a row, STOP and re-synthesize — the plan was wrong.

## Phase 4: VERIFY (Adversarial, Independent)

Dispatch verification adversarially — the verifier hasn't seen the implementation reasoning, only the diff and the spec.

- Run `adversarial-reviewer` (3 personas, mandatory dissent)
- For high-stakes: also `/code-review` (4+N parallel)
- For security-sensitive: also `/security-scan`
- For UI: browser/screenshot verification
- For data: edge case + idempotency tests

**Verification gate**: if verifier finds any CRITICAL, the cycle returns to Phase 2 (re-synthesize, don't just patch).

## When to Use `/coordinator`

| Trigger | Use coordinator? |
|---|---|
| 5+ files changing OR 200+ lines | Yes |
| New architecture or framework | Yes |
| Security-sensitive changes | Yes |
| Multi-step migration | Yes |
| Single-file fix < 50 lines | No (overkill) |
| Trivial typo / rename | No |
| Exploration-only tasks | No (use Explore agent directly) |

## Output Format

Save the coordination log to `wiki/learnings/[YYYY-MM-DD]-[task-name].md`:

```markdown
# Coordination: [Task Name]
**Date:** YYYY-MM-DD

## Phase 1: Research
- Agent A findings: [...]
- Agent B findings: [...]
- Agent C findings: [...]

## Phase 2: Synthesis
- What we know: [...]
- What we don't know: [...]
- Decision: [...]
- Plan: [...]
- Open risks: [...]

## Phase 3: Implementation
- Tasks dispatched: [...]
- Results: [...]

## Phase 4: Verification
- Reviewers dispatched: [...]
- Findings: [...]
- Resolution: [...]

## Outcome
- Time spent: [hours]
- Token cost (estimate): [Mtok]
- Quality signal: [tests pass / failures caught / regressions detected]
```

## Anti-Patterns

- **"Based on your findings, implement X"** — banned. The coordinator must synthesize.
- **Skipping Phase 4** — verification is not optional. Every coordinator cycle ends in adversarial verification.
- **Running coordinator on trivial tasks** — overhead exceeds value. Reserve for non-trivial work.
- **Sequential research instead of parallel** — defeats the point. Phase 1 agents must run in parallel.
- **Letting one subagent's output drive the plan unchallenged** — coordinator must cross-check.
