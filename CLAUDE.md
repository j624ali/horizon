# Arctic Fresh — Horizon Theme

Custom Shopify Horizon theme for Arctic Fresh, an Inuit-operated grocery store delivering to 15 communities across Nunavut under the Nutrition North Canada (NNC) subsidy program.

## Working Philosophy

This is a ground-up theme build on Shopify's Horizon base theme, **not** a migration from the legacy theme. Don't inherit patterns from the legacy theme (jQuery, Bootstrap 4, deprecated `{% include %}`, third-party snippets). Follow Horizon conventions where they apply, take the shortcut where they don't (see "Shortcut doctrine" below). Flag root causes — don't add workarounds on top of broken abstractions.

The user is a junior-level developer experienced with the MERN stack but new to Shopify (Liquid, Web Components, Shopify-specific patterns). Lead with "what" and "why" before "how"; explain reasoning when making design decisions; show concrete examples when correcting; be direct about quality.

## Client context

**Store:** Arctic Fresh — `arcticfresh.ca` (storefront), `arcticfresh.myshopify.com` (admin). Parent: Friendship Fast (Ottawa-based procurement & shipping). Friendship Fast's own site is on WooCommerce and out of scope.

**Business model:** Online grocery delivery to 15 remote Nunavut communities. Shipping rates vary by community and product category. Nutrition North Canada subsidizes eligible food items.

## Theme architecture

**Base:** Shopify Horizon v3.4.0 — block-based OS2.0 theme with nested theme blocks (up to 8 levels).

**Stack:** Liquid + vanilla CSS (BEM, scoped via `{% stylesheet %}`) + vanilla JS (Web Components via the `Component` framework in `assets/component.js`).

```
assets/         JS, CSS, SVGs (flat — no subdirectories)
blocks/         Theme blocks (reusable, nestable, composable)
config/         settings_schema.json, settings_data.json
layout/         theme.liquid (required base layout)
locales/        Translation JSON files
sections/       Page-level containers
snippets/       Reusable Liquid fragments
templates/      JSON page templates
playground/     Static HTML/CSS/JS prototypes (see playground/CLAUDE.md)
docs/           Project-internal docs (see "Reference documents" below)
references/     Read-only third-party material (gitignored)
.claude/rules/  Path-scoped instructions (lazy-loaded for matching files)
.claude/skills/ On-demand skills: shopify-liquid, accessibility, section-builder
```

### Constraints

- Max 25 sections per JSON template, max 50 blocks per section.
- Theme blocks and section blocks cannot coexist in the same section.
- Only ONE `{% content_for 'blocks' %}` per Liquid file.
- JS must be under 16KB minified per file. No external dependencies.
- Development themes auto-delete after 7 days of inactivity or on `shopify auth logout`.

## Commands

```bash
# Development
shopify theme dev --store arcticfresh   # Local dev with hot reload (localhost:9292)
shopify theme check                      # Lint Liquid and JSON

# Deployment
shopify theme push --unpublished         # First deploy: create new unpublished theme
shopify theme push                       # Update existing theme on store
shopify theme publish                    # Make theme live (confirm with user first)

# Utilities
shopify theme pull                       # Download theme from store
shopify theme list                       # List all themes with IDs
shopify theme console                    # Liquid REPL
```

## External tools

- **`sonar "<query>"`** — Perplexity sonar-pro-search CLI. Reach for it when Shopify docs or local context aren't enough (Horizon edge cases, NNC program details, ecommerce best practices).
- **`admin_gql '<query>'`** — direct Admin GraphQL access to the live store. Use to verify catalog state — collection handles, product metafields, image presence, inventory — instead of guessing or relying on stale exports. **Don't mutate.** Recipes in `docs/admin-gql-recipes.md`.

Append `.md` to any Shopify docs URL for clean markdown. Start at https://shopify.dev/docs/storefronts/themes.md.

## Domain context

### Nutrition North Canada (NNC)

Federal subsidy program reducing the cost of eligible food and household items shipped to isolated northern communities.

- **Shipping classes:** `NNC1` (highest subsidy — perishable/nutritious), `NNC2` (moderate — other food), `NNC7` (non-food essentials), `UNSUBSIDIZED`.
- **Community zones:** subsidy amounts vary by isolation level.
- Theme code reads NNC product data from `custom.shipping_class` and `custom.nnc_item_code` metafields. Northbound is the source of truth for pricing math.
- See `docs/nnc-display.md` for the storefront pricing pattern (NNC is treated as eligibility, not as a sale).

### Arctic Fresh specifics

