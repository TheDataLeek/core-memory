---
name: core-mem-todo
description: Add a new todo to today's Daily/ note with relevant tags, priority, and captured session context to aid resumption
allowed-tools: Read Write Edit Glob Grep Bash mcp__mcp-obsidian__obsidian_get_file_contents mcp__mcp-obsidian__obsidian_patch_content mcp__mcp-obsidian__obsidian_get_periodic_note
---

You are adding a new todo item to the user's daily note. The todo text is: **$ARGUMENTS**

Work through the steps below in order.

---

## Step 1 — Determine today's date and daily note path

Get today's date:

```
!`date +%Y-%m-%d`
```

The daily note path is `~/core/Daily/YYYY-MM-DD.md`.

If the file does not exist, create it by copying `~/core/Templates/Daily.md` and replacing any template variables with today's date. Then proceed.

---

## Step 2 — Analyze the todo text

Read the todo text from `$ARGUMENTS` and determine:

**Priority** — look for signal words and apply the Tasks plugin emoji:
- "urgent", "asap", "critical", "blocker", "today" → `🔺` (highest)
- "important", "soon", "this week" → `⏫` (high)
- "eventually", "someday", "low priority", "when i can" → `🔽` (low)
- No signal → no priority emoji

**Due date** — if the user mentioned a specific date or relative time ("by Friday", "next week", "tomorrow"), convert to `📅 YYYY-MM-DD` and append it. Use today's date as the reference. If ambiguous, skip the due date.

**Project / area match** — scan `$ARGUMENTS` for any word that matches a known project or area name. To find known names:

```
!`ls ~/core/Projects/`
!`ls ~/core/Areas/`
```

If a match is found, note the tag: `#project/<name>` or `#area/<name>`. Match case-insensitively and partial-match. If no match, do not guess.

---

## Step 3 — Capture session context

This step creates a resumption note — metadata that will help the user pick up exactly where they left off when they eventually action this todo.

Gather the following:

**Current directory / project:**
```
!`pwd`
```
If this matches a known project directory, note the project name.

**Relevant files or locations** — if the current conversation involved specific files, functions, line numbers, or URLs that are directly relevant to this todo, note up to 3 of the most important ones. Use the format `file:line` where known. If nothing specific is relevant, omit this.

**What was in progress** — in one sentence, summarize what was being worked on in the current session that prompted this todo. Pull from conversation context. If the todo is unrelated to the current session (e.g. a stray thought), write "Added outside of active work context."

**Proposed first step** — based on the todo text and session context, write one concrete next action the user should take when they return to this todo. Be specific. If you cannot determine a specific next step, omit this field.

Format the context block as an indented blockquote beneath the checkbox:

```markdown
- [ ] <todo text> <priority> <due date>
  > **Context:** <one-sentence summary of what was in progress>
  > **Where:** `<directory or project>` — <relevant file:line if applicable>
  > **Next step:** <concrete first action>
```

Omit any field that has no useful content.

---

## Step 4 — Format the final todo block

Assemble the complete entry:

```markdown
- [ ] Fix auth bug in API ⏫
  > **Context:** Refactoring the authentication layer to meet new compliance requirements.
  > **Where:** `~/projects/myapp` — `src/auth.py:45`
  > **Next step:** Check token expiry logic in `validate_token()` — it may be ignoring the `exp` claim.
```

Do **not** add `#project/<name>` inline in the todo text — the Tasks plugin scopes by folder path, not inline tags.

---

## Step 5 — Write to the daily note

Read the current content of `~/core/Daily/YYYY-MM-DD.md`.

Find the `## Todos` section. Insert the new todo block as the last item in that section (before the next `---` or `##` heading). If the section has an empty `- [ ]` placeholder, replace that line instead of appending.

If there is no `## Todos` section, append one at the end of the file:

```markdown
## Todos

- [ ] <formatted todo line>
  > ...context block...
```

Write the updated file.

---

## Step 6 — Confirm

Reply with a single short confirmation. Show the full formatted block that was written:

```
Added to Daily/YYYY-MM-DD.md:

- [ ] Fix auth bug in API ⏫
  > **Context:** Refactoring the authentication layer.
  > **Where:** `~/projects/myapp` — `src/auth.py:45`
  > **Next step:** Check token expiry logic in `validate_token()`.
```

Do not summarize the steps taken or explain the process beyond this.
