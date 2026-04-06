# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

This is an Obsidian vault — a persistent "second memory" wiki. It is not a software project. There are no build, test, or lint commands. The vault replaces `~/.claude/projects/` file memory for all project facts.

## Start Here

Before any work session:
1. Read `Core.md` — the agent bootstrap document (vault structure, rules, conventions, todo syntax, how to create projects/areas/writing topics)
2. Read `Projects/<Name>/_Node.md` for the relevant project context

## Integration & Skills Setup

If the MCP server or vault skills are not yet configured on this device, run the following. Full context is in `Core.md` § "New Device / New Agent Setup".

**MCP server** (gives Claude direct vault access via `mcp-obsidian`):
```bash
claude mcp add --global mcp-obsidian \
  -e OBSIDIAN_API_KEY=<key from Obsidian → Local REST API settings> \
  -e OBSIDIAN_HOST=127.0.0.1 \
  -e OBSIDIAN_PORT=27124 \
  -- uvx mcp-obsidian
```

**Auto-memory directory** (add to `~/.claude/settings.json`):
```json
{ "autoMemoryDirectory": "~/core/memory/" }
```
Per-project override: add `.claude/settings.local.json` with `"autoMemoryDirectory": "~/core/memory/<project-name>/"` in the project root.

**Vault skills** (`/sweep`, `/todo`):
```bash
mkdir -p ~/.claude/skills
ln -s ~/core/memory/sweep ~/.claude/skills/sweep
ln -s ~/core/memory/todo ~/.claude/skills/todo
```

## Memory Policy

Project facts → write to `Projects/<Name>/_Node.md` (or a sub-page linked from it). Agent behavior preferences → write to `~/.claude/` memory only. Never use `~/.claude/projects/` for project facts; the vault is the authoritative store.
