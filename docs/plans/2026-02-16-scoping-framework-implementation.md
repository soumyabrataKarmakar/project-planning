# Project Scoping Framework — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build an AI-assisted project scoping framework as Claude Code skills with YAML config files, invoked via `/scope` slash commands.

**Architecture:** Claude Code skills (Markdown prompt templates) that load YAML config files (feature catalog, budget tiers, discovery rules) and guide the user through client project scoping with intelligent discovery and feature-based pricing. No custom code — the AI runtime is Claude Code itself.

**Tech Stack:** Claude Code skills (Markdown), YAML config files, Git

---

## Prerequisites

- Claude Code with skill support
- Global skills directory: `C:/Users/soumy/.claude/skills/`
- Project data directory: `D:/Projects/project-planning/`

---

### Task 1: Create directory structure

**Files:**
- Create: `D:/Projects/project-planning/config/features/` (directory)
- Create: `D:/Projects/project-planning/projects/` (directory)
- Create: `D:/Projects/project-planning/.gitkeep` files for empty dirs

**Step 1: Create all directories**

```bash
mkdir -p D:/Projects/project-planning/config/features
mkdir -p D:/Projects/project-planning/projects
```

**Step 2: Add .gitkeep for projects directory**

```bash
touch D:/Projects/project-planning/projects/.gitkeep
```

**Step 3: Commit**

```bash
cd D:/Projects/project-planning
git add config/ projects/
git commit -m "chore: create config and projects directory structure"
```

---

### Task 2: Create budget-tiers.yaml

**Files:**
- Create: `D:/Projects/project-planning/config/budget-tiers.yaml`

**Step 1: Write the budget tiers config**

```yaml
# Budget tiers define regional pricing multipliers.
# base_price × multiplier = client price.
# The "premium" tier (1.0x) IS the base price — all others scale down.

tiers:
  starter:
    multiplier: 0.30
    countries:
      - India
      - Bangladesh
      - Nepal
      - Pakistan
      - Sri Lanka
    suggestion_mode: suggest_minimal
    description: >
      Cost-conscious markets. Recommend only essentials and basic tier variants.
      Premium features hidden unless explicitly requested.

  growth:
    multiplier: 0.50
    countries:
      - Indonesia
      - Vietnam
      - Philippines
      - Brazil
      - Mexico
      - Poland
      - Romania
      - Nigeria
      - Kenya
      - Thailand
      - Colombia
      - Argentina
      - Egypt
      - South Africa
    suggestion_mode: suggest_balanced
    description: >
      Growing tech markets. Show basic and standard variants.
      Premium available on request.

  standard:
    multiplier: 0.75
    countries:
      - Germany
      - France
      - Japan
      - South Korea
      - Australia
      - Canada
      - Italy
      - Spain
      - Netherlands
      - Sweden
      - Israel
      - New Zealand
    suggestion_mode: suggest_full
    description: >
      Established markets. Show all variants with value recommendations.

  premium:
    multiplier: 1.0
    countries:
      - United States
      - United Kingdom
      - UAE
      - Singapore
      - Switzerland
      - Norway
      - Denmark
      - Qatar
      - Saudi Arabia
    suggestion_mode: suggest_full_plus_premium
    description: >
      Top-tier markets. Show all variants including premium add-ons and
      optional enhancements.

# Calibration reference:
# GAKO Technologies branding site (US, premium): $500
# Same scope (India, starter): ~Rs. 10,000-15,000 (~$150)
# Ratio: 0.30x confirmed against real project data.
```

**Step 2: Validate YAML syntax**

```bash
python -c "import yaml; yaml.safe_load(open('D:/Projects/project-planning/config/budget-tiers.yaml'))"
```

Expected: No output (valid YAML).

**Step 3: Commit**

```bash
cd D:/Projects/project-planning
git add config/budget-tiers.yaml
git commit -m "feat: add budget tiers config with regional multipliers"
```

---

### Task 3: Create discovery-rules.yaml

**Files:**
- Create: `D:/Projects/project-planning/config/discovery-rules.yaml`

**Step 1: Write the discovery rules config**

