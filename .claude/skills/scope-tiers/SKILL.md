---
name: scope-tiers
description: Use when viewing or editing budget tier multipliers and country assignments in the project scoping framework
---

# Manage Budget Tiers

Budget tiers are defined in `.claude/skills/scope-data/budget-tiers.yaml`.

## Default Action: View Tiers

1. Read the budget-tiers.yaml config file
2. Display as a table:

| Tier | Multiplier | Suggestion Mode | Countries |
|------|-----------|-----------------|-----------|
| starter | 0.30x | suggest_minimal | India, Bangladesh, Nepal, Pakistan, Sri Lanka |
| growth | 0.50x | suggest_balanced | Indonesia, Vietnam, ... |
| standard | 0.75x | suggest_full | Germany, France, ... |
| premium | 1.0x | suggest_full_plus_premium | US, UK, UAE, ... |

## Edit Commands

Ask what they want to change using AskUserQuestion:

- **Adjust multiplier** — Ask which tier, then new multiplier value (0.0 to 1.0). Show impact: "Changing starter from 0.30 to 0.35 means a $500-base project goes from $150 to $175 for Indian clients."
- **Move country** — Ask which country and which tier to move it to. Update the YAML.
- **Add country** — Ask country name and which tier. Append to the tier's countries list.
- **Add tier** — Ask for tier name, multiplier, suggestion_mode, description, and initial countries. Add new tier to YAML.
- **Remove country** — Ask which country to remove from its current tier.

After changes:
1. Write updated YAML using the Edit tool
2. Validate: `python -c "import yaml; yaml.safe_load(open('.claude/skills/scope-data/budget-tiers.yaml', encoding='utf-8'))"`
3. Show the updated tier table
