# Client Project Scoping & Pricing Framework — Design Document

**Date:** 2026-02-16
**Status:** Approved

---

## Overview

An AI-assisted CLI tool that guides the user through scoping client projects, mapping requirements to feature bundles, and generating itemized pricing adjusted by regional budget tiers.

**Primary user:** Internal (developer/team) — not client-facing.
**Interaction model:** CLI sessions through Claude Code.
**Project types supported:** Domain-agnostic — web apps, mobile apps, websites, APIs, integrations.
**Output:** Itemized feature list with pricing (menu-style).

---

## Core Concepts

### Feature-Based Pricing (Not Hourly)

Projects are priced by the features they include, not by hours worked. Each feature bundle has a **base price** representing the maximum rate (Premium tier). All other budget tiers scale down from there using multipliers.

### Regional Budget Tiers

Country determines the default budget tier. The tier can be manually overridden per client.

### Feature Bundles (Not Micro Line Items)

Features are grouped into meaningful bundles (3-6 per project) that clients can understand. Not granular line items like "navigation" or "footer."

---

## Architecture

### Session Flow

```
1. CLIENT PROFILE
   → Client name, country, industry
   → Budget tier auto-set from country (overridable)
   → Project type (web app, mobile, website, API...)

2. AI-POWERED DISCOVERY
   → Client describes what they want (open-ended)
   → AI asks smart follow-ups based on feature catalog gaps
   → AI is budget-aware — adapts suggestions to tier
   → Cross-references dependencies and relationships
   → Stops when it can map to feature bundles

3. FEATURE SELECTION
   → AI recommends feature bundles based on discovery
   → For tiered features: appropriate tier pre-selected by budget
   → User confirms/adjusts the selection
   → AI suggests features they might be missing (suggested_with)

4. PRICING OUTPUT
   → Apply budget-tier multiplier to base prices
   → Generate itemized feature list with prices
   → Show total project cost
   → List optional add-ons not selected
```

---

## Component 1: Feature Catalog

Stored as YAML files, one per domain category.

### Location

```
config/features/
  website.yaml
  auth.yaml
  payments.yaml
  mobile.yaml
  infrastructure.yaml
  communication.yaml
  integrations.yaml
  data.yaml
```

### Feature Bundle Schema

```yaml
bundles:
  - name: "Bundle Name"
    description: "What this bundle provides"
    includes: "Detailed list of what's included"
    project_types: [website, web_app, mobile, api]

    # EITHER tiered:
    tier_variants:
      basic:
        scope: "What basic includes"
        base_price: 200
      standard:
        scope: "What standard includes"
        base_price: 400
      premium:
        scope: "What premium includes"
        base_price: 700

    # OR flat:
    base_price: 50
    scope: "What's included"

    dependencies: ["Other Bundle Name"]
    suggested_with: ["Related Bundle 1", "Related Bundle 2"]
```

### Key Fields

| Field | Purpose |
|---|---|
| `project_types` | Filter features relevant to the project type |
| `tier_variants` | Optional 2-3 scope levels at different price points |
| `base_price` | Maximum rate (Premium tier price) |
| `dependencies` | Features that must be included if this one is selected |
| `suggested_with` | Features commonly paired with this one — AI uses for recommendations |

### Example Feature Categories

| Category | Example Bundles |
|---|---|
| Website | Branding Website, Blog/CMS, Landing Page |
| Auth & Users | User Authentication, User Profiles, Role-Based Access |
| Payments | Payment Gateway, Subscription Billing, Invoice Generation |
| Communication | Email Notifications, SMS Integration, In-App Messaging |
| Integrations | Third-Party API, Social Media, Analytics, CRM |
| Infrastructure | Deployment & Setup, CI/CD Pipeline, Backup System |
| Mobile | App Store Deployment, Push Notifications, Offline Mode |
| Data & Content | CRUD Operations, Search & Filtering, File Uploads |

---

## Component 2: Budget Tiers

Stored in a single config file.

### Location

```
config/budget-tiers.yaml
```

### Schema

```yaml
tiers:
  starter:
    multiplier: 0.30
    countries: [India, Bangladesh, Nepal, Pakistan, Sri Lanka]
    suggestion_mode: "suggest_minimal"
    description: "Cost-conscious markets. Only essentials and basic tier variants shown."

  growth:
    multiplier: 0.50
    countries: [Indonesia, Vietnam, Philippines, Brazil, Mexico, Poland, Romania, Nigeria, Kenya]
    suggestion_mode: "suggest_balanced"
    description: "Growing tech markets. Basic and standard variants shown."

  standard:
    multiplier: 0.75
    countries: [Germany, France, Japan, South Korea, Australia, Canada, Italy, Spain]
    suggestion_mode: "suggest_full"
    description: "Established markets. All variants shown with recommendations."

  premium:
    multiplier: 1.0
    countries: [United States, United Kingdom, UAE, Singapore, Switzerland]
    suggestion_mode: "suggest_full_plus_premium"
    description: "Top-tier markets. All variants shown including premium add-ons."
```

