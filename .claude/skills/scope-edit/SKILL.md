---
name: scope-edit
description: Use when editing or modifying a saved client project scope, changing features, tier, or pricing
---

# Edit a Saved Project Scope

## Load Data

1. If a project name was provided, find the matching YAML file in `projects/` using Glob
2. If not found, invoke scope-list skill to show available projects
3. Read the selected YAML file
4. Also load:
   - Feature catalog: Read ALL files in `.claude/skills/scope-data/features/`
   - Budget tiers: Read `.claude/skills/scope-data/budget-tiers.yaml`

## Show Current State

Display the current scope summary (client info, selected features with prices, total).

## Edit Options

Ask what they want to change using AskUserQuestion:

- **Change budget tier** — Show available tiers, let them pick. Update multiplier, recalculate ALL final_prices.
- **Add features** — Show available feature bundles from catalog that are NOT currently selected (filtered by project_type). Let them pick, choose tier variant if applicable. Add to selected_features.
- **Remove features** — Show currently selected features. Let them pick which to remove. Warn if removing would break a dependency for another selected feature.
- **Change feature tier** — For tiered features in selected_features, let them switch between basic/standard/premium. Update base_price and final_price.
- **Update client info** — Change name, country, industry. If country changes, offer to update budget tier.
- **Done editing** — Save and exit.

## After Each Change

1. Recalculate: `final_price = base_price * multiplier` for all selected features
2. Recalculate: `total = sum of all final_prices`
3. Show updated pricing breakdown
4. Ask if they want to make more changes

## Save

When done editing:
1. Write the updated YAML back to the SAME file path
2. Confirm: "Scope updated and saved to {file_path}"
