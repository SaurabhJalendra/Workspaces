---
name: debug
description: Systematic debugging — reproduce, isolate, root-cause, fix, verify. Fixes the cause, not the symptom.
allowed-tools: [Read, Edit, Bash, Grep, Glob]
---

# Systematic Debugging

## Steps
1. **Reproduce** — get the exact error (run the failing command/test)
2. **Isolate** — find failing file and line (read error traces)
3. **Root cause** — understand WHY it fails (read code, trace logic)
4. **Fix** — minimal change at the root cause
5. **Verify** — run the original failing command — confirm pass
6. **Regression** — full test suite — confirm nothing else broke

## Rules
- Fix root cause, not symptom
- Don't add try/catch to hide errors
- Don't skip tests with .skip / @pytest.mark.skip
- If unsure, add temporary log and rerun before guessing
- After fix: capture in `tasks/lessons.md` for future prevention
