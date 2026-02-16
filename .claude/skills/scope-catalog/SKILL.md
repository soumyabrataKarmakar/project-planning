---
name: scope-catalog
description: Use when viewing, adding, or editing features in the project scoping feature catalog
---

# Manage Feature Catalog

The feature catalog lives in `.claude/skills/scope-data/features/`.

## Default Action: View Catalog

1. Read all YAML files in the features directory
2. For each category, show a summary table:

| Category | Bundle | Tiered? | Base Price | Project Types |
|----------|--------|---------|-----------|---------------|
| Website & Branding | Branding Website | Yes (basic/standard/premium) | $200-700 | website, ecommerce |
| Website & Branding | Landing Page | No | $150 | website |

## Commands

If user says "add" or wants to add a feature:

### Add a Feature Bundle

Ask the user for (use AskUserQuestion where possible):
1. Which category file to add to (show existing categories, or "create new")
2. Bundle name
3. Description and includes
4. Project types it applies to
5. Is it tiered? If yes: get scope + base_price for each tier (basic/standard/premium). If no: get single base_price + scope.
6. Dependencies (which other bundles must be present)
7. Suggested_with (commonly paired bundles)

Write the new bundle to the appropriate YAML file using the Edit tool (append to bundles list).

### Edit a Feature Bundle

If user says "edit":
1. Show all bundles, ask which to edit
2. Show current values for the selected bundle
3. Ask what field to change
4. Update the YAML file

### Validate

After any write operation, validate the YAML file:
```bash
python -c "import yaml; yaml.safe_load(open('path/to/file.yaml', encoding='utf-8'))"
```
