---
name: scope
description: Use when scoping client projects, pricing features, or managing the project planning framework. Invoke for any /scope command.
---

# Project Scoping Framework

Route to the appropriate sub-skill based on the command:

| Command | Skill | Purpose |
|---|---|---|
| `/scope new` | scope-new | Start a new client scoping session |
| `/scope list` | scope-list | List all saved project scopes |
| `/scope view <name>` | scope-view | View a saved project scope |
| `/scope edit <name>` | scope-edit | Edit a saved project scope |
| `/scope catalog` | scope-catalog | View or edit the feature catalog |
| `/scope tiers` | scope-tiers | View or edit budget tier config |

## Routing Instructions

1. Read the user's command/arguments
2. Invoke the matching sub-skill using the Skill tool
3. If no sub-command specified or just `/scope` invoked, show the command list above and ask which they want

**Config directory:** `.claude/skills/scope-data/`
**Projects directory:** `projects/`
