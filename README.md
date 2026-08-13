# core-memory

A ready-to-use Obsidian vault that serves as a persistent second memory for Claude Code. Instead of scattering project context across Claude's `~/.claude/projects/` files, everything lives in a browsable wiki that syncs across devices and survives model upgrades.

## What this is

- An Obsidian vault structured for AI-assisted knowledge work
- Claude agents read `Core.md` at session start for operating instructions, then read the relevant `Projects/<Name>/<Name>.md` for project context
- Four vault-maintenance skills (in `.skills/`, symlinked into `~/.claude/skills`): `/core-mem-sweep` (process daily notes into project nodes), `/core-mem-todo` (add a todo with session context), `/core-mem-install` (setup wizard for new devices), `/core-mem-sync` (pull template updates into vault)

## Quickstart

### 1. Clone and open in Obsidian

```bash
git clone <this-repo> ~/core
```

Open Obsidian → **Open folder as vault** → select `~/core`.

### 2. Install community plugins

In Obsidian: **Settings → Community Plugins → Browse**

| Plugin | Notes |
|---|---|
| **Tasks** | Indexes all `- [ ]` checkboxes; powers todo queries |
| **Templater** | Set template folder to `Templates`; enable trigger on new file |
| **Dataview** | Powers index queries in `Index.md` files |
| **Local REST API** | Exposes vault to Claude via MCP; note your API key |
| **Homepage** | Set homepage to `Home` |
| **Tag Wrangler** | Optional: rename/merge tags vault-wide |
| **Icon Folder** | Optional: cosmetic folder icons |

### 3. Connect Claude Code

Register the MCP server (replace `<api_key>` with the key from Local REST API settings):

```bash
claude mcp add --global mcp-obsidian \
  -e OBSIDIAN_API_KEY=<api_key> \
  -e OBSIDIAN_HOST=127.0.0.1 \
  -e OBSIDIAN_PORT=27124 \
  -- uvx mcp-obsidian
```

Link the vault skills:

```bash
mkdir -p ~/.claude/skills
ln -s ~/core/.skills/core-mem-sweep ~/.claude/skills/core-mem-sweep
ln -s ~/core/.skills/core-mem-todo ~/.claude/skills/core-mem-todo
ln -s ~/core/.skills/core-mem-install ~/.claude/skills/core-mem-install
ln -s ~/core/.skills/core-mem-sync ~/.claude/skills/core-mem-sync
```

Or just run `/core-mem-install` in Claude Code after cloning — the wizard handles steps 2–3 interactively.

### 4. Personalize

- Start adding projects: `Projects/<Name>/` using `Templates/Project-Node.md`

## Vault structure

```
core/
├── Core.md              ← agent bootstrap (read this first)
├── CLAUDE.md            ← Claude Code-specific config
├── Home.md              ← dashboard (opens on launch)
├── Todo.md              ← global todo view
├── Inbox.md             ← quick capture
├── Drive.md             ← Google Drive master directory
├── Templates/           ← Templater templates for all node types
├── Projects/            ← time-bounded work
├── Writing/             ← writing topics and drafts
├── Areas/               ← ongoing responsibilities
├── People/              ← contacts and collaborators
├── Lists/               ← simple lists
├── Daily/               ← daily notes and mobile captures
└── .skills/             ← vault-maintenance skills (symlinked into ~/.claude/skills)
    ├── core-mem-sweep/SKILL.md   ← /core-mem-sweep skill definition
    ├── core-mem-todo/SKILL.md    ← /core-mem-todo skill definition
    ├── core-mem-install/SKILL.md ← /core-mem-install skill definition
    └── core-mem-sync/SKILL.md    ← /core-mem-sync skill definition
```

## How it works

At the start of any session, an agent reads `Core.md` for operating instructions. For project work, it reads `Projects/<Name>/<Name>.md` for context, decisions, and current status. It writes facts back to the project node — never to scattered per-session files.

Project knowledge lives in the vault (project nodes, daily notes, decisions). Agent identity and behavioral memory live in the agent's own store, not in the vault.

## New device setup

Run `/core-mem-install` in Claude Code. The wizard walks through four setup phases: vault sync check, plugin install, MCP registration, and skill linking.

## License

MIT
