---
name: launch
description: Cat Wu's #evergreen-launch loop — prototype works → docs + blog + social + announcement ready by next morning. Multi-output launch coordinator. Fires after Internal-DAU 7-day gate passes.
allowed-tools: [Read, Write, Edit, Bash, Grep, Glob, Agent, Skill]
---

# /launch — From Working Prototype to Public Launch

> Anthropic's `#evergreen-launch` channel: engineer posts a working prototype, the documentation lead together with product marketing and developer relations turn around an announcement the next day.

Solo equivalent: Claude is documentation lead, product marketing, and developer relations. This skill orchestrates all three.

## When to invoke

**Auto-fire (Tier 3 — announce then run):**
- Internal-DAU 7-day gate passed (you used the feature daily for 7 consecutive days)
- User says "ready to ship" / "let's launch" / "ready for public"

**User-invoked:**
- `/launch [feature-name]` — full launch sequence
- `/launch dry-run [feature-name]` — generate materials but DON'T post anywhere

## Pre-flight Checks (BLOCK if any fail)

1. **Spec exists** at `specs/*-[feature].md` with Status: ready or shipped
2. **Tests pass** — run `/go` step 1 verify gates
3. **Internal-DAU verified** — explicit check: did you use it daily for 7 days? If unsure, STOP.
4. **Docs current** — `/docs sync` shows no drift
5. **Security clean** — `/security-scan` ran in last 7 days with no CRITICAL
6. **Performance budget met** — `/perf-budget check` passes (if applicable)
7. **A11y clean** — `/a11y-check` passes (if UI feature)

If any fail, STOP. Tell user what's missing. Don't generate launch materials for a half-baked thing.

## Phase 1 — Generate launch materials (parallel, 10 min)

Dispatch parallel subagents to generate:

### A. CHANGELOG entry
Append to `CHANGELOG.md` under `## [Unreleased]` or new version:
```markdown
### Added
- [Feature name]: [1 sentence user-visible description]
  - [Specific user benefit]
  - See [docs link]
```

### B. Blog post draft
Save to `posts/drafts/[YYYY-MM-DD]-[feature-name].md`:
- Title (under 70 chars, specific and useful)
- TL;DR (3 lines max)
- The Problem (1-2 paragraphs, real story)
- The Solution (what we built, with screenshot/code if applicable)
- How It Works (technical detail for engineers)
- Use Cases (2-3 concrete scenarios)
- Try It (link to repo / demo / docs)
- What's Next (one line)

Pre-pass through `/de-ai-ify` if available — strip AI-tells.

### C. Social copy variants
Save to `posts/drafts/[YYYY-MM-DD]-[feature-name]-social.md`:

```markdown
## Twitter/X (single tweet, 240 chars)
[hook + one-line value + link]

## Twitter/X thread (5-7 tweets)
1/ [hook]
2/ [problem]
3/ [solution]
4/ [demo or screenshot]
5/ [use case]
6/ [link + CTA]

## LinkedIn (problem-first, 150-250 words)
[professional framing, ends with question for engagement]

## Hacker News title (not clickbait)
[descriptive, not "Show HN" unless actually new]

## Reddit r/[relevant subreddit] post
[follow subreddit rules; lead with use case, not promotion]
```

### D. README update
If feature is a new public capability, add to project README:
- Features list
- Usage example (real, runnable)
- Link to docs/blog post

### E. Docs page (if applicable)
Save to `docs/[feature-name].md`:
- Overview
- Setup (commands, exact)
- Usage (real example)
- API reference (if public API)
- Troubleshooting (common errors with EXACT messages)
- Examples / cookbook

## Phase 2 — User Review Gate (MANDATORY)

**STOP here.** Show user:
- All generated materials (file paths)
- Recommended publish order (CHANGELOG → blog → social → docs link)
- Any caveats from pre-flight checks

User reviews and edits before anything goes public.

**Do NOT proceed to Phase 3 without explicit user approval.**

## Phase 3 — Publish (Tier 4 — user runs, Claude assists)

User runs each step manually OR Claude runs ONE at a time with confirmation:

1. `git commit -m "feat: ship [feature]"`
2. `git push`
3. Move blog post: `posts/drafts/X.md → posts/published/X.md`
4. Tag release if shipping versioned: `git tag v[X.Y.Z]` + `git push --tags`
5. Update GitHub release notes (if GitHub project)
6. Post to social — Claude generates final copy, user posts
7. Update wiki/log.md: `## [YYYY-MM-DD] launch | [feature] | [v]`

## Output Path

`posts/drafts/[YYYY-MM-DD]-[feature-name]/` directory containing all materials.

## Rules

- **Internal-DAU gate is hard.** No skipping. If you skip a day, reset the clock.
- **Pre-flight checks are hard.** Don't launch a feature with failing tests / missing docs / security issues.
- **Marketing arrives AFTER prototype works.** Never write launch copy before the feature is verified working.
- **De-AI-ify all generated copy** before publishing. Reddit moderators actively suppress AI tells.
- **Prefer "research preview" framing** for new features — lowers commitment, allows iteration without breaking promises.

## Anti-Patterns

- **Launch announcement before code is stable** — hype creates pressure that compromises quality
- **Same copy across all platforms** — Twitter ≠ LinkedIn ≠ HN. Each has audience norms.
- **Hyperbole** ("revolutionary", "game-changing", "unprecedented") — gets suppressed/downvoted
- **Skipping README update** — landing page for the feature is the README on a public repo
- **Forgetting CHANGELOG** — users searching for "what's new" hit CHANGELOG first

## Integration

- Pre-flight: hooks into `/go`, `/security-scan`, `/perf-budget`, `/a11y-check`, `/docs sync`
- Material generation: dispatches `documenter` agent + parallel content subagents
- After publish: starts feedback monitoring (manual — set Twitter / GitHub issue notifications)
