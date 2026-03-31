# Arctic Fresh — Horizon Theme

Custom Shopify Horizon theme for Arctic Fresh, an Inuit-operated grocery store delivering to 15 communities across Nunavut under the Nutrition North Canada (NNC) subsidy program.

## Working Philosophy

### Expert Mindset

This is a ground-up theme build on Shopify's Horizon base theme, not a migration from the legacy theme. When working on this project:

- **Think from first principles.** Don't inherit patterns from the legacy theme. The old theme used jQuery, Bootstrap 4, deprecated `{% include %}` tags, and baked-in third-party snippets — none of that carries forward.
- **Be the expert.** The user relies on your judgment for production-level design decisions. If something is wrong or could be done better, say so and explain why.
- **Follow Horizon conventions.** The `.cursor/rules/` folder contains 44 rule files covering blocks, CSS, JS, Liquid, schemas, and accessibility standards. These are authoritative — read and follow them.
- **Flag root causes.** Don't add workarounds on top of broken abstractions. Identify what's actually wrong and fix it at the source.

### Communication Style

The user is a junior-level developer experienced with the MERN stack (MongoDB, Express, React, Node.js). Shopify theme development (Liquid, Web Components, Shopify-specific patterns) is new territory.

- **Default to high-level explanations.** Lead with "what" and "why" before "how."
- **Teach as you go.** When making a design decision, explain the reasoning — not just what to do, but why.
- **Use concrete examples.** Instead of "this violates BEM," show the correct naming.
- **Be direct about quality.** If code is messy, say so clearly.

## Client Context

**Store:** Arctic Fresh (arcticfresh.ca) — Shopify store with 9,000+ SKUs of food and household items.

**Business model:** Online grocery delivery to remote Nunavut communities. Products are shipped from southern Canada with shipping rates that vary by community and product category. Nutrition North Canada subsidizes eligible food items.

**Key people:**
- Hani (owner)
- Waleed (manager, main point of contact)
- Mu'aadh (day-to-day operations)

**Constraints:** Budget-conscious project. The theme should be professional and polished but built efficiently using Horizon's existing blocks and sections wherever possible. Prefer configuration over custom code.

## References

Two reference directories and one reference note are included for context — these are **read-only references**, not code to migrate or copy from.

### `references/northbound/`

The Northbound custom Shopify app (React Router v7 + Prisma + TypeScript). This is the shipping logic backend that integrates with this theme. Key integration points:

- **Theme app extension** (`extensions/storefront-selector/`): Community selector block that injects into the storefront via `@app` blocks. Uses `data-northbound-*` attributes for DOM hooks.
- **App proxy endpoints:**
  - `GET /apps/northbound/bootstrap` — community list, selected community, shop config
  - `POST /apps/northbound/prices` — community-specific pricing for product variants
- **Carrier service:** `POST /api/carrier-service` — real-time shipping rate calculation at checkout
- **Storefront JS modules:** runtime, UI, DOM, pricing, controller — all vanilla JS, loaded via deferred script tags
- **`references/northbound/CLAUDE.md`** has full architecture docs, domain model, and decision principles for the app itself.

### `references/arcticfresh-legacy-theme-export/`

The existing pre-OS2.0 vintage theme running on the live store. Reference only — we are NOT migrating from this. Useful for understanding:

- **What the store currently looks like** (layout, sections, features)
- **Northbound integration hooks** in the legacy theme: `data-northbound-price-surface`, `data-northbound-price-display`, `data-northbound-add-to-cart` attributes in `snippets/product-item.liquid`, `snippets/product-form.liquid`, `templates/product.liquid`, `templates/cart.liquid`
- **Legacy navigation structure:** 3-tier header (announcement bar, logo/search/cart, department mega menu)
- **Legacy homepage sections:** jumbotron slider, featured collections (department grid), featured products, featured content, testimonials, newsletter
- **Cart features:** order preferences (substitutions), minimum order alert, estimated shipping display

**Do not** copy code from the legacy theme. It uses jQuery, Bootstrap 4, Slick.js, deprecated Liquid tags, and has console.log statements left in production code.

### `references/live-shopify-catalog-context.md`

A read-only context note summarizing the live Shopify catalog structure that is relevant to theme work. Useful for understanding:

- live catalog scale
- current collection-driven merchandising structure
- observed product metadata patterns
- the mix of standard grocery and bulk/commercial products
- live inventory-state patterns visible in the catalog

## Theme Architecture

**Base:** Shopify Horizon v3.4.0 — block-based OS2.0 theme with nested blocks (up to 8 levels).

**Stack:** Liquid templating + vanilla CSS (BEM, scoped via `{% stylesheet %}`) + vanilla JS (Web Components via `Component` framework in `assets/component.js`).

### Directory Structure

```
assets/          — JS, CSS, SVGs (flat, no subdirectories allowed)
blocks/          — Theme blocks (reusable, nestable, composable)
config/          — settings_schema.json, settings_data.json
layout/          — theme.liquid (required base layout)
locales/         — Translation JSON files
sections/        — Section templates (page-level containers)
snippets/        — Reusable Liquid fragments
templates/       — JSON page templates (auto-generated by theme editor)
references/      — Read-only reference code and notes (northbound app, legacy theme, live catalog context)
```

