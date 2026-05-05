---
name: add-project
description: Clone a git repo into the workspace and wire it up — adds to the active machine's .code-workspace, prevents folder duplication via files.exclude, optionally bootstraps project-level CLAUDE.md/wiki/lessons. Auto-detects laptop vs PC.
allowed-tools: [Bash, Read, Write, Edit, Grep, Glob]
---

# /add-project — Clone and Wire a New Repo

## Usage

- `/add-project <git-url>` — clone, name folder from repo URL
- `/add-project <git-url> <folder-name>` — clone with custom folder name
- `/add-project <git-url> --display "Pretty Name"` — set VS Code display name

## Steps

### 1. Validate and clone

```bash
cd "D:/Git repos"

# Default folder name from URL: github.com/owner/repo.git → "repo"
# Override if user passes second arg
URL="<provided>"
FOLDER="${2:-$(basename "$URL" .git)}"

# Refuse if folder already exists
if [ -d "$FOLDER" ]; then
  echo "STOP: D:/Git repos/$FOLDER already exists. Choose a different folder name or remove existing."
  exit 1
fi

git clone "$URL" "$FOLDER"
```

If clone fails (auth, network, wrong URL), STOP and report.

### 2. Detect active machine

```bash
# Laptop file exists if we're on laptop, PC file if on PC. Both might exist;
# determine which to update by looking at recent edits or by hostname.
HOSTNAME=$(hostname)
# Heuristic: if "laptop" in hostname OR if saurabh-laptop file recently modified, use laptop
# Otherwise use PC
# Simpler: ASK the user once, save preference to D:/Git repos/.machine
if [ -f "D:/Git repos/.machine" ]; then
  MACHINE=$(cat "D:/Git repos/.machine")
else
  # First run — ask user
  echo "Which machine is this? (laptop / pc)"
  # Wait for answer, save to .machine
fi

WORKSPACE_FILE="D:/Git repos/saurabh-${MACHINE}.code-workspace"
```

If `.machine` file doesn't exist on first run: ask the user, save the answer.

### 3. Update the workspace file

Read the .code-workspace JSON, then add two entries:

**A. New folder entry (in alphabetical order in `folders` array):**
```json
{
  "name": "Pretty Display Name",
  "path": "./folder-name"
}
```

If `--display` not provided, derive from folder name (Title Case, replace `-`/`_` with space).

**B. Add to `settings.files.exclude` (prevents duplication in meta-root view):**
```json
"folder-name": true
```

Use Edit tool with surgical replacements. Validate JSON after edit by parsing.

### 4. Detect project stack (read-only scan)

```bash
cd "D:/Git repos/$FOLDER"

# Scan for stack signals
[ -f package.json ] && STACK="$STACK node"
[ -f pyproject.toml ] || [ -f requirements.txt ] && STACK="$STACK python"
[ -f go.mod ] && STACK="$STACK go"
[ -f Cargo.toml ] && STACK="$STACK rust"
[ -f Gemfile ] && STACK="$STACK ruby"
[ -f pom.xml ] || [ -f build.gradle ] && STACK="$STACK java"
[ -f Makefile ] && STACK="$STACK make"

# Detect test/build/lint commands per stack
# (Same logic as SETUP.md Step 1)
```

Report detected stack to user.

### 5. Ask user about opt-in bootstrap (one prompt, brief)

Show:
```
Detected: [stack]
Project: D:/Git repos/[folder]

Optional bootstraps (skip any with N):
- [ ] Project CLAUDE.md (stack-specific rules, build/test commands)
- [ ] tasks/lessons.md (mistake log)
- [ ] wiki/ (Karpathy LLM Wiki — decisions, learnings, patterns)
- [ ] docs/adr/ (architecture decisions)
- [ ] .claude/settings.json (only if project needs different permissions/hooks)

Which to bootstrap? (e.g., "all" / "1,3,4" / "none")
```

Apply user's selection. Default to "none" if no answer — workspace inheritance covers most cases.

### 6. If creating project CLAUDE.md, use this template:

```markdown
# [Project Name]

## Stack
[Detected stack: language, framework, key dependencies]

## Conventions
[Coding style, naming, file org — infer from existing code or ask user]

## Architecture
[High-level design — infer or ask]

## Key Commands
- Build: [detected]
- Test: [detected]
- Lint: [detected]
- Run: [detected]

## Workspace Inheritance
This project inherits universal rules from `D:/Git repos/CLAUDE.md`
(workflow, doc excellence, security gates, inheritance behavior, auto mode,
PM patterns, goal cadence). No need to repeat them here. Only put
project-SPECIFIC rules below.

## Project-Specific Rules
[Empty by default. Add ONLY rules that override or extend workspace rules.]

## Current Focus
[Update between sessions only — what we're working on right now.]
```

### 7. Don't auto-commit the workspace file

After updating the .code-workspace file, run `git status` at `D:/Git repos/` and report:
```
Modified: saurabh-[machine].code-workspace (added [project name])
```

User reviews and commits manually:
```bash
cd "D:/Git repos"
git add saurabh-[machine].code-workspace
git commit -m "chore: add [project name] to [machine] workspace"
git push
```

### 8. Final report

```
✅ Cloned: D:/Git repos/[folder]
✅ Added to: saurabh-[machine].code-workspace
✅ files.exclude entry added (no meta-root duplication)
[✅/⚪] Project CLAUDE.md bootstrap (yes/skipped)
[✅/⚪] tasks/lessons.md (yes/skipped)
[✅/⚪] wiki/ (yes/skipped)

Inherits automatically (no setup needed):
- 23 universal skills
- 3 universal agents
- Workspace CLAUDE.md rules
- Hardened settings.json

Next: VS Code → reopen workspace OR Reload Window to pick up new folder.
Commit + push the .code-workspace change when ready.
```

## Rules

- **Refuse if target folder exists** — don't overwrite or merge into existing
- **Don't auto-commit** — user reviews .code-workspace change before push
- **Inheritance is automatic** — don't recreate workspace skills/agents per project
- **Project CLAUDE.md is OPT-IN** — most projects don't need one; workspace rules cover universal cases
- **One workspace file per machine** — laptop edits saurabh-laptop, PC edits saurabh-pc
- **Save machine preference** — `.machine` file at workspace root, gitignored

## Anti-Patterns

- Cloning project repos OUTSIDE `D:/Git repos/` — they lose workspace inheritance
- Adding project-level skill duplicates of workspace skills — never needed
- Forgetting the files.exclude entry — causes folder duplication in VS Code sidebar
- Editing both `saurabh-laptop` and `saurabh-pc` from one machine — only edit the one for current machine
- Heavy project CLAUDE.md repeating workspace rules — workspace is loaded automatically, don't duplicate

## Update .gitignore at workspace root

Add `.machine` to `D:/Git repos/.gitignore` (already gitignored by default since it doesn't match the re-include patterns, but explicit is better).
