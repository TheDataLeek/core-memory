# Projects

Time-bounded work with a goal and an end.

```dataview
LIST WITHOUT ID link(file.path, file.aliases[0])
FROM "Projects"
WHERE node = true
SORT file.mtime DESC
```

---

**To add a project:**
1. Create `Projects/<Name>/`
2. Copy `Templates/Project-Node.md` → `Projects/<Name>/<Name>.md` (filename = folder name)
3. Copy `Templates/Project-Todo.md` → `Projects/<Name>/Todo.md`
4. Add `#active` to the node when work begins
