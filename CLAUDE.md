# Arctic Fresh — Horizon Theme

Custom Shopify Horizon theme for Arctic Fresh, an Inuit-operated grocery store delivering to 15 communities across Nunavut under the Nutrition North Canada (NNC) subsidy program.

## Working Philosophy

### Expert Mindset

This is a ground-up theme build on Shopify's Horizon base theme, not a migration from the legacy theme. When working on this project:

- **Think from first principles.** Don't inherit patterns from the legacy theme. The old theme used jQuery, Bootstrap 4, deprecated `{% include %}` tags, and baked-in third-party snippets — none of that carries forward.
- **Be the expert.** The user relies on your judgment for production-level design decisions. If something is wrong or could be done better, say so and explain why.
- **Follow Horizon conventions.** The `.claude/skills/` folder holds skills (`shopify-liquid`, `accessibility`, `section-builder`) covering blocks, CSS, JS, Liquid, schemas, and accessibility standards. These are authoritative — read and follow them.
- **Flag root causes.** Don't add workarounds on top of broken abstractions. Identify what's actually wrong and fix it at the source.

### Communication Style

The user is a junior-level developer experienced with the MERN stack (MongoDB, Express, React, Node.js). Shopify theme development (Liquid, Web Components, Shopify-specific patterns) is new territory.

- **Default to high-level explanations.** Lead with "what" and "why" before "how."
- **Teach as you go.** When making a design decision, explain the reasoning — not just what to do, but why.
- **Use concrete examples.** Instead of "this violates BEM," show the correct naming.
- **Be direct about quality.** If code is messy, say so clearly.

## Client Context

**Store:** Arctic Fresh — `arcticfresh.ca` (storefront), `arcticfresh.myshopify.com` (admin).

**Parent company:** Friendship Fast (Ottawa-based procurement & shipping). Arctic Fresh was inherited, not built — they're committed to keeping it on Shopify as a separate brand. Friendship Fast's own site (`shop.friendshipfast.ca`) is on WooCommerce and out of scope for this repo.

**Business model:** Online grocery delivery to remote Nunavut communities. Products are shipped from southern Canada with shipping rates that vary by community and product category. Nutrition North Canada subsidizes eligible food items.

**Brand guardrails (Inuit-operated, Northern Canadian context):** Read `docs/brand-guardrails.md` before making visual or copy decisions that touch the brand surface. Generic Arctic/Northern design tropes — totems (those are West Coast First Nations, not Inuit), igloos, sled dogs, decorative syllabics — actively damage credibility. The doc captures the don'ts and the reference brands (Canadian North, Air Inuit, Government of Nunavut) worth following.


## References

The `references/` directory is **gitignored** — fresh clones won't have it. Locally it holds read-only material for context, not code to migrate or copy from.

Beyond the two project-specific references documented below, several **third-party theme exports** are kept as read-only lookups for "how does Horizon do X" or "what's a sensible pattern for Y":

- `references/arcticfresh-horizon-export/` — Shopify's stock Horizon theme (vanilla baseline)
- `references/arcticfresh-dawn-export/`, `references/dawn/` — Dawn (older Shopify reference theme; useful for established patterns)
- `references/dwell/`, `references/hyper/` — newer Horizon-based themes; helpful for seeing how others extend the framework

Read first; don't reinvent. Don't flag these as bloat — they're intentional.

Two of the directories actively inform theme decisions:

### `references/northbound/`

The Northbound custom Shopify app (React Router v7 + Prisma + TypeScript). This is the shipping logic backend that integrates with this theme. Key integration points:

- **Theme app extension** (`extensions/storefront-selector/`): Community selector block that injects into the storefront via `@app` blocks. Uses `data-northbound-*` attributes for DOM hooks.
- **App proxy endpoints:**
  - `GET /apps/northbound/bootstrap` — community list, selected community, shop config
  - `POST /apps/northbound/prices` — community-specific pricing for product variants
- **Carrier service:** `POST /api/carrier-service` — real-time shipping rate calculation at checkout
- **Storefront JS modules:** runtime, UI, DOM, pricing, controller — all vanilla JS, loaded via deferred script tags
- **`references/northbound/CLAUDE.md`** has full architecture docs, domain model, and decision principles for the app itself.

**Active theme-side feature requests** (Apr 2026 client feedback — captured in `references/northbound/docs/waleed-feedback-2026-04-27.md`): NNC eligibility badges on product cards, per-item NNC breakdown panel on PDP, +/- qty buttons in cart line items. Checkout disclaimers and the Canadian North employee gate are likely checkout-extension work, not theme. Cross-check `references/northbound/docs/resources/arcticfresh-working-doc.md` for current state before starting any of these.

### `references/arcticfresh-legacy-theme-export/`