```yaml
# Rules that govern the AI-powered discovery conversation.
# These are guardrails — the AI is conversational within these bounds.

discovery:
  min_questions: 2
  max_questions: 8

  must_confirm:
    - project_type
    - core_features

  budget_awareness:
    suggest_minimal: |
      Only recommend essential features and basic tier variants.
      Do not surface premium options unless the client explicitly asks.
      Frame suggestions as "what you need" not "what's available."
    suggest_balanced: |
      Recommend basic and standard tier variants.
      Mention premium options exist but don't push them.
      Frame suggestions as "good value options."
    suggest_full: |
      Show all tier variants with recommendations.
      Highlight best-value options.
      Mention premium add-ons as "worth considering."
    suggest_full_plus_premium: |
      Show all tier variants and premium add-ons.
      Recommend the best options regardless of cost.
      Present optional enhancements proactively.

  project_types:
    - id: website
      label: "Website / Branding Site"
      description: "Marketing sites, portfolios, business branding, landing pages"
    - id: web_app
      label: "Web Application"
      description: "SaaS, dashboards, portals, admin panels, CRUD apps"
    - id: mobile
      label: "Mobile App"
      description: "iOS/Android native or cross-platform apps"
    - id: api
      label: "API / Backend Service"
      description: "REST/GraphQL APIs, microservices, backend systems"
    - id: ecommerce
      label: "E-Commerce"
      description: "Online stores with product catalog, cart, checkout"
    - id: custom
      label: "Custom / Mixed"
      description: "Combination of above or unique project type"

output_format: |
  ═══════════════════════════════════════════════════
    PROJECT SCOPE: {client_name}
    Client: {country} | Budget Tier: {tier} ({multiplier}x)
    Date: {date}
  ═══════════════════════════════════════════════════

    SELECTED FEATURES:

    {numbered_feature_list}

    ─────────────────────────────────────────────────
    TOTAL                                   ${total}
    ═════════════════════════════════════════════════

    OPTIONAL ADD-ONS:
    {optional_addons_list}
```

**Step 2: Validate YAML syntax**

```bash
python -c "import yaml; yaml.safe_load(open('D:/Projects/project-planning/config/discovery-rules.yaml'))"
```

**Step 3: Commit**

```bash
cd D:/Projects/project-planning
git add config/discovery-rules.yaml
git commit -m "feat: add discovery rules with AI behavior config"
```

---

### Task 4: Create feature catalog — website.yaml

This is the first and most important feature catalog file. It's calibrated against the real GAKO Technologies project ($500 premium-tier branding site).

**Files:**
- Create: `D:/Projects/project-planning/config/features/website.yaml`

**Step 1: Write the website features catalog**

```yaml
category: Website & Branding
description: Features for marketing sites, portfolios, and business branding

bundles:
  - name: Branding Website
    description: Professional multi-page business/marketing website
    includes: Custom design, responsive layout, navigation, footer, hero section, content sections
    project_types: [website, ecommerce]
    tier_variants:
      basic:
        scope: Up to 3 pages, template-based design, responsive layout
        base_price: 200
      standard:
        scope: Up to 6 pages, custom design, animations, hero with media
        base_price: 400
      premium:
        scope: 10+ pages, premium design, micro-interactions, parallax, CMS integration
        base_price: 700
    dependencies: [Deployment & Setup]
    suggested_with: [Contact/Query Integration, SEO Optimization]

  - name: Landing Page
    description: Single-page focused site for a product, campaign, or service
    includes: Hero, features section, testimonials, CTA, responsive design
    project_types: [website]
    base_price: 150
    scope: Single page with multiple sections, responsive, optimized for conversions
    dependencies: [Deployment & Setup]
    suggested_with: [Contact/Query Integration, Analytics Integration]

  - name: Blog / CMS
    description: Content management system for publishing articles and updates
    includes: Article listing, individual post pages, categories, author pages
    project_types: [website, web_app]
    tier_variants:
      basic:
        scope: Static blog with markdown content, no admin panel
        base_price: 150
      standard:
        scope: CMS with admin panel, rich text editor, image uploads, categories
        base_price: 350
    dependencies: [Deployment & Setup]
    suggested_with: [SEO Optimization, Analytics Integration]

  - name: Portfolio / Gallery
    description: Showcase work, projects, or products with visual layouts
    includes: Grid/masonry layout, lightbox, filtering, responsive images
    project_types: [website]
    base_price: 150
    scope: Visual gallery with filtering and lightbox, responsive
    dependencies: [Deployment & Setup]
    suggested_with: [Contact/Query Integration]

  - name: SEO Optimization
    description: Search engine optimization setup
    includes: Meta tags, sitemap, structured data, Open Graph, page speed optimization
    project_types: [website, web_app, ecommerce]
    base_price: 100
    scope: Technical SEO setup — meta tags, sitemap.xml, robots.txt, structured data, OG tags

  - name: Contact/Query Integration
    description: Contact form with delivery to email or messaging platform
    includes: Form design, validation, email/WhatsApp delivery, confirmation
    project_types: [website, web_app, ecommerce]
    base_price: 50
    scope: Contact form with field validation and email delivery
    suggested_with: [Branding Website, Landing Page]
```

