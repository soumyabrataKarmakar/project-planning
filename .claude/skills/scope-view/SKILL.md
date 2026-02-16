---
name: scope-view
description: Use when viewing a saved client project scope with full pricing breakdown
---

# View a Saved Project Scope

1. If a project name was provided as argument, search for matching files in `projects/` using Glob
2. If no name provided or multiple matches, invoke the scope-list skill to show available projects and ask the user to pick one
3. Read the selected YAML file
4. Display the full pricing breakdown:

```
═══════════════════════════════════════════════════
  PROJECT SCOPE: {client.name}
  Client: {client.country} | Budget Tier: {budget.tier} ({budget.multiplier}x)
  Date: {created}
═══════════════════════════════════════════════════

  SELECTED FEATURES:

  {For each item in selected_features:}
  N. {bundle} ({variant if present})       ${final_price}
     {scope}

  ─────────────────────────────────────────────────
  TOTAL                                   ${total}
  ═════════════════════════════════════════════════

  OPTIONAL ADD-ONS (not included):
  {For each item in optional_addons:}
  ○ {bundle}                              ${final_price}
```

5. After displaying, ask: "What would you like to do?" with options:
   - Edit this scope (invoke scope-edit skill)
   - Start a new scope (invoke scope-new skill)
   - Done
