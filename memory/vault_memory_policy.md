---
name: Vault Memory Policy
description: Project facts go to Obsidian _Node.md files, NOT to ~/.claude/projects/ memory files
type: feedback
---

Do NOT write project context, decisions, or status to `~/.claude/projects/` memory files.

**Why:** The Obsidian vault at `~/core` is the authoritative second memory. Writing to Claude memory files creates a parallel system that fragments context and defeats the purpose of the wiki.

**How to apply:** Write any fact you'd normally save as a project memory to the relevant `Projects/<name>/_Node.md` instead. Reserve `~/.claude/` memory only for agent behavior preferences (how to work, not what to work on — e.g., this file itself).