**Step 2: Validate YAML syntax**

```bash
python -c "import yaml; yaml.safe_load(open('D:/Projects/project-planning/config/features/website.yaml'))"
```

**Step 3: Commit**

```bash
cd D:/Projects/project-planning
git add config/features/website.yaml
git commit -m "feat: add website feature catalog with real pricing data"
```

---

### Task 5: Create feature catalog — auth.yaml

**Files:**
- Create: `D:/Projects/project-planning/config/features/auth.yaml`

**Step 1: Write the auth features catalog**

```yaml
category: Authentication & Users
description: User account management, login, and access control

bundles:
  - name: User Authentication
    description: Allow users to register, log in, and manage their accounts
    includes: Registration, login, session management
    project_types: [web_app, mobile, ecommerce]
    tier_variants:
      basic:
        scope: Email/password registration and login, session handling
        base_price: 200
      standard:
        scope: Basic + OAuth (Google/GitHub), password reset, email verification
        base_price: 400
      premium:
        scope: Standard + 2FA, SSO, advanced session management, account lockout
        base_price: 700
    dependencies: [Deployment & Setup]
    suggested_with: [User Profiles, Role-Based Access]

  - name: User Profiles
    description: User profile pages with editable personal information
    includes: Profile page, avatar upload, edit form, public/private settings
    project_types: [web_app, mobile]
    base_price: 150
    scope: Profile page with editable fields, avatar, and display settings
    dependencies: [User Authentication]
    suggested_with: [User Authentication]

  - name: Role-Based Access
    description: Permission system with multiple user roles
    includes: Role definitions, permission gates, admin role management
    project_types: [web_app, mobile]
    tier_variants:
      basic:
        scope: Two roles (admin/user) with basic permission checks
        base_price: 150
      standard:
        scope: Multiple custom roles, granular permissions, role management UI
        base_price: 350
    dependencies: [User Authentication]
    suggested_with: [Admin Panel]
```

**Step 2: Validate YAML and commit**

```bash
cd D:/Projects/project-planning
python -c "import yaml; yaml.safe_load(open('config/features/auth.yaml'))"
git add config/features/auth.yaml
git commit -m "feat: add authentication feature catalog"
```

---

### Task 6: Create feature catalog — remaining categories

Create the remaining 6 feature catalog files. Each follows the same schema.

**Files:**
- Create: `D:/Projects/project-planning/config/features/payments.yaml`
- Create: `D:/Projects/project-planning/config/features/infrastructure.yaml`
- Create: `D:/Projects/project-planning/config/features/communication.yaml`
- Create: `D:/Projects/project-planning/config/features/integrations.yaml`
- Create: `D:/Projects/project-planning/config/features/mobile.yaml`
- Create: `D:/Projects/project-planning/config/features/data.yaml`

**Step 1: Create payments.yaml**

```yaml
category: Payments & Billing
description: Payment processing, subscriptions, and financial features

bundles:
  - name: Payment Gateway
    description: Accept payments via credit card, UPI, or other methods
    includes: Payment provider integration, checkout flow, payment confirmation
    project_types: [web_app, ecommerce, mobile]
    tier_variants:
      basic:
        scope: Single payment provider (Stripe/Razorpay), one-time payments
        base_price: 250
      standard:
        scope: Multiple providers, saved cards, payment history, refund support
        base_price: 500
    dependencies: [User Authentication, Deployment & Setup]
    suggested_with: [Invoice Generation, Subscription Billing]

  - name: Subscription Billing
    description: Recurring payment plans with subscription management
    includes: Plan selection, recurring billing, upgrade/downgrade, cancellation
    project_types: [web_app, ecommerce]
    base_price: 400
    scope: Subscription plans, recurring billing, plan management, usage tracking
    dependencies: [Payment Gateway, User Authentication]
    suggested_with: [Payment Gateway]

  - name: Invoice Generation
    description: Generate and manage invoices for orders or services
    includes: Invoice template, PDF generation, email delivery, invoice history
    project_types: [web_app, ecommerce]
    base_price: 200
    scope: Auto-generated invoices with PDF export and email delivery
    dependencies: [Payment Gateway]
```