### Multiplier Calibration Reference

Based on real pricing data:
- GAKO Technologies site (US client, Premium tier): **$500**
- Same scope for Indian client (Starter tier): **~Rs. 10,000-15,000 (~$150)**
- Ratio: 0.30x — confirmed against actual project pricing.

### Override Mechanism

After setting tier from country, manual adjustment is supported:
- Switch to a different named tier
- Set a custom multiplier (e.g., 0.65x)

---

## Component 3: Discovery Engine

### Design Philosophy

The discovery engine is **AI-conversational**, not a fixed question track. The AI uses the loaded feature catalog as its knowledge base and asks smart follow-ups based on what's missing or ambiguous.

### Configuration

```
config/discovery-rules.yaml
```

```yaml
discovery:
  min_questions: 2
  max_questions: 8
  must_confirm:
    - project_type
    - core_features
  budget_awareness:
    starter: "suggest_minimal"
    growth: "suggest_balanced"
    standard: "suggest_full"
    premium: "suggest_full_plus_premium"
```

### AI Behavior Rules

1. **Context-aware questioning** — AI has the full feature catalog loaded. It confirms obvious features (implied by project type) and only asks about ambiguous or missing pieces.

2. **Budget-aware suggestions** — For lower-tier clients, the AI frames options toward essentials and basic variants. Premium features are only surfaced if explicitly requested.

3. **Gap detection** — Cross-references client description against feature `dependencies` and `suggested_with`. Asks about missing dependent features.

4. **Conversational, not interrogative** — Natural back-and-forth, not a checklist. AI explains its reasoning: "Based on what you've described, this sounds like X. The main decision is Y."

5. **Knows when to stop** — Once it can confidently map to feature bundles, it presents the recommendation instead of asking more questions.

### System Prompt Template

The AI session is initialized with a system prompt that includes:
- Discovery rules (min/max questions, must-confirm items)
- Client context (name, country, tier, multiplier, suggestion mode)
- Full feature catalog (all bundles, descriptions, prices, dependencies, relationships)
- Output format specification

---

## Component 4: Output Format

### Pricing Breakdown

```
═══════════════════════════════════════════════════
  PROJECT SCOPE: {Client Name}
  Client: {Country} | Budget Tier: {Tier} ({Multiplier}x)
  Date: {Date}
═══════════════════════════════════════════════════

  SELECTED FEATURES:

  1. {Feature Bundle} ({Variant})          ${Price}
     {Scope description}

  2. {Feature Bundle}                      ${Price}
     {Scope description}

  ─────────────────────────────────────────────────
  TOTAL                                   ${Total}
  ═════════════════════════════════════════════════

  OPTIONAL ADD-ONS:
  ○ {Add-on Bundle}                       ${Price}
  ○ {Add-on Bundle}                       ${Price}
```

### What this output does NOT include:
- No timeline estimates
- No technical implementation details
- No hourly breakdowns
- No proposal formatting — that's up to the user

---

## Component 5: Session Storage

### Location

```
projects/
  YYYY-MM-DD-{client-name-slug}.yaml
```

### Schema

```yaml
client:
  name: "GAKO Technologies"
  country: "United States"
  industry: "Automotive Data Intelligence"

budget:
  tier: "premium"
  multiplier: 1.0
  override: false

project_type: "website"

discovery:
  description: "Professional B2B site for automotive data intelligence company..."
  conversation_log: [...]  # Key Q&A from discovery

selected_features:
  - bundle: "Branding Website"
    variant: "standard"
    base_price: 400
    final_price: 400
  - bundle: "Contact/Query Integration"
    base_price: 50
    final_price: 50
  - bundle: "Deployment & Setup"
    base_price: 50
    final_price: 50

total: 500

optional_addons:
  - bundle: "Blog/CMS"
    base_price: 200
    final_price: 200
```

---

## File Structure

```
project-planning/
├── config/
│   ├── budget-tiers.yaml
│   ├── discovery-rules.yaml
│   └── features/
│       ├── website.yaml
│       ├── auth.yaml
│       ├── payments.yaml
│       ├── mobile.yaml
│       ├── infrastructure.yaml
│       ├── communication.yaml
│       ├── integrations.yaml
│       └── data.yaml
├── projects/
│   └── (saved scoping sessions)
├── docs/
│   └── plans/
│       └── (this file)
└── src/
    └── (implementation code — CLI tool, AI prompts, etc.)
```

---

## What This Is NOT (Scope Boundaries)

- **Not a web UI** — CLI/AI-driven only
- **Not a database** — flat YAML/JSON files
- **Not a proposal generator** — outputs structured data, not formatted documents
- **Not a project manager** — no time tracking, no task management, no milestones
- **Not client-facing** — internal tool for the developer/team only
