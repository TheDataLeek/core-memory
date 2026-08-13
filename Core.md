# Core — Agent Operating Instructions

This file is the bootstrap document for any Claude agent working in or alongside this Obsidian vault. Read this first. Then read the relevant project node before starting work.

> Claude Code CLI tool config: [[CLAUDE.md]]

---

## What This Vault Is

A persistent "second memory" organized as a wiki. It replaces Claude's file-based memory system (`~/.claude/projects/`). Anything worth remembering across sessions lives here, not in Claude's memory files.

---

## Design Philosophy: Small Notes, Tightly Linked

This vault follows a Wikipedia-style model. Each note should cover **one topic** and be digestible on its own. If a Wikipedia article would have five sections, this vault would have five linked pages instead of one long file.

**Node files** (`<Name>.md`) are index pages — a concise overview with links to focused sub-pages. They should stay short enough to skim in 30 seconds. When a section of a node grows past a few paragraphs, break it out into its own page and link to it.

**Sub-pages** (`Topic.md`) go deep on a single subject: a decision, an event, a technical approach, a timeline. They link back to their parent node and cross-link to related pages in other projects.

**Why this matters:**
- You can read at the level of detail you need — skim the node, or drill into a sub-page
- Cross-linking surfaces connections between projects that a monolithic file buries
- Agents can read just the pages they need instead of loading entire project histories
- Obsidian's graph view and backlinks become useful when notes are granular

**Rules of thumb:**
- A node file should rarely exceed ~100 lines
- If you're appending more than a few paragraphs to an existing file, make a new sub-page instead
- Every sub-page links back to its parent node; the parent node lists all sub-pages under `## Pages`
- Prefer a new page over a new section in an existing page

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
- **Keep notes small and focused** — if you're writing more than a few paragraphs, create a sub-page and link to it from the node. See *Design Philosophy* above
- **Log decisions and context** to the project node — anything a future agent needs to know
- **Add todos** as `- [ ] Task description` (add `📅 YYYY-MM-DD` or `⏫` if relevant)
- **Link related pages** from the project node — keep it as the entry point
- **Cross-link between projects** when topics overlap — a sub-page in one project can link to a sub-page in another
- **Add Drive links** to the `## Drive` section of the relevant node when referencing Google Drive files

### When Finishing
- Update the project node status and any open todos
- If you created new pages, link them from the project node

### Memory: Vault vs Agent Store
- **Do NOT scatter project facts into per-session agent memory files** — write them to the relevant project node or sub-page instead
- The vault is the authoritative store for *what* to work on (project knowledge, decisions, history)
- The agent's own store is reserved for *how* to work (agent identity and behavior preferences), not project facts

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

## Daily Capture & the fold model (`Daily/` folder)

Two kinds of note live here. **Quick captures** (from a mobile widget) — dump thoughts, links, and tasks freely in a note named `YYYY-MM-DD`, no structure required. And **dated index pages** — when a batch of collected/synthesized work is folded into the vault, its dated note is a *thin index*, not a summary.

**The fold model (how a period's work enters the vault):** substance folds **natively into the project nodes/pages, read from the source** (raw meeting notes, PR/commit detail, threads) at high fidelity — with links out to primary sources (tickets, PRs, docs, runs). The dated `Daily/YYYY-MM-DD.md` becomes a **thin index** that only points to where the detail landed; it holds no unique substance. Don't write one big lossy dated summary and route scraps out of it — that funnel (`source → summary → dated note → distilled bullet`) throws away detail before it ever reaches the project page.

To fold a batch (or route raw captures), ask: *"process my daily notes"* → runs `/core-mem-sweep`.

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

### 4. Link vault skills into Claude Code

```bash
mkdir -p ~/.claude/skills
ln -s ~/core/.skills/core-mem-sweep ~/.claude/skills/core-mem-sweep
ln -s ~/core/.skills/core-mem-todo ~/.claude/skills/core-mem-todo
ln -s ~/core/.skills/core-mem-install ~/.claude/skills/core-mem-install
ln -s ~/core/.skills/core-mem-sync ~/.claude/skills/core-mem-sync
```

This makes `/core-mem-sweep`, `/core-mem-todo`, `/core-mem-install`, and `/core-mem-sync` available as Claude Code skills. Definitions live in the vault (`.skills/`) so they sync automatically — just re-run these on each new device.

### 5. Start a session
Open the agent in or near the relevant project directory. It reads `Core.md` first, then `Projects/<relevant>/<relevant>.md`.

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