**Step 2: Create infrastructure.yaml**

```yaml
category: Infrastructure & Deployment
description: Hosting, deployment, and operational setup

bundles:
  - name: Deployment & Setup
    description: Initial hosting, domain, and deployment configuration
    includes: Hosting setup, domain config, SSL certificate, basic deployment
    project_types: [website, web_app, ecommerce, api, mobile]
    tier_variants:
      basic:
        scope: Shared hosting or free tier, manual deployment, SSL
        base_price: 50
      standard:
        scope: Cloud hosting (Vercel/Railway/AWS), CI/CD pipeline, SSL, domain
        base_price: 150
    suggested_with: [Backup System]

  - name: Admin Panel
    description: Back-office interface for managing content, users, and data
    includes: Dashboard, data tables, CRUD operations, user management
    project_types: [web_app, ecommerce]
    tier_variants:
      basic:
        scope: Simple admin with data tables and basic CRUD
        base_price: 250
      standard:
        scope: Full admin panel with charts, filters, bulk actions, export
        base_price: 500
    dependencies: [User Authentication, Role-Based Access]
    suggested_with: [Role-Based Access]

  - name: Backup System
    description: Automated data backup and recovery
    includes: Scheduled backups, cloud storage, recovery procedures
    project_types: [web_app, ecommerce, api]
    base_price: 100
    scope: Automated daily backups to cloud storage with restore capability
    dependencies: [Deployment & Setup]

  - name: CI/CD Pipeline
    description: Continuous integration and deployment automation
    includes: Auto-testing on push, staging environment, automated deployment
    project_types: [web_app, api]
    base_price: 150
    scope: GitHub Actions or similar — test on push, auto-deploy to staging/production
    dependencies: [Deployment & Setup]
```

**Step 3: Create communication.yaml**

```yaml
category: Communication & Notifications
description: Email, SMS, push notifications, and messaging features

bundles:
  - name: Email Notifications
    description: Transactional and system emails
    includes: Email templates, triggers, delivery via email service
    project_types: [web_app, ecommerce, mobile]
    tier_variants:
      basic:
        scope: Basic transactional emails (welcome, password reset) via SendGrid/SES
        base_price: 100
      standard:
        scope: Custom templates, multiple triggers, email preferences, tracking
        base_price: 250
    dependencies: [User Authentication]
    suggested_with: [User Authentication]

  - name: SMS Integration
    description: SMS notifications for alerts and verification
    includes: SMS provider setup, templates, delivery tracking
    project_types: [web_app, ecommerce, mobile]
    base_price: 150
    scope: SMS via Twilio/SNS for OTP, alerts, and notifications
    dependencies: [User Authentication]

  - name: In-App Messaging / Chat
    description: Real-time messaging between users or with support
    includes: Chat UI, message storage, real-time delivery, read receipts
    project_types: [web_app, mobile]
    tier_variants:
      basic:
        scope: Simple support chat widget (Crisp/Intercom integration)
        base_price: 100
      standard:
        scope: Custom built chat with real-time messaging, history, notifications
        base_price: 500
    dependencies: [User Authentication]

  - name: Push Notifications
    description: Browser or mobile push notifications
    includes: Push service setup, notification triggers, user preferences
    project_types: [web_app, mobile]
    base_price: 150
    scope: Push notification setup with configurable triggers and user opt-in
    dependencies: [User Authentication]
```

**Step 4: Create integrations.yaml**

