---
name: Justfile Priority
description: When a justfile exists in a project directory, prefer its commands over running things ad-hoc
type: feedback
---

Always check for a `justfile` in the project directory first. If a relevant command exists, use `just <command>` instead of running the equivalent shell invocation manually.

**Why:** Justfiles encode the project's canonical way to run things. Running ad-hoc commands bypasses that and can diverge from the intended workflow.

**How to apply:** Before running a build, test, lint, format, or other repeatable command, check if a justfile exists and whether it covers the operation. If the same ad-hoc command would be run more than once and there's no justfile recipe for it, propose adding one to the justfile instead of continuing to run it inline.
