---
name: Obsidian Vault Strategy
description: ~/core is the user's Obsidian wiki/second memory — agents should read Core.md at session start and write context to _Node.md files
type: project
---

The vault at `~/core` is the user's persistent second memory, replacing Claude's file-based project memory.

**How to apply:** At the start of any session, read `~/core/Core.md` for operating instructions. Then read `Projects/<name>/_Node.md` for project context. Write decisions, status, and persistent facts back to the relevant node. Use the Obsidian MCP tools (mcp__mcp-obsidian__*) for all vault reads and writes — they are available globally.

**Why:** User wants a wiki-based system (easier to browse and maintain than Claude memory files), synced across devices via Obsidian Sync.

**Structure:**
- `Core.md` — agent bootstrap instructions
- `Projects/<Name>/_Node.md` — central node per project (keyed to the directory name)
- `Writing/<Topic>/_Node.md` — writing topic nodes
- `Areas/<Name>/_Node.md` — ongoing responsibilities
- `Daily/` — quick capture and daily notes (iOS widget or desktop)
- `Drive.md` — Google Drive master directory
- `_Todo.md` (root) — global todo view
