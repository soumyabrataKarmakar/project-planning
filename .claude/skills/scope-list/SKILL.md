---
name: scope-list
description: Use when listing saved client project scopes from the project planning framework
---

# List Saved Project Scopes

1. Use Glob to find all scope files matching `projects/*/scope.yaml`
2. For each file, Read it and extract: `client.name`, `project_type`, `total`, `budget.tier`, and `created`
3. Also count other files in each project folder (images, notes, etc.) to show asset count
4. Display as a table sorted by date (newest first):

| # | Date | Client | Type | Tier | Total | Assets |
|---|------|--------|------|------|-------|--------|
| 1 | 2026-02-16 | GAKO Technologies | website | premium | $500 | 3 files |

5. If no project folders found, say: "No saved projects yet. Use `/scope new` to start one."