```yaml
category: Integrations
description: Third-party service integrations

bundles:
  - name: Analytics Integration
    description: Website or app analytics tracking
    includes: Analytics provider setup, event tracking, dashboard access
    project_types: [website, web_app, ecommerce, mobile]
    base_price: 75
    scope: Google Analytics / Mixpanel setup with custom event tracking
    suggested_with: [Branding Website, SEO Optimization]

  - name: Social Media Integration
    description: Social sharing, feeds, or login via social platforms
    includes: Share buttons, social feeds, OAuth login via social providers
    project_types: [website, web_app, mobile]
    base_price: 100
    scope: Social share buttons, Open Graph tags, optional social login
    suggested_with: [User Authentication, Branding Website]

  - name: Third-Party API Integration
    description: Connect to external services (CRM, shipping, maps, etc.)
    includes: API client setup, data sync, error handling, webhook support
    project_types: [web_app, ecommerce, api, mobile]
    tier_variants:
      basic:
        scope: Single API integration with basic data sync
        base_price: 150
      standard:
        scope: Multiple API integrations with webhook support and retry logic
        base_price: 400
    suggested_with: []

  - name: CRM Integration
    description: Connect to CRM platforms (HubSpot, Salesforce, etc.)
    includes: CRM provider setup, lead capture, data sync
    project_types: [web_app, ecommerce]
    base_price: 200
    scope: CRM integration with lead capture from forms and basic data sync
    dependencies: [Contact/Query Integration]
```

**Step 5: Create mobile.yaml**

```yaml
category: Mobile
description: Mobile-specific features and deployment

bundles:
  - name: Mobile App Core
    description: Base mobile application with navigation and core screens
    includes: App shell, navigation, core screens, responsive layouts
    project_types: [mobile]
    tier_variants:
      basic:
        scope: Cross-platform (React Native/Flutter), 3-5 screens, basic navigation
        base_price: 500
      standard:
        scope: 5-10 screens, tab/drawer navigation, onboarding flow, theming
        base_price: 900
      premium:
        scope: 10+ screens, complex navigation, animations, offline support
        base_price: 1500
    dependencies: [Deployment & Setup]
    suggested_with: [User Authentication, Push Notifications]

  - name: App Store Deployment
    description: Publishing to Apple App Store and Google Play Store
    includes: Store listing, screenshots, build signing, submission
    project_types: [mobile]
    base_price: 150
    scope: App store listing, screenshots, metadata, build signing, and submission
    dependencies: [Mobile App Core]

  - name: Offline Mode
    description: App functionality when device is offline
    includes: Local data caching, sync on reconnect, offline indicators
    project_types: [mobile]
    base_price: 300
    scope: Local data storage with background sync when connection restores
    dependencies: [Mobile App Core]

  - name: Device Features
    description: Camera, GPS, biometrics, and other native device capabilities
    includes: Camera access, location services, biometric auth, file access
    project_types: [mobile]
    base_price: 200
    scope: Integration with 1-2 device features (camera, GPS, biometrics)
    dependencies: [Mobile App Core]
```

**Step 6: Create data.yaml**

```yaml
category: Data & Content
description: Data management, search, and content features

bundles:
  - name: CRUD Operations
    description: Create, read, update, delete interface for data entities
    includes: Data forms, validation, listing with pagination, detail views
    project_types: [web_app, ecommerce, api]
    base_price: 200
    scope: Full CRUD interface per data entity with validation and pagination
    dependencies: [Deployment & Setup]
    suggested_with: [Search & Filtering]

  - name: Search & Filtering
    description: Search functionality with filters and sorting
    includes: Search bar, filters, sorting options, results display
    project_types: [web_app, ecommerce]
    tier_variants:
      basic:
        scope: Simple text search with basic filters
        base_price: 100
      standard:
        scope: Full-text search, multi-field filters, saved searches, sort options
        base_price: 250
    dependencies: [CRUD Operations]

  - name: File Uploads
    description: Upload and manage files (images, documents, media)
    includes: Upload UI, cloud storage, file preview, size/type validation
    project_types: [web_app, ecommerce, mobile]
    tier_variants:
      basic:
        scope: Single file upload with size/type validation, cloud storage
        base_price: 100
      standard:
        scope: Multi-file upload, drag-and-drop, image preview, progress bar
        base_price: 200
    dependencies: [Deployment & Setup]

  - name: Data Export
    description: Export data to CSV, Excel, or PDF
    includes: Export button, format selection, download
    project_types: [web_app, ecommerce]
    base_price: 100
    scope: Export data tables to CSV/Excel with column selection
    dependencies: [CRUD Operations]

  - name: Rich Text Editor
    description: WYSIWYG content editor for creating formatted content
    includes: Editor component, formatting tools, image embed, preview
    project_types: [web_app]
    base_price: 100
    scope: Rich text editor with formatting, images, links, and preview
```