The existing pre-OS2.0 vintage theme **currently running on the live store** and the one Northbound is integrated with today. This is a copy of the canonical export at `northbound/docs/prod_theme_export_apr_27_2026/` — re-sync from there if you suspect drift (last sync: 2026-04-29). Reference only — we are NOT migrating from this. Useful for understanding:

- **What the store currently looks like** (layout, sections, features)
- **Northbound integration hooks** in the legacy theme: `data-northbound-price-surface`, `data-northbound-price-display`, `data-northbound-add-to-cart` attributes in `snippets/product-item.liquid`, `snippets/product-form.liquid`, `templates/product.liquid`, `templates/cart.liquid`
- **Legacy navigation structure:** 3-tier header (announcement bar, logo/search/cart, department mega menu)
- **Legacy homepage sections:** jumbotron slider, featured collections (department grid), featured products, featured content, testimonials, newsletter
- **Cart features:** order preferences (substitutions), minimum order alert, estimated shipping display

**Do not** copy code from the legacy theme. It uses jQuery, Bootstrap 4, Slick.js, deprecated Liquid tags, and has console.log statements left in production code.

## Playground

`playground/` is a static HTML/CSS/JS sandbox for prototyping section designs in the browser before committing to a Liquid implementation. Vanilla only — no build step, no frameworks. Use real catalog data (department names, collection handles) and the Arctic Fresh color tokens so prototypes approximate production. See `playground/CLAUDE.md` for naming conventions and rules. Delete files once the corresponding Liquid section is built and approved.

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
playground/      — Static HTML/CSS/JS prototypes for visual iteration before Liquid (see playground/CLAUDE.md)
docs/            — Project-internal docs: roadmap, Horizon learnings, ADRs (committed, fresh sessions read these)
references/      — Read-only reference material (gitignored, see References section)
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
- **Development themes auto-delete after 7 days of inactivity** or on `shopify auth logout`.

## Commands

```bash
# Development
shopify theme dev --store arcticfresh       # Local dev server with hot reload (localhost:9292)
shopify theme check                          # Lint Liquid and JSON (run before pushing)

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
sonar "your query here"              # Use when Shopify docs or local context aren't enough
```

Use `sonar` when you need up-to-date information that isn't in the codebase or Shopify docs — e.g., Horizon-specific behavior, Liquid edge cases, NNC program details, or community-specific context. It queries Perplexity's sonar-pro-search model and returns results directly in the terminal.

## Shopify Documentation

Append `.md` to any Shopify docs URL for clean markdown. Start at https://shopify.dev/docs/storefronts/themes.md.

## Live Store Recon (`admin_gql`)

A standalone helper at `~/.local/bin/admin_gql` provides direct Admin GraphQL access to the live `arcticfresh.myshopify.com` store. Use it whenever you need to verify catalog state — collection handles, product metafields, image presence, inventory, etc. — instead of guessing or relying on stale exports.

**Token:** stored in `~/.config/arcticfresh/admin.token` (chmod 600), sourced from the Northbound app's offline session. If `admin_gql` returns 401, ask the user to refresh the token.

**Usage:**

```bash
admin_gql '{ shop { name } }' | jq .
admin_gql 'query { collectionByHandle(handle: "fruits-vegetables") { handle title image { url } productsCount { count } } }' | jq .
admin_gql 'query { productByHandle(handle: "some-handle") { handle metafields(first: 20) { nodes { namespace key value } } } }' | jq .
```

API version defaults to `2026-04`. Override with a second arg: `admin_gql 'query { ... }' 2025-10`.

**When to reach for it:** confirming collection handles, checking for admin-uploaded collection images, reading product metafields (`custom.shipping_class`, `custom.nnc_item_code`), searching the catalog by title/tag/vendor.

Theme code should read NNC product data from `custom.shipping_class` and `custom.nnc_item_code` (Northbound is the source of truth). For background on the legacy data layout, see `references/northbound/docs/resources/current-site-setup.md`.

**Don't** mutate data — the token has write scopes, but theme work should never write to the live store.

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

The theme should not contain Northbound-specific code — the app extension handles all Northbound logic. Where the theme needs to accommodate it:

- Ensure product card block schemas include `{ "type": "@app" }` in their allowed blocks
- Ensure cart and product sections accept app blocks
- Avoid overriding price display in ways that conflict with Northbound's dynamic pricing

### App modification is in scope

The Northbound app source lives at `references/northbound/` and **can be modified** to support new theme features. The integration boundary still holds (Northbound logic stays in the app, not in theme code), but extending the app — new `@app` blocks, new app proxy endpoints, new storefront JS hooks, schema additions — is fair game when the theme needs them. When making app changes:

- Coordinate the theme change and the app change so they ship together
- Document the new contract (block name, endpoint shape) in `docs/horizon-notes.md` so future theme work knows what's available
- Don't fork integration logic into the theme just to avoid touching the app

## Domain Context

### Nutrition North Canada (NNC)

