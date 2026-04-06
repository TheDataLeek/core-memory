# core-memory

A ready-to-use Obsidian vault that serves as a persistent second memory for Claude Code. Instead of scattering project context across Claude's `~/.claude/projects/` files, everything lives in a browsable wiki that syncs across devices and survives model upgrades.

## What this is

- An Obsidian vault structured for AI-assisted knowledge work
- Claude agents read `Core.md` at session start for operating instructions, then read the relevant `Projects/<Name>/_Node.md` for project context
- Three Claude Code skills: `/sweep` (process daily notes into project nodes), `/todo` (add a todo with session context), `/install` (setup wizard for new devices)
- Memory files that wire Claude's auto-memory into the vault instead of scattered project files

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
| **Dataview** | Powers index queries in `_Index.md` files |
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

Configure auto-memory to write into the vault (`~/.claude/settings.json`):

```json
{
  "autoMemoryDirectory": "~/core/memory/"
}
```

Link the vault skills:

```bash
mkdir -p ~/.claude/skills
ln -s ~/core/memory/sweep ~/.claude/skills/sweep
ln -s ~/core/memory/todo ~/.claude/skills/todo
ln -s ~/core/memory/install ~/.claude/skills/install
```

Or just run `/install` in Claude Code after cloning — the wizard handles steps 2–3 interactively.

### 4. Personalize

- Edit `memory/user_profile.md` with your own context (role, preferences, working style)
- Remove `memory/feedback_justfile.md` if you don't use justfiles
- Start adding projects: `Projects/<Name>/` using `Templates/Project-Node.md`

## Vault structure

```
core/
├── Core.md              ← agent bootstrap (read this first)
├── CLAUDE.md            ← Claude Code-specific config
├── Home.md              ← dashboard (opens on launch)
├── _Todo.md             ← global todo view
├── Inbox.md             ← quick capture
├── Drive.md             ← Google Drive master directory
├── Templates/           ← Templater templates for all node types
├── Projects/            ← time-bounded work
├── Writing/             ← writing topics and drafts
├── Areas/               ← ongoing responsibilities
├── People/              ← contacts and collaborators
├── Lists/               ← simple lists
├── Daily/               ← daily notes and mobile captures
└── memory/              ← Claude auto-memory (synced with vault)
    ├── sweep/SKILL.md   ← /sweep skill definition
    ├── todo/SKILL.md    ← /todo skill definition
    └── install/SKILL.md ← /install skill definition
```

## How it works with Claude

At the start of any session, Claude reads `Core.md` for operating instructions. For project work, it reads `Projects/<Name>/_Node.md` for context, decisions, and current status. It writes facts back to `_Node.md` — never to `~/.claude/projects/`.

The `memory/` directory is Claude's auto-memory destination. Memory files written here sync across devices with the vault, and Claude reads them at the start of each conversation via `MEMORY.md`.

## New device setup

Run `/install` in Claude Code. The wizard walks through all six setup phases: vault sync check, plugin install, MCP registration, auto-memory config, per-project overrides, and skill linking.

## License

MIT
