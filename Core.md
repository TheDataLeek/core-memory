# Core — Agent Operating Instructions

This file is the bootstrap document for any Claude agent working in or alongside this Obsidian vault. Read this first. Then read the relevant project node before starting work.

> Claude Code CLI tool config: [[CLAUDE.md]]

---

## What This Vault Is

A persistent "second memory" organized as a wiki. It replaces Claude's file-based memory system (`~/.claude/projects/`). Anything worth remembering across sessions lives here, not in Claude's memory files.

---

## Vault Structure

```
core/
├── Core.md              ← you are here
├── Home.md              ← human dashboard (open on startup)
├── Todo.md             ← global todo view (all projects)
├── Inbox.md             ← desktop quick capture
├── Drive.md             ← Google Drive master directory
│
├── Projects/
│   └── <Name>/
│       ├── <Name>.md    ← central node: context, decisions, status
│       ├── Todo.md     ← project-scoped todo list
│       └── <Topic>.md   ← sub-pages linked from the node
│
├── Writing/
│   └── <Topic>/
│       ├── <Topic>.md   ← writing topic node: themes, status, links
│       ├── Todo.md     ← topic-scoped todo list
│       └── <Draft>.md
│
├── Areas/               ← ongoing responsibilities (not time-bounded)
│   └── <Name>/
│       ├── <Name>.md    ← central node
│       └── Todo.md     ← area-scoped todo list
│
├── People/              ← contacts and collaborators
│   └── <Name>.md
│
├── Daily/               ← quick capture (iOS widget) and daily notes
│   └── YYYY-MM-DD.md
│
└── Templates/           ← use via Obsidian's Templates plugin
```

---

## Agent Rules

### Before Starting Work
1. Read `Core.md` (this file)
2. Find the relevant project: `Projects/<Name>/<Name>.md`
3. Read that node for context, decisions, and current status

### While Working
- **Log decisions and context** to the project node — anything a future agent needs to know
- **Add todos** as `- [ ] Task description` (add `📅 YYYY-MM-DD` or `⏫` if relevant)
- **Link related pages** from the project node — keep it as the entry point
- **Add Drive links** to the `## Drive` section of the relevant node when referencing Google Drive files

### When Finishing
- Update the project node status and any open todos
- If you created new pages, link them from the project node

### Memory: Obsidian vs Claude Files
- **Do NOT write to `~/.claude/projects/` memory files** for facts about this user's projects
- **Do write** to the relevant project node or sub-page instead
- Claude's `~/.claude/` memory is reserved for agent behavior preferences only (how to work, not what to work on)

---

## Projects

**To find a project:** `Projects/<Name>/<Name>.md`

**To create a new project:**
1. Create folder `Projects/<Name>/`
2. Copy `Templates/Project-Node.md` → `Projects/<Name>/<Name>.md` (filename = folder name)
3. Copy `Templates/Project-Todo.md` → `Projects/<Name>/Todo.md` (edit the FROM path to match the folder)
4. Fill in the node's context, goals, and status

**Project naming:** Use the directory name of the associated codebase/work if applicable (e.g., `Projects/myapp/` for work in `~/myapp/`).

---

## Writing Topics

**To find a writing topic:** `Writing/<Topic>/<Topic>.md`

**To create a writing topic:**
1. Create folder `Writing/<Topic>/`
2. Copy `Templates/Writing-Node.md` → `Writing/<Topic>/<Topic>.md` (filename = folder name)
3. Copy `Templates/Writing-Todo.md` → `Writing/<Topic>/Todo.md`
4. Add a link to `Writing/Index.md`

---

## Areas

Areas are ongoing responsibilities with no defined end (health, finances, a creative practice, learning a skill).

**To find an area:** `Areas/<Name>/<Name>.md`

**To create a new area:**
1. Create folder `Areas/<Name>/`
2. Copy `Templates/Area-Node.md` → `Areas/<Name>/<Name>.md` (filename = folder name)
3. Copy `Templates/Area-Todo.md` → `Areas/<Name>/Todo.md`
4. Add a link to `Areas/Index.md`
5. Fill in the node's context and current focus

---

## Todos

Todos use the **Tasks plugin**. Write tasks as standard checkboxes anywhere in the vault; the plugin indexes them automatically.

**Adding a todo:**
```
- [ ] Task description
- [ ] Task with due date 📅 2026-04-15
- [ ] High priority task ⏫
```

**Priority emoji:** `🔺` highest · `⏫` high · `🔼` medium · `🔽` low  
**Other metadata:** `📅 YYYY-MM-DD` due · `⏳ YYYY-MM-DD` scheduled · `🔁 every week` recurrence

**Viewing todos:**
- All open tasks: `Todo.md` at vault root
- Project-scoped: `Projects/<Name>/Todo.md`

