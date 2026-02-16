---
name: scope-list
description: Use when listing saved client project scopes from the project planning framework
---

# List Saved Project Scopes

1. Use Glob to find all YAML files matching `projects/*.yaml`
2. For each file, Read it and extract: `client.name`, `project_type`, `total`, `budget.tier`, and `created`
3. Display as a table sorted by date (newest first):

| # | Date | Client | Type | Tier | Total |
|---|------|--------|------|------|-------|
| 1 | 2026-02-16 | GAKO Technologies | website | premium | $500 |

4. If no YAML files found (only .gitkeep), say: "No saved projects yet. Use `/scope new` to start one."
