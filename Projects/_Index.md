# Projects

Time-bounded work with a goal and an end.

```dataview
LIST
FROM "Projects"
WHERE file.name = "_Node"
SORT file.mtime DESC
```

---

**To add a project:**
1. Create `Projects/<Name>/`
2. Copy `Templates/Project-Node.md` → `Projects/<Name>/_Node.md`
3. Copy `Templates/Project-Todo.md` → `Projects/<Name>/_Todo.md`
4. Add `#active` to the node when work begins
