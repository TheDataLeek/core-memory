# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

This is an Obsidian vault — a persistent "second memory" wiki. It is not a software project. There are no build, test, or lint commands. The vault replaces `~/.claude/projects/` file memory for all project facts.

## Start Here

Before any work session:
1. Read `Core.md` — the agent bootstrap document (vault structure, rules, conventions, todo syntax, how to create projects/areas/writing topics)
2. Read `Projects/<Name>/<Name>.md` for the relevant project context

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

**Vault skills** (`/core-mem-sweep`, `/core-mem-todo`, `/core-mem-install`, `/core-mem-sync`) — definitions live in `.skills/`:
```bash
mkdir -p ~/.claude/skills
ln -s ~/core/.skills/core-mem-sweep ~/.claude/skills/core-mem-sweep
ln -s ~/core/.skills/core-mem-todo ~/.claude/skills/core-mem-todo
ln -s ~/core/.skills/core-mem-install ~/.claude/skills/core-mem-install
ln -s ~/core/.skills/core-mem-sync ~/.claude/skills/core-mem-sync
```

## Memory Policy

Project facts → write to `Projects/<Name>/<Name>.md` (or a sub-page linked from it). Agent identity and behavioral memory → the agent's own store, not the vault. The vault is the authoritative store for project knowledge; never scatter project facts into per-session agent memory files.
