---
name: core-mem-sweep
description: Fold a batch of collected or synthesized notes (a weekly work digest, a research/meeting dump, or raw daily captures) into project nodes, topic pages, and other homes at high fidelity — from the SOURCE — then leave a thin dated index that links out. Not a "route scraps out of a compressed summary" tool.
allowed-tools: Read Write Edit Glob Grep Bash mcp__mcp-obsidian__obsidian_get_file_contents mcp__mcp-obsidian__obsidian_list_files_in_dir mcp__mcp-obsidian__obsidian_patch_content mcp__mcp-obsidian__obsidian_simple_search mcp__mcp-obsidian__obsidian_complex_search mcp__mcp-obsidian__obsidian_get_recent_changes
---

You fold collected/synthesized work into this Obsidian vault (`~/core`). The input is a batch of notes for a period — e.g. a weekly work digest, a stack of meeting notes, a research dump, or a set of raw daily captures — plus, sometimes, a pre-written summary of it.

## The model (read this first)

A tempting-but-wrong approach is to route small items out of an already-compressed dated summary into the nodes. That makes a **lossy funnel** — `raw source → summary → dated note → distilled node bullet` — and the richest material (a raw PR diff, the full meeting notes) never reaches the project page where it belongs.

**The model: substance folds NATIVELY into projects, from the SOURCE; the dated note is a thin INDEX, not a summary.**

- **Read the raw source, not a summary of it.** The detail lives in the raw material (full meeting notes, PR/commit activity with diffs, source threads). A pre-written summary is *framing only* — use it to orient, never as the thing you fold. If a sub-agent gathered the material, read the actual source files yourself; don't fold off a summary-of-a-summary.
- **Fold each slice at HIGH FIDELITY into where it belongs:**
  - Engineering / project work → the project node or its lab-notebook page: provenance (PR/commit/ticket/run links, outcomes) + narrative.
  - **Meeting notes → folded FULLY, per-meeting**, into the relevant node. Meetings are high-signal (decisions, alignment, direction) — don't compress several to one headline or drop one.
  - Non-project content → its native home (personal/career → an `Areas/` page; a tooling or process note → the relevant node).
  - **Link out liberally** — tickets, PRs/commits, docs, threads, run URLs. Real clickable URLs over bare references, even when a project page already carries the link (duplication is fine).
- **Don't re-fold what's already native.** A well-maintained node often already holds the period's work — the fold catches the **homeless** content and *adds* provenance/links, rather than duplicating.
- **The dated `Daily/YYYY-MM-DD.md` becomes a thin index:** "threads that moved → [[project page]] where the detail landed," with links out, and NO duplicated substance.

Non-destructive: append/restructure, never delete the user's content. Present a plan before writing.

---

## Pass 0 — Pick the input
1. Identify the batch to fold (the period's collected notes / digest, or a set of `Daily/` captures). Confirm with the user.
2. If the input is genuinely raw **quick-capture scraps** (a stray todo/link/idea, no richer source behind it), use the **Quick-capture fallback** at the bottom — those get routed, not folded-from-source.

## Pass 1 — Read the source
Read the raw material directly, prioritizing by signal: **meeting notes first**, then project/engineering activity (with diffs/detail), then tickets, then threads/email. Skim any pre-written summary for framing only.

## Pass 2 — Partition by destination
Group the material by where it will LAND (project node / topic page / Area / People), not by source. For each destination note: what's new, what's likely already-native, and the external links to carry. Show the user the partition before continuing.

## Pass 3 — Check existing destinations
Read each target page. Decide per slice: **already native** (skip / add only missing links) vs **homeless** (fold in full). This keeps the fold from duplicating a well-maintained node.

## Pass 4 — Fold plan review
Present the plan (destinations + what lands in each, already-native no-ops, and the thin index) and wait for the user's "go" before writing.

## Pass 5 — Execute the folds
Append into the right section of each destination (create if absent): lab-notebook/log entries get provenance + narrative; nodes get a dated meeting/decision entry (meetings in full, per-meeting); todos become `- [ ] task` checkboxes on the relevant node; Drive/links under `## Drive` or inline. Carry the external links. Only append/restructure.

## Pass 6 — Write the thin index
Write `Daily/YYYY-MM-DD.md` as a thin index (its substance now lives natively):

```markdown
---
node: false
---
#index

# Index — YYYY-MM-DD
> An INDEX, not a summary. Detail is folded natively into the linked pages; this records what moved and links out.

## Threads that moved → where the detail lives
- **<thread>** — <one line> [PR/ticket/doc/run links] → [[project page]]

## Non-project
- <personal / process / tooling> → [[Area or node]]

## Open todos (roll-up — detail in each project)
- [ ] … 📅 YYYY-MM-DD ⏫
```

After writing, scan for the leading-bracket link footgun: a doc titled `[X] Title` written `[[X] Title](url)` breaks in Obsidian → reword the display text to drop the leading bracket (`[X Title](url)`). Grep the index for stray `[[...](` patterns.

## Pass 7 — Summary
Report: what was folded, destinations touched, already-native skips, index word count, links carried.

---

## Quick-capture fallback (raw scraps, no richer source)
When the input is genuinely raw quick-capture (a stray todo/link/idea in `Daily/`, nothing richer behind it), there's nothing to fold-from-source — just route it: read the scrap, cluster by project, append to the relevant node (`## Log` / a `- [ ]` todo / a `## Drive` link), and tag the scrap `#processed`. This is the only case where the old route-and-tag behavior applies. A scrap already tagged `#processed` was handled — skip it.

## Edge cases
- **Multiple projects in one note/thread:** split and route each piece.
- **Already-native content:** don't duplicate — add only missing provenance/links.
- **Nothing to fold:** report it and offer the quick-capture fallback.
- **Obsidian MCP unavailable:** fall back to direct Read/Write against `~/core/`.
