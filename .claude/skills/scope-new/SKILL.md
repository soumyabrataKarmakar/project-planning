---
name: scope-new
description: Use when starting a new client project scoping session to discover requirements, select features, and generate pricing
---

# New Project Scoping Session

Guide the user through scoping a new client project. This is a 4-step flow: Client Profile → AI Discovery → Feature Selection → Pricing Output.

## Setup

Before starting, load the configuration files:

1. Read `.claude/skills/scope-data/budget-tiers.yaml` — budget tier definitions
2. Read `.claude/skills/scope-data/discovery-rules.yaml` — discovery rules and project types
3. Read ALL files in `.claude/skills/scope-data/features/` — the full feature catalog

Hold all this data in context. You will need it throughout the session.

## Step 1: Client Profile

Ask the user for (use AskUserQuestion with multiple choice where possible):

1. **Client name** (open text)
2. **Client country** — match to a budget tier from budget-tiers.yaml
3. **Industry/domain** (brief, for context)
4. **Project type** — use the project_types list from discovery-rules.yaml

After collecting:
- Display the auto-detected budget tier, multiplier, and suggestion mode
- Ask if they want to override the tier or multiplier

## Step 2: AI-Powered Discovery

Now conduct an intelligent discovery conversation. You have the full feature catalog loaded.

**Rules (from discovery-rules.yaml):**
- Ask at least `min_questions` and at most `max_questions` follow-up questions
- MUST confirm: project_type, core_features
- Follow the `budget_awareness` guidance for the client's suggestion_mode

**How to discover:**
1. Ask the user to describe what the client wants built (open-ended)
2. Based on their answer, identify which feature bundles from the catalog are relevant
3. Ask follow-up questions about **gaps and ambiguities only** — don't ask about things that are obvious from the description
4. Check feature `dependencies` — if a dependency is missing, ask about it
5. Check `suggested_with` — mention commonly paired features the client might need
6. Be budget-aware: for `suggest_minimal` clients, don't surface premium options. For `suggest_full_plus_premium`, proactively mention premium add-ons.
7. Stop when you can confidently map to feature bundles

**Conversation style:**
- Natural and helpful, not interrogative
- Explain your reasoning: "Since you mentioned payments, you'll also need user accounts — should I include that?"
- One question at a time
- Use AskUserQuestion with multiple choice when the options are clear

## Step 3: Feature Selection

Present the recommended feature bundles based on discovery:

For each bundle show:
- Name and which tier variant (if tiered)
- Scope description
- Base price × multiplier = final price

Format as a clear table. Then:
- Ask if they want to add, remove, or change any features
- Show optional add-ons that weren't selected but are relevant
- Resolve any dependency issues (warn if a dependency is missing)

## Step 4: Pricing Output

Generate the final pricing breakdown using this format:

```
═══════════════════════════════════════════════════
  PROJECT SCOPE: {Client Name}
  Client: {Country} | Budget Tier: {Tier} ({Multiplier}x)
  Date: {Today's date}
═══════════════════════════════════════════════════

  SELECTED FEATURES:

  1. {Bundle Name} ({Variant if tiered})       ${final_price}
     {Scope description}

  2. {Bundle Name}                             ${final_price}
     {Scope description}

  ─────────────────────────────────────────────────
  TOTAL                                   ${total}
  ═════════════════════════════════════════════════

  OPTIONAL ADD-ONS (not included):
  ○ {Bundle Name}                         ${price}
  ○ {Bundle Name}                         ${price}
```

## Step 5: Save Session

After the user confirms the pricing output:

1. Generate a YAML file with the full session data
2. Save to `projects/YYYY-MM-DD-{client-name-slug}.yaml`
3. Use this schema:

```yaml
client:
  name: "{Client Name}"
  country: "{Country}"
  industry: "{Industry}"

budget:
  tier: "{tier_name}"
  multiplier: {multiplier}
  override: {true/false}
  override_reason: "{if overridden, why}"

project_type: "{project_type_id}"

discovery:
  description: "{Client's original description}"
  key_decisions:
    - question: "{What was asked}"
      answer: "{What was decided}"

selected_features:
  - bundle: "{Bundle Name}"
    variant: "{basic/standard/premium or null}"
    base_price: {base_price}
    final_price: {base_price * multiplier}
    scope: "{Scope description}"

total: {sum of final_prices}

optional_addons:
  - bundle: "{Bundle Name}"
    base_price: {base_price}
    final_price: {base_price * multiplier}

created: "{ISO date}"
```

4. Confirm the file was saved and show the path
