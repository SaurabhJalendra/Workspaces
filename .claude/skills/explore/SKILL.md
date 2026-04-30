---
name: explore
description: Deep codebase exploration — maps architecture, finds patterns, documents structure. Run before working in unfamiliar areas.
allowed-tools: [Read, Grep, Glob, Agent]
---

# Explore Codebase

## Steps
1. Map directory structure (Glob for key file patterns)
2. Identify entry point(s)
3. Trace main data flow / request lifecycle
4. Identify key abstractions and patterns
5. Document findings to memory or wiki/

## Output
- Directory tree with annotations
- Key files and their purposes
- Architecture patterns identified
- Data flow diagram (text)
- Save findings to `wiki/decisions/` or memory for future sessions
