# External Skills — Per-Machine Install

Skills in this folder that come from external git repos (their own remotes, not vendored into the meta-repo). The meta-repo `.gitignore` excludes them. Each machine must run the install commands below once.

> Why per-machine instead of submodule: auto-mode classifier blocks submodule wiring for external executable code. Manual `git clone` is explicit and traceable. The trade-off is one extra command per machine on first setup.

---

## html-anything

Turn any file/folder/URL into a polished single-file HTML page. 17 design systems (teaching, dashboard, atlas, terminal, etc.). Inspired by Thariq Shihipar's "Unreasonable Effectiveness of HTML" (Claude Code team).

- **Upstream:** https://github.com/clockless-org/html-anything
- **Your fork:** https://github.com/SaurabhJalendra/html-anything
- **License:** Apache-2.0
- **Requires:** Node 20+, npm
- **Activates on:** "make a webpage", "create a teaching site", "turn this into HTML", "visualize this", "make a dashboard/report", or when a file/folder/URL would benefit from a rich HTML artifact over a long Markdown reply.

### Install (first time on a new machine)

```powershell
cd "D:/Git repos/.claude/skills"
git clone https://github.com/SaurabhJalendra/html-anything.git
cd html-anything
npm install
npm run build
```

### Update to latest upstream

```powershell
cd "D:/Git repos/.claude/skills/html-anything"
gh repo sync SaurabhJalendra/html-anything   # sync fork with clockless-org/html-anything
git pull
npm install
npm run build
```

### Uninstall

```powershell
Remove-Item -Recurse -Force "D:/Git repos/.claude/skills/html-anything"
```

### Notes

- The skill works via SKILL.md instructions (no API key needed) — Claude reads SKILL.md and writes HTML directly in your active session.
- The bundled CLI (`dist/cli.js`) is optional and requires `ANTHROPIC_API_KEY` or `OPENAI_API_KEY` (separate billing, not Max plan). You normally won't use it.
- `node_modules/` is gitignored at the workspace level — won't accidentally commit.