NNC is a federal subsidy program that reduces the cost of eligible food and household items shipped to isolated northern communities. Key concepts:

- **Shipping classes:** `NNC1` (highest subsidy — perishable/nutritious), `NNC2` (moderate subsidy — other food), `NNC7` (non-food essential items), `UNSUBSIDIZED` (no subsidy)
- **Community zones:** Different communities receive different subsidy amounts based on isolation level
- **Subsidy display:** Arctic Fresh shows community-specific "all-in" pricing (base price + shipping - subsidy) on the storefront, while the actual shipping calculation happens at checkout via the carrier service. **For storefront pricing patterns** (cards, PDP breakdown, what to never show), see `docs/nnc-display.md` — NNC is treated as eligibility, not as a sale.

### Arctic Fresh Specifics

- **15 Nunavut communities served:** Arctic Bay, Cape Dorset, Clyde River, Grise Fiord, Hall Beach, Igloolik, Iqaluit, Kimmirut, Kuujjuaq, Pangnirtung, Pond Inlet, Qikiqtarjuaq, Rankin Inlet, Resolute, Sanikiluaq.
- **9,000+ SKUs** across departments: Meat & Seafood, Produce, Dairy & Eggs, Pantry, Frozen Foods, Drinks, Baby & Kids, Household, Health & Beauty.
- **Minimum order:** $95 (raised from $75 in the 2026-03-08 client email; Northbound's `ShopConfig` is authoritative).
- **Community selection** is the first thing a customer does — it determines all displayed pricing and available shipping rates.
- **Northbound app is live on production**, processing real orders. Theme changes that interact with pricing, cart, or shipping should not assume the app's storefront JS is absent.

## Workflow

1. **Research first.** Before non-trivial design or architecture decisions (homepage layout, cart UX, navigation patterns, conversion-critical UI), validate with `sonar` against current ecommerce best practices and Shopify-specific guidance. Cheaper than rework. Skip only when the decision is genuinely clear-cut.
2. **Document Horizon learnings as you go.** Horizon is new and the conventions aren't fully established. When you discover a gotcha, working pattern, or non-obvious behavior, capture it in `docs/horizon-notes.md`. Future Claude sessions read this before diving in — it's the shortcut around the learning curve.
3. **Test in the browser.** `shopify theme check` and type-checking don't catch layout bugs. Every section/block change should be loaded via `shopify theme dev` and visually inspected at mobile and desktop widths before declaring done. Check the golden path and at least one edge case (long text, missing image, empty state).
4. **Commit often.** Small, focused commits with meaningful messages. Reset cleanly when an experiment fails. The worktree should rarely have more than one feature's worth of uncommitted changes.
5. **Roadmap lives in `docs/roadmap.md`.** Major work areas, current status, open questions. Read it before starting and update it as scope shifts.

## Decision Principles

1. **Code over configuration.** Write custom sections and Liquid rather than composing layouts through JSON template config and theme editor settings. Keep `templates/*.json` files lean — they should reference sections, not contain sprawling block/setting definitions. Hardcode values directly in `.liquid` files where the content is known and stable. Theme editor configurability for the client is not a priority.
2. **App blocks for integration.** Northbound integration should happen through `@app` blocks, not baked-in theme code. This keeps the theme and app independently deployable.
3. **Mobile-first for grocery.** Many Arctic Fresh customers browse on phones. Every layout decision should consider the mobile experience first — horizontal scrolling for department nav, accordion menus, touch-friendly quick-add targets (44x44px minimum).
4. **Performance matters.** Remote Nunavut communities often have limited bandwidth. Lazy load images, keep JS minimal, avoid render-blocking resources. Target Lighthouse score of 60+ across home, product, and collection pages.
5. **Static pages are template-driven.** The legacy theme has many static informational pages (about, FAQ, shipping policy, return policy, etc.). Build one flexible page template (`templates/page.json` + a content-rich section) and populate via the Shopify admin's page editor. Do not write a custom section per static page.

## Code Style

Follows Horizon conventions (see `.claude/skills/shopify-liquid/` and `.claude/skills/accessibility/`):

- **CSS:** BEM naming (`.block__element--modifier`), specificity target `0 1 0`, never IDs, scoped via `{% stylesheet %}`, CSS custom properties namespaced per component, logical properties for RTL support
- **JS:** `const` over `let`, `for...of` over `.forEach()`, Web Components extending `Component`, declarative event binding (`on:click="/method"`), JSDoc typedefs for refs, `AbortController` for fetch cleanup, no external dependencies
- **Liquid:** `{% render %}` over `{% include %}` (deprecated), `{% liquid %}` for multiline blocks, `{% doc %}` for snippet params, inline Liquid preferred over variable declarations
- **Schemas:** Edit `{% schema %}` inline in `.liquid` files. Keep minimal — usually empty `settings`, just `name` and `presets`.
