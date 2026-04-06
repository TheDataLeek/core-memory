---
name: install
description: Guided setup wizard — configures MCP server, auto-memory, and vault skills for a fresh Claude Code + Obsidian vault integration on any device
allowed-tools: Read Write Edit Bash Glob
---

You are running the vault setup wizard. Walk the user through every integration step needed to get this Obsidian vault fully connected to Claude Code on the current device.

Work through each phase in order. At steps requiring user action in Obsidian (which you cannot automate), print clear instructions and wait for confirmation. At steps you can automate (file edits, symlinks, verifying paths), do the work and report results.

**Do not skip phases.** If a phase is already complete, say so briefly and move on.

---

## Phase 0 — Orient

Establish the vault path and scenario.

1. Check if the vault directory exists:
   ```
   !`ls ~/core/Core.md 2>/dev/null && echo "EXISTS" || echo "MISSING"`
   ```

2. **If EXISTS:** The vault is already synced. Tell the user:
   > "Vault found at ~/core. This wizard will configure the Claude Code tooling (MCP, memory, skills). Vault files won't be touched."

3. **If MISSING:** The vault files aren't present. Tell the user they need to get the vault onto this device first, then re-run `/install`. Provide options:
   - **Obsidian Sync:** open the Obsidian app, sign in, and let the vault sync. Once complete, re-run `/install`.
   - **iCloud / git:** if the vault is in a cloud folder or git repo, sync/clone it to `~/core/`, then re-run `/install`.
   
   Stop here until the vault exists.

---

## Phase 1 — Obsidian App

You cannot automate Obsidian plugin installation. Walk the user through it.

Tell the user:

> **Step 1 of 2 — Open the vault in Obsidian**
>
> 1. Open the Obsidian app
> 2. Click **Open folder as vault**
> 3. Select `~/core`
> 4. Obsidian will open the vault

