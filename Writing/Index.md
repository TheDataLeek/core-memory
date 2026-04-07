# Writing

Topics, drafts, and long-form work.

```dataview
LIST WITHOUT ID link(file.path, file.aliases[0])
FROM "Writing"
WHERE node = true
SORT file.mtime DESC
```

---

**To add a topic:**
1. Create `Writing/<Topic>/`
2. Copy `Templates/Writing-Node.md` → `Writing/<Topic>/<Topic>.md` (filename = folder name)
3. Copy `Templates/Writing-Todo.md` → `Writing/<Topic>/Todo.md`