**Step 7: Validate all YAML files and commit**

```bash
cd D:/Projects/project-planning
for f in config/features/*.yaml; do python -c "import yaml; yaml.safe_load(open('$f'))"; echo "$f OK"; done
git add config/features/
git commit -m "feat: add complete feature catalog (payments, infra, comms, integrations, mobile, data)"
```

---

### Task 7: Create the main scope skill (router)

This is the entry-point skill that routes `/scope` commands to sub-skills.

**Files:**
- Create: `C:/Users/soumy/.claude/skills/scope/SKILL.md`

**Step 1: Create the skill directory**

```bash
mkdir -p "C:/Users/soumy/.claude/skills/scope"
```

**Step 2: Write the main scope skill**

Create `C:/Users/soumy/.claude/skills/scope/SKILL.md`:

```markdown
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
3. If no sub-command specified, show the command list above and ask which they want

**Data directory:** `D:/Projects/project-planning/`
**Config directory:** `D:/Projects/project-planning/config/`
**Projects directory:** `D:/Projects/project-planning/projects/`
```

**Step 3: Commit the project files (skill is outside repo)**

No commit needed — skill file lives in `~/.claude/skills/`, not in the git repo.

---

### Task 8: Create scope-new skill (core discovery flow)

This is the heart of the framework — the AI-powered scoping session.

**Files:**
- Create: `C:/Users/soumy/.claude/skills/scope-new/SKILL.md`

**Step 1: Create the skill directory**

```bash
mkdir -p "C:/Users/soumy/.claude/skills/scope-new"
```

**Step 2: Write the scope-new skill**

Create `C:/Users/soumy/.claude/skills/scope-new/SKILL.md`:

````markdown
---
name: scope-new
description: Use when starting a new client project scoping session to discover requirements, select features, and generate pricing
---

# New Project Scoping Session

Guide the user through scoping a new client project. This is a 4-step flow: Client Profile → AI Discovery → Feature Selection → Pricing Output.

## Setup

Before starting, load the configuration files:

1. Read `D:/Projects/project-planning/config/budget-tiers.yaml` — budget tier definitions
2. Read `D:/Projects/project-planning/config/discovery-rules.yaml` — discovery rules and project types
3. Read ALL files in `D:/Projects/project-planning/config/features/` — the full feature catalog

Hold all this data in context. You will need it throughout the session.

## Step 1: Client Profile

Ask the user for (use AskUserQuestion with multiple choice where possible):

1. **Client name**
2. **Client country** — match to a budget tier from budget-tiers.yaml
3. **Industry/domain** (brief, for context)
4. **Project type** — use the project_types list from discovery-rules.yaml

After collecting:
- Display the auto-detected budget tier, multiplier, and suggestion mode
- Ask if they want to override the tier or multiplier

## Step 2: AI-Powered Discovery

Now conduct an intelligent discovery conversation. You have the full feature catalog loaded.

**Rules (from discovery-rules.yaml):**
- Ask at least {min_questions} and at most {max_questions} follow-up questions
- MUST confirm: project_type, core_features
- Follow the budget_awareness guidance for the client's suggestion_mode

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

For each bundle:
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
2. Save to `D:/Projects/project-planning/projects/YYYY-MM-DD-{client-name-slug}.yaml`
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
````

**Step 3: No git commit needed** — skill is in `~/.claude/skills/`

---

### Task 9: Create scope-list skill

**Files:**
- Create: `C:/Users/soumy/.claude/skills/scope-list/SKILL.md`

**Step 1: Create directory and write skill**

```bash
mkdir -p "C:/Users/soumy/.claude/skills/scope-list"
```

Create `C:/Users/soumy/.claude/skills/scope-list/SKILL.md`:

