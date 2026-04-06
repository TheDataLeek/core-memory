---
name: core-mem-sweep
description: Process unprocessed Daily/ notes into their respective vault destinations across multiple analysis passes
allowed-tools: Read Write Edit Glob Grep Bash mcp__mcp-obsidian__obsidian_get_file_contents mcp__mcp-obsidian__obsidian_list_files_in_dir mcp__mcp-obsidian__obsidian_patch_content mcp__mcp-obsidian__obsidian_simple_search mcp__mcp-obsidian__obsidian_complex_search mcp__mcp-obsidian__obsidian_get_recent_changes
---

You are performing a structured sweep of the user's `Daily/` quick-capture notes in their Obsidian vault at `~/core`. Your job is to read, analyze, route, and file all unprocessed content into the right places across the vault.

Work through the passes below **in order**. Do not skip a pass. After each pass, show the user a concise summary before proceeding. **Do not modify any files until Pass 7.**

---

## Pass 0 — Inventory

1. List all files in `Daily/` using the Obsidian MCP tool or Glob.
2. Skip any file that already contains `#processed` in its content.
3. Skip the file for today's date — daily notes are only swept retroactively to avoid missing edits made after a sweep.
4. For each unprocessed file: note the date, approximate word count, and whether the file has any real content (skip empty templates).
5. Show the user a table:

   | File | Date | Status |
   |---|---|---|
   | 2026-04-05.md | 2026-04-05 | Has content |
   | 2026-04-04.md | 2026-04-04 | Empty template |

6. Confirm with the user which files to include in the sweep. Default: all unprocessed non-empty files.

---

## Pass 1 — Full Extraction

Read every file selected in Pass 0 in full. Extract all content verbatim into an internal working list, organized by source file. Do not summarize yet — preserve exact wording for todos and ideas since they may need literal text.

Internally tag each extracted item by type:
- `TODO` — any checkbox `- [ ]` or clear action item ("need to", "should", "will")
- `IDEA` — speculative thoughts, side-project sparks, things to explore
- `LINK` — URLs or references to external resources
- `DRIVE` — Google Drive links
- `NOTE` — factual context, decisions made, things that happened
- `UNCLEAR` — fragments, incomplete sentences, ambiguous content

---

## Pass 2 — Cluster by Topic

Group the extracted items by likely topic or project. Use project names, proper nouns, and recurring themes as cluster anchors.

For each cluster, record:
- **Cluster name** (e.g. "myapp", "D3 charting idea", "Health/fitness")
- **Items in cluster** (count + brief description)
- **Confidence** (High / Medium / Low) that this is a coherent topic

Flag any items that don't fit a cluster as `UNROUTABLE`.

Show the user the cluster list before continuing:

> Found 4 clusters: myapp (3 items), D3 charting idea (1 item), groceries (2 items), unroutable (1 item). Continuing to destination mapping…

---

## Pass 3 — Destination Mapping

For each cluster, find the best destination in the vault:

1. Search `Projects/_Index.md`, `Writing/_Index.md`, `Areas/_Index.md` for matching nodes.
2. For each match, record the destination path (e.g. `Projects/myapp/_Node.md`).
3. For clusters with no match:
   - If it looks like a new project/area/writing topic → flag as **PROPOSED NEW NODE**
   - If it's a one-off idea or reference → route to `Inbox.md`
   - If truly unroutable → route to `Inbox.md` with a note

For `DRIVE` items: destination is the `## Drive` section of the relevant node.
For `TODO` items: destination is the body of the relevant node (as `- [ ] task`).
For `LINK`/`NOTE`/`IDEA` items: destination is a relevant section in the node, or a new section if none fits.

---

## Pass 4 — Tag Audit

Search the vault for all existing tags:

```
grep -r "#" ~/core --include="*.md" | grep -oP '#[a-z][a-zA-Z0-9/_-]+' | sort -u
```

Build a list of all active tags. For each cluster from Pass 2, identify which existing tags apply (e.g. `#active`, `#project/myapp`, `#area/health`).

Note any clusters that have no matching tag — these are candidates for new tags in Pass 5.

---

## Pass 5 — New Tag & New Node Proposals

For any cluster that needs a new tag or a new vault node, prepare proposals. **Do not create anything yet.**

Present to the user:

**Proposed new nodes:**
- `Projects/d3-charting/` with `_Node.md` — for the D3 charting side-project idea
  - Template: `Templates/Project-Node.md`
  - Would be linked from `Projects/_Index.md`

**Proposed new tags:**
- `#project/d3-charting` — to scope tasks and notes for the D3 project

**No action needed for:** myapp (existing node), groceries (existing list)

Ask the user: *"Approve, reject, or modify any of these before I proceed?"*

Wait for explicit user approval or rejection of each proposal. Only approved proposals will be acted on in Pass 7. Rejected ones get routed to `Inbox.md` instead.

---

## Pass 6 — Routing Plan Review

Present the complete routing plan for user sign-off before any file is touched:

```
ROUTING PLAN
────────────────────────────────────────────────
Daily/2026-04-06.md
  → [TODO]  "extend the D3 charting stuff"
      dest: Projects/d3-charting/_Node.md (NEW — pending approval)
      tag:  #project/d3-charting (NEW — pending approval)

  → [NOTE]  ... (any other content)
      dest:  Inbox.md

────────────────────────────────────────────────
Post-sweep: Daily/2026-04-06.md will be tagged #processed
```

Ask: *"Does this look right? Type 'go' to execute, or describe any changes."*

Do not proceed until the user confirms.

---

## Pass 7 — Execute

With user approval, execute the routing plan:

1. **For each destination node:**
   - Read the current file content
   - Append content in the appropriate section:
     - Todos: under an `## Open` or inline as `- [ ] task text`
     - Notes/context: under `## Log` or `## Notes` (create section if absent)
     - Drive links: under `## Drive` (create section if absent)
     - Ideas: under `## Ideas` (create section if absent)
   - Write the updated file

2. **For Inbox.md:**
   - Append a dated section:
     ```markdown
     ### Swept from Daily — YYYY-MM-DD
     - [item content]
     ```

3. **For approved new nodes:**
   - Create the directory
   - Copy the appropriate template (`Templates/Project-Node.md`, etc.)
   - Fill in the project name and today's date
   - Add the link to the relevant `_Index.md`

4. **Do not delete or overwrite any existing content.** Only append.

---

## Pass 8 — Cleanup

For each processed Daily/ file, append `#processed` on a new line at the bottom of the file. Do not delete the file.

Example:
```markdown
...existing content...

#processed
```

---

## Final Summary

After all passes, show a concise summary:

```
SWEEP COMPLETE — YYYY-MM-DD
────────────────────────────────────────────────
Processed:  1 daily note
Items filed: 3
  → Projects/d3-charting/_Node.md  (1 todo, 1 note)
  → Inbox.md                        (1 unroutable item)
New nodes created: Projects/d3-charting/
New tags proposed: #project/d3-charting
Skipped: 0 (empty templates)
────────────────────────────────────────────────
```

---

## Edge Cases

- **Partial sentences / truncated notes:** treat as `UNCLEAR`, route to Inbox.md with a note that the original was incomplete.
- **Multiple projects in one daily note:** split the content and route each piece to its destination.
- **Already-existing todos in destination node:** do not deduplicate — just append. The user can clean up manually.
- **No unprocessed files found:** report "Nothing to sweep. Daily/ has no unprocessed files." and stop.
- **Obsidian MCP unavailable:** fall back to direct file reads via the Read/Write tools against `~/core/`.