- **15 Nunavut communities served:** Arctic Bay, Cape Dorset, Clyde River, Grise Fiord, Hall Beach, Igloolik, Iqaluit, Kimmirut, Kuujjuaq, Pangnirtung, Pond Inlet, Qikiqtarjuaq, Rankin Inlet, Resolute, Sanikiluaq.
- **9,000+ SKUs** across departments: Meat & Seafood, Produce, Dairy & Eggs, Pantry, Frozen Foods, Drinks, Baby & Kids, Household, Health & Beauty.
- **Minimum order:** $95 (raised from $75 in the 2026-03-08 client email; Northbound's `ShopConfig` is authoritative).
- **Community selection** is the first thing a customer does — it determines all displayed pricing and available shipping rates.
- **Northbound app is live on production**, processing real orders. Theme changes that touch pricing, cart, or shipping must not assume the app's storefront JS is absent.

## Decision principles

1. **Code over configuration.** Custom sections in Liquid over JSON-template-and-theme-editor composition. Keep `templates/*.json` lean — they should reference sections, not contain sprawling block/setting definitions. Hardcode values in `.liquid` where the content is known and stable. Theme editor configurability for the client is not a priority.
2. **DOM-attribute hooks for Northbound.** Northbound is a global app-embed + a `data-northbound-*` attribute contract. The theme renders the markup; the app's runtime mutates it. Don't bake pricing/shipping logic into Liquid — emit the right attributes and let Northbound do the math. Full contract in `docs/northbound-integration.md`.
3. **Mobile-first for grocery.** Many Arctic Fresh customers browse on phones. Horizontal scrolling for department nav, accordion menus, 44×44px touch targets minimum.
4. **Performance matters.** Remote Nunavut communities often have limited bandwidth. Lazy load images, keep JS minimal, avoid render-blocking resources. Lighthouse target 60+ across home, product, collection.
5. **Static pages are template-driven.** One flexible `templates/page.json` + content-rich section, populated via the Shopify admin's page editor. No custom section per static page.

## Shortcut doctrine

Horizon's full conventions (Component framework, theme blocks, scoped `{% stylesheet %}`, `{% content_for 'blocks' %}`, 3-tier tokens) are heavyweight and not always worth the ceremony for net-new work. Two patterns coexist:

**Stock Horizon files** — anything that ships in vanilla Horizon (`sections/header.liquid`, `blocks/group.liquid`, `snippets/resource-card.liquid`, etc.): keep using Horizon conventions. Don't fork the file's pattern; that breaks future merges from upstream. When forking a stock file's `{% stylesheet %}` block, extract it into a sibling `*-styles.liquid` snippet first (Horizon's own pattern — `predictive-search-styles.liquid`, `slideshow-styles.liquid`).

**New Arctic Fresh sections** — anything net-new for this project: take the shortcut. Section blocks or hardcoded markup (no theme blocks). Plain `class extends HTMLElement` (no `Component` framework). Per-feature `assets/arctic-*.css` or component-scoped `{% stylesheet %}` if self-contained. `settings: []` by default. Separate files per variant rather than `{% case %}` mega-blocks.

The line: if it ships in vanilla Horizon, treat it as Horizon. If it's net-new, take the shortcut. Detailed code-style rules are in `.claude/rules/code-style.md` (lazy-loaded when editing matching files).

## How Horizon themes are typically extended

The Dwell/Dawn audits (2026-04-30) confirm: brand work on Horizon-based themes is mostly `settings_data.json` config + a thin CSS overlay (`*-styles.liquid` snippets, additions in `theme-styles-variables.liquid`). Liquid markup stays close to vanilla. When working on visual/brand changes, reach for tokens and `*-styles.liquid` snippets first; only fork Liquid when the markup itself needs to change. Findings logged in `docs/horizon-notes.md`.

## Workflow

1. **Research first** for non-trivial design/architecture decisions (homepage layout, cart UX, navigation, conversion-critical UI). Validate with `sonar` against current ecommerce best practices and Shopify-specific guidance. Cheaper than rework.
2. **Document Horizon learnings as you go** in `docs/horizon-notes.md`. Horizon's conventions aren't fully established and future sessions read this before diving in.
3. **Test in the browser.** `shopify theme check` won't catch layout bugs. Load via `shopify theme dev` and visually inspect at mobile and desktop widths. Check the golden path and at least one edge case (long text, missing image, empty state).
4. **Commit often.** Small, focused commits with meaningful messages. Reset cleanly when an experiment fails.
5. **Roadmap lives in `docs/roadmap.md`** — read before starting and update as scope shifts.

## Reference documents

Before starting a task, identify which of these are relevant and read them. These are progressive disclosure — don't load everything every session.

- `docs/roadmap.md` — Major work areas, current status, open questions. **Read when:** scoping new work or unsure if something is in-flight.
- `docs/horizon-notes.md` — Horizon-specific gotchas, working patterns, token system, theme delta findings. **Read when:** writing any new section/block/snippet, or hitting a Horizon-specific bug.
- `docs/northbound-integration.md` — DOM contract, app proxy endpoints, integration approach, app-modification scope. **Read when:** touching pricing surfaces, cart, ATC buttons, community selector, or shipping display.
- `docs/nnc-display.md` — Storefront pricing patterns (cards, PDP breakdown, what to never show). **Read when:** building or editing any UI that shows price.
- `docs/brand-guardrails.md` — Inuit-operated context, what to avoid, reference brands. **Read when:** any visual or copy decision touching the brand surface.
- `docs/header-audit.md` — Current header analysis vs. references. **Read when:** modifying header/nav.
- `docs/admin-gql-recipes.md` — Common Admin GraphQL queries for catalog recon. **Read when:** verifying live store state.
- `docs/research-reports/` — Deep-dive investigations (design, header, web research). **Read when:** doing exploratory work in those areas.

**Skills** in `.claude/skills/` (`shopify-liquid`, `accessibility`, `section-builder`) load on-demand when their description matches the task. The `section-builder` skill has the full inventory of Horizon's reusable snippets (cards, carousels, grids, layout primitives) — reach for it before building custom UI.

**References** in `references/` (gitignored, fresh clones won't have it):

- `references/horizon/`, `references/dawn/`, `references/dwell/`, `references/hyper/` — example Shopify themes, read-only lookups for "how does Horizon do X." None are this project's vanilla baseline; for that, use `git diff <Horizon vX.X merge commit>..HEAD -- <file>` against the upstream merge commits in this repo's history.
- `references/northbound/` — the Northbound app source (React Router v7 + Prisma + TypeScript). **Modifiable in scope** — extending the app for new theme features is fair game when needed. See `references/northbound/CLAUDE.md`.
- `references/arcticfresh-legacy-theme-export/` — legacy pre-OS2.0 theme currently live in production. Reference for the Northbound DOM contract. **Do not migrate code from it** — jQuery, Bootstrap 4, Slick.js, deprecated tags.