> **Step 2 of 2 — Install required community plugins**
>
> Go to: **Settings → Community Plugins → Browse**
>
> Install and enable each of the following:
>
> | Plugin | Required config after install |
> |---|---|
> | **Tasks** | None — works out of the box |
> | **Dataview** | None — works out of the box |
> | **Templater** | Settings → Templater → set *Template folder location* to `Templates`; enable *Trigger Templater on new file creation* |
> | **Local REST API** | Settings → Local REST API → note the **API Key** (you'll need it in the next phase) |
> | **Homepage** | Settings → Homepage → set *Homepage* to `Home` |
> | **Tag Wrangler** | None |
> | **Icon Folder** | None (cosmetic) |

Ask: *"Once you've installed and configured all plugins and have your Local REST API key ready, type the key here to continue."*

Wait for the user to provide the API key. Store it as `$API_KEY` for use in Phase 2.

---

## Phase 2 — MCP Server

Register the `mcp-obsidian` server with Claude Code so agents can read and write the vault directly.

1. Check if it's already configured:
   ```
   !`claude mcp list 2>/dev/null | grep mcp-obsidian || echo "NOT_FOUND"`
   ```

2. **If already registered:** Tell the user it's already configured and skip to Phase 3.

3. **If NOT_FOUND:** Run the registration command, substituting the API key the user provided:

   Tell the user you're running:
   ```bash
   claude mcp add --global mcp-obsidian \
     -e OBSIDIAN_API_KEY=<their key> \
     -e OBSIDIAN_HOST=127.0.0.1 \
     -e OBSIDIAN_PORT=27124 \
     -- uvx mcp-obsidian
   ```
   
   Execute it:
   ```
   !`claude mcp add --global mcp-obsidian -e OBSIDIAN_API_KEY=$API_KEY -e OBSIDIAN_HOST=127.0.0.1 -e OBSIDIAN_PORT=27124 -- uvx mcp-obsidian`
   ```

4. Verify registration succeeded:
   ```
   !`claude mcp list 2>/dev/null | grep mcp-obsidian || echo "FAILED"`
   ```
   
   If it shows FAILED, tell the user to add it manually via `~/.claude/settings.json` under `mcpServers`:
   ```json
   "mcp-obsidian": {
     "command": "uvx",
     "args": ["mcp-obsidian"],
     "env": {
       "OBSIDIAN_API_KEY": "<api_key>",
       "OBSIDIAN_HOST": "127.0.0.1",
       "OBSIDIAN_PORT": "27124"
     }
   }
   ```
   Ask them to confirm once done before continuing.

---

## Phase 3 — Auto-Memory Directory

Configure Claude Code to write memory files into the vault instead of `~/.claude/projects/`.

1. Locate the settings file:
   ```
   !`ls ~/.claude/settings.json 2>/dev/null && echo "EXISTS" || echo "MISSING"`
   ```

2. Read the current content (if it exists). Use the Read tool on `~/.claude/settings.json`.

3. Check if `autoMemoryDirectory` is already set to `~/core/memory/`:
   - **Already set correctly:** report and skip to Phase 4.
   - **Set to something else:** ask the user whether to update it.
   - **Not set or file missing:** add or create it.

4. To add `autoMemoryDirectory` to an existing settings file, edit it to include:
   ```json
   "autoMemoryDirectory": "~/core/memory/"
   ```
   Preserve all other existing keys.

   If the file doesn't exist, create it:
   ```json
   {
     "autoMemoryDirectory": "~/core/memory/"
   }
   ```

5. Verify the memory directory itself exists:
   ```
   !`ls ~/core/memory/ 2>/dev/null && echo "OK" || echo "MISSING"`
   ```
   If missing, create it: `!`mkdir -p ~/core/memory/``

---

## Phase 4 — Per-Project Memory Overrides

Some projects write memory to a project-specific subdirectory. Check if any are needed for projects on this device.

1. List known configured projects:
   ```
   !`ls ~/core/memory/ 2>/dev/null`
   ```
   Any subdirectory here (other than files like `MEMORY.md` or skill directories like `sweep/`, `todo/`, `install/`) is a project-scoped memory directory.

2. For each project-scoped directory found, check if the corresponding project root exists on this device:
   ```
   !`ls ~/projects/<name>/ 2>/dev/null && echo "EXISTS" || echo "MISSING"`
   ```
   (Try common locations: `~/projects/`, `~/<name>/`, `~/code/`.)

3. For each project that **exists locally**, check if `.claude/settings.local.json` is already present in it:
   ```
   !`cat ~/<project-path>/.claude/settings.local.json 2>/dev/null || echo "MISSING"`
   ```

4. For any project missing the local override, offer to create it. If the user confirms:
   - Create `<project-path>/.claude/settings.local.json`:
     ```json
     {
       "autoMemoryDirectory": "~/core/memory/<project-name>/"
     }
     ```

---

## Phase 5 — Vault Skills

Link the vault's skill definitions into Claude Code's skills directory so `/sweep`, `/todo`, and `/install` are available in every session.

1. Check if the skills directory exists:
   ```
   !`ls ~/.claude/skills/ 2>/dev/null && echo "OK" || echo "MISSING"`
   ```
   If missing: `!`mkdir -p ~/.claude/skills/``

2. For each skill (`sweep`, `todo`, `install`), check if the symlink exists and points to the right place:
   ```
   !`readlink ~/.claude/skills/sweep 2>/dev/null || echo "MISSING"`
   !`readlink ~/.claude/skills/todo 2>/dev/null || echo "MISSING"`
   !`readlink ~/.claude/skills/install 2>/dev/null || echo "MISSING"`
   ```

3. For any missing or broken link, create it:
   ```
   !`ln -sf ~/core/memory/sweep ~/.claude/skills/sweep`
   !`ln -sf ~/core/memory/todo ~/.claude/skills/todo`
   !`ln -sf ~/core/memory/install ~/.claude/skills/install`
   ```

4. Verify all three symlinks resolve:
   ```
   !`ls -la ~/.claude/skills/`
   ```

---

## Phase 6 — Connectivity Test

Verify the MCP server can actually reach the vault.

Tell the user:

> "Make sure Obsidian is open and the vault is loaded, then I'll test the connection."

Ask the user to confirm Obsidian is open. Then attempt a read via MCP:

Use the `mcp__mcp-obsidian__obsidian_get_file_contents` tool (if available in this session) to fetch `Core.md`. If it returns content, the connection is working.

If the MCP tool is not available in this session (the server was just registered and requires a restart), tell the user:

> "The MCP server was registered but won't be active until Claude Code restarts. Quit and reopen Claude Code, then run a quick test:
>
> Ask me: 'Read Core.md from the vault via MCP' — if I return the file contents, everything is working."

---

## Final Summary

Print a checklist of what was configured:

```
VAULT SETUP COMPLETE
────────────────────────────────────────────────
✓  Vault found at ~/core
✓  Obsidian plugins installed (confirmed by user)
✓  MCP server: mcp-obsidian registered
✓  Auto-memory: ~/core/memory/
✓  Per-project overrides: <list or "none needed">
✓  Skills linked: /sweep  /todo  /install
────────────────────────────────────────────────
Next: restart Claude Code to activate the MCP server,
then open ~/core in Obsidian to verify the vault loads.
```

Flag anything that was skipped or needs follow-up with ⚠ instead of ✓.
