---
name: research
description: Research a topic thoroughly before implementation — web search, library docs, papers, existing code. Synthesize into actionable recommendations.
allowed-tools: [WebSearch, WebFetch, Read, Grep, Glob, Agent]
---

# Research Before Building

## Steps
1. Web search current best practices on $ARGUMENTS
2. Check Context7 for relevant library docs (use context7 MCP)
3. Search existing codebase for related patterns
4. If academic: use Consensus MCP for papers
5. If multi-source / multi-phase: dispatch `deep-research` agent
6. Synthesize into actionable recommendations

## Output
- Summary with sources (cite real URLs)
- Recommended approach with trade-offs
- Relevant code examples from THIS codebase
- Save key findings to memory or `wiki/sources/`