```markdown
---
name: scope-list
description: Use when listing saved client project scopes from the project planning framework
---

# List Saved Project Scopes

1. Use Glob to find all YAML files in `D:/Projects/project-planning/projects/*.yaml`
2. For each file, read the `client.name`, `project_type`, `total`, `budget.tier`, and `created` fields
3. Display as a table:

| # | Date | Client | Type | Tier | Total |
|---|------|--------|------|------|-------|
| 1 | 2026-02-16 | GAKO Technologies | website | premium | $500 |

4. If no files found, say "No saved projects yet. Use `/scope new` to start one."
```

---

### Task 10: Create scope-view skill

**Files:**
- Create: `C:/Users/soumy/.claude/skills/scope-view/SKILL.md`

**Step 1: Create directory and write skill**

```bash
mkdir -p "C:/Users/soumy/.claude/skills/scope-view"
```

Create `C:/Users/soumy/.claude/skills/scope-view/SKILL.md`:

```markdown
---
name: scope-view
description: Use when viewing a saved client project scope with full pricing breakdown
---

# View a Saved Project Scope

1. If a project name was provided as argument, search for matching files in `D:/Projects/project-planning/projects/`
2. If no name provided or multiple matches, list available projects and ask user to pick
3. Read the selected YAML file
4. Display the full pricing breakdown using this format:

```
═══════════════════════════════════════════════════
  PROJECT SCOPE: {client.name}
  Client: {client.country} | Budget Tier: {budget.tier} ({budget.multiplier}x)
  Date: {created}
═══════════════════════════════════════════════════

  SELECTED FEATURES:

  {numbered list of selected_features with bundle, variant, scope, final_price}

  ─────────────────────────────────────────────────
  TOTAL                                   ${total}
  ═════════════════════════════════════════════════

  OPTIONAL ADD-ONS (not included):
  {list of optional_addons with bundle, final_price}
```

5. After showing, ask if they want to edit (invoke scope-edit) or start a new scope
```

---

### Task 11: Create scope-edit skill

**Files:**
- Create: `C:/Users/soumy/.claude/skills/scope-edit/SKILL.md`

**Step 1: Create directory and write skill**

```bash
mkdir -p "C:/Users/soumy/.claude/skills/scope-edit"
```

Create `C:/Users/soumy/.claude/skills/scope-edit/SKILL.md`:

```markdown
---
name: scope-edit
description: Use when editing or modifying a saved client project scope, changing features, tier, or pricing
---

# Edit a Saved Project Scope

1. If a project name was provided, find the matching file in `D:/Projects/project-planning/projects/`
2. If not found, use scope-list to show available projects
3. Read the selected YAML file and display the current scope summary
4. Load the feature catalog from `D:/Projects/project-planning/config/features/` and budget tiers from `D:/Projects/project-planning/config/budget-tiers.yaml`

Ask what they want to change (use AskUserQuestion):
- **Change budget tier** — update multiplier, recalculate all prices
- **Add features** — show available features not currently selected, add chosen ones
- **Remove features** — show selected features, remove chosen ones (warn about dependency breaks)
- **Change feature tier** — for tiered features, switch between basic/standard/premium
- **Update client info** — change name, country, industry

After changes:
1. Recalculate all final_prices using current multiplier
2. Recalculate total
3. Show the updated pricing breakdown
4. Ask to confirm and save
5. Write the updated YAML back to the same file path
```

---

### Task 12: Create scope-catalog and scope-tiers skills

**Files:**
- Create: `C:/Users/soumy/.claude/skills/scope-catalog/SKILL.md`
- Create: `C:/Users/soumy/.claude/skills/scope-tiers/SKILL.md`

**Step 1: Create directories**

```bash
mkdir -p "C:/Users/soumy/.claude/skills/scope-catalog"
mkdir -p "C:/Users/soumy/.claude/skills/scope-tiers"
```

**Step 2: Write scope-catalog skill**

Create `C:/Users/soumy/.claude/skills/scope-catalog/SKILL.md`:

```markdown
---
name: scope-catalog
description: Use when viewing, adding, or editing features in the project scoping feature catalog
---

# Manage Feature Catalog

The feature catalog lives in `D:/Projects/project-planning/config/features/`.

## View Catalog

1. Read all YAML files in the features directory
2. For each category, show a summary table:

| Bundle | Type | Base Price | Tiered? |
|--------|------|-----------|---------|
| Branding Website | website | $200-700 | Yes (basic/standard/premium) |

## Add a Feature Bundle

Ask the user for:
1. Which category file to add to (or create new category)
2. Bundle name, description, includes
3. Project types it applies to
4. Is it tiered? If yes, get scope + base_price for each tier. If no, get single base_price + scope.
5. Dependencies (which other bundles must be present)
6. Suggested_with (commonly paired bundles)

Write the new bundle to the appropriate YAML file.

## Edit a Feature Bundle

1. Ask which bundle to edit
2. Show current values
3. Ask what to change
4. Update the YAML file

Always validate YAML after writing.
```

**Step 3: Write scope-tiers skill**

Create `C:/Users/soumy/.claude/skills/scope-tiers/SKILL.md`:

```markdown
---
name: scope-tiers
description: Use when viewing or editing budget tier multipliers and country assignments
---

# Manage Budget Tiers

Budget tiers are defined in `D:/Projects/project-planning/config/budget-tiers.yaml`.

## View Tiers

Read the config and display:

| Tier | Multiplier | Countries | Suggestion Mode |
|------|-----------|-----------|-----------------|
| starter | 0.30x | India, Bangladesh, ... | suggest_minimal |

## Edit Tiers

Ask the user what to change:
- **Adjust multiplier** — change a tier's multiplier value
- **Move country** — reassign a country to a different tier
- **Add country** — add a new country to a tier
- **Add tier** — create an entirely new tier

After changes, update the YAML file and validate.

Show a before/after comparison for any pricing changes:
"Changing starter multiplier from 0.30 to 0.35 means a $500-base project goes from $150 to $175 for Indian clients."
```

---

### Task 13: End-to-end test — run a real scoping session

This validates the entire framework works together.

**Step 1: Invoke `/scope new` and run through the GAKO Technologies scenario**

Test with these inputs:
- Client: GAKO Technologies
- Country: United States (should auto-detect Premium tier, 1.0x)
- Industry: Automotive Data Intelligence
- Project type: Website / Branding Site
- Description: "Professional B2B site for automotive data intelligence company. Need hero with video, sections explaining their product, and a demo request form."

**Expected outcome:**
- AI should recommend: Branding Website (standard, $400) + Contact/Query Integration ($50) + Deployment & Setup (basic, $50) = $500
- Should suggest optional: SEO Optimization ($100), Analytics Integration ($75)
- Should save YAML to `projects/2026-02-16-gako-technologies.yaml`

**Step 2: Invoke `/scope list`**

Expected: Shows the GAKO Technologies project in a table.

**Step 3: Invoke `/scope view gako`**

Expected: Shows full pricing breakdown.

**Step 4: Test with an Indian client scenario**

Test with:
- Client: LocalShop India
- Country: India (should auto-detect Starter tier, 0.30x)
- Project type: E-Commerce
- Description: "Simple online store for local handicraft shop"

**Expected behavior:**
- AI should suggest minimal features (basic tiers only)
- Should NOT proactively suggest premium features
- Prices should be 0.30x of base prices

**Step 5: Verify both sessions are saved in projects/**

```bash
ls D:/Projects/project-planning/projects/
```

---

## Execution Order & Dependencies

```
Task 1 (dirs) → Task 2 (tiers) → Task 3 (rules) → Task 4-6 (features) → Task 7-12 (skills) → Task 13 (test)
     │                                                    │
     └── Tasks 2-6 can run in parallel ──────────────────┘

Tasks 7-12 (skills) can run in parallel — they are independent files.
Task 13 depends on ALL previous tasks.
```

## Summary

| Task | What | Files |
|------|------|-------|
| 1 | Directory structure | config/, projects/ |
| 2 | Budget tiers | config/budget-tiers.yaml |
| 3 | Discovery rules | config/discovery-rules.yaml |
| 4 | Website features | config/features/website.yaml |
| 5 | Auth features | config/features/auth.yaml |
| 6 | Remaining features (6 files) | config/features/*.yaml |
| 7 | Main scope skill | ~/.claude/skills/scope/SKILL.md |
| 8 | Scope-new skill (core) | ~/.claude/skills/scope-new/SKILL.md |
| 9 | Scope-list skill | ~/.claude/skills/scope-list/SKILL.md |
| 10 | Scope-view skill | ~/.claude/skills/scope-view/SKILL.md |
| 11 | Scope-edit skill | ~/.claude/skills/scope-edit/SKILL.md |
| 12 | Scope-catalog + scope-tiers | ~/.claude/skills/scope-*/SKILL.md |
| 13 | End-to-end test | Run real scoping sessions |