### Key Files

- **`assets/component.js`** — Core Component framework. Provides `refs` system (auto-tracks elements with `ref="name"` attributes), declarative event binding (`on:click="/methodName"`), MutationObserver-based ref updates, and `requiredRefs` validation. Read this first before writing any JS.
- **`blocks/group.liquid`** — The foundational layout primitive. A flex container with configurable direction, alignment, gap, width, height, background, and border. Most custom layouts are composed by nesting content blocks inside group blocks.
- **`snippets/mega-menu-list.liquid`** — Mega menu rendering. Driven by Shopify navigation link lists.
- **`config/settings_schema.json`** — Global theme settings (colors, fonts, spacing).

### Built-in UI Infrastructure

Horizon has production-grade reusable components. **Always check these before building custom UI:**

- **Carousel/Slideshow:** `snippets/slideshow.liquid` + `slideshow-slide.liquid` + `slideshow-arrows.liquid` + `slideshow-controls.liquid` + `assets/slideshow.js`. Full carousel with arrows, pagination, autoplay, infinite loop, drag/swipe, and keyboard nav. Use `snippets/resource-list.liquid` for automatic grid/carousel/bento/editorial layout switching.
- **Cards:** `snippets/resource-card.liquid` (universal), `product-card.liquid`, `collection-card.liquid`, `card-gallery.liquid` (product image carousel in cards).
- **Media:** `snippets/image.liquid` (responsive images), `video.liquid` (all video types), `background-media.liquid` (full-bleed backgrounds), `overlay.liquid` (color/gradient overlays).
- **Grids:** `snippets/bento-grid.liquid`, `editorial-product-grid.liquid`, `editorial-collection-grid.liquid`, `product-grid.liquid` (collection page with infinite scroll).
- **Interactive JS:** `assets/dialog.js` (modals), `anchored-popover.js` (dropdowns), `floating-panel.js` (viewport-aware panels), `accordion-custom.js` (collapsibles), `marquee.js` (continuous scroll).
- **Layout primitives:** `snippets/spacing-style.liquid`, `gap-style.liquid`, `size-style.liquid`, `layout-panel-style.liquid` — generate responsive CSS variables for spacing, gaps, and sizing.
- **Utilities:** `snippets/section-header.liquid` (title + "View all"), `button.liquid`, `icon.liquid`, `divider.liquid`, `price.liquid`.

See the `section-builder` skill (`.claude/skills/section-builder/SKILL.md`) for the full inventory with parameters and usage guidance.

### How Things Connect

1. **JSON templates** (`templates/*.json`) define which sections appear on a page and in what order.
2. **Sections** (`sections/*.liquid`) are page-level containers with schemas. They render theme blocks via `{% content_for 'blocks' %}`.
3. **Theme blocks** (`blocks/*.liquid`) are composable units with their own markup, scoped CSS, and schemas. Blocks can nest other blocks.
4. **Snippets** (`snippets/*.liquid`) are pure reusable code with no settings UI. Blocks/sections render snippets via `{% render 'snippet-name' %}`.
5. **Assets** (`assets/*.js`, `assets/*.css`) provide JS behavior (Web Components) and shared styles.

### Constraints

- **Max 25 sections per JSON template**, max 50 blocks per section.
- **Theme blocks and section blocks cannot coexist** in the same section.
- **Only ONE `{% content_for 'blocks' %}` per Liquid file.** If needed in multiple places, capture it first.
- **JS must be under 16KB minified** per file. No external frameworks.
- **Never edit `{% schema %}` blocks directly in `.liquid` files.** Edit JS sources in `schemas/` and run `npm run build:schemas`.
- **Development themes auto-delete after 7 days of inactivity** or on `shopify auth logout`.

## Commands

```bash
# Development
shopify theme dev --store arcticfresh       # Local dev server with hot reload (localhost:9292)
shopify theme check                          # Lint Liquid and JSON (run before pushing)

# Schema build (required after editing schemas/ files)
npm run build:schemas                        # Compile JS schemas into {% schema %} blocks

# Deployment
shopify theme push --unpublished             # First deploy: create new unpublished theme
shopify theme push                           # Update existing theme on store
shopify theme publish                        # Make theme live (confirm with user first)

# Utilities
shopify theme pull                           # Download theme from store
shopify theme list                           # List all themes with IDs
shopify theme console                        # Liquid REPL for testing expressions
shopify theme profile                        # Profile Liquid rendering performance
```

## External Tools

```bash
# Internet search (Perplexity sonar-pro-search via CLI)
deep-search "your query here"              # Use when Shopify docs or local context aren't enough
```

Use `deep-search` when you need up-to-date information that isn't in the codebase or Shopify docs — e.g., Horizon-specific behavior, Liquid edge cases, NNC program details, or community-specific context. It queries Perplexity's sonar-pro-search model and returns results directly in the terminal.

## Shopify Documentation

Append `.md` to any Shopify docs URL for clean markdown. Key starting points:

- https://shopify.dev/docs/storefronts/themes.md — theme docs root
- https://shopify.dev/docs/storefronts/themes/architecture.md — architecture overview
- https://shopify.dev/docs/storefronts/themes/tools/cli.md — CLI command reference
- https://shopify.dev/docs/storefronts/themes/best-practices.md — performance, accessibility, UX

## Northbound Integration

The Northbound app integrates with the storefront via two mechanisms:

### 1. Theme App Extension (App Blocks)

The `community-selector` block (`@app` type) can be dropped into any section that accepts app blocks. It injects:
- A community selector UI (dropdown for selecting the delivery community)
- JS modules that handle pricing display, cart management, and community state
- DOM hooks via `data-northbound-*` attributes

On Horizon, app blocks can be placed **inside product cards** (`{ "type": "@app" }` in block schemas), which means Northbound pricing can render directly on collection grids without editing theme code.

### 2. App Proxy Endpoints

- **Bootstrap** (`/apps/northbound/bootstrap`): Returns community list, selected community, and shop configuration. Called once on page load.
- **Prices** (`/apps/northbound/prices`): Returns community-adjusted prices for a set of variant IDs. Called when community changes or new products are displayed.
- **Carrier service** (`/api/carrier-service`): Shopify calls this at checkout for real-time shipping rates. Not directly theme-related but affects displayed shipping estimates.

### Integration Approach

For this theme, Northbound integration should work primarily through **app blocks** — the theme should accept `@app` blocks in product cards, product pages, cart drawers, and any section where pricing or community context is relevant. The theme itself should NOT contain Northbound-specific code. The app extension handles all Northbound logic.

Where the theme needs to accommodate Northbound:
- Ensure product card block schemas include `{ "type": "@app" }` in their allowed blocks
- Ensure cart and product sections accept app blocks
- Avoid overriding price display in ways that conflict with Northbound's dynamic pricing

## Domain Context

### Nutrition North Canada (NNC)

NNC is a federal subsidy program that reduces the cost of eligible food and household items shipped to isolated northern communities. Key concepts:

- **Shipping classes:** `NNC1` (highest subsidy — perishable/nutritious), `NNC2` (moderate subsidy — other food), `NNC7` (non-food essential items), `UNSUBSIDIZED` (no subsidy)
- **Community zones:** Different communities receive different subsidy amounts based on isolation level
- **Subsidy display:** Arctic Fresh shows community-specific "all-in" pricing (base price + shipping - subsidy) on the storefront, while the actual shipping calculation happens at checkout via the carrier service

### Arctic Fresh Specifics

- **15 Nunavut communities** served (Iqaluit, Rankin Inlet, Arviat, Baker Lake, etc.)
- **9,000+ SKUs** across departments: Meat & Seafood, Produce, Dairy & Eggs, Pantry, Frozen Foods, Drinks, Baby & Kids, Household, Health & Beauty
- **Minimum order:** $95
- **Community selection** is the first thing a customer does — it determines all displayed pricing and available shipping rates

## Decision Principles

1. **Code over configuration.** Write custom sections and Liquid rather than composing layouts through JSON template config and theme editor settings. Keep `templates/*.json` files lean — they should reference sections, not contain sprawling block/setting definitions. Hardcode values directly in `.liquid` files where the content is known and stable. Theme editor configurability for the client is not a priority.
2. **Follow Horizon conventions.** BEM CSS, Component framework for JS, `{% stylesheet %}` for scoped CSS, `{% doc %}` for snippet documentation, `schemas/` for schema sources. The `.cursor/rules/` folder is the style guide.
3. **App blocks for integration.** Northbound integration should happen through `@app` blocks, not baked-in theme code. This keeps the theme and app independently deployable.
4. **Mobile-first for grocery.** Many Arctic Fresh customers browse on phones. Every layout decision should consider the mobile experience first — horizontal scrolling for department nav, accordion menus, touch-friendly quick-add targets (44x44px minimum).
5. **Performance matters.** Remote Nunavut communities often have limited bandwidth. Lazy load images, keep JS minimal, avoid render-blocking resources. Target Lighthouse score of 60+ across home, product, and collection pages.
6. **Clean break from legacy.** Do not reference, copy, or adapt patterns from the legacy theme. Build fresh on Horizon.

## Code Style

Follows Horizon conventions (enforced by `.cursor/rules/`):

- **CSS:** BEM naming (`.block__element--modifier`), specificity target `0 1 0`, never IDs, scoped via `{% stylesheet %}`, CSS custom properties namespaced per component, logical properties for RTL support
- **JS:** `const` over `let`, `for...of` over `.forEach()`, Web Components extending `Component`, declarative event binding (`on:click="/method"`), JSDoc typedefs for refs, `AbortController` for fetch cleanup, no external dependencies
- **Liquid:** `{% render %}` over `{% include %}` (deprecated), `{% liquid %}` for multiline blocks, `{% doc %}` for snippet params, inline Liquid preferred over variable declarations
- **Schemas:** Edit JS sources in `schemas/`, run `npm run build:schemas`, never edit `{% schema %}` directly