**Agents:** write plain `- [ ] Task` checkboxes. Add `📅` or `⏫` only when the due date or priority is genuinely known. Do not add `#project/<name>` tags — scoping is handled by folder path in the query.

---

## Daily Capture (`Daily/` folder)

Quick captures from the iOS widget and daily notes live here — no structure required. Just dump thoughts, links, and tasks freely in a note named `YYYY-MM-DD`.

If a structured daily note for today already exists, append new mobile captures to it rather than creating a second file for the same date. The `Mobile-Drop` template is for days where no daily note exists yet.

An agent can sweep `Daily/` and route notes into proper project nodes. To trigger a sweep, ask: *"process my daily notes."*

---

## Google Drive

See `Drive.md` for the master directory of Drive locations.

In project nodes, Drive files are listed under a `## Drive` section:
```markdown
## Drive
- [Document Name](https://drive.google.com/...)
```

**From mobile:** Share a Drive link → copy → paste into that day's `Mobile/` note with a label. It gets routed to the right node during the next sweep.

---

## New Device / New Agent Setup

### 1. Sync the vault
Sync to the new device via Obsidian Sync, iCloud, or git. Open the vault in Obsidian and verify the folder structure looks correct.

### 2. Install and configure community plugins

**Tasks** — indexes and queries all `- [ ]` checkboxes across the vault.  
No required setup. Works out of the box.

**Templater** — template engine for creating new nodes.  
Settings → Templater → set *Template folder location* to `Templates`.  
Enable *Trigger Templater on new file creation*.

**Dataview** — available for ad-hoc queries; not required for todos.  
No required setup.

**Local REST API** — exposes the vault to Claude via MCP.  
Settings → Local REST API → note the **API Key** (needed in step 3).  
Default port: `27124`. Enable HTTPS only if needed.

**Homepage** — opens `Home.md` on vault launch.  
Settings → Homepage → set *Homepage* to `Home`.

**Tag Wrangler** — right-click any tag to rename/merge it vault-wide.  
No required setup.

**Icon Folder** — cosmetic folder/file icons.  
No required setup.

### 3. Register the MCP server with Claude Code

```bash
claude mcp add --global mcp-obsidian \
  -e OBSIDIAN_API_KEY=<api_key_from_step_2> \
  -e OBSIDIAN_HOST=127.0.0.1 \
  -e OBSIDIAN_PORT=27124 \
  -- uvx mcp-obsidian
```

Or add manually to `~/.claude/settings.json` under `mcpServers`:
```json
"mcp-obsidian": {
  "command": "uvx",
  "args": ["mcp-obsidian"],
  "env": {
    "OBSIDIAN_API_KEY": "<your_api_key>",
    "OBSIDIAN_HOST": "127.0.0.1",
    "OBSIDIAN_PORT": "27124"
  }
}
```
MCP source: https://github.com/MarkusPfundstein/mcp-obsidian

### 4. Configure auto-memory directory

In `~/.claude/settings.json`, set:
```json
{
  "autoMemoryDirectory": "~/core/memory/"
}
```

For each project that has its own memory subdirectory, create `.claude/settings.local.json` in that project's root:
```json
{
  "autoMemoryDirectory": "~/core/memory/<project-name>/"
}
```

### 5. Link vault skills into Claude Code

```bash
mkdir -p ~/.claude/skills
ln -s ~/core/memory/core-mem-sweep ~/.claude/skills/core-mem-sweep
ln -s ~/core/memory/core-mem-todo ~/.claude/skills/core-mem-todo
ln -s ~/core/memory/core-mem-install ~/.claude/skills/core-mem-install
ln -s ~/core/memory/core-mem-sync ~/.claude/skills/core-mem-sync
```

This makes `/core-mem-sweep`, `/core-mem-todo`, `/core-mem-install`, and `/core-mem-sync` available as Claude Code skills. Definitions live in the vault so they sync automatically — just re-run these on each new device.

### 6. Start a session
Open Claude Code in or near the relevant project directory. Agent reads `Core.md` first, then `Projects/<relevant>/<relevant>.md`.

---

## Naming Conventions

| Pattern | Meaning |
|---|---|
| `<Name>.md` (matches folder name) | Central node for a project/area/topic — has `node: true` frontmatter |
| `Todo.md` | Todo aggregator (query or manual list) |
| `Index.md` | Directory listing for a top-level section |
| `YYYY-MM-DD.md` | Date-stamped note in `Daily/` |
| `Title Case.md` | Regular sub-page |

**Tags:**
- `#active` — currently in progress
- `#waiting` / `#blocked` — stalled pending something
- `#area/<name>` — area association
- `#daily` — unprocessed daily/quick-capture note
