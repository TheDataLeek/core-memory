# Areas

Ongoing responsibilities — no defined end date. Think: health, finances, a creative practice, professional development.

```dataview
LIST WITHOUT ID link(file.path, file.aliases[0])
FROM "Areas"
WHERE node = true
SORT file.mtime DESC
```

---

**To add an area:**
1. Create `Areas/<Name>/`
2. Copy `Templates/Area-Node.md` → `Areas/<Name>/<Name>.md` (filename = folder name)
3. Copy `Templates/Area-Todo.md` → `Areas/<Name>/Todo.md`
