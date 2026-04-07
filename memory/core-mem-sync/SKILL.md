---
name: core-mem-sync
description: Check ~/projects/core-memory template repo for updates and apply them to the ~/core vault
allowed-tools: Read Write Edit Bash Glob Grep Agent
---

You are syncing the user's Obsidian vault at `~/core` with its upstream template at `~/projects/core-memory`.

The template is the source of truth for **structural files** (templates, skills, CLAUDE.md, Core.md, index pages, Obsidian config, memory scaffolding). The vault contains **user content** (project nodes, daily notes, people, custom memory files) that must never be overwritten.

---

## Step 1 — Verify both repos exist

```
!`ls ~/projects/core-memory/Core.md 2>/dev/null && echo "TEMPLATE OK" || echo "TEMPLATE MISSING"`
!`ls ~/core/Core.md 2>/dev/null && echo "VAULT OK" || echo "VAULT MISSING"`
```

If either is missing, tell the user and stop.

---

## Step 2 — Check for upstream changes

Check if the template has newer commits than the vault's last sync:

```
!`cd ~/projects/core-memory && git log --oneline -10`
```

Also check for uncommitted changes:
```
!`cd ~/projects/core-memory && git status --short`
```

Show the user a summary of recent template changes.

---

## Step 3 — Diff structural files

Compare the following categories of files between template and vault. For each, show what changed.

### 3a — Core documents
Compare these files directly (template is authoritative for structure, but vault may have customizations):
- `Core.md`
- `CLAUDE.md`
- `Home.md`
- `Todo.md`
- `Drive.md`
- `Inbox.md`

For each file: read both versions, diff them, and categorize changes as:
- **Structural** (headings, queries, conventions) → apply from template
- **User content** (focus items, custom links) → preserve from vault

### 3b — Templates
These are fully template-owned. Replace vault versions with template versions:
- `Templates/Project-Node.md`
- `Templates/Project-Todo.md`
- `Templates/Area-Node.md`
- `Templates/Area-Todo.md`
- `Templates/Writing-Node.md`
- `Templates/Writing-Todo.md`
- `Templates/Daily.md`
- `Templates/Mobile-Drop.md`
- `Templates/Person.md`
- `Templates/List.md`

### 3c — Index pages
Compare `Index.md` files in Projects/, Writing/, Areas/, People/, Lists/. Template owns the Dataview queries and instructions; vault owns the manual links.

### 3d — Skills
Compare skill definitions:
- `memory/core-mem-sweep/SKILL.md`
- `memory/core-mem-todo/SKILL.md`
- `memory/core-mem-install/SKILL.md`
- `memory/core-mem-sync/SKILL.md`

Check for new skills in the template that don't exist in the vault.

### 3e — Memory scaffolding
Compare template-provided memory files:
- `memory/MEMORY.md` (merge — template adds new entries, vault has custom entries)
- `memory/obsidian_vault.md`
- `memory/vault_memory_policy.md` (or `vault_policy.md`)
- `memory/user_profile.md` (template has placeholder; vault has real content — never overwrite)
- `memory/feedback_justfile.md`

### 3f — Obsidian config
Compare `.obsidian/` config files:
- `community-plugins.json`
- `core-plugins.json`
- `app.json`
- `daily-notes.json`
- `templates.json`

### 3g — Claude config
Compare `.claude/settings.local.json`

### 3h — New files
Check for files that exist in the template but not in the vault. These are candidates for addition.

---

## Step 4 — Present sync plan

Show the user a complete plan before making any changes:

```
SYNC PLAN — ~/projects/core-memory → ~/core
────────────────────────────────────────────────
APPLY (template → vault):
  ✎ Core.md — updated vault structure diagram, naming conventions
  ✎ Templates/Project-Node.md — added node:true frontmatter
  + memory/core-mem-sync/SKILL.md — new /core-mem-sync skill

MERGE (combine both):
  ⊕ memory/MEMORY.md — add new skill entry, keep custom entries

SKIP (vault content preserved):
  ≡ memory/user_profile.md — vault has real content
  ≡ Projects/ — user content, not touched

NO CHANGES:
  = Todo.md — identical
────────────────────────────────────────────────
```

Ask: *"Apply this sync? Type 'go' to proceed, or describe changes."*

---

## Step 5 — Execute

Apply the approved changes:

1. For **APPLY** items: copy template version to vault, overwriting
2. For **MERGE** items: read both, combine (template structure + vault custom content), write
3. For **new files**: copy from template to vault
4. For **new skills**: create symlinks in `~/.claude/skills/` if missing

**Never overwrite:**
- Project node files (`Projects/*/`)
- Daily notes (`Daily/`)
- People pages (`People/`)
- User-customized memory files (`memory/user_profile.md`, custom feedback files)

---

## Step 6 — Post-sync checks

1. Verify all node files have `node: true` frontmatter:
   ```
   !`grep -rL "^node: true" ~/core/Projects/*/$(basename $(dirname {})).md 2>/dev/null`
   ```

2. Verify all skills are symlinked:
   ```
   !`ls -la ~/.claude/skills/ | grep core-mem`
   ```

3. Verify Dataview queries use `node = true` (not `file.name = "_Node"`):
   ```
   !`grep -r "file.name.*_Node" ~/core/*.md ~/core/**/Index.md 2>/dev/null`
   ```

---

## Step 7 — Summary

```
SYNC COMPLETE
────────────────────────────────────────────────
Applied:  N files updated from template
Merged:   N files combined
Added:    N new files
Skipped:  N files (user content preserved)
────────────────────────────────────────────────
```
